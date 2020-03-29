---
title: AS2 訊息設定
description: 使用企業集成包在 Azure 邏輯應用中發送和接收設置的 AS2 參考指南
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793038"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>使用企業集成包的 Azure 邏輯應用中的 AS2 消息設置參考

此引用描述您可以設置的屬性來指定 AS2 協定如何處理在交易夥伴之間發送和接收的消息。 基於您與您交換消息的合作夥伴的協定設置這些屬性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收設定

![選擇"接收設置"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 屬性 | 必要 | 描述 |
|----------|----------|-------------|
| **覆寫訊息屬性** | 否 | 使用屬性設置覆蓋傳入郵件的屬性。 |
| **訊息應該簽署** | 否 | 指定是否必須對所有傳入郵件進行數位簽章。 如果需要簽名，請從 **"證書"** 清單中選擇現有來賓合作夥伴公共證書以驗證郵件上的簽名。 如果沒有證書，可以瞭解有關[添加證書](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多資訊。 |
| **訊息應該加密** | 否 | 指定是否必須加密所有傳入郵件。 未加密的訊息會遭到拒絕。 如果需要加密，請從**證書**清單中選擇用於解密傳入消息的現有主機合作夥伴專用證書。 如果沒有證書，可以瞭解有關[添加證書](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多資訊。 |
| **訊息應該壓縮** | 否 | 指定是否必須壓縮所有傳入消息。 未壓縮的訊息會遭到拒絕。 |
| **禁止郵件 ID 重複** | 否 | 指定是否允許具有重複的號資料交換的郵件。 如果不允許重複的已號檔，請選擇支票之間的天數。 您還可以選擇是否掛起重複項。 |
| **MDN 文字** | 否 | 指定要發送到郵件寄件者的預設郵件處置通知 （MDN）。 |
| **傳送 MDN** | 否 | 指定是否為接收的消息發送同步 MN。  |
| **傳送簽署的 MDN** | 否 | 指定是否為已接收的消息發送已簽名的 MN。 如果需要簽名，請從**MIC 演算法**清單中選擇用於對消息進行簽名的演算法。 |
| **傳送非同步 MDN** | 否 | 指定是否非同步發送 MDN。 如果在**URL**框中選擇非同步 MDN，請指定用於發送 MDN 的位置的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 傳送設定

![選擇"發送設置"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 屬性 | 必要 | 描述 |
|----------|----------|-------------|
| **啟用訊息簽署** | 否 | 指定是否必須對所有傳出郵件進行數位簽章。 如果需要簽名，請選擇以下值： <p>- 從**簽名演算法**清單中，選擇用於對消息進行簽名的演算法。 <br>- 從**證書**清單中，選擇用於對消息進行簽名的現有主機合作夥伴專用證書。 如果沒有證書，可以瞭解有關[添加證書](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多資訊。 |
| **啟用訊息加密** | 否 | 指定是否必須加密所有傳出郵件。 如果需要加密，請選擇以下值： <p>- 從**加密演算法**清單中，選擇用於加密消息的來賓合作夥伴公共證書演算法。 <br>- 從**證書**清單中，選擇用於加密傳出消息的現有來賓合作夥伴專用證書。 如果沒有證書，可以瞭解有關[添加證書](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多資訊。 |
| **啟用訊息壓縮** | 否 | 指定是否必須壓縮所有傳出消息。 |
| **展開 HTTP 標頭** | 否 | 將 HTTP`content-type`標頭放在一行上。 |
| **在 MIME 標頭中傳輸檔案名** | 否 | 指定是否將檔案名包括在 MIME 標頭中。 |
| **要求 MDN** | 否 | 指定是否接收所有傳出消息的消息處置通知 （MDN）。 |
| **要求簽署的 MDN** | 否 | 指定是否接收所有傳出消息的已簽名 MN。 如果需要簽名，請從**MIC 演算法**清單中選擇用於對消息進行簽名的演算法。 |
| **要求非同步 MDN** | 否 | 指定是否非同步接收 MDN。 如果在**URL**框中選擇非同步 MDN，請指定用於發送 MDN 的位置的 URL。 |
| **啟用 NRR** | 否 | 指定是否要求非拒絕接收 （NRR）。 此通信屬性提供資料作為定址接收的證據。 |
| **SHA2 演算法格式** | 否 | 指定用於在傳出 AS2 消息或 MDN 的標頭中簽名的 MIC 演算法格式 |
||||

## <a name="next-steps"></a>後續步驟

[交換 AS2 訊息](../logic-apps/logic-apps-enterprise-integration-as2.md)
