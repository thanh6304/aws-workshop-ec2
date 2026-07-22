---
title: "5.5.5 Xử lý thông điệp từ Amazon SQS"
date: 2024-01-01
weight: 5
chapter: false
---

# 5.5.5 Xử lý thông điệp từ Amazon SQS

## Tổng quan

Amazon SQS được sử dụng để truyền các yêu cầu phân tích AI từ Backend Lambda đến AI Worker Lambda theo cơ chế bất đồng bộ.

Khi người dùng gửi yêu cầu dự báo qua API, Backend Lambda không thực hiện truy vấn Athena hoặc gọi Amazon Bedrock ngay lập tức. Thay vào đó, Backend Lambda tạo một thông điệp và gửi vào Amazon SQS.

AI Worker Lambda sẽ tự động nhận thông điệp từ Queue, xử lý yêu cầu và xóa thông điệp sau khi hoàn thành thành công.

Cơ chế này giúp:

- Giảm thời gian phản hồi của Backend API.
- Tách biệt API và tác vụ AI.
- Hỗ trợ xử lý nhiều yêu cầu đồng thời.
- Tự động thử lại khi quá trình xử lý gặp lỗi.
- Hạn chế mất dữ liệu khi dịch vụ tạm thời không khả dụng.

---

# Kiến trúc

```text
Client
   │
   ▼
Amazon API Gateway
   │
   ▼
Backend Lambda
   │
   │ SendMessage
   ▼
Amazon SQS
   │
   │ Event Source Mapping
   ▼
AI Worker Lambda
   │
   ├── Amazon Athena
   ├── Amazon Bedrock
   └── Amazon RDS PostgreSQL
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Hiểu cấu trúc thông điệp gửi vào Amazon SQS.
- Cấu hình SQS Event Source Mapping cho Lambda.
- Xử lý nhiều thông điệp trong một Batch.
- Cấu hình Visibility Timeout.
- Thiết lập cơ chế Retry.
- Sử dụng Dead Letter Queue.
- Xử lý lỗi từng phần với Partial Batch Response.

---

# Điều kiện tiên quyết

Trước khi thực hiện, hãy đảm bảo:

- Queue `ai-processing-queue` đã được tạo.
- Dead Letter Queue đã được cấu hình.
- AI Worker Lambda đã được triển khai.
- Lambda Execution Role có quyền đọc và xóa thông điệp từ SQS.
- Trigger SQS đã được gắn với AI Worker Lambda.

---

# Luồng xử lý thông điệp

Quá trình xử lý diễn ra như sau:

```text
1. Backend Lambda nhận yêu cầu từ API Gateway
                    │
                    ▼
2. Backend Lambda gửi Message vào Amazon SQS
                    │
                    ▼
3. SQS kích hoạt AI Worker Lambda
                    │
                    ▼
4. Lambda đọc và xử lý Message
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
      Thành công             Thất bại
          │                   │
          ▼                   ▼
SQS xóa Message        Message xuất hiện lại
                              │
                              ▼
                     Thử lại đến giới hạn
                              │
                              ▼
                      Dead Letter Queue
```

---

# Các bước thực hiện

## Bước 1. Chuẩn bị cấu trúc thông điệp

Khi người dùng gửi yêu cầu:

```http
POST /ai/predict
```

Request body:

```json
{
  "warehouse": "WH001",
  "forecastDays": 30
}
```

Backend Lambda nên bổ sung các trường phục vụ theo dõi yêu cầu.

Ví dụ thông điệp gửi vào SQS:

```json
{
  "requestId": "REQ-20260722-001",
  "requestType": "DEMAND_FORECAST",
  "warehouse": "WH001",
  "forecastDays": 30,
  "requestedBy": "admin@example.com",
  "requestedAt": "2026-07-22T09:30:00Z",
  "status": "PENDING"
}
```

Trong đó:

| Trường         | Ý nghĩa                      |
| -------------- | ---------------------------- |
| `requestId`    | Mã duy nhất của yêu cầu      |
| `requestType`  | Loại tác vụ AI cần thực hiện |
| `warehouse`    | Kho cần phân tích            |
| `forecastDays` | Số ngày cần dự báo           |
| `requestedBy`  | Người gửi yêu cầu            |
| `requestedAt`  | Thời điểm tạo yêu cầu        |
| `status`       | Trạng thái ban đầu           |

---

## Bước 2. Gửi thông điệp từ Backend Lambda

Backend Lambda sử dụng AWS SDK để gửi thông điệp vào Amazon SQS.

Ví dụ Java:

```java
import java.util.UUID;

import software.amazon.awssdk.services.sqs.SqsClient;
import software.amazon.awssdk.services.sqs.model.SendMessageRequest;

public class AiRequestPublisher {

    private final SqsClient sqsClient;
    private final String queueUrl;

    public AiRequestPublisher(SqsClient sqsClient, String queueUrl) {
        this.sqsClient = sqsClient;
        this.queueUrl = queueUrl;
    }

    public String publish(String messageBody) {
        String requestId = UUID.randomUUID().toString();

        SendMessageRequest request = SendMessageRequest.builder()
                .queueUrl(queueUrl)
                .messageBody(messageBody)
                .messageAttributes(Map.of(
                        "requestId",
                        MessageAttributeValue.builder()
                                .dataType("String")
                                .stringValue(requestId)
                                .build()
                ))
                .build();

        sqsClient.sendMessage(request);

        return requestId;
    }
}
```

> Khi triển khai thực tế, nên lấy Queue URL từ biến môi trường hoặc thông qua AWS SDK, không ghi cố định trong mã nguồn.

---

## Bước 3. Kiểm tra Trigger của AI Worker Lambda

Mở AWS Lambda Console.

Chọn Function:

```text
ai-supplychain-worker
```

Chọn:

```text
Configuration
```

Sau đó chọn:

```text
Triggers
```

Đảm bảo Trigger hiển thị:

| Thuộc tính | Giá trị             |
| ---------- | ------------------- |
| Service    | Amazon SQS          |
| Queue      | ai-processing-queue |
| Status     | Enabled             |
| Batch size | 10                  |

Nếu chưa có Trigger, chọn:

```text
Add trigger
```

Sau đó chọn Amazon SQS và Queue tương ứng.

---

## Bước 4. Xử lý SQS Event trong Lambda

Khi SQS kích hoạt Lambda, Lambda nhận được một danh sách các thông điệp trong trường `Records`.

Ví dụ SQS Event:

```json
{
  "Records": [
    {
      "messageId": "4f624458-1f8c-4d52-b659-1f234567890a",
      "receiptHandle": "AQEB...",
      "body": "{\"requestId\":\"REQ-001\",\"warehouse\":\"WH001\",\"forecastDays\":30}",
      "eventSource": "aws:sqs",
      "awsRegion": "ap-southeast-1"
    }
  ]
}
```

Lambda cần đọc trường:

```text
Records[].body
```

để lấy nội dung yêu cầu.

Ví dụ Handler Java:

```java
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import com.amazonaws.services.lambda.runtime.events.SQSEvent;
import com.fasterxml.jackson.databind.ObjectMapper;

public class AiWorkerHandler implements RequestHandler<SQSEvent, Void> {

    private final ObjectMapper objectMapper = new ObjectMapper();
    private final AiProcessingService aiProcessingService =
            new AiProcessingService();

    @Override
    public Void handleRequest(SQSEvent event, Context context) {
        for (SQSEvent.SQSMessage message : event.getRecords()) {
            try {
                AiRequest request = objectMapper.readValue(
                        message.getBody(),
                        AiRequest.class
                );

                context.getLogger().log(
                        "Processing request: " + request.getRequestId()
                );

                aiProcessingService.process(request);

                context.getLogger().log(
                        "Completed request: " + request.getRequestId()
                );

            } catch (Exception exception) {
                context.getLogger().log(
                        "Failed message: " + message.getMessageId()
                                + " - " + exception.getMessage()
                );

                throw new RuntimeException(exception);
            }
        }

        return null;
    }
}
```

Nếu Lambda ném ra Exception, SQS sẽ không xóa các thông điệp trong Batch và sẽ thử gửi lại sau khi Visibility Timeout kết thúc.

---

## Bước 5. Xử lý nghiệp vụ AI

Mỗi thông điệp sẽ đi qua quy trình:

```text
Đọc yêu cầu từ SQS
        │
        ▼
Kiểm tra dữ liệu đầu vào
        │
        ▼
Chạy truy vấn Amazon Athena
        │
        ▼
Chuẩn bị Prompt
        │
        ▼
Gọi Amazon Bedrock
        │
        ▼
Lưu kết quả vào Amazon RDS
```

Ví dụ:

```java
public void process(AiRequest request) {
    validateRequest(request);

    String athenaResult = athenaService.querySupplyChainData(
            request.getWarehouse(),
            request.getForecastDays()
    );

    String prompt = promptService.buildPrompt(
            request,
            athenaResult
    );

    String aiResponse = bedrockService.invokeModel(prompt);

    resultService.saveResult(
            request,
            aiResponse
    );
}
```

---

## Bước 6. Cấu hình Visibility Timeout

Visibility Timeout là khoảng thời gian thông điệp bị ẩn khỏi những Consumer khác sau khi Lambda nhận thông điệp.

Ví dụ:

```text
Lambda Timeout: 60 giây
SQS Visibility Timeout: 180 giây
```

Nên đặt Visibility Timeout lớn hơn thời gian xử lý Lambda để tránh cùng một thông điệp bị xử lý đồng thời nhiều lần.

Cấu hình khuyến nghị cho workshop:

| Thuộc tính                | Giá trị  |
| ------------------------- | -------- |
| Lambda Timeout            | 60 giây  |
| Visibility Timeout        | 180 giây |
| Message Retention         | 4 ngày   |
| Receive Message Wait Time | 20 giây  |

Nếu tác vụ Athena hoặc Bedrock cần nhiều thời gian hơn, hãy tăng Lambda Timeout và Visibility Timeout tương ứng.

---

## Bước 7. Cấu hình Retry và Dead Letter Queue

Khi xử lý thất bại, SQS sẽ đưa thông điệp trở lại Queue sau khi hết Visibility Timeout.

Sau một số lần thất bại, thông điệp sẽ được chuyển đến Dead Letter Queue.

Ví dụ:

```text
Source Queue:
ai-processing-queue

Dead Letter Queue:
ai-processing-dlq

Maximum receives:
3
```

Luồng xử lý:

```text
Lần 1 thất bại
      │
      ▼
Lần 2 thất bại
      │
      ▼
Lần 3 thất bại
      │
      ▼
Dead Letter Queue
```

Dead Letter Queue giúp lưu lại những yêu cầu không thể xử lý để quản trị viên có thể kiểm tra sau.

---

## Bước 8. Bật Partial Batch Response

Theo cách xử lý mặc định, nếu một thông điệp trong Batch thất bại thì toàn bộ Batch có thể được xử lý lại.

Ví dụ Batch có năm thông điệp:

```text
Message 1: Thành công
Message 2: Thành công
Message 3: Thất bại
Message 4: Thành công
Message 5: Thành công
```

Nếu không sử dụng Partial Batch Response, cả năm thông điệp có thể xuất hiện lại trong Queue.

Để chỉ thử lại Message 3, hãy bật:

```text
Report batch item failures
```

Lambda trả về danh sách Message ID thất bại.

Ví dụ Handler:

```java
import com.amazonaws.services.lambda.runtime.events.SQSBatchResponse;
import com.amazonaws.services.lambda.runtime.events.SQSEvent;

public class AiWorkerHandler
        implements RequestHandler<SQSEvent, SQSBatchResponse> {

    @Override
    public SQSBatchResponse handleRequest(
            SQSEvent event,
            Context context
    ) {
        List<SQSBatchResponse.BatchItemFailure> failures =
                new ArrayList<>();

        for (SQSEvent.SQSMessage message : event.getRecords()) {
            try {
                processMessage(message);
            } catch (Exception exception) {
                context.getLogger().log(
                        "Failed message: " + message.getMessageId()
                );

                failures.add(
                        new SQSBatchResponse.BatchItemFailure(
                                message.getMessageId()
                        )
                );
            }
        }

        return new SQSBatchResponse(failures);
    }
}
```

Cơ chế này giúp giảm việc xử lý lặp lại những thông điệp đã thành công.

---

## Bước 9. Đảm bảo tính Idempotent

Amazon SQS Standard Queue cung cấp cơ chế giao nhận ít nhất một lần. Vì vậy, cùng một thông điệp có thể được gửi đến Lambda nhiều hơn một lần.

Lambda Worker cần được thiết kế theo nguyên tắc Idempotent, nghĩa là xử lý lại cùng một `requestId` không tạo ra nhiều kết quả trùng lặp.

Ví dụ kiểm tra trước khi xử lý:

```java
if (resultRepository.existsByRequestId(request.getRequestId())) {
    logger.info(
            "Request {} was already processed",
            request.getRequestId()
    );

    return;
}
```

Trong PostgreSQL, nên đặt ràng buộc duy nhất:

```sql
ALTER TABLE ai_analysis_results
ADD CONSTRAINT uk_ai_request_id UNIQUE (request_id);
```

Nhờ vậy, một yêu cầu chỉ được lưu một kết quả.

---

## Bước 10. Kiểm tra thủ công bằng AWS Console

Mở Amazon SQS Console.

Chọn Queue:

```text
ai-processing-queue
```

Chọn:

```text
Send and receive messages
```

Nhập Message Body:

```json
{
  "requestId": "REQ-MANUAL-001",
  "requestType": "DEMAND_FORECAST",
  "warehouse": "WH001",
  "forecastDays": 30,
  "requestedBy": "admin@example.com",
  "requestedAt": "2026-07-22T09:30:00Z"
}
```

Chọn:

```text
Send message
```

Sau đó mở CloudWatch Log Group:

```text
/aws/lambda/ai-supplychain-worker
```

Kết quả mong đợi:

```text
Processing request: REQ-MANUAL-001

Athena query completed

Bedrock model invoked successfully

AI result saved to PostgreSQL

Completed request: REQ-MANUAL-001
```

---

# Theo dõi trạng thái Queue

Trong Amazon SQS Console, theo dõi các chỉ số:

| Chỉ số             | Ý nghĩa                           |
| ------------------ | --------------------------------- |
| Messages available | Thông điệp đang chờ xử lý         |
| Messages in flight | Thông điệp đang được Lambda xử lý |
| Messages delayed   | Thông điệp đang bị trì hoãn       |
| Messages sent      | Tổng số thông điệp đã gửi         |
| Messages received  | Tổng số thông điệp đã được đọc    |

Sau khi xử lý thành công:

```text
Messages available = 0
```

Nếu có lỗi liên tục, kiểm tra:

```text
ai-processing-dlq
```

---

# Xử lý một số lỗi thường gặp

| Lỗi                            | Nguyên nhân                       | Cách khắc phục                        |
| ------------------------------ | --------------------------------- | ------------------------------------- |
| Lambda không được kích hoạt    | Trigger bị Disable                | Bật lại SQS Trigger                   |
| Access Denied                  | Lambda thiếu quyền SQS            | Kiểm tra IAM Role                     |
| Message được xử lý nhiều lần   | Lambda chưa Idempotent            | Kiểm tra `requestId` trước khi xử lý  |
| Message liên tục xuất hiện lại | Lambda xử lý lỗi hoặc hết Timeout | Kiểm tra CloudWatch Logs              |
| Toàn bộ Batch bị Retry         | Chưa bật Partial Batch Response   | Bật Report batch item failures        |
| Message bị đưa vào DLQ         | Vượt quá Maximum receives         | Kiểm tra lỗi Athena, Bedrock hoặc RDS |
| Lambda bị Timeout              | Tác vụ AI mất quá nhiều thời gian | Tăng Timeout và Visibility Timeout    |

---

# Best Practices

- Sử dụng `requestId` duy nhất cho từng yêu cầu.
- Thiết kế Lambda theo nguyên tắc Idempotent.
- Bật Partial Batch Response.
- Cấu hình Dead Letter Queue.
- Đặt Visibility Timeout lớn hơn Lambda Timeout.
- Không ghi dữ liệu nhạy cảm vào Message Body.
- Theo dõi Queue và Lambda bằng CloudWatch.
- Giới hạn Batch Size phù hợp với tài nguyên Lambda.
- Mã hóa Queue bằng AWS KMS trong môi trường Production.
- Cấu hình Reserved Concurrency nếu cần kiểm soát tải đến Athena, Bedrock hoặc RDS.

---

# Kiểm tra kết quả

Đảm bảo:

- Backend Lambda gửi được thông điệp vào SQS.
- AI Worker Lambda tự động nhận thông điệp.
- Lambda xử lý thành công từng yêu cầu.
- Thông điệp thành công được xóa khỏi Queue.
- Thông điệp thất bại được thử lại.
- Thông điệp lỗi liên tục được chuyển đến DLQ.
- CloudWatch ghi nhận đầy đủ quá trình xử lý.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Hoàn thiện cơ chế xử lý AI bất đồng bộ.
- Kết nối Amazon SQS với AI Worker Lambda.
- Cấu hình Retry và Dead Letter Queue.
- Hạn chế xử lý lặp bằng Idempotency.
- Tối ưu Batch bằng Partial Batch Response.
- Tăng độ tin cậy và khả năng mở rộng cho AI Pipeline.
