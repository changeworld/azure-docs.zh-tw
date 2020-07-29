---
title: 適用于 B2B 訊息的 AS2 追蹤架構
description: 建立追蹤架構以監視 Azure Logic Apps 中的 AS2 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906968"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>在 Azure Logic Apps 中建立用來追蹤 AS2 訊息的架構

您可以在整合帳戶中使用這些 AS2 追蹤結構描述，協助您監視企業對企業 (B2B) 交易的成功、錯誤及訊息屬性︰

* AS2 訊息追蹤結構描述
* AS2 訊息處置通知（MDN）追蹤架構

## <a name="as2-message-tracking-schema"></a>AS2 訊息追蹤結構描述

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| 屬性 | 必要 | 類型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | String | AS2 訊息接收者的夥伴名稱 |
| as2To | Yes | String | As2 訊息的標頭中的 AS2 訊息接收者名稱 |
| as2From | Yes | String | As2 訊息的標頭中的 AS2 訊息寄件者名稱 |
| agreementName | 否 | String | 據以解析訊息的 AS2 合約名稱 |
| direction | Yes | String | 訊息流程的方向，也就是 `receive` 或`send` |
| messageId | 否 | String | As2 訊息標頭的 AS2 訊息識別碼 |
| dispositionType | 否 | String | 訊息處置通知（MDN）配置類型值 |
| fileName | 否 | String | AS2 訊息標頭中的檔案名 |
| isMessageFailed | 是 | Boolean | AS2 訊息是否失敗 |
| isMessageSigned | 是 | Boolean | 是否已簽署 AS2 訊息 |
| isMessageEncrypted | 是 | Boolean | AS2 訊息是否已加密 |
| isMessageCompressed | 是 | Boolean | 是否已壓縮 AS2 訊息 |
| correlationMessageId | 否 | String | AS2 訊息識別碼，用來將訊息與 Mdn 相互關聯 |
| incomingHeaders | No | JToken 的字典 | 傳入的 AS2 訊息標頭詳細資料 |
| outgoingHeaders | No | JToken 的字典 | 外寄 AS2 訊息標頭詳細資料 |
| isNrrEnabled | 是 | Boolean | 如果不知道值，是否要使用預設值 |
| isMdnExpected | 是 | Boolean | 如果不知道值，是否要使用預設值 |
| mdnType | Yes | 列舉 | 允許的值： `NotConfigured` 、 `Sync` 和`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 追蹤結構描述

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| 屬性 | 必要 | 類型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 訊息寄件者的夥伴名稱 |
| receiverPartnerName | 否 | String | AS2 訊息接收者的夥伴名稱 |
| as2To | Yes | String | 接收 AS2 訊息的夥伴名稱 |
| as2From | Yes | String | 傳送 AS2 訊息的夥伴名稱 |
| agreementName | 否 | String | 據以解析訊息的 AS2 合約名稱 |
| direction | Yes | String | 訊息流程的方向，也就是 `receive` 或`send` |
| messageId | 否 | String | AS2 訊息識別碼 |
| originalMessageId | 否 | String | AS2 原始訊息識別碼 |
| dispositionType | 否 | String | MDN 配置類型值 |
| isMessageFailed | 是 | Boolean | AS2 訊息是否失敗 |
| isMessageSigned | 是 | Boolean | 是否已簽署 AS2 訊息 |
| isNrrEnabled | 是 | Boolean | 如果不知道值，是否要使用預設值 |
| StatusCode | Yes | 列舉 | 允許的值： `Accepted` 、 `Rejected` 和`AcceptedWithErrors` |
| micVerificationStatus | Yes | 列舉 | 允許的值： `NotApplicable` 、 `Succeeded` 和`Failed` |
| correlationMessageId | 否 | String | 相互關聯識別碼，這是已設定 MDN 之原始訊息的識別碼 |
| incomingHeaders | No | JToken 的字典 | 傳入訊息標頭詳細資料 |
| outgoingHeaders | No | JToken 的字典 | 外寄訊息標頭詳細資料 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [X12 追蹤架構](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器記錄監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)