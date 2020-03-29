---
title: B2B 消息的 X12 跟蹤架構
description: 創建跟蹤架構以監視 Azure 邏輯應用的 X12 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905295"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>創建用於在 Azure 邏輯應用中跟蹤 X12 消息的架構

您可以在整合帳戶中使用這些 X12 追蹤結構描述，協助您監視企業對企業 (B2B) 交易的成功、錯誤及訊息屬性︰

* X12 交易集追蹤結構描述
* X12 事務集確認跟蹤架構
* X12 交換追蹤結構描述
* X12 交換確認跟蹤架構
* X12 功能群組追蹤結構描述
* X12 功能組確認跟蹤架構

## <a name="x12-transaction-set-tracking-schema"></a>X12 交易集追蹤結構描述

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 消息流的方向，該方向為`receive`或`send` |
| interchangeControlNumber | 否 | String | 交換控制編號 |
| functionalGroupControlNumber | 否 | String | 功能控制編號 |
| transactionSetControlNumber | 否 | String | 交易集控制編號 |
| CorrelationMessageId | 否 | String | 關聯消息 ID，它是 [協定名稱]*組控制號*[事務集控制編號] 的組合 |
| messageType | 否 | String | 事務集或單據類型 |
| isMessageFailed | 是 | Boolean | X12 消息是否失敗 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 技術確認是否在 X12 協定中配置 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 功能確認是否在 X12 協定中配置 |
| needAk2LoopForValidMessages | 是 | Boolean | 有效消息是否需要 AK2 迴圈 |
| segmentsCount | 否 | 整數  | X12 事務集中的段數 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 事務集確認跟蹤架構

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 消息流的方向，該方向為`receive`或`send` |
| interchangeControlNumber | 否 | String | 交換功能確認的控制編號。 該值僅填充發送到合作夥伴的消息的發送端，其中收到發送給合作夥伴的消息的功能確認。 |
| functionalGroupControlNumber | 否 | String | 功能確認的功能組控制編號。 該值僅填充發送到合作夥伴的消息的發送端，其中收到發送給合作夥伴的消息的功能確認 |
| isaSegment | 否 | String | 訊息的 ISA 區段。 該值僅填充發送到合作夥伴的消息的發送端，其中收到發送給合作夥伴的消息的功能確認 |
| isaSegment | 否 | String | 訊息的 GS 區段。 該值僅填充發送到合作夥伴的消息的發送端，其中收到發送給合作夥伴的消息的功能確認 |
| respondingfunctionalGroupControlNumber | 否 | String | 回應交換控制編號 |
| respondingFunctionalGroupId | 否 | String | 回應功能組 ID，在確認中映射到 AK101 |
| respondingtransactionSetControlNumber | 否 | String | 回應事務集控制編號 |
| respondingTransactionSetId | 否 | String | 回應事務集 ID，該 ID 映射到確認中的 AK201 |
| StatusCode | 是 | Boolean | 事務集確認狀態碼 |
| segmentsCount | 是 | 例舉 | 具有這些允許值的確認狀態碼`Accepted`： `Rejected`、 和`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 使用這些允許值處理確認的狀態：`Received`和`Generated`。`Sent` |
| CorrelationMessageId | 否 | String | 關聯消息 ID，它是 [協定名稱]*組控制號*[事務集控制編號] 的組合 |
| isMessageFailed | 是 | Boolean | X12 消息是否失敗 |
| ak2Segment | 否 | String | 確認接收的功能組中的事務集 |
| ak3Segment | 否 | String | 報告資料段中的錯誤 |
| ak5Segment | 否 | String | 報告 AK2 段中標識的事務集是否被接受或拒絕，以及原因 |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 交換追蹤結構描述

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 消息流的方向，該方向為`receive`或`send` |
| interchangeControlNumber | 否 | String | 交換控制編號 |
| isaSegment | 否 | String | 消息 ISA 段 |
| isTechnicalAcknowledgmentExpected | Boolean | 技術確認是否在 X12 協定中配置  |
| isMessageFailed | 是 | Boolean | X12 消息是否失敗 |
| isa09 | 否 | String | X12 文檔交換日期 |
| isa10 | 否 | String | X12 文檔交換時間 |
| isa11 | 否 | String | X12 交換控制標準識別碼 |
| isa12 | 否 | String | X12 交換控制版本號 |
| isa14 | 否 | String | 請求 X12 確認 |
| isa15 | 否 | String | 測試或生產的指標 |
| isa16 | 否 | String | 項目分隔符號 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 交換確認跟蹤架構

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 消息流的方向，該方向為`receive`或`send` |
| interchangeControlNumber | 否 | String | 從合作夥伴收到的技術確認的交換控制編號 |
| isaSegment | 否 | String | 從合作夥伴收到的技術確認的 ISA 段 |
| respondingInterchangeControlNumber | 否 | String | 從合作夥伴收到的技術確認的交換控制編號 |
| isMessageFailed | 是 | Boolean | X12 消息是否失敗 |
| StatusCode | 是 | 例舉 | 將確認狀態碼與這些允許的值交換`Accepted`：`Rejected`和 。`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 具有這些允許值的確認狀態`Received`： `Generated`、 和`Sent` |
| ta102 | 否 | String | 交換日期 |
| ta103 | 否 | String | 交換時間 |
| ta105 | 否 | String | 交換注釋代碼 |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 功能群組追蹤結構描述

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 消息解析到的 X12 協定的名稱 |
| direction | 是 | 例舉 | 消息流的方向，接收或發送 |
| interchangeControlNumber | 否 | String | 交換控制編號 |
| functionalGroupControlNumber | 否 | String | 功能控制編號 |
| isaSegment | 否 | String | 消息 GS 段 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 技術確認是否在 X12 協定中配置 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 功能確認是否在 X12 協定中配置 |
| isMessageFailed | 是 | Boolean | X12 消息是否失敗 |
| gs01 | 否 | String | 功能識別碼代碼 |
| gs02 | 否 | String | 應用程式寄件者的代碼 |
| gs03 | 否 | String | 應用程式接收器的代碼 |
| gs04 | 否 | String | 功能組日期 |
| gs05 | 否 | String | 功能組時間 |
| gs07 | 否 | String | 負責代理代碼 |
| gs08 | 否 | String | 版本、版本或行業的識別碼代碼 |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 功能組確認跟蹤架構

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | X12 消息接收方的合作夥伴名稱 |
| senderQualifier | 是 | String | 發送合作夥伴限定詞 |
| senderIdentifier | 是 | String | 發送合作夥伴識別碼 |
| receiverQualifier | 是 | String | 接收合作夥伴限定詞 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | String | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 消息流的方向，該方向為`receive`或`send` |
| interchangeControlNumber | 否 | String | 交換控制編號，當從合作夥伴收到技術確認時填充發送端 |
| functionalGroupControlNumber | 否 | String | 技術確認的功能組控制編號，當從合作夥伴收到技術確認時，該數位填充給發送方 |
| isaSegment | 否 | String | 與交換控制編號相同，但僅在特定情況下填充 |
| isaSegment | 否 | String | 與功能組控制編號相同，但僅在特定情況下填充 |
| respondingfunctionalGroupControlNumber | 否 | String | 原始功能組的控制編號 |
| respondingFunctionalGroupId | 否 | String | 在確認功能組 ID 中映射到 AK101 |
| isMessageFailed | Boolean | X12 消息是否失敗 |
| StatusCode | 是 | 例舉 | 具有這些允許值的確認狀態碼`Accepted`： `Rejected`、 和`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 使用這些允許值處理確認的狀態：`Received`和`Generated`。`Sent` |
| ak903 | 否 | String | 已接收的交易集合數目 |
| ak904 | 否 | String | 標識的功能組中接受的事務集數 |
| ak9Segment | 否 | String | AK1 部門中標識的功能組是否被接受或拒絕，以及原因 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [AS2 跟蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器記錄監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)