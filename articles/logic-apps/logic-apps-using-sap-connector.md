---
title: 連接至 SAP 系統
description: 使用 Azure Logic Apps 將工作流程自動化，以存取和管理 SAP 資源
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 01/25/2021
tags: connectors
ms.openlocfilehash: 93e705eea39443ffc15fbdd079e1376ec46cb51c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786685"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>從 Azure Logic Apps 連線至 SAP 系統

本文說明如何使用 [sap 連接器](https://docs.microsoft.com/connectors/sap/)，從 Logic Apps 存取您的 sap 資源。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要從中存取 SAP 資源的邏輯應用程式。 如果您不熟悉 Logic Apps，請參閱 [Logic Apps 服務總覽](../logic-apps/logic-apps-overview.md) 和 [快速入門，以在 Azure 入口網站中建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

    * 如果您使用的是舊版 SAP 連接器，則必須先 [遷移至目前的連接器](#migrate-to-current-connector) ，然後才能連線到您的 sap 伺服器。

    * 如果您是在多租使用者 Azure 中執行邏輯應用程式，請參閱 [多租](#multi-tenant-azure-prerequisites)使用者的必要條件。

    * 如果您是在高階整合服務環境中執行邏輯應用程式[ (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，請參閱 [ise 必要條件](#ise-prerequisites)。

* 您想要從 Logic Apps 存取的 [sap 應用程式伺服器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 或 [sap 訊息伺服器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) 。 如需可與連接器搭配使用的 SAP 伺服器和 SAP 動作的詳細資訊，請參閱 [sap 相容性](#sap-compatibility)。

* 要傳送至 SAP 伺服器的訊息內容，例如範例 IDoc 檔。 此內容必須是 XML 格式，並且包含您想要使用的 SAP 動作的命名空間。 您可以 [使用一般檔案架構來傳送 idoc，方法是將它們包裝在 XML 信封中](#send-flat-file-idocs)。

* 如果您想要在 **收到來自 SAP 觸發程式的訊息時** 使用，您也必須執行下列動作：

    * 使用此設定來設定您的 SAP 閘道安全性許可權： `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * 設定您的 SAP 閘道安全性記錄，以協助尋找 (ACL) 的存取控制清單。 如需詳細資訊，請參閱 [SAP 說明主題，以瞭解如何設定閘道記錄](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)。 否則，您將會收到此錯誤： `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > 此觸發程式會使用相同的 URI 位置來更新和取消訂閱 webhook 訂用帳戶。 更新作業會使用 HTTP `PATCH` 方法，而取消訂閱作業則使用 HTTP `DELETE` 方法。 這種行為可能會讓更新作業顯示為觸發程式歷程記錄中的取消訂閱作業，但作業仍是更新，因為觸發程式會使用 `PATCH` 做為 HTTP 方法，而不是 `DELETE` 。

### <a name="sap-compatibility"></a>SAP 相容性

SAP 連接器與下列類型的 SAP 系統相容：

* 內部部署和以雲端為基礎的 HANA 型 SAP 系統，例如 S/4 HANA。

* 傳統內部部署 SAP 系統，例如 R/3 和 ECC。

SAP 連接器支援下列來自 SAP NetWeaver 型系統的訊息和資料整合類型：

*  (IDoc) 的中繼檔

* 商務應用程式設計介面 (BAPI) 

*  (RFC) 的遠端函式呼叫和交易式 RFC (tRFC) 

SAP 連接器使用 [sap .Net connector (NCo) 程式庫](https://support.sap.com/en/product/connectors/msnet.html)。 您可以搭配連接器使用下列 SAP 動作和觸發程式：

* 將 **訊息傳送至 SAP** 以透過 [TRFC 動作傳送 idoc](#send-idoc-action) ，您可以使用此動作：

    * [透過 RFC 呼叫 BAPI 函式](#call-bapi-action)

    * 在 SAP 系統中呼叫 RFC/tRFC

    * 建立或關閉具狀態會話

    * 認可或復原 BAPI 交易

    * 確認交易識別碼

    * 傳送 Idoc，從其數位取得 IDoc 的狀態，並取得交易的 Idoc 清單

    * 讀取 SAP 資料表

* **從 SAP 觸發程式收到訊息時** ，您可以使用此觸發程式：

    * 透過 tRFC 接收 Idoc

    * 透過 tRFC 呼叫 BAPI 函式

    * 在 SAP 系統中呼叫 RFC/tRFC

* **產生架構** 動作，您可以使用它來為 IDOC、BAPI 或 RFC 的 SAP 成品產生架構。

若要使用這些 SAP 動作，您必須先使用使用者名稱和密碼來驗證您的連接。 SAP 連接器也支援 [ (SNC) 的安全網路通訊 ](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)。 您可以使用 SNC for SAP NetWeaver 單一登入 (SSO) 或外部產品的其他安全性功能。 如果您使用 SNC，請參閱 [SNC 必要條件](#snc-prerequisites)。

### <a name="migrate-to-current-connector"></a>遷移至目前的連接器

先前的 SAP 應用程式伺服器和 SAP 訊息伺服器連接器已于2020年2月29日淘汰。 若要遷移至目前的 SAP 連接器，請遵循下列步驟：

1. 將您 [的內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127) 更新為最新版本。 如需詳細資訊，請參閱 [安裝 Azure Logic Apps 的內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用已淘汰之 SAP 連接器的邏輯應用程式中，刪除 [ **傳送至 sap** ] 動作。

1. 從目前的 SAP 連接器將 **傳送訊息新增至 SAP** 動作。

1. 在新的動作中重新連接到您的 SAP 系統。

1. 儲存邏輯應用程式。

### <a name="multi-tenant-azure-prerequisites"></a>多租用戶 Azure 必要條件

如果您的邏輯應用程式在多租使用者 Azure 中執行，則適用這些必要條件。 受控 SAP 連接器不會在 [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中以原生方式執行。

> [!TIP]
> 如果您使用 Premium 層級 ISE，則可以使用 SAP ISE 連接器，而不是受控 SAP 連接器。 如需詳細資訊，請參閱 [ISE 必要條件](#ise-prerequisites)。

受控 SAP 連接器可透過您 [的內部部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)與 SAP 系統整合。 例如，在傳送訊息案例中，當訊息從邏輯應用程式傳送至 SAP 系統時，資料閘道會作為 RFC 用戶端，並將接收自邏輯應用程式的要求轉送到 SAP。 同樣地，在接收訊息案例中，資料閘道會作為 RFC 伺服器，以從 SAP 接收要求並轉送至邏輯應用程式。

* [下載內部部署資料閘道，並](../logic-apps/logic-apps-gateway-install.md) 將其安裝在與您要連線之 SAP 系統相同的虛擬網路中的主機電腦或虛擬機器上。

* 在 Azure 入口網站中建立內部部署資料閘道的[Azure 閘道資源](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource)。 此閘道可協助您安全地存取內部部署資料和資源。 請務必使用支援的閘道版本。

    * 如果您的閘道發生問題，請嘗試 [升級至最新版本](https://aka.ms/on-premises-data-gateway-installer)，其中可能包含可解決問題的更新。

* 在與您的內部部署資料閘道相同的本機電腦上[，下載並安裝最新的 SAP 用戶端程式庫](#sap-client-library-prerequisites)。

### <a name="ise-prerequisites"></a>ISE 必要條件

如果您是在高階 ISE 中執行邏輯應用程式，則適用這些必要條件。 不過，這些應用程式不會套用至在開發人員層級 ISE 中執行的邏輯應用程式。 ISE 可讓您存取受 Azure 虛擬網路保護的資源，並提供其他 ISE 原生連接器，讓邏輯應用程式直接存取內部部署資源，而不需要使用內部部署資料閘道。

> [!NOTE]
> 雖然 SAP ISE 連接器可在開發人員層級 ISE 內看見，但嘗試安裝連接器將不會成功。

1. 如果您還沒有具有 blob 容器的 Azure 儲存體帳戶，請使用 [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md) 或 [Azure 儲存體總管](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)建立容器。

1. 在您的本機電腦上[下載並安裝最新的 SAP 用戶端程式庫](#sap-client-library-prerequisites)。 您應該會有下列元件檔：

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. 建立包含這些元件檔案的 .zip 檔案。 將套件上傳至 Azure 儲存體中的 blob 容器。

1. 在 Azure 入口網站或 Azure 儲存體總管中，流覽至您上傳 .zip 檔案的容器位置。

1. 複製容器位置的 URL。 請務必將共用存取簽章包含 (SAS) 權杖，以便授權 SAS 權杖。 否則，SAP ISE 連接器的部署將會失敗。

1. 在 ISE 中安裝及部署 SAP 連接器。 如需詳細資訊，請參閱 [新增 ISE 連接器](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)。

   1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的 ISE。

   1. 在 ISE 功能表上，選取 [ **受管理的連接器** &gt; **新增**]。 從 [連接器] 清單中，尋找並選取 **SAP**。

   1. 在 [ **新增受管理的連接器** ] 窗格的 [ **sap 封裝** ] 方塊中，貼上具有 SAP 元件的 .zip 檔案的 URL。 同樣地，請務必包含 SAS 權杖。
 
  1. 選取 [ **建立** ] 以完成建立 ISE 連接器。

1. 如果您的 SAP 實例和 ISE 位於不同的虛擬網路中，您也必須 [對等網路](../virtual-network/tutorial-connect-virtual-networks-portal.md) 進行連線。

### <a name="sap-client-library-prerequisites"></a>SAP 用戶端程式庫必要條件

這些是您搭配連接器使用的 SAP 用戶端程式庫的必要條件。

* 請確定您安裝的是最新版本、 [SAP 連接器 (NCo 3.0) 適用于以 .NET Framework 4.0-Windows 64 位 (x64) 編譯的 Microsoft .net 3.0.22.0 ](https://support.sap.com/en/product/connectors/msnet.html)。 當同時傳送多個 IDoc 訊息時，較早的 SAP NCo 版本可能會遇到問題。 此條件會封鎖傳送至 SAP 目的地的所有後續訊息，這會導致訊息超時。

* 您必須安裝64位版本的 SAP 用戶端程式庫，因為資料閘道只會在64位系統上執行。 安裝不支援的32位版本會導致「映射錯誤」錯誤。

* 根據您的案例，將元件檔案從預設的安裝資料夾複製到另一個位置，如下所示。

    * 針對在 ISE 中執行的邏輯應用程式，請改為遵循 [ise 必要條件](#ise-prerequisites) 。

    * 針對在多租使用者 Azure 中執行的邏輯應用程式，並使用您的內部部署資料閘道，將元件檔複製到資料閘道安裝資料夾。 

        
        * 如果您的 SAP 連線失敗並出現錯誤訊息， **請檢查您的帳戶資訊和/或許可權，然後再試一** 次，確定您已將元件檔案複製到資料閘道安裝資料夾。
        
        * 使用 .Net 元件系結 [記錄檔檢視器](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)針對進一步的問題進行疑難排解。 這項工具可讓您檢查元件檔是否位於正確的位置。 
        
        * （選擇性）當您安裝 SAP 用戶端程式庫時，請選取 [ **全域組件快取] 註冊** 選項。

請注意 SAP 用戶端程式庫、.NET Framework、.NET 執行時間和閘道之間的下列關聯性：

* Microsoft SAP Adapter 和閘道主機服務都會使用 .NET Framework 4.7.2。

* 適用于 .NET Framework 4.0 的 SAP NCo 適用于使用 .NET 執行時間4.0 到4.8 的進程。

* 適用于 .NET Framework 2.0 的 SAP NCo 適用于使用 .NET 執行時間2.0 到3.5 的處理常式，但不再適用于最新的閘道。

### <a name="snc-prerequisites"></a>SNC 必要條件

如果您使用內部部署資料閘道搭配選擇性 SNC （僅在多租使用者 Azure 中支援），您必須設定這些額外的設定。

如果您使用 SNC 搭配 SSO，請確定資料閘道服務是以針對 SAP 使用者進行對應的使用者身分執行。 若要變更預設帳戶，請選取 [ **變更帳戶**]，然後輸入使用者認證。

![Azure 入口網站中內部部署資料閘道設定的螢幕擷取畫面，其中顯示 [服務設定] 頁面，其中包含用來變更所選取閘道服務帳戶的按鈕。](./media/logic-apps-using-sap-connector/gateway-account.png)

如果您是透過外部安全性產品啟用 SNC，請將 SNC 程式庫複製到您的資料閘道安裝所在的同一部電腦上。 SNC 產品的一些範例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。 如需啟用資料閘道 SNC 的詳細資訊，請參閱 [啟用安全網路通訊](#enable-secure-network-communications)。

## <a name="send-idoc-messages-to-sap-server"></a>將 IDoc 訊息傳送至 SAP 伺服器

請遵循下列範例來建立邏輯應用程式，以將 IDoc 訊息傳送至 SAP 伺服器，並傳迴響應：

1. [建立由 HTTP 要求所觸發的邏輯應用程式。](#create-http-request-trigger)

1. [在您的工作流程中建立動作，以將訊息傳送至 SAP。](#create-sap-action-to-send-message)

1. [在您的工作流程中建立 HTTP 回應動作。](#create-http-response-action)

1. [如果您要使用 RFC 來接收 SAP ABAP 的回復，請建立遠端函數呼叫 (RFC) 要求-回應模式。](#create-rfc-request-response)

1. [測試您的邏輯應用程式。](#test-logic-app)

### <a name="create-http-request-trigger"></a>建立 HTTP 要求觸發程式

> [!NOTE]
> 當邏輯應用程式接收到來自 SAP 的 Idoc 時， [要求觸發](../connectors/connectors-native-reqres.md) 程式現在支援 SAP 純 XML 格式。 若要以純 XML 接收 Idoc，請 **在從 SAP 收到訊息時** 使用觸發程式。 將參數 **IDOC 格式** 設定為 **SapPlainXml**。

首先，使用 Azure 中的端點建立邏輯應用程式，以將 *HTTP POST* 要求傳送至您的邏輯應用程式。 當您的邏輯應用程式收到這些 HTTP 要求時， [觸發](../logic-apps/logic-apps-overview.md#logic-app-concepts) 程式會引發並執行工作流程中的下一個步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)中，建立空白的邏輯應用程式，以開啟 **Logic Apps 設計** 工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從 [ **觸發** 程式] 清單中，選取 [ **收到 HTTP 要求時**]。

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示新增至邏輯應用程式的 HTTP 要求觸發程式。](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 儲存您的邏輯應用程式，讓您可以產生邏輯應用程式的端點 URL。 在設計工具的工具列上，選取 [儲存]  。 端點 URL 現在會出現在您的觸發程式中。 

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示覆制產生的貼文 URL 的 HTTP 要求觸發程式。](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>建立 SAP 動作以傳送訊息

接下來，建立動作，以在您的 [HTTP 要求觸發](#create-http-request-trigger) 程式引發時將 IDoc 訊息傳送至 SAP。

1. 在 [Logic Apps 設計工具] 的 [觸發程式] 底下，選取 [ **新增步驟**]。

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示正在編輯的邏輯應用程式，以新增步驟。](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [ **動作** ] 清單中，選取 [ **將訊息傳送至 SAP**]。
  
   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示 [傳送訊息至 SAP] 動作的選取專案。](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   或者，您可以選取 [ **企業** ] 索引標籤，然後選取 SAP 動作。

   ![Logic Apps 設計工具的螢幕擷取畫面，其中顯示 [企業] 索引標籤下的 [傳送訊息至 SAP] 動作的選取專案。](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟。 如果系統提示您建立新的連線，請提供下列資訊以連接到您的內部部署 SAP 伺服器。

   1. 提供連線的名稱。

   1. 如果您使用的是資料閘道，請遵循下列步驟：
   
      1. 在 [ **資料閘道** ] 區段的 [ **訂** 用帳戶] 下，先針對您在資料閘道安裝的 Azure 入口網站中建立的資料閘道資源選取 Azure 訂用帳戶。
   
      1. 在 [連線 **閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接資訊。 針對 [ **登入類型** ] 屬性，根據屬性設定為 [ **應用程式伺服器** ] 或 [ **群組**]，執行下列步驟：
   
      * 針對 **應用程式伺服器**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 針對 **群組**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強型別是用來對架構執行 XML 驗證，以檢查是否有不正確值。 此行為可協助您及早偵測問題。 **Safe 輸入** 選項可提供回溯相容性，而且只會檢查字串長度。 深入瞭解 [Safe 輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 設定並測試您的連接，以確保連線正常運作。

    > [!NOTE]

    > 如果您收到下列錯誤，您的 SAP NCo 用戶端程式庫安裝有問題： 
    >
    > **測試連接失敗。錯誤：無法處理要求。錯誤詳細資料：「無法載入檔案或元件 ' sapnco，Version = 3.0.0.42，Culture = 中性，PublicKeyToken 50436dca5c7f7d23 ' 或其相依性的其中之一。系統找不到指定的檔案。 '. '**
    >
    > 請務必 [安裝所需的 SAP NCo 用戶端程式庫版本，並符合所有其他先決條件](#sap-client-library-prerequisites)。

1. 立即尋找並從 SAP 伺服器選取動作。

   1. 在 [ **SAP 動作** ] 方塊中，選取資料夾圖示。 從檔案清單中，尋找並選取您要使用的 SAP 訊息。 若要瀏覽清單，請使用箭號。

      此範例會選取具有 **Orders** 類型的 IDoc。

      ![尋找並選取 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到您需要的動作，您可手動輸入路徑，例如：

      ![手動提供路徑給 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 透過運算式編輯器提供 **SAP 動作** 的值。 如此一來，您就可以針對不同的訊息類型使用相同的動作。

      如需 IDoc 作業的詳細資訊，請參閱 [IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在 [輸入訊息] 方塊內按一下，動態內容清單隨即顯示。 在該清單中的 [收到 HTTP 要求時] 之下，選取 [內文] 欄位。

      此步驟包含來自 HTTP 要求觸發程式的本文內容，並將輸出傳送至您的 SAP 伺服器。

      ![從觸發程式中選取 [Body] 屬性](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      當您完成時，您的 SAP 動作看起來會像這個範例：

      ![完成 SAP 動作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

#### <a name="send-flat-file-idocs"></a>傳送一般檔案 Idoc

如果您將 Idoc 包裝在 XML 信封中，就可以使用它搭配一般檔案架構。 若要傳送一般檔案 IDoc，請使用一般指示來 [建立 SAP 動作，以使用下列變更來傳送 IDoc 訊息](#create-sap-action-to-send-message) 。

1. 針對 [ **傳送訊息至 SAP** ] 動作，使用 [SAP 動作 URI] `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` 。

1. 使用 XML 信封來格式化您的輸入訊息。 如需範例，請參閱下列範例訊息：

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>建立 HTTP 回應動作

現在請將回應動作新增至邏輯應用程式的工作流程，並納入 SAP 動作的輸出結果。 如此一來，邏輯應用程式便會從 SAP 伺服器將結果傳回至原始的要求者。

1. 在 [Logic Apps 設計工具] 的 [SAP] 動作底下，選取 [ **新增步驟**]。

1. 在搜尋方塊中，輸入 `response` 作為篩選條件。 從 [ **動作** ] 清單中選取 [ **回應**]。

1. 在 [內文] 方塊內按一下，動態內容清單隨即顯示。 從該清單中 **的 [** **將訊息傳送至 SAP**] 底下，選取 [內文] 欄位。

   ![完整的 SAP 動作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 儲存邏輯應用程式。

#### <a name="create-rfc-request-response"></a>建立 RFC 要求-回應

> [!NOTE]
> SAP 觸發程式會透過 tRFC 接收 Idoc，而不會有設計的回應參數。 

如果您需要使用遠端函數呼叫來接收回複，則必須建立要求和回應模式， (RFC) 從 SAP ABAP Logic Apps。 若要在您的邏輯應用程式中接收 Idoc，您應該將其第一個動作設為 [HTTP 要求](../connectors/connectors-native-reqres.md#add-a-response-action) ，並顯示狀態碼 `200 OK` 和沒有內容。 此建議步驟會立即完成 tRFC 的 SAP LUW 非同步傳送，這會讓 SAP CPIC 對話再次可供使用。 然後，您可以在邏輯應用程式中新增進一步的動作來處理接收的 IDoc，而不會封鎖進一步的傳送。

若要執行要求和回應模式，您必須先使用[ `generate schema` 命令](#generate-schemas-for-artifacts-in-sap)探索 RFC 架構。 產生的架構有兩個可能的根節點： 

1. 要求節點，也就是您從 SAP 收到的呼叫。

1. 回應節點，這是回復至 SAP 的回復。

在下列範例中，會從 RFC 模組產生要求和回應模式 `STFC_CONNECTION` 。 要求 XML 會經過剖析，以將 SAP 要求的節點值解壓縮 `<ECHOTEXT>` 。 回應會將目前的時間戳記插入為動態值。 當您 `STFC_CONNECTION` 從邏輯應用程式將 RFC 應用程式傳送至 SAP 時，您會收到類似的回應。

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>測試邏輯應用程式

1. 如果您的邏輯應用程式尚未啟用，請在邏輯應用程式功能表上選取 **[總覽**]。 在工具列上，選取 [ **啟用**]。

1. 在設計工具的工具列上，選取 [ **執行**]。 此步驟會手動啟動您的邏輯應用程式。

1. 將 HTTP POST 要求傳送至 HTTP 要求觸發程式中的 URL，以觸發邏輯應用程式。
將您的訊息內容包含在您的要求中。 若要傳送內容，您可使用 [Postman](https://www.getpostman.com/apps) 之類的工具。

   在本文中，要求會傳送 IDoc 檔案，其必須為 XML 格式並且包含您要使用的 SAP 動作的命名空間，例如：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. 傳送 HTTP 要求後，請等候來自邏輯應用程式的回應。

   > [!NOTE]
   > 如果回應所需的所有步驟未在[要求逾時期限](./logic-apps-limits-and-config.md)內完成，邏輯應用程式可能會逾時。 如果發生此情況，要求可能就會遭到封鎖。 為協助您診斷問題，請了解如何[檢查和監控邏輯應用程式](../logic-apps/monitor-logic-apps.md)。

您現在已建立可與 SAP 伺服器通訊的邏輯應用程式。 您現在已為邏輯應用程式設定 SAP 連線，接下來可探索其他可用的 SAP 動作如：BAPI 和 RFC。

## <a name="receive-message-from-sap"></a>從 SAP 接收訊息

此範例會使用邏輯應用程式，該應用程式會在應用程式從 SAP 系統接收訊息時觸發。

### <a name="add-an-sap-trigger"></a>新增 SAP 觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 **觸發** 程式清單中，選取 **從 SAP 收到訊息時**。

   ![新增 SAP 觸發程序](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   或者，您可以選取 [ **企業** ] 索引標籤，然後選取觸發程式：

   ![從 [企業] 索引標籤新增 SAP 觸發程式](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果您的連線已存在，請繼續下一個步驟，讓您可以設定 SAP 動作。 但是，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連線的名稱。

   1. 如果您使用的是資料閘道，請遵循下列步驟：

      1. 在 [ **資料閘道** ] 區段的 [ **訂** 用帳戶] 下，先針對您在資料閘道安裝的 Azure 入口網站中建立的資料閘道資源選取 Azure 訂用帳戶。

      1. 在 [連線 **閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [ **登入類型** ] 屬性，根據屬性設定為 [ **應用程式伺服器** ] 或 [ **群組**]，執行下列步驟：

      * 針對 **應用程式伺服器**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 針對 **群組**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      根據預設，強型別是用來對架構執行 XML 驗證，以檢查是否有不正確值。 此行為可協助您及早偵測問題。 **Safe 輸入** 選項可提供回溯相容性，而且只會檢查字串長度。 深入瞭解 [Safe 輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 設定並測試您的連接，以確保連線正常運作。

1. 根據您的 SAP 系統組態提供 [必要參數](#parameters) 。 

   您可以藉 [由指定 sap 動作清單來篩選您從 sap 伺服器收到的訊息](#filter-with-sap-actions)。

   您可以從檔案選擇器選取 SAP 動作：

   ![將 SAP 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，您可以手動指定動作：

   ![手動輸入您想要使用的 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下舉例說明當您設定觸發程序來接收多個訊息時，此動作的顯示方式。

   ![接收多個訊息的觸發程式範例](media/logic-apps-using-sap-connector/example-trigger.png)

   如需 SAP 動作的詳細資訊，請參閱 [IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 現在儲存邏輯應用程式，以便可以開始從 SAP 系統接收訊息。 在設計工具的工具列上，選取 [儲存]  。

邏輯應用程式現在已準備好從 SAP 系統接收訊息。

> [!NOTE]
> SAP 觸發程式不是輪詢觸發程式，而是以 webhook 為基礎的觸發程式。 如果您使用的是資料閘道，則只有在訊息存在時才會從資料閘道呼叫觸發程式，因此不需要進行輪詢。

#### <a name="parameters"></a>參數

除了簡單的字串和數位輸入，SAP 連接器還接受下列資料表參數 (`Type=ITAB` 輸入) ：

* 適用于較舊 SAP 版本的資料表方向參數（輸入和輸出）。

* 變更參數，以取代較新 SAP 版本的資料表方向參數。

* 階層式資料表參數

#### <a name="filter-with-sap-actions"></a>使用 SAP 動作篩選

您可以選擇性地篩選邏輯應用程式從 SAP 伺服器接收的訊息，方法是提供包含單一或多個 SAP 動作的清單或陣列。 根據預設，此陣列是空的，這表示您的邏輯應用程式會從您的 SAP 伺服器接收所有訊息，而不進行篩選。 

當您設定陣列篩選器時，觸發程式只會接收來自指定之 SAP 動作類型的訊息，並拒絕 SAP 伺服器的所有其他訊息。 不過，此篩選準則不會影響所接收承載的輸入是否為弱式或強式。

任何 SAP 動作篩選都會發生在內部部署資料閘道的 SAP 介面卡層級上。 如需詳細資訊，請參閱 [如何將測試 idoc 傳送至 SAP 的 Logic Apps](#test-sending-idocs-from-sap)。

如果您無法將 IDoc 封包從 SAP 傳送到邏輯應用程式的觸發程式，請參閱 [SAP tRFC] 對話方塊中的交易式 RFC (tRFC) 呼叫拒絕訊息 (T 程式碼 SM58) 。 在 SAP 介面中，您可能會收到下列錯誤訊息，這是因為 [ **狀態文字** ] 欄位上的子字串限制而被剪下。

* `The RequestContext on the IReplyChannel was closed without a reply being`：當通道的 catch 全部處理常式因為錯誤而終止通道時，會發生未預期的失敗，並重建通道以處理其他訊息。

  * 若要確認您的邏輯應用程式已收到 IDoc，請新增會傳回狀態碼的 [回應動作](../connectors/connectors-native-reqres.md#add-a-response-action) `200 OK` 。 IDoc 會透過 tRFC 傳輸，而不允許回應裝載。

  * 如果您需要改為拒絕 IDoc，則會回應任何 HTTP 狀態碼以外的任何 HTTP 狀態碼， `200 OK` 讓 Sap 介面卡代表您將例外狀況傳回給 sap。 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`：發生其他錯誤時，發生預期的失敗，例如因為無法產生 IDoc XML 裝載，而無法產生其區段，因為 SAP 未釋出其區段，所以遺漏轉換所需的區段類型中繼資料。 

  * 若要讓 SAP 發行這些區段，請洽詢 SAP 系統的 ABAP 工程師。
### <a name="asynchronous-request-reply-for-triggers"></a>非同步要求-回復觸發程式

SAP 連接器支援 Azure 的 Logic Apps 觸發 [程式的非同步要求-回復模式](/azure/architecture/patterns/async-request-reply.md) 。 您可以使用此模式來建立成功的要求，否則會失敗，並使用預設的同步要求-回復模式。 

> [!TIP]
> 在具有多個回應動作的邏輯應用程式中，所有回應動作都必須使用相同的要求-回復模式。 例如，如果您的邏輯應用程式使用具有多個可能回應動作的 switch 控制項，則您必須將所有回應動作設定為使用相同的要求-回復模式，也就是同步或非同步。 

啟用回應動作的非同步回應，可讓您的邏輯應用程式 `202 Accepted` 在接受要求以進行處理時回應回復。 回復包含位置標頭，您可以使用此標頭來取得要求的最終狀態。

若要使用 SAP 連接器設定邏輯應用程式的非同步要求-回復模式：

1. 在 **Logic Apps 設計** 工具中開啟邏輯應用程式。

1. 確認 SAP 連接器是邏輯應用程式的觸發程式。

1. 開啟邏輯應用程式的 **回應** 動作。 在動作的標題列中，選取 **功能表 ()** &gt; **設定**]。

1. 在 [回應動作] 的 [ **設定** ] 中，開啟 [ **非同步回應**] 下的切換。 選取 [完成]。

1. 將變更儲存至您的邏輯應用程式。

## <a name="find-extended-error-logs"></a>尋找延伸的錯誤記錄檔

如需完整的錯誤訊息，請檢查您的 SAP adapter 擴充記錄。 您也可以 [啟用 SAP 連接器的擴充記錄](#extended-sap-logging-in-on-premises-data-gateway)檔。

若為從2020年6月和之後的內部部署資料閘道版本，您可以 [在應用程式設定中啟用閘道記錄](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)。 

針對從2020年4月和更早版本的內部部署資料閘道版本，預設會停用記錄。

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>內部部署資料閘道中的擴充 SAP 記錄

如果您使用內部 [部署資料閘道進行 Logic Apps](../logic-apps/logic-apps-gateway-install.md)，您可以設定 SAP 連接器的擴充記錄檔。 您可以使用內部部署資料閘道來重新導向 Windows (ETW) 事件的事件追蹤，以輪替包含在閘道記錄 .zip 檔案中的記錄檔。 

您可以從閘道應用程式的設定，將 [所有閘道的設定和服務記錄匯出](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) 至中的 .zip 檔案。

> [!NOTE]
> 當 always enabled 時，擴充記錄可能會影響邏輯應用程式的效能。 完成分析和疑難排解問題之後，最好先關閉擴充的記錄檔。

#### <a name="capture-etw-events"></a>捕捉 ETW 事件

（選擇性） advanced 使用者可以直接捕捉 ETW 事件。 然後，您可以 [在事件中樞 Azure 診斷中取用您的資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs) ，或 [收集您的資料以 Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs)。 如需詳細資訊，請參閱 [收集和儲存資料的最佳做法](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#collecting-and-storing-data)。 您可以使用 [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) 來處理產生的 ETL 檔案，也可以撰寫自己的程式。 本逐步解說會使用 PerfView：

1. 在 [PerfView] 功能表中，選取 [ **收集** &gt; **收集** ] 以抓取事件。

1. 在 [ **其他提供者** ] 欄位中，輸入 `*Microsoft-LobAdapter` 以指定要用來捕捉 sap 介面卡事件的 sap 提供者。 如果您未指定此資訊，您的追蹤只會包含一般 ETW 事件。

1. 保留其他預設設定。 如果您想要的話，可以在 [ **資料檔案** ] 欄位中變更檔案名或位置。

1. 選取 [ **開始收集** ] 開始追蹤。

1. 重現您的問題或收集到足夠的分析資料之後，請選取 [ **停止收集**]。

若要與其他一方（例如 Azure 支援工程師）共用您的資料，請壓縮 ETL 檔案。

若要查看追蹤的內容：

1. 在 PerfView 中， **選取** &gt; [ **開啟** 檔案]，然後選取您剛剛產生的 ETL 檔案。

1. 在 PerfView 提要欄位中，您 ETL 檔案底下的 **Events** 區段。

1. 在 [ **篩選準則**] 底下，篩選依據 `Microsoft-LobAdapter` 以僅查看相關事件和閘道處理常式。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 若要觸發邏輯應用程式，請從 SAP 系統傳送訊息。

1. 在 [邏輯應用程式] 功能表上，選取 **[總覽**]。 檢查邏輯應用程式的任何新回合的 **執行歷程記錄** 。

1. 開啟最近的執行，其會在觸發程序的輸出區段中顯示從 SAP 系統傳送的訊息。

### <a name="test-sending-idocs-from-sap"></a>從 SAP 測試傳送 Idoc

若要將 Idoc 從 SAP 傳送至您的邏輯應用程式，您需要下列最低設定：

> [!IMPORTANT]
> 只有當您使用邏輯應用程式測試 SAP 設定時，才使用這些步驟。 生產環境需要額外的設定。

1. [在 SAP 中設定 RFC 目的地](#create-rfc-destination)

1. [建立與您的 RFC 目的地的 ABAP 連接](#create-abap-connection)

1. [建立接收者埠](#create-receiver-port)

1. [建立傳送者埠](#create-sender-port)

1. [建立邏輯系統合作夥伴](#create-logical-system-partner)

1. [建立夥伴設定檔](#create-partner-profiles)

1. [測試傳送訊息](#test-sending-messages)

#### <a name="create-rfc-destination"></a>建立 RFC 目的地

1. 若要開啟 **RFC** 連線設定的設定，請在您的 SAP 介面中使用 **sm59** Transaction 程式碼 (T 程式碼) 搭配 **/n** 前置詞。

1. 選取 [ **tcp/ip 連接**  >  **建立**]。

1. 使用下列設定建立新的 RFC 目的地：
    
    * 針對您的 **RFC 目的地**，輸入名稱。
    
    * 在 [ **技術設定** ] 索引標籤上，針對 [ **啟用類型**] 選取 [ **已註冊的伺服器程式**] 針對您的 **程式識別碼**，輸入值。 在 SAP 中，邏輯應用程式的觸發程式將會使用此識別碼來註冊。
    
    * 在 [ **unicode** ] 索引標籤上，針對 [ **目標系統的通訊類型**] 選取 [ **unicode**]。

1. 儲存您的變更。

1. 向 Azure Logic Apps 註冊新的 **程式識別碼** 。

1. 若要測試您的連線，請在 SAP 介面的 [新的 **RFC 目的地**] 底下，選取 [ **連接測試**]。

#### <a name="create-abap-connection"></a>建立 ABAP 連接

1. 若要開啟 **RFC** 連線設定的設定，請在您的 SAP 介面中使用 **sm59** _ 交易程式碼 (T 程式碼) 搭配 _ */n** 前置詞。

1. 選取 [ **ABAP 連接**  >  **建立**]。

1. 針對 [ **RFC 目的地**]，輸入 [測試 SAP 系統](#create-rfc-destination)的識別碼。

1. 儲存您的變更。

1. 若要測試您的連接，請選取 [ **連接測試**]。

#### <a name="create-receiver-port"></a>建立接收者埠

1. 若要在 **IDOC 處理** 設定中開啟埠，請在您的 SAP 介面中使用 **we21** Transaction 程式碼 (T 程式碼) 搭配 **/n** 前置詞。

1. 選取  >  **交易 RFC**  >  **建立** 的埠。

1. 在開啟的 [設定] 方塊中，選取 [ **自己的埠名稱**]。 針對您的測試埠，輸入 **名稱**。 儲存您的變更。

1. 在 [新接收器埠的設定] 中，針對 [ **rfc 目的地**] 輸入 [測試 rfc 目的地](#create-rfc-destination)的識別碼。

1. 儲存您的變更。

#### <a name="create-sender-port"></a>建立傳送者埠

1.  若要在 **IDOC 處理** 設定中開啟埠，請在您的 SAP 介面中使用 **we21** Transaction 程式碼 (T 程式碼) 搭配 **/n** 前置詞。

1. 選取  >  **交易 RFC**  >  **建立** 的埠。

1. 在開啟的 [設定] 方塊中，選取 [ **自己的埠名稱**]。 針對您的測試埠，輸入以 **SAP** 開頭的 **名稱**。 所有的寄件者埠名稱都必須以字母 **SAP** 開頭，例如， **SAPTEST**。 儲存您的變更。

1. 在新傳送者埠的設定中，針對 [ **RFC 目的地**] 輸入 [ABAP 連接](#create-abap-connection)的識別碼。

1. 儲存您的變更。

#### <a name="create-logical-system-partner"></a>建立邏輯系統夥伴

1. 若要開啟 **變更視圖的「邏輯系統」：總覽** 設定，請在您的 SAP 介面中使用 **bd54** 的交易程式碼 (T 程式碼) 。

1. 接受出現的警告訊息： **注意：資料表是跨用戶端**

1. 在顯示現有邏輯系統的清單上方，選取 [ **新增專案**]。

1. 針對新的邏輯系統，輸入 **Log.System** 識別碼和簡短 **名稱** 描述。 儲存您的變更。

1. 當 **工作臺出現提示** 時，請提供描述來建立新的要求，或者，如果您已經建立要求，請略過此步驟。

1. 建立工作臺要求之後，將該要求連結至資料表更新要求。 若要確認資料表是否已更新，請儲存您的變更。

#### <a name="create-partner-profiles"></a>建立夥伴設定檔

針對生產環境，您必須建立兩個夥伴設定檔。 第一個設定檔是針對寄件者，也就是您的組織和 SAP 系統。 第二個設定檔適用于接收者，也就是您的邏輯應用程式。

1. 若要開啟 **夥伴設定檔** 設定，請在您的 SAP 介面中使用 **we20** 的交易程式碼 (T 程式碼) 搭配 **/n** 前置詞。

1. 在 [**夥伴設定檔**] 底下，選取 [**夥伴類型 LS**  >  **建立**]。

1. 使用下列設定建立新的夥伴設定檔：

    * 針對 [ **夥伴編號**]，輸入 [邏輯系統合作夥伴的識別碼](#create-logical-system-partner)。

    * 適用于 **Partn。輸入，** 輸入 **LS**。

    * 針對 [ **代理程式**]，輸入為 Azure Logic Apps 或其他非 SAP 系統註冊程式識別碼時，所要使用的 SAP 使用者帳戶的識別碼。

1. 儲存您的變更。 如果您尚未 [建立邏輯系統夥伴](#create-logical-system-partner)，您會收到錯誤，請 **輸入有效的夥伴編號**。

1. 在您夥伴設定檔的設定中，于 [ **輸出 parmtrs**] 底下，選取 [ **建立輸出參數**]。

1. 使用下列設定建立新的輸出參數：

    * 輸入您的 **訊息類型**，例如 **CREMAS**。

    * 輸入 [接收器埠的識別碼](#create-receiver-port)。

    * 輸入套件的 IDoc 大小 **。大小**。 或者，若要 [一次從 SAP 傳送一個 idoc](#receive-idoc-packets-from-sap)，請選取 [ **立即傳遞 IDoc**]。

1. 儲存您的變更。

#### <a name="test-sending-messages"></a>測試傳送訊息

1. 若要開啟 **IDoc 處理設定的測試控管** ，請在您的 SAP 介面中使用 **we19** Transaction 程式碼 (T 程式碼) 搭配 **/n** 前置詞。

1. 在 [ **測試的範本**] 下，選取 [ **Via 訊息類型**]，然後輸入您的訊息類型，例如 **CREMAS**。 選取 [建立]  。

1. 選取 [**繼續**]，確認 **哪一種 IDoc 類型** 的訊息。

1. 選取 [ **EDIDC** ] 節點。 為您的接收者和寄件者埠輸入適當的值。 選取 [繼續]。

1. 選取 **標準輸出處理**。

1. 若要開始輸出 IDoc 處理，請選取 [ **繼續**]。 當處理完成時，會顯示 **傳送至 SAP 系統或外部程式** 訊息的 IDoc。

1.  若要檢查處理錯誤，請使用 **sm58** 的交易程式碼 (T 程式碼) 搭配 **/n** 前置詞。

## <a name="receive-idoc-packets-from-sap"></a>接收來自 SAP 的 IDoc 封包

您可以設定 SAP 在封 [包中傳送 idoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)，也就是批次或 idoc 群組。 若要接收 IDoc 封包，SAP 連接器（尤其是觸發程式）不需要額外的設定。 不過，若要在觸發程式收到封包之後處理 IDoc 封包中的每個專案，則需要執行一些額外的步驟，才能將封包分割成個別 Idoc。

以下範例示範如何使用函式從封包解壓縮個別的[ `xpath()` idoc：](./workflow-definition-language-functions-reference.md#xpath)

1. 開始之前，您需要具有 SAP 觸發程式的邏輯應用程式。 如果您還沒有此邏輯應用程式，請遵循本主題先前的步驟， [設定具有 SAP 觸發程式的邏輯應用程式](#receive-message-from-sap)。

   例如：

   ![將 SAP 觸發程式新增至邏輯應用程式](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 從邏輯應用程式從 SAP 收到的 XML IDoc 取得根命名空間。 若要從 XML 檔解壓縮此命名空間，請加入一個步驟來建立本機字串變數，並使用運算式來儲存該命名空間 `xpath()` ：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![從 IDoc 取得根命名空間](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要解壓縮個別的 IDoc，請加入一個步驟來建立陣列變數，並使用另一個運算式儲存 IDoc 集合 `xpath()` ：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![取得專案陣列](./media/logic-apps-using-sap-connector/get-array.png)

   陣列變數可讓您的邏輯應用程式使用每個 IDoc，藉由列舉集合來個別處理。 在此範例中，邏輯應用程式會使用迴圈，將每個 IDoc 傳送至 SFTP 伺服器：

   ![將 IDoc 傳送至 SFTP 伺服器](./media/logic-apps-using-sap-connector/loop-batch.png)

   每個 IDoc 都必須包含根命名空間，也就是 `<Receive></Receive` 在此情況下，將 IDoc 傳送至下游應用程式或 SFTP 伺服器之前，檔案內容會包裝在元素中的原因和根命名空間。

當您建立新的邏輯應用程式時，您可以在 Logic Apps 設計工具中選取此範本，以使用此模式的快速入門範本。

![選取 batch 邏輯應用程式範本](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>在 SAP 中產生成品的結構描述

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 為了產生指定 IDoc 和 BAPI 的架構，SAP 動作 **產生架構** 會將要求傳送給 sap 系統。

此 SAP 動作會傳回 [xml 架構](#sample-xml-schemas)，而不是 xml 檔本身的內容或資料。 在回應中傳回的架構會使用 Azure Resource Manager 連接器上傳至整合帳戶。 架構包含下列部分：

* 要求訊息的結構。 使用此資訊來形成您的 BAPI `get` 清單。

* 回應訊息的結構。 使用此資訊來剖析回應。 

若要傳送要求訊息，請使用一般 SAP 動作 **傳送訊息至 SAP** 或目標 **呼叫 BAPI** 動作。

### <a name="sample-xml-schemas"></a>範例 XML 架構

如果您要瞭解如何產生 XML 架構以用於建立範例檔，請參閱下列範例。 這些範例會示範如何使用許多類型的承載，包括：

* [RFC 要求](#xml-samples-for-rfc-requests)

* [BAPI 要求](#xml-samples-for-bapi-requests)

* [IDoc 要求](#xml-samples-for-idoc-requests)

* 簡單或複雜的 XML 架構資料類型

* 資料表參數

* 選擇性 XML 行為

您可以使用選擇性的 XML 初構來開始 XML 架構。 SAP 連接器適用于或不含 XML 初構。

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 要求的 XML 範例

下列範例是基本的 RFC 呼叫。 RFC 名稱為 `STFC_CONNECTION` 。 此要求會使用預設的命名空間 `xmlns=` ，不過，您可以指派和使用命名空間別名（例如） `xmmlns:exampleAlias=` 。 命名空間值是 SAP 中適用于 Microsoft 服務的所有 Rfc 的命名空間。 要求中有簡單的輸入參數 `<REQUTEXT>` 。

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

下列範例是具有資料表參數的 RFC 呼叫。 此範例呼叫和其測試 Rfc 群組可作為所有 SAP 系統的一部分。 資料表參數的名稱為 `TCPICDAT` 。 資料表行類型為 `ABAPTEXT` ，而且這個元素會針對資料表中的每個資料列重複。 此範例包含稱為的單行 `LINE` 。 具有資料表參數的要求可以包含任意數目的欄位，其中數位是 (*n*) 的正整數。 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

下列範例是具有具有匿名欄位之資料表參數的 RFC 呼叫。 當欄位未指派名稱時，即為匿名欄位。 複雜型別是在不同的命名空間下宣告，在此命名空間中，宣告會為目前的節點和其所有子專案設定新的預設值。 此範例會使用十六進位碼 `x002F` 作為符號的 escape 字元 */* ，因為此符號會保留在 SAP 功能變數名稱中。

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

下列範例包含命名空間的前置詞。 您可以一次宣告所有前置詞，也可以將任意數目的前置詞宣告為節點的屬性。 RFC 命名空間別名 `ns0` 會用來做為基本類型的根和參數。

> [!NOTE]
> 複雜型別是在不同的命名空間下，針對具有別名的 RFC 型別， `ns3` 而不是具有別名的一般 rfc 命名空間 `ns0` 。

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 要求的 XML 範例

下列 XML 範例是 [呼叫 BAPI 方法](#call-bapi-action)的範例要求。

> [!NOTE]
> SAP 會藉由描述 RFC `RPY_BOR_TREE_INIT` （Logic Apps 沒有輸入篩選器的問題），將商務物件提供給外部系統使用。 Logic Apps 會檢查輸出資料表 `BOR_TREE` 。 此 `SHORT_TEXT` 欄位會用於商務物件的名稱。 Logic Apps 不能存取輸出資料表中 SAP 所傳回的商務物件。
> 
> 如果您使用自訂商務物件，您必須確定在 SAP 中發行和發行這些商務物件。 否則，SAP 不會在輸出資料表中列出您的自訂商務物件 `BOR_TREE` 。 您無法在 Logic Apps 中存取自訂商務物件，除非您從 SAP 公開商務物件。 

下列範例會使用 BAPI 方法取得銀行的清單 `GETLIST` 。 此範例包含銀行的商務物件 `BUS1011` 。 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

下列範例會使用方法建立銀行物件 `CREATE` 。 此範例使用與上一個範例相同的商務物件 `BUS1011` 。 當您使用 `CREATE` 方法建立銀行時，請務必認可您的變更，因為預設不會認可此方法。

> [!TIP]
> 請確定您的 XML 檔遵循 SAP 系統中設定的任何驗證規則。 例如，在此範例檔中， () 的 bank 金鑰 `<BANK_KEY>` 必須是美國的銀行路線號碼，也稱為 ABA 號碼。

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

下列範例會取得銀行的詳細資料，並使用銀行的路由號碼，其值為 `<BANK_KEY>` 。 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 要求的 XML 範例

若要產生純 SAP IDoc XML 架構，請使用 **SAP 登** 入應用程式和 T 程式碼 `WE-60` 。 透過 GUI 存取 SAP 檔，並針對您的 IDoc 類型和延伸模組產生 XSD 格式的 XML 架構。 如需一般 SAP 格式和承載的說明，以及其內建對話方塊的相關說明，請參閱 [SAP 檔](https://help.sap.com/viewer/index)。

此範例會宣告根節點和命名空間。 範例程式碼中的 URI 會宣告 `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` 下列設定：

* `/IDoc` 是所有 Idoc 的根附注
* `/3` 是通用區段定義的記錄類型版本
* `/ORDERS05` 這是 IDoc 類型
* `//` 是空的區段，因為沒有 IDoc 延伸模組
* `/700` 是 SAP 版本
* `/Send` 這是將資訊傳送至 SAP 的動作

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

您可以 `idocData` 在單一呼叫中重複節點，以傳送 idoc 批次。 在下列範例中，有一個控制項記錄、 `EDI_DC40` 和多個資料記錄。

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

下列範例是使用前置詞的範例 IDoc 控制項記錄 `EDI_DC` 。 您必須更新這些值，以符合您的 SAP 安裝和 IDoc 類型。 例如，您的 IDoc 用戶端程式代碼可能不是 `800` 。 請洽詢您的 SAP 小組，以確定您使用的是 SAP 安裝的正確值。

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

下列範例是具有一般區段的範例資料記錄。 此範例會使用 SAP 日期格式。 強型別檔可以使用原生 XML 日期格式，例如 `2020-12-31 23:59:59` 。

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

下列範例是具有分組區段的資料記錄。 記錄包含群組父節點、 `E2EDKT1002GRP` 和多個子節點，包括 `E2EDKT1002` 和 `E2EDKT2001` 。 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

建議的方法是建立 IDoc 識別碼以搭配 tRFC 使用。 您可以 `tid` 使用 SAP 連接器 API 中的 [傳送 IDoc 操作](/connectors/sap/#send-idoc) 來設定此交易識別碼。

下列範例是設定交易識別碼或的替代方法 `tid` 。 在此範例中，會關閉最後一個資料記錄區段節點和 IDoc 資料節點。 然後，GUID `guid` 會用來做為偵測重複專案的 tRFC 識別碼。 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>新增 HTTP 要求觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟 Logic Apps 設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從 [ **觸發** 程式] 清單中，選取 [ **收到 HTTP 要求時**]。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在請儲存邏輯應用程式，即可產生邏輯應用程式的端點 URL。
在設計工具的工具列上，選取 [儲存]  。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>新增 SAP 動作來產生架構

1. 在 [Logic Apps 設計工具] 的 [觸發程式] 底下，選取 [ **新增步驟**]。

   ![將新步驟新增至邏輯應用程式](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [ **動作** ] 清單中選取 [ **產生架構**]。
  
   ![將 [產生架構] 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，您可以選取 [ **企業** ] 索引標籤，然後選取 SAP 動作。

   ![從 [企業] 索引標籤選取 SAP 傳送動作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果您的連線已存在，請繼續下一個步驟，讓您可以設定 SAP 動作。 但是，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連線的名稱。

   1. 在 [ **資料閘道** ] 區段的 [ **訂** 用帳戶] 下，先針對您在資料閘道安裝的 Azure 入口網站中建立的資料閘道資源選取 Azure 訂用帳戶。 
   
   1. 在 [連線 **閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [ **登入類型** ] 屬性，根據屬性設定為 [ **應用程式伺服器** ] 或 [ **群組**]，執行下列步驟：
   
      * 針對 **應用程式伺服器**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 針對 **群組**，需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強型別是用來對架構執行 XML 驗證，以檢查是否有不正確值。 此行為可協助您及早偵測問題。 **Safe 輸入** 選項可提供回溯相容性，而且只會檢查字串長度。 深入瞭解 [Safe 輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 設定並測試您的連接，以確保連線正常運作。

1. 針對您要產生結構描述的成品提供其路徑。

   您可以從檔案選擇器選取 SAP 動作：

   ![選取 SAP 動作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，您也可以手動輸入動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要針對多個成品產生結構描述，請提供每個成品的 SAP 動作詳細資料，例如：

   ![選取 [新增項目]](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![顯示兩個項目](media/logic-apps-using-sap-connector/schema-generator-example.png)

   如需有關 SAP 動作的詳細資訊，請參閱 [IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計工具的工具列上，選取 [ **執行** ] 以觸發邏輯應用程式的執行。

1. 開啟 [執行]，並檢查 [ **產生架構** ] 動作的輸出。

   輸出中會顯示針對所指定訊息清單產生的結構描述。

### <a name="upload-schemas-to-an-integration-account"></a>將架構上傳至整合帳戶

(選擇性) 您可以在存放庫 (例如，Blob、儲存體或企業整合帳戶) 下載或儲存產生的結構描述。 企業整合帳戶搭配其他 XML 動作可提供一流的體驗，因此這個範例會說明如何使用 Azure Resource Manager 連接器，將相同邏輯應用程式的結構描述上傳到企業整合帳戶。

1. 在 [Logic Apps 設計工具] 的 [觸發程式] 底下，選取 [ **新增步驟**]。

1. 在搜尋方塊中，輸入 `Resource Manager` 作為篩選條件。 選取 [ **建立或更新資源**]。

   ![選取 Azure Resource Manager 動作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 輸入動作的詳細資料，包括您的 Azure 訂用帳戶、Azure 資源群組和整合帳戶。 若要將 SAP 權杖新增至欄位，請在這些欄位的方塊內按一下，然後從顯示的動態內容清單中選取。

   1. 開啟 [ **加入新的參數** ] 清單，然後選取 [ **位置** ] 和 [ **屬性** ] 欄位。

   1. 提供這些新欄位的詳細資料，如本範例所示。

      ![輸入 Azure Resource Manager 動作的詳細資料](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的 **產生結構描述** 動作會以集合的形式產生結構描述，讓設計工具可以在動作中自動新增 **For each** 迴圈。 以下舉例說明此動作的顯示方式：

   ![Azure Resource Manager 動作與 "for each" 迴圈](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 結構描述會使用 base64 編碼格式。 若要將結構描述上傳到企業整合帳戶，必須先使用 `base64ToString()` 函式將結構描述解碼。 以下舉例說明 `"properties"` 元素的程式碼：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計工具的工具列上，選取 [ **執行** ] 以手動觸發邏輯應用程式。

1. 成功執行之後，請移至整合帳戶，然後檢查產生的架構是否存在。

## <a name="enable-secure-network-communications"></a>啟用安全網路通訊

開始之前，請確定您符合先前所列的 [必要條件](#prerequisites)，這只適用于您在多租使用者 Azure 中使用資料閘道和邏輯應用程式執行時：

* 確定內部部署資料閘道安裝在與 SAP 系統位於相同網路的電腦上。

* 針對 SSO，資料閘道會以對應至 SAP 使用者的使用者的形式執行。

* 提供額外安全性功能的 SNC 程式庫會安裝在與資料閘道相同的電腦上。 其中一些範例包括 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。

   若要針對您的 SAP 系統要求或從 SAP 系統啟用 SNC，請選取 SAP 連線中的 [ **使用 SNC** ] 核取方塊，並提供下列屬性：

   ![在連接中設定 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 屬性 | 描述 |
   |----------| ------------|
   | **SNC 程式庫路徑** | SNC 程式庫名稱或相對於 NCo 安裝位置或絕對路徑的路徑。 例如 `sapsnc.dll` 或 `.\security\sapsnc.dll` 或 `c:\security\sapsnc.dll` 。 |
   | **SNC SSO** | 當您透過 SNC 連接時，SNC 身分識別通常會用來驗證呼叫者。 另一個選項是覆寫，讓使用者和密碼資訊可以用來驗證呼叫者，但該行仍會加密。 |
   | **SNC 我的姓名** | 在大部分的情況下，可以省略這個屬性。 已安裝的 SNC 解決方案通常會知道自己的 SNC 名稱。 只有針對支援多個身分識別的解決方案，您可能需要指定要用於此特定目的地或伺服器的身分識別。 |
   | **SNC 夥伴名稱** | 後端 SNC 的名稱。 |
   | **SNC 防護品質** | 用於 SNC 此特定目的地或伺服器之通訊的服務品質。 預設值是由後端系統所定義。 最大值是由用於 SNC 的安全性產品所定義。 |
   |||

   > [!NOTE]
   > 請勿在您擁有資料閘道和 SNC 程式庫的電腦上 SNC_LIB 和 SNC_LIB_64 設定環境變數。 如果設定，則會優先于透過連接器傳遞的 SNC 程式庫值。

## <a name="safe-typing"></a>安全類型

依預設，當您建立 SAP 連接時，會使用強式類型來檢查是否有不正確值，方法是對架構執行 XML 驗證。 此行為可協助您及早偵測問題。 **Safe 輸入** 選項可提供回溯相容性，而且只會檢查字串長度。 如果您選擇 [ **安全類型**]，則 SAP 中的 [dat 類型] 和 [TIMS] 類型會視為字串，而不是它們的 XML 對等專案， `xs:date` 以及 `xs:time` `xmlns:xs="http://www.w3.org/2001/XMLSchema"` 。 安全類型會影響所有架構產生的行為、「已傳送」承載的傳送訊息和「已接收」的回應，以及觸發程式。 

使用強式型別時 (不會) 啟用 **安全** 類型，架構會將 DAT 和 TIMS 類型對應到更簡單的 XML 類型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

當您使用強型別傳送訊息時，DAT 和 TIMS 回應會符合相符的 XML 類型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

啟用 **安全類型** 時，架構只會將 DAT 和 TIMS 類型對應到長度限制的 XML 字串欄位，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

當訊息在啟用 **安全輸入** 時傳送時，DAT 和 TIMS 回應會如下列範例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>進階案例

### <a name="change-language-headers"></a>變更語言標頭

當您從 Logic Apps 連接到 SAP 時，連線的預設語言為英文。 您可以使用[標準 HTTP 標頭 `Accept-Language` ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4)與輸入要求來設定連線的語言。

> [!TIP]
> 大部分的網頁瀏覽器都會 `Accept-Language` 根據使用者的設定來新增標頭。 當您在 Logic Apps 設計工具中建立新的 SAP 連接時，網頁瀏覽器會套用此標頭。 如果您不想要在網頁瀏覽器的慣用語言中建立 SAP 連線，請將網頁瀏覽器的設定更新為使用您慣用的語言，或使用 Azure Resource Manager 而非 Logic Apps 設計工具來建立您的 SAP 連接。 

例如，您可以 `Accept-Language` 使用 **HTTP 要求** 觸發程式，將標頭的要求傳送至邏輯應用程式。 邏輯應用程式中的所有動作都會收到標頭。 然後，SAP 會在其系統訊息中使用指定的語言，例如 BAPI 錯誤訊息。

邏輯應用程式的 SAP 連接參數沒有 language 屬性。 因此，如果您使用 `Accept-Language` 標頭，您可能會收到下列錯誤： **請檢查您的帳戶資訊和/或許可權，然後再試一次。** 在此情況下，請改為檢查 SAP 元件的錯誤記錄檔。 此錯誤實際上發生在使用標頭的 SAP 元件中，因此您可能會收到下列其中一個錯誤訊息：

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>明確確認交易

當您從 Logic Apps 將交易傳送至 SAP 時，此交換會以 SAP 檔、 [交易式 RFC 伺服器程式](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)中所述的兩個步驟進行。 [ **傳送至 SAP** ] 動作預設會處理函式傳送的步驟，以及在單一呼叫中進行交易確認的步驟。 SAP 連接器可讓您選擇分離這些步驟。 您可以傳送 IDoc，而不是自動確認交易，您可以使用明確的 [ **確認交易識別碼** ] 動作。

當您不想要在 SAP 中複製交易時（例如，因為網路問題所造成的失敗），這項功能可讓交易識別碼的分離確認相當實用。 藉由單獨確認交易識別碼，交易只會在您的 SAP 系統中完成一次。

以下是顯示此模式的範例：

1. 建立空白的邏輯應用程式，並新增 HTTP 觸發程式。

1. 從 SAP 連接器新增 **傳送 IDOC** 動作。 提供您傳送至 SAP 系統的 IDoc 詳細資料。

1. 若要在個別步驟中明確確認交易識別碼，請在 [ **確認 TID** ] 欄位中選取 [ **否**]。 針對 [選用 **交易識別碼 GUID** ] 欄位，您可以手動指定值，或讓連接器自動產生，並在傳送 IDOC 動作的回應中傳回此 GUID。

   ![傳送 IDOC 動作屬性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要明確確認交易識別碼，請新增 **確認交易識別碼** 動作，並務必 [避免將重複的 IDOC 傳送至 SAP](#avoid-sending-duplicate-idocs)。 在 [ **交易識別碼** ] 方塊內按一下，以顯示動態內容清單。 從該清單中，選取從 **傳送 IDOC** 動作傳回的 **交易識別碼** 值。

   ![確認交易識別碼動作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   執行此步驟之後，目前的交易會在 SAP 連接器端和 SAP 系統端的兩端標示為完成。

#### <a name="avoid-sending-duplicate-idocs"></a>避免傳送重複的 Idoc

如果您在邏輯應用程式中從邏輯應用程式傳送重複的 Idoc 時遇到問題，請遵循下列步驟來建立字串變數，以作為您的 IDoc 交易識別碼。 當發生暫時性中斷、網路問題或遺失通知等問題時，建立此交易識別碼可協助防止重複的網路傳輸。

> [!NOTE]
> SAP 系統預設會在指定的時間之後忘記交易識別碼，或預設為24小時。 如此一來，如果識別碼或 GUID 未知，SAP 就永遠無法確認交易識別碼。
> 如果交易識別碼確認失敗，則此失敗表示 SAP 系統的通訊失敗，SAP 才能夠確認確認。

1. 在 Logic Apps 設計工具中，將動作 **Initialize 變數** 新增至您的邏輯應用程式。 

1. 在動作 **Initialize 變數** 的編輯器中，設定下列設定。 然後，儲存您的變更。

    1. 針對 [ **名稱**]，輸入變數的名稱。 例如： `IDOCtransferID` 。

    1. 針對 [ **類型**]，選取 [ **字串** ] 作為變數類型。

    1. 針對 [ **值**]，選取 [文字方塊] **輸入初始值** 以開啟 [動態內容] 功能表。 選取 [ **運算式** ] 索引標籤。在函數清單中，輸入函數 `guid()` 。 然後，選取 **[確定]** 儲存您的變更。 **值** 欄位現在會設定為函式，此函式會 `guid()` 產生 GUID。

1. 在 [ **初始化變數** ] 動作之後，新增動作 **傳送 IDOC**。

1. 在動作 **傳送 IDOC** 的編輯器中，設定下列設定。 然後，儲存您的變更。

    1. 在 [ **IDOC 類型** ] 中，選取您的訊息類型，然後針對 [ **輸入 IDOC 訊息**] 指定您的訊息。

    1. 針對 **sap 發行版本**，請選取您 SAP 設定的值。

    1. 針對 [ **記錄類型版本**]，選取您的 SAP 設定的值。

    1. 針對 [ **確認 TID**]，請選取 [ **否**]。

    1. 選取 [**加入新的參數清單**  >  **交易識別碼 GUID**]。 選取文字方塊以開啟 [動態內容] 功能表。 在 [ **變數** ] 索引標籤下，選取您所建立的變數名稱。 例如： `IDOCtransferID` 。

1. 在動作 **傳送 IDOC** 的標題列上，選取 **...**  > **設定**。 針對 **重試原則**，建議您選取 [ **預設** &gt; **完成**]。 不過，您可以改為針對您的特定需求設定自訂原則。 若為自訂原則，建議您至少設定一次重試，以克服暫時性網路中斷。

1. 在動作 **傳送 IDOC** 之後，新增動作 [ **確認交易識別碼**]。

1. 在 [動作 **確認交易識別碼**] 的編輯器中，設定下列設定。 然後，儲存您的變更。

    1. 在 [ **交易識別碼**] 中，再次輸入變數的名稱。 例如： `IDOCtransferID` 。

1. （選擇性）在測試環境中驗證重復資料刪除。 使用您在上一個步驟中使用的相同 **交易識別碼** GUID，重複 **傳送 IDOC** 動作。 當您傳送兩次相同的 IDoc 時，您可以驗證 SAP 是否能夠識別 tRFC 呼叫的重複，並將兩個呼叫解析成單一的輸入 IDoc 訊息。

## <a name="known-issues-and-limitations"></a>已知的問題及限制

以下是受控 (非 ISE) SAP 連接器目前已知的問題和限制：

* SAP 觸發程序不支援資料閘道叢集。 在某些容錯移轉案例中，與 SAP 系統通訊的資料閘道節點可能與使用中節點不同，這會導致非預期的行為。 針對傳送案例，支援資料閘道叢集。

* SAP 連接器目前不支援 SAP 路由器字串。 內部部署資料閘道必須和所要連線的 SAP 系統存在於相同 LAN 內。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，如連接器的 Swagger 檔案所述的觸發程式、動作和限制，請參閱 [連接器的參考頁面](/connectors/sap/)。 針對下列動作提供 Logic Apps 的其他檔：

* [呼叫 BAPI](#call-bapi-action)

* [傳送 IDOC](#send-idoc-action)

> [!NOTE]
> 若為 [整合服務環境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的邏輯應用程式 (ISE) ，此連接器的 ISE 標記版本會改用 [ise 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 。

### <a name="call-bapi-action"></a>呼叫 BAPI 動作

[呼叫 BAPI (`CallBapi`) ](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview))動作會在您的 SAP 伺服器上呼叫 BAPI 方法。 

您必須使用下列參數搭配您的呼叫： 

* **商務物件** (`businessObject`) ，這是可搜尋的下拉式功能表。

* **方法** (`method`) ，在您選取 **商務物件** 之後，會填入可用的方法。 可用的方法會根據選取的 **商務物件** 而有所不同。

* **輸入 BAPI 參數** (`body`) ，您可以在其中呼叫包含呼叫之 BAPI 方法輸入參數值的 XML 檔，或包含 BAPI 參數的儲存體 blob URI。

如需如何使用 [呼叫 BAPI] 動作的詳細範例，請參閱 [BAPI 要求的 XML 範例](#xml-samples-for-bapi-requests)。

> [!TIP]
> 如果您要使用 Logic Apps 設計工具來編輯 BAPI 要求，您可以使用下列搜尋函式： 
> 
> * 在設計工具中選取物件，以查看可用方法的下拉式清單。
> * 使用 BAPI API 呼叫所提供的可搜尋清單，依關鍵字篩選商業物件類型。

### <a name="send-idoc-action"></a>傳送 IDoc 動作

[傳送 IDoc (`SendIDoc`) ](https://docs.microsoft.com/connectors/sap/#send-idoc-(preview))動作會將 IDoc 訊息傳送至您的 SAP 伺服器。

您必須使用下列參數搭配您的呼叫： 

* **具有選用延伸** () 的 IDOC 類型 `idocType` ，這是可搜尋的下拉式功能表。

    * 選擇性參數 **SAP 發行版本** (`releaseVersion`) 在您選取 IDoc 類型後填入值，並視選取的 IDoc 類型而定。

* **輸入 IDOC 訊息** (`body`) ，您可以在其中呼叫包含 IDOC 承載的 XML 檔，或包含 IDOC XML 檔之儲存體 blob 的 URI。 本檔必須根據 WE60 IDoc 檔或針對符合的 SAP IDoc 動作 URI 所產生的架構，遵守 SAP IDOC XML 架構。

如需如何使用傳送 IDoc 動作的詳細範例，請參閱將 [IDoc 訊息傳送至 SAP 伺服器的逐步](#send-idoc-messages-to-sap-server)解說。

如需如何使用選擇性參數 **確認 TID** (`confirmTid`) ，請參閱 [明確確認交易的逐步](#confirm-transaction-explicitly)解說。

## <a name="next-steps"></a>後續步驟

* 從 Azure Logic Apps[連接至內部部署系統](../logic-apps/logic-apps-gateway-connection.md)。

* 瞭解如何使用 [企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)驗證、轉換和使用其他訊息作業。

* 瞭解其他 [Logic Apps 連接器](../connectors/apis-list.md)。
