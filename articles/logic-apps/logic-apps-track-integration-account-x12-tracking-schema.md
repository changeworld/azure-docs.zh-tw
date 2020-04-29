---
title: 適用于 B2B 訊息的 X12 追蹤架構
description: 建立追蹤架構以監視 Azure Logic Apps 的 X12 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905295"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立用來追蹤 X12 訊息的架構

您可以在整合帳戶中使用這些 X12 追蹤結構描述，協助您監視企業對企業 (B2B) 交易的成功、錯誤及訊息屬性︰

* X12 交易集追蹤結構描述
* X12 交易集通知追蹤架構
* X12 交換追蹤結構描述
* X12 交換通知追蹤架構
* X12 功能群組追蹤結構描述
* X12 功能群組通知追蹤架構

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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，也就是`receive`或`send` |
| interchangeControlNumber | 否 | 字串 | 交換控制編號 |
| functionalGroupControlNumber | 否 | 字串 | 功能控制編號 |
| transactionSetControlNumber | 否 | 字串 | 交易集控制編號 |
| CorrelationMessageId | 否 | 字串 | 相互關聯訊息識別碼，這是 {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} 的組合 |
| messageType | 否 | 字串 | 交易集或檔案類型 |
| isMessageFailed | 是 | Boolean | X12 訊息是否失敗 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 是否已在 X12 合約中設定技術通知 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 是否已在 X12 合約中設定功能通知 |
| needAk2LoopForValidMessages | 是 | Boolean | 是否需要有效訊息的 AK2 迴圈 |
| segmentsCount | 否 | 整數 | X12 交易集中的區段數目 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 交易集通知追蹤架構

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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，也就是`receive`或`send` |
| interchangeControlNumber | 否 | 字串 | 功能通知的交換控制編號。 僅針對傳送至夥伴的訊息接收功能通知的傳送端，其值才會填入。 |
| functionalGroupControlNumber | 否 | 字串 | 功能認可的功能群組控制編號。 僅針對傳送至夥伴的訊息接收功能通知的傳送端填入值 |
| isaSegment | 否 | 字串 | 訊息的 ISA 區段。 僅針對傳送至夥伴的訊息接收功能通知的傳送端填入值 |
| isaSegment | 否 | 字串 | 訊息的 GS 區段。 僅針對傳送至夥伴的訊息接收功能通知的傳送端填入值 |
| respondingfunctionalGroupControlNumber | 否 | 字串 | 回應交換控制編號 |
| respondingFunctionalGroupId | 否 | 字串 | 回應中的功能群組識別碼，對應至通知中的 AK101 |
| respondingtransactionSetControlNumber | 否 | 字串 | 回應的交易集控制編號 |
| respondingTransactionSetId | 否 | 字串 | 回應中的交易集識別碼，對應至通知中的通知 AK201 |
| StatusCode | 是 | Boolean | 交易集通知狀態碼 |
| segmentsCount | 是 | 例舉 | 具有下列允許值的認可狀態碼`Accepted`： `Rejected`、和`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 具有下列允許值的確認處理狀態： `Received`、和`Generated``Sent` |
| CorrelationMessageId | 否 | 字串 | 相互關聯訊息識別碼，這是 {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} 的組合 |
| isMessageFailed | 是 | Boolean | X12 訊息是否失敗 |
| ak2Segment | 否 | 字串 | 已接收的功能群組內交易集的認可 |
| ak3Segment | 否 | 字串 | 報告資料區段中的錯誤 |
| ak5Segment | 否 | 字串 | 報告是否接受或拒絕 AK2 區段中識別的交易集，以及原因 |
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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，也就是`receive`或`send` |
| interchangeControlNumber | 否 | 字串 | 交換控制編號 |
| isaSegment | 否 | 字串 | 訊息 ISA 區段 |
| isTechnicalAcknowledgmentExpected | 布林值 | 是否已在 X12 合約中設定技術通知  |
| isMessageFailed | 是 | Boolean | X12 訊息是否失敗 |
| isa09 | 否 | 字串 | X12 檔交換日期 |
| isa10 | 否 | 字串 | X12 檔交換時間 |
| isa11 | 否 | 字串 | X12 交換控制標準識別碼 |
| isa12 | 否 | 字串 | X12 交換控制版本號碼 |
| isa14 | 否 | 字串 | 已要求 X12 通知 |
| isa15 | 否 | 字串 | 測試或生產的指標 |
| isa16 | 否 | 字串 | 項目分隔符號 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 交換通知追蹤架構

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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，也就是`receive`或`send` |
| interchangeControlNumber | 否 | 字串 | 從合作夥伴收到的技術通知的交換控制編號 |
| isaSegment | 否 | 字串 | 從合作夥伴收到的技術通知的 ISA 區段 |
| respondingInterchangeControlNumber | 否 | 字串 | 從合作夥伴收到的技術通知的交換控制編號 |
| isMessageFailed | 是 | Boolean | X12 訊息是否失敗 |
| StatusCode | 是 | 例舉 | 交換通知狀態碼與下列允許的值`Accepted`： `Rejected`、和`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 具有下列允許值的認可狀態`Received`： `Generated`、和`Sent` |
| ta102 | 否 | 字串 | 交換日期 |
| ta103 | 否 | 字串 | 交換時間 |
| ta105 | 否 | 字串 | 交換附注代碼 |
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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 要解析訊息之 X12 協定的名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，可以是接收或傳送 |
| interchangeControlNumber | 否 | 字串 | 交換控制編號 |
| functionalGroupControlNumber | 否 | 字串 | 功能控制編號 |
| isaSegment | 否 | 字串 | 訊息 GS 區段 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 是否已在 X12 合約中設定技術通知 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 是否已在 X12 合約中設定功能通知 |
| isMessageFailed | 是 | Boolean | X12 訊息是否失敗 |
| gs01 | 否 | 字串 | 功能識別碼程式碼 |
| gs02 | 否 | 字串 | 應用程式寄件者的代碼 |
| gs03 | 否 | 字串 | 應用程式接收者的代碼 |
| gs04 | 否 | 字串 | 功能群組日期 |
| gs05 | 否 | 字串 | 功能群組時間 |
| gs07 | 否 | 字串 | 負責的機構代碼 |
| gs08 | 否 | 字串 | 版本、版本或產業的識別碼代碼 |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 功能群組通知追蹤架構

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
| senderPartnerName | 否 | 字串 | X12 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | 字串 | X12 訊息接收者的夥伴名稱 |
| senderQualifier | 是 | String | 傳送夥伴辨識符號 |
| senderIdentifier | 是 | String | 傳送夥伴識別碼 |
| receiverQualifier | 是 | String | 接收夥伴辨識符號 |
| receiverQualifier | 是 | String | 接收合作夥伴識別碼 |
| agreementName | 否 | 字串 | 據以解析訊息的 X12 合約名稱 |
| direction | 是 | 例舉 | 訊息流程的方向，也就是`receive`或`send` |
| interchangeControlNumber | 否 | 字串 | 交換控制編號，它會在收到來自合作夥伴的技術通知時，填入傳送端 |
| functionalGroupControlNumber | 否 | 字串 | 技術通知的功能群組控制編號，會在收到來自合作夥伴的技術通知時，填入傳送端 |
| isaSegment | 否 | 字串 | 與交換控制編號相同，但只在特定情況下才會填入 |
| isaSegment | 否 | 字串 | 與功能群組控制編號相同，但只在特定情況下才會填入 |
| respondingfunctionalGroupControlNumber | 否 | 字串 | 原始功能群組的控制編號 |
| respondingFunctionalGroupId | 否 | 字串 | 對應至認可功能群組識別碼中的 AK101 |
| isMessageFailed | 布林值 | X12 訊息是否失敗 |
| StatusCode | 是 | 例舉 | 具有下列允許值的認可狀態碼`Accepted`： `Rejected`、和`AcceptedWithErrors` |
| processingStatus | 是 | 例舉 | 具有下列允許值的確認處理狀態： `Received`、和`Generated``Sent` |
| ak903 | 否 | 字串 | 已接收的交易集合數目 |
| ak904 | 否 | 字串 | 已識別的功能群組中接受的交易集數目 |
| ak9Segment | 否 | 字串 | 是否接受或拒絕 AK1 區段中識別的功能群組，以及為何 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [AS2 追蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器記錄監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)