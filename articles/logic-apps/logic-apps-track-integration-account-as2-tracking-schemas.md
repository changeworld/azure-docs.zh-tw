---
title: B2B 消息的 AS2 跟蹤架構
description: 創建跟蹤架構以在 Azure 邏輯應用中監視 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906968"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>創建用於在 Azure 邏輯應用中跟蹤 AS2 消息的架構

您可以在整合帳戶中使用這些 AS2 追蹤結構描述，協助您監視企業對企業 (B2B) 交易的成功、錯誤及訊息屬性︰

* AS2 訊息追蹤結構描述
* AS2 消息處置通知 （MDN） 跟蹤架構

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

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | AS2 消息接收方的合作夥伴名稱 |
| as2To | 是 | String | AS2 消息接收方的名稱來自 AS2 消息的標頭 |
| as2From | 是 | String | AS2 消息寄件者的姓名來自 AS2 消息的標頭 |
| agreementName | 否 | String | 據以解析訊息的 AS2 合約名稱 |
| direction | 是 | String | 消息流的方向，該方向為`receive`或`send` |
| messageId | 否 | String | AS2 消息頭中的 AS2 消息 ID |
| dispositionType | 否 | String | 消息處置通知 （MDN） 處置類型值 |
| fileName | 否 | String | 來自 AS2 消息頭的檔案名 |
| isMessageFailed | 是 | Boolean | AS2 消息是否失敗 |
| isMessageSigned | 是 | Boolean | AS2 消息是否已簽名 |
| isMessageEncrypted | 是 | Boolean | AS2 消息是否已加密 |
| isMessageCompressed | 是 | Boolean | AS2 消息是否被壓縮 |
| correlationMessageId | 否 | String | AS2 消息 ID，用於將消息與 MN 關聯 |
| incomingHeaders | 否 | JToken 的字典 | 傳入 AS2 消息標頭詳細資訊 |
| outgoingHeaders | 否 | JToken 的字典 | 傳出 AS2 消息頭詳細資訊 |
| isNrrEnabled | 是 | Boolean | 如果值不知道，是否使用預設值 |
| isMdnExpected | 是 | Boolean | 如果值不知道，是否使用預設值 |
| mdnType | 是 | 例舉 | 允許的值： `NotConfigured` `Sync`、 和`Async` |
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

| 屬性 | 必要 | 類型 | 描述 |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | AS2 郵件寄件者的合作夥伴名稱 |
| receiverPartnerName | 否 | String | AS2 消息接收方的合作夥伴名稱 |
| as2To | 是 | String | 接收 AS2 消息的合作夥伴名稱 |
| as2From | 是 | String | 發送 AS2 消息的合作夥伴名稱 |
| agreementName | 否 | String | 據以解析訊息的 AS2 合約名稱 |
| direction | 是 | String | 消息流的方向，該方向為`receive`或`send` |
| messageId | 否 | String | AS2 消息 ID |
| originalMessageId | 否 | String | AS2 原始消息 ID |
| dispositionType | 否 | String | MDN 處置類型值 |
| isMessageFailed | 是 | Boolean | AS2 消息是否失敗 |
| isMessageSigned | 是 | Boolean | AS2 消息是否已簽名 |
| isNrrEnabled | 是 | Boolean | 如果值不知道，是否使用預設值 |
| StatusCode | 是 | 例舉 | 允許的值： `Accepted` `Rejected`、 和`AcceptedWithErrors` |
| micVerificationStatus | 是 | 例舉 | 允許的值：`NotApplicable` `Succeeded`、 和`Failed` |
| correlationMessageId | 否 | String | 關聯 ID，它是配置 MDN 的原始消息的 ID |
| incomingHeaders | 否 | JToken 的字典 | 傳入消息標頭詳細資訊 |
| outgoingHeaders | 否 | JToken 的字典 | 傳出消息頭詳細資訊 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [X12 跟蹤架構](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B 自訂追蹤結構描述](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器記錄監視 B2B 訊息](../logic-apps/monitor-b2b-messages-log-analytics.md)