---
title: AS2 訊息設定
description: 使用 Enterprise Integration Pack Azure Logic Apps 中的 AS2 傳送和接收設定參考指南
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793038"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>使用 Enterprise Integration Pack Azure Logic Apps 中的 AS2 訊息設定參考

此參考描述您可以設定的屬性，以指定 AS2 合約如何處理在交易夥伴之間傳送和接收的訊息。 根據您與您交換訊息之夥伴的合約，設定這些屬性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收設定

![選取 [接收設定]](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 屬性 | 必要 | 描述 |
|----------|----------|-------------|
| **覆寫訊息屬性** | 否 | 使用您的屬性設定覆寫傳入訊息上的屬性。 |
| **訊息應該簽署** | 否 | 指定是否必須以數位方式簽署所有傳入的訊息。 如果您需要簽署，請從 [ **憑證** ] 清單中選取現有的來賓夥伴公開憑證，以驗證訊息上的簽章。 如果您沒有憑證，請深入瞭解如何 [新增憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **訊息應該加密** | 否 | 指定是否必須加密所有傳入的訊息。 未加密的訊息會遭到拒絕。 如果您需要加密，請從 [ **憑證** ] 清單中選取現有的 [主機夥伴私用憑證] 來解密傳入訊息。 如果您沒有憑證，請深入瞭解如何 [新增憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **訊息應該壓縮** | 否 | 指定是否必須壓縮所有傳入的訊息。 未壓縮的訊息會遭到拒絕。 |
| **不允許訊息識別碼重複** | 否 | 指定是否允許具有重複識別碼的訊息。 如果您不允許重複的識別碼，請選取檢查之間的天數。 您也可以選擇是否要暫止重複專案。 |
| **MDN 文字** | 否 | 指定您想要傳送給訊息寄件者 (MDN) 的預設訊息處置通知。 |
| **傳送 MDN** | 否 | 指定是否針對接收的訊息傳送同步 Mdn。  |
| **傳送簽署的 MDN** | 否 | 指定是否要針對已接收的訊息傳送已簽署的 Mdn。 如果您需要簽署，請從 [ **MIC 演算法]** 清單中選取要用於簽署訊息的演算法。 |
| **傳送非同步 MDN** | 否 | 指定是否要以非同步方式傳送 Mdn。 如果您選取 [非同步 Mdn]，請在 [ **URL** ] 方塊中，指定要傳送 mdn 之位置的 url。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 傳送設定

![選取 [傳送設定]](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 屬性 | 必要 | 描述 |
|----------|----------|-------------|
| **啟用訊息簽署** | 否 | 指定是否必須以數位方式簽署所有外寄訊息。 如果您需要簽署，請選取下列值： <p>-從 [ **簽署演算法]** 清單中，選取要用於簽署訊息的演算法。 <br>-從 [ **憑證** ] 清單中選取現有的 [主機夥伴私用憑證] 來簽署訊息。 如果您沒有憑證，請深入瞭解如何 [新增憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **啟用訊息加密** | 否 | 指定是否必須加密所有外寄訊息。 如果您需要加密，請選取下列值： <p>-從 [ **加密演算法]** 清單中，選取要用於加密訊息的來賓夥伴公開憑證演算法。 <br>-從 [ **憑證** ] 清單中，選取現有的來賓夥伴私用憑證來加密外寄訊息。 如果您沒有憑證，請深入瞭解如何 [新增憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **啟用訊息壓縮** | 否 | 指定是否必須壓縮所有外寄訊息。 |
| **展開 HTTP 標頭** | 否 | 將 HTTP 標頭放在一行 `content-type` 。 |
| **以 MIME 標頭傳輸檔案名** | 否 | 指定是否要在 MIME 標頭中包含檔案名。 |
| **要求 MDN** | 否 | 指定是否要接收所有外寄訊息的訊息處置通知 (Mdn) 。 |
| **要求簽署的 MDN** | 否 | 指定是否要接收所有外寄訊息的已簽署 Mdn。 如果您需要簽署，請從 [ **MIC 演算法]** 清單中選取要用於簽署訊息的演算法。 |
| **要求非同步 MDN** | 否 | 指定是否要以非同步方式接收 Mdn。 如果您選取 [非同步 Mdn]，請在 [ **URL** ] 方塊中，指定要傳送 mdn 之位置的 url。 |
| **啟用 NRR** | 否 | 指定是否需要 (NRR) 的不可否認性回條。 此通訊屬性會提供已接收之資料的辨識項。 |
| **SHA2 演算法格式** | 否 | 指定用來簽署外寄 AS2 訊息或 MDN 之標頭的 MIC 演算法格式 |
||||

## <a name="next-steps"></a>後續步驟

[交換 AS2 訊息](../logic-apps/logic-apps-enterprise-integration-as2.md)
