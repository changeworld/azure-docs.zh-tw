---
title: 傳送及接收 B2B 的 AS2 訊息
description: 將 Azure 邏輯應用與企業整合套件一起使用,為 B2B 企業整合方案交換 AS2 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0e7c34e42d0ab68a5dab9718075f02a85322ce6c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458823"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中交換適用於 B2B 企業整合的 AS2 訊息

> [!IMPORTANT]
> 正在棄用原始 AS2 連接器,因此請確保改用**AS2 (v2)** 連接器。 此版本提供與原始版本相同的功能,是邏輯應用運行時的本機功能,並在輸送量和消息大小方面提供了顯著的性能改進。 此外,本機 v2 連接器不需要創建與整合帳戶的連接。 相反,如先決條件中所述,請確保將集成帳戶連結到計劃使用連接器的邏輯應用。

要在 Azure 邏輯應用中處理 AS2 消息,可以使用 AS2 連接器,該連接器提供用於管理 AS2 通訊的觸發器和操作。 例如,為了在傳輸消息時建立安全性和可靠性,可以使用以下操作:

* [ **AS2 編碼**操作](#encode),用於通過消息處置通知 (MDN) 提供加密、數位簽名和確認,這有助於支援非否認。 例如,此操作應用 AS2/HTTP 標頭,並在設定時執行這些任務:

  * 對傳出消息進行簽名。
  * 加密傳出消息。
  * 壓縮消息。
  * 在 MIME 標頭中傳輸檔名。

* [ **AS2 解碼**操作](#decode),用於通過消息處置通知 (MDN) 提供解密、數位簽名和確認。 例如,此操作執行以下工作:

  * 處理 AS2/HTTP 標頭。
  * 將收到的 MDN 與原始出站消息進行協調。
  * 更新和關聯非否認資料庫中的記錄。
  * 寫入 AS2 狀態報告的記錄
  * 將有效負載內容輸出為基本編碼。
  * 確定是否需要 MN。 根據 AS2 協議,確定 MDN 是同步的還是異步的。
  * 根據 AS2 協定生成同步或非同步 MDN。
  * 設置 MDN 上的關聯權杖和屬性。

  此操作在設定時還會執行以下工作:

  * 驗證簽章。
  * 解密郵件。
  * 解壓縮消息。
  * 檢查並禁止郵件 ID 重複。

本文演示如何將 AS2 編碼和解碼操作添加到現有邏輯應用。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 要從何處使用 AS2 連接器的邏輯應用和啟動邏輯應用工作流的觸發器。 AS2 連接器僅提供操作,不提供觸發器。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與 Azure 訂閱關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md),並連結到計畫使用 AS2 連接器的邏輯應用。 邏輯應用和整合帳戶必須存在於同一位置或 Azure 區域中。

* 使用 AS2 識別限定的帳號中定義的至少兩個[貿易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 在使用 AS2 連接器之前,您必須在貿易夥伴之間創建 AS2[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md),並將該協定儲存在整合帳戶中。

* 如果將[Azure 金鑰保管庫](../key-vault/general/overview.md)用於證書管理,請檢查保管庫密鑰是否允許**加密**和**解密**操作。 否則,編碼和解碼操作將失敗。

  在 Azure 門戶中,轉到金鑰保管庫中的金鑰,查看金鑰的**允許操作**,並確認已選擇**加密**和**解密**操作,例如:

  ![檢查保存庫金鑰](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>編碼 AS2 訊息

1. 如果尚未在[Azure 門戶](https://portal.azure.com)中,請在邏輯應用設計器中打開邏輯應用。

1. 在設計器中,向邏輯應用添加新操作。

1. 在 [選擇動作]**** 和搜尋方塊中，選取 [全部]****。 在搜尋框中,輸入「as2 編碼」,並確保選擇 AS2 (v2) 操作 **:AS2 編碼**

   ![選擇"AS2 編碼"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 現在提供有關這些屬性的資訊:

   | 屬性 | 描述 |
   |----------|-------------|
   | **要編碼的消息** | 訊息有效負載 |
   | **AS2 從** | AS2 協定指定的郵件發件人識別碼 |
   | **AS2 到** | AS2 協定指定的訊息接收者識別碼 |
   |||

   例如：

   ![訊息編碼屬性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 如果在發送簽名或加密消息時遇到問題,請考慮嘗試不同的 SHA256 演演演算法格式。 AS2 規範不提供有關 SHA256 格式的任何資訊,因此每個提供程式都使用自己的實現或格式。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>解碼 AS2 訊息

1. 如果尚未在[Azure 門戶](https://portal.azure.com)中,請在邏輯應用設計器中打開邏輯應用。

1. 在設計器中,向邏輯應用添加新操作。

1. 在 [選擇動作]**** 和搜尋方塊中，選取 [全部]****。 在搜尋框中,輸入「as2 解碼」,並確保選擇 AS2 (v2) 操作 **:AS2 解碼**

   ![選擇"AS2 解碼"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 要**對「消息」進行編碼**和**消息標頭**屬性,請從以前的觸發器或操作輸出中選擇這些值。

   例如,假設您的邏輯應用通過請求觸發器接收消息。 您可以從該觸發器中選擇輸出。

   ![從要求輸出選取內文和標頭](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>範例

若要嘗試部署可完整運作的邏輯應用程式和範例 AS2 案例，請參閱 [AS2 邏輯應用程式範本和案例](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>連接器參考

有關此連線器的更多技術詳細資訊(如連接器的 Swagger 檔所述的操作和限制),請參閱[連接器的參考頁](https://docs.microsoft.com/connectors/as2/)。 

> [!NOTE]
> 對於[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)的邏輯應用,此連線器的原始 ISE 標籤版本使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
