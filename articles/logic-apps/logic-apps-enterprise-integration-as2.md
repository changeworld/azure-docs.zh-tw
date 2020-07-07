---
title: 傳送和接收 B2B 的 AS2 訊息
description: 在企業整合套件中使用 Azure Logic Apps，以交換 B2B 企業整合案例的 AS2 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 545c1720ef379ec74bd2e7c0bc68f6a2fcbba789
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82115494"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中交換適用於 B2B 企業整合的 AS2 訊息

> [!IMPORTANT]
> 原始的 AS2 連接器即將淘汰，因此請確定您改用**AS2 （v2）** 連接器。 這個版本提供的功能與原始版本相同，是 Logic Apps 執行時間的原生，而且可在輸送量和訊息大小方面提供顯著的效能改進。 此外，原生 v2 連接器不會要求您建立與整合帳戶的連線。 相反地，如必要條件中所述，請務必將整合帳戶連結至您打算使用連接器的邏輯應用程式。

若要使用 Azure Logic Apps 中的 AS2 訊息，您可使用 AS2 連接器，其提供用來管理 AS2 通訊的觸發程序和動作。 例如，若要在傳輸訊息時建立安全性和可靠性，您可以使用下列動作：

* [ **AS2 編碼**動作](#encode)，可透過訊息處置通知（MDN）提供加密、數位簽章和通知，以協助支援不可否認性。 例如，此動作會套用 AS2/HTTP 標頭，並在設定時執行下列工作：

  * 簽署外寄訊息。
  * 加密外寄訊息。
  * 壓縮訊息。
  * 在 MIME 標頭中傳輸檔案名。

* [ **AS2**解碼動作](#decode)，可透過訊息處置通知（MDN）提供解密、數位簽章和通知。 例如，此動作會執行下列工作：

  * 處理 AS2/HTTP 標頭。
  * 將接收的 Mdn 與原始輸出訊息協調。
  * 在不可否認性的資料庫中更新記錄並使其相互關聯。
  * 寫入 AS2 狀態報告的記錄
  * 將裝載內容輸出為 base64 編碼。
  * 判斷是否需要 Mdn。 根據 AS2 合約，判斷 Mdn 是否應為同步或非同步。
  * 根據 AS2 合約產生同步或非同步 Mdn。
  * 在 Mdn 上設定相互關聯標記和屬性。

  此動作也會在設定時執行下列工作：

  * 驗證簽章。
  * 解密訊息。
  * 解壓縮訊息。
  * 檢查並不允許訊息識別碼重複。

本文說明如何將 AS2 編碼和解碼動作新增至現有的邏輯應用程式。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您想要使用 AS2 連接器的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程式。 AS2 連接器只會提供動作，而非觸發程式。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 與您的 Azure 訂用帳戶相關聯的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，並連結至您打算使用 AS2 連接器的邏輯應用程式。 邏輯應用程式與整合帳戶必須位於相同位置或相同的 Azure 區域中。

* 至少有兩個您已在整合帳戶中使用 AS2 身分識別辨識符號定義的[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 在您可以使用 AS2 連接器之前，您必須先建立交易夥伴之間的 AS2[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)，並將該合約儲存在您的整合帳戶中。

* 如果您使用[Azure Key Vault](../key-vault/general/overview.md)進行憑證管理，請檢查您的保存庫金鑰是否允許**加密**和**解密**作業。 否則，編碼和解碼動作會失敗。

  在 Azure 入口網站中，移至金鑰保存庫中的金鑰，檢查金鑰的**允許作業**，並確認已選取 [**加密**] 和 [**解密**] 作業，例如：

  ![檢查保存庫金鑰作業](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>編碼 AS2 訊息

1. 如果您還沒有這麼做，請在 [ [Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在設計工具中，將新動作新增至您的邏輯應用程式。

1. 在 [選擇動作]**** 和搜尋方塊中，選取 [全部]****。 在搜尋方塊中，輸入「as2 編碼」，並確定您選取的是 AS2 （v2）動作： **As2 編碼**

   ![選取 [AS2 編碼]](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 現在提供這些屬性的資訊：

   | 屬性 | 描述 |
   |----------|-------------|
   | **要編碼的訊息** | 訊息承載 |
   | **AS2 來源** | AS2 合約所指定之訊息寄件者的識別碼 |
   | **AS2 至** | AS2 合約所指定的訊息接收器識別碼 |
   |||

   例如：

   ![訊息編碼屬性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 如果您在傳送已簽署或已加密的訊息時遇到問題，請考慮嘗試不同的 SHA256 演算法格式。 AS2 規格不會提供 SHA256 格式的任何相關資訊，因此每個提供者都會使用自己的實作為或格式。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>解碼 AS2 訊息

1. 如果您還沒有這麼做，請在 [ [Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 在設計工具中，將新動作新增至您的邏輯應用程式。

1. 在 [選擇動作]**** 和搜尋方塊中，選取 [全部]****。 在搜尋方塊中，輸入「as2 解碼」，並確定您選取的是 AS2 （v2）動作： **as2**解碼

   ![選取 [AS2 解碼]](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 針對 [**要編碼的訊息**] 和 [**訊息標頭**] 屬性，從 [先前的觸發程式] 或 [動作輸出] 選取這些值。

   例如，假設您的邏輯應用程式會透過要求觸發程式來接收訊息。 您可以選取該觸發程式的輸出。

   ![從要求輸出選取內文和標頭](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>範例

若要嘗試部署可完整運作的邏輯應用程式和範例 AS2 案例，請參閱 [AS2 邏輯應用程式範本和案例](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，例如連接器的 Swagger 檔案所述的動作和限制，請參閱[連接器的參考頁面](https://docs.microsoft.com/connectors/as2/)。 

> [!NOTE]
> 針對[整合服務環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的邏輯應用程式，此連接器其 ISE 標籤版本使用 [ISE 的 B2B 訊息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
