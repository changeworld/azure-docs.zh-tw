---
title: 適用于 B2B 整合的 RosettaNet 訊息
description: 使用企業整合套件在 Azure Logic Apps 中交換 RosettaNet 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 833e2a6f64301c39ba2b9e7959585c3edeab9445
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072587"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps 中的 B2B 企業整合交換 RosettaNet 訊息

[RosettaNet](https://resources.gs1us.org)是一個非收益聯盟，已建立共用商務資訊的標準程式。 這些標準通常用於供應鏈程式，並且在半導體、電子和物流產業中廣泛使用。 RosettaNet 聯盟會建立並維護交易夥伴介面程式（Pip），以提供所有 RosettaNet 訊息交換的一般商務程式定義。 RosettaNet 是以 XML 為基礎，定義訊息方針、商務程式的介面，以及公司間通訊的實架構。

在[Azure Logic Apps](../logic-apps/logic-apps-overview.md)中，RosettaNet 連接器可協助您建立支援 RosettaNet 標準的整合解決方案。 連接器是以 RosettaNet 執行架構（RNIF）版本2.0.01 版為基礎。 RNIF 是開放式網路應用程式架構，可讓商務夥伴共同執行 RosettaNet Pip。 此架構會定義訊息結構、通知的需求、多用途網際網路郵件延伸（MIME）編碼，以及數位簽章。

具體而言，連接器會提供下列功能：

* 編碼或接收 RosettaNet 訊息。
* 解碼或傳送 RosettaNet 訊息。
* 等待回應，並產生失敗的通知。

針對這些功能，連接器支援 RNIF 2.0.01 版所定義的所有 Pip。 與夥伴的通訊可以是同步或非同步。

## <a name="rosettanet-concepts"></a>RosettaNet 概念

以下是 RosettaNet 規格特有的一些概念和詞彙，而且在建立 RosettaNet 型整合時非常重要：

* **PIP**

  RosettaNet 組織會建立並維護交易夥伴介面程式（Pip），以提供所有 RosettaNet 訊息交換的一般商務程式定義。 每個 PIP 規格都提供了文件類型定義 (DTD) 檔案以及訊息指導方針文件。 DTD 檔案定義服務內容訊息結構。 訊息指導方針文件是可以閱讀的 HTML 檔案，其中指定元素階層條件約束。 這些檔案一起提供完整的商務程式定義。

   Pip 是依高階商務功能或叢集，以及子或區段來分類。 例如，"3A4" 是採購單的 PIP，而 "3" 是訂單管理函式，而 "3A" 是報價 & 訂單輸入子。 如需詳細資訊，請參閱[RosettaNet 網站](https://resources.gs1us.org)。

* **動作**

  部分的 PIP，動作訊息是在合作夥伴之間交換的商務訊息。

* **發送**

   部分的 PIP，信號訊息是為了回應動作訊息而傳送的通知。

* **單一動作和雙向動作**

  對於單一動作 PIP，唯一的回應是通知信號訊息。 對於雙動作 PIP，起始端會收到回應訊息，並除了單向動作訊息流程之外，還會回復通知。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 用於儲存合約和其他 B2B 成品的[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 此整合帳戶必須與您的 Azure 訂用帳戶相關聯。

* 至少有兩個[夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)定義于您的整合帳戶中，且在**商務**識別之下設定了 "DUNS" 辨識符號

* 在您的整合帳戶中傳送或接收 RosettaNet 訊息所需的 PIP 進程設定。 進程設定會儲存所有 PIP 設定特性。 當您建立與合作夥伴的合約時，可以參考此設定。 若要在整合帳戶中建立 PIP 進程設定，請參閱[新增 pip 進程](#add-pip)設定。

* 選擇性[憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)，用於加密、解密或簽署您上傳至整合帳戶的訊息。 只有當您使用簽署或加密時，才需要憑證。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>新增 PIP 進程設定

若要將 PIP 進程設定新增至您的整合帳戶，請遵循下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶。

1. 在 [**總覽**] 窗格中，選取 [ **RosettaNet PIP** ] 磚。

   ![選擇 RosettaNet 磚](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 在 [ **ROSETTANET PIP**] 底下，選擇 [**新增**]。 提供您的 PIP 詳細資料。

   ![新增 RosettaNet PIP 詳細資料](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 您的 PIP 名稱 |
   | **PIP 代碼** | 是 | PIP 三位數代碼。 如需詳細資訊，請參閱[RosettaNet pip](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 版本號碼，可根據您選取的 PIP 代碼來取得 |
   ||||

   如需這些 PIP 屬性的詳細資訊，請造訪[RosettaNet 網站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 當您完成時，請選擇 **[確定]**，這會建立 PIP 設定。

1. 若要查看或編輯進程設定，請選取 PIP，然後選擇 [**編輯為 JSON**]。

   所有處理常式設定都是來自 PIP 的規格。 Logic Apps 會以這些屬性最常使用的預設值填入大部分的設定。

   ![編輯 RosettaNet PIP 設定](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 確認設定對應到適當 PIP 規格中的值，並符合您的商務需求。 如有需要，請更新 JSON 中的值，並儲存這些變更。

## <a name="create-rosettanet-agreement"></a>建立 RosettaNet 合約

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在 [**總覽**] 窗格中，選取 [合約 **] 圖格**。

   ![選擇協定磚](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在 [合約]**** 之下，選擇 [新增]****。 請提供您的合約詳細資料。

   ![新增合約詳細資料](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 合約的名稱 |
   | **合約類型** | 是 | 選取 [ **RosettaNet**]。 |
   | **主控夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 主機夥伴代表設定合約的組織。 |
   | **主控身分識別** | 是 | 主控夥伴的識別碼 |
   | **來賓夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 來賓夥伴代表與主控夥伴有生意往來的組織。 |
   | **來賓身分識別** | 是 | 來賓合作夥伴的識別碼 |
   | **接收設定** | 不定 | 這些屬性會套用至主機合作夥伴接收的所有訊息 |
   | **傳送設定** | 不定 | 這些屬性會套用至主機夥伴所傳送的所有訊息 |  
   | **RosettaNet PIP 參考** | 是 | 合約的 PIP 參考。 所有 RosettaNet 訊息都需要 PIP 設定。 |
   ||||

1. 若要設定您的合約以接收來自來賓夥伴的傳入訊息，請選取 [**接收設定**]。

   ![接收設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 若要啟用傳入訊息的簽章或加密，請在 [**訊息**] 底下，選取 [**應簽署訊息**] 或 [**訊息應該分別加密**]。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **訊息應該簽署** | 否 | 使用選取的憑證來簽署傳入訊息。 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用簽署 | 要用於簽署的憑證 |
      | **啟用訊息加密** | 否 | 使用選取的憑證來加密傳入訊息。 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用加密 | 要用於加密的憑證 |
      ||||

   1. 在每個選項下，選取您先前新增至整合帳戶的個別[憑證](./logic-apps-enterprise-integration-certificates.md)，以用於簽署或加密。

1. 若要設定將訊息傳送至來賓夥伴的合約，請選取 [**傳送設定**]。

   ![傳送設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 若要啟用外寄訊息的簽署或加密，請在 [**訊息**] 底下，選取 [**啟用訊息簽署**] 或 [分別**啟用訊息加密**]。 在每個選項下，選取您先前新增至整合帳戶的個別演算法和[憑證](./logic-apps-enterprise-integration-certificates.md)，以用於簽署或加密。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **啟用訊息簽署** | 否 | 使用選取的簽署演算法和憑證來簽署外寄訊息。 |
      | **簽署演算法** | 是，如果已啟用簽署 | 要使用的簽署演算法（根據選取的憑證） |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用簽署 | 要用於簽署的憑證 |
      | **啟用訊息加密** | 否 | 使用選取的加密演算法和憑證來加密外寄。 |
      | **加密演算法** | 是，如果已啟用加密 | 要使用的加密演算法（根據選取的憑證） |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用加密 | 要用於加密的憑證 |
      ||||

   1. 在 [**端點**] 下，指定要用來傳送動作訊息和通知的必要 url。

      | 屬性 | 必要 | 描述 |
      |----------|----------|-------------|
      | **動作 URL** |  是 | 用於傳送動作訊息的 URL。 URL 是同步和非同步訊息的必要欄位。 |
      | **認可 URL** | 是 | 用來傳送通知訊息的 URL。 URL 是非同步訊息的必要欄位。 |
      ||||

1. 若要使用適用于合作夥伴的 RosettaNet PIP 參考來設定您的合約，請選取 [ **ROSETTANET pip 參考**]。 在 [ **PIP 名稱**] 底下，選取您先前建立之 PIP 的名稱。

   ![PIP 參考](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   您的選擇會填入其餘的屬性，這些屬性是以您在整合帳戶中設定的 PIP 為基礎。 如有必要，您可以變更**PIP 角色**。

   ![選取的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成這些步驟之後，您就可以開始傳送或接收 RosettaNet 訊息。

## <a name="rosettanet-templates"></a>RosettaNet 範本

若要加速開發並建議整合模式，您可以使用邏輯應用程式範本來解碼和編碼 RosettaNet 訊息。 當您建立邏輯應用程式時，您可以從邏輯應用程式設計工具中的範本庫選取。 您也可以在適用于 Azure Logic Apps 的[GitHub 存放庫](https://github.com/Azure/logicapps)中找到這些範本。

![RosettaNet 範本](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收或解碼 RosettaNet 訊息

1. [建立空白邏輯應用程式](quickstart-create-first-logic-app-workflow.md)。

1. 將[您的整合帳戶連結](logic-apps-enterprise-integration-create-integration-account.md#link-account)至邏輯應用程式。

1. 您必須先新增觸發程式來啟動邏輯應用程式（例如要求觸發程式），才可以新增動作來將 RosettaNet 訊息解碼。

1. 新增觸發程式之後，請選擇 [**新增步驟**]。

   ![新增要求觸發程序](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜尋方塊中，輸入 "rosettanet"，然後選取此動作： **rosettanet**解碼

   ![尋找並選取「RosettaNet 解碼」動作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供動作屬性的資訊：

   ![提供動作詳細資料](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **訊息** | 是 | 要解碼的 RosettaNet 訊息  |
   | **標頭** | 是 | 提供版本值的 HTTP 標頭，也就是 RNIF 版本和回應類型，這會指出夥伴之間的通訊類型，而且可以是同步或非同步。 |
   | **角色** | 是 | 主機夥伴在 PIP 中的角色 |
   ||||

   在 RosettaNet 解碼動作中，輸出和其他屬性會包含輸出**信號**，您可以選擇進行編碼並回到夥伴，或對該輸出採取任何其他動作。

## <a name="send-or-encode-rosettanet-messages"></a>傳送或編碼 RosettaNet 訊息

1. [建立空白邏輯應用程式](quickstart-create-first-logic-app-workflow.md)。

1. 將[您的整合帳戶連結](logic-apps-enterprise-integration-create-integration-account.md#link-account)至邏輯應用程式。

1. 在您可以新增動作以編碼 RosettaNet 訊息之前，您必須先新增觸發程式來啟動邏輯應用程式，例如要求觸發程式。

1. 新增觸發程式之後，請選擇 [**新增步驟**]。

   ![新增要求觸發程序](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在搜尋方塊中，輸入 "rosettanet"，然後選取此動作： **Rosettanet 編碼**

   ![尋找並選取 [RosettaNet 編碼] 動作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供動作屬性的資訊：

   ![提供動作詳細資料](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 屬性 | 必要 | 描述 |
   |----------|----------|-------------|
   | **訊息** | 是 | 要編碼的 RosettaNet 訊息  |
   | **主機合作夥伴** | 是 | 主機夥伴名稱 |
   | **來賓合作夥伴** | 是 | 來賓夥伴名稱 |
   | **PIP 代碼** | 是 | PIP 代碼 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 實例識別** | 是 | 此 PIP 訊息的唯一識別碼 |  
   | **訊息類型** | 是 | 要編碼的訊息類型 |  
   | **角色** | 是 | 主機夥伴的角色 |
   ||||

   已編碼的訊息現在已準備好傳送給夥伴。

1. 為了傳送編碼的訊息，此範例會使用**HTTP**動作，這會重新命名為「HTTP 傳送編碼的訊息到夥伴」。

   ![用於傳送 RosettaNet 訊息的 HTTP 動作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   根據 RosettaNet 標準，只有在 PIP 定義的所有步驟都完成時，才會將商務交易視為完成。

1. 在主機將編碼的訊息傳送給夥伴之後，主機會等候信號和通知。 若要完成這項工作，請新增**RosettaNet 等待回應**動作。

   ![新增「RosettaNet 等待回應」動作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   用於等候的持續時間和重試次數是以您整合帳戶中的 PIP 設定為基礎。 如果未收到回應，此動作會產生失敗的通知。 若要處理重試，請一律將**編碼**和**等候回應**動作放在**Until**迴圈中。

   ![使用 RosettaNet 動作執行迴圈](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>後續步驟

* 了解驗證和轉換方式，以及[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)中的其他訊息作業
* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
