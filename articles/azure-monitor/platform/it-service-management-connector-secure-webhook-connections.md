---
title: IT 服務管理連接器-Azure 監視器中的安全匯出
description: 本文說明如何在 Azure 監視器中將 ITSM 產品/服務與安全匯出連線，以集中監視和管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 64d45861f37e2015b747a4db0feb2d32e68fe893
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427328"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>使用安全匯出將 Azure 連接至 ITSM 工具

本文說明如何使用「安全匯出」來設定您的 IT 服務管理 (ITSM) 產品或服務之間的連線。

安全匯出是 [IT 服務管理連接器 (ITSMC) ](./itsmc-overview.md)的更新版本。 這兩個版本都可讓您在 Azure 監視器傳送警示時，在 ITSM 工具中建立工作專案。 這些功能包括計量、記錄和活動記錄警示。

ITSMC 會使用使用者名稱和密碼認證。 安全匯出有更強的驗證，因為它使用 Azure Active Directory (Azure AD) 。 Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 它可協助使用者登入並存取內部或外部資源。 搭配使用 Azure AD 與 ITSM 有助於透過傳送至外部系統的 Azure AD 應用程式識別碼) ，來識別 (的 Azure 警示。

> [!NOTE]
> 使用「安全匯出」將 Azure 連線到 ITSM 工具的功能目前為預覽狀態。

## <a name="secure-export-architecture"></a>安全匯出架構

安全匯出架構引進了下列新功能：

* **新的動作群組**：透過安全 Webhook 動作群組將警示傳送至 ITSM 工具，而不是 ITSMC 所使用的 ITSM 動作群組。
* **Azure AD authentication**：驗證是透過 Azure AD 進行，而不是使用者名稱/密碼認證。

## <a name="secure-export-data-flow"></a>安全匯出資料流程

安全匯出資料流程的步驟如下：

1. Azure 監視器傳送已設定為使用安全匯出的警示。
2. 警示內容是由安全的 Webhook 動作傳送至 ITSM 工具。
3. 如果警示已獲授權進入 ITSM 工具，ITSM 應用程式會檢查 Azure AD。
4. 如果警示已獲得授權，則應用程式：
   
   1. 建立工作專案 (例如，ITSM 工具中的事件) 。
   2. 將設定專案的識別碼 (CI) 系結至客戶管理資料庫 (CMDB) 。

![此圖顯示 ITSM 工具如何與 Azure A D、Azure 警示和動作群組進行通訊。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>安全匯出的優點

整合的主要優點如下：

* **更好的驗證**： Azure AD 提供更安全的驗證，而不會發生 ITSMC 中經常發生的超時狀況。
* **ITSM 工具中已解決的警示**：計量警示會執行「已引發」和「已解決」狀態。 符合條件時，警示狀態會是「已引發」。 當條件不再符合時，警示狀態會是「已解決」。 在 ITSMC 中，無法自動解決警示。 使用「安全匯出」時，已解決狀態會流向 ITSM 工具，因此會自動更新。
* **[常見的警示架構](./alerts-common-schema.md)**：在 ITSMC 中，警示承載的架構會根據警示類型而有所不同。 在安全匯出中，所有警示類型都有一個通用的架構。 此通用架構包含所有警示類型的 CI。 所有警示類型都能夠將其 CI 系結至 CMDB。

使用下列步驟開始使用 ITSM 連接器工具：

1. 向 Azure AD 註冊應用程式.
2. 建立安全 Webhook 動作群組。
3. 設定您的夥伴環境。 

安全匯出支援連接與下列 ITSM 工具：
* [BMC Helix](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>向 Azure Active Directory 註冊

請遵循下列步驟，向 Azure AD 註冊應用程式：

1. 遵循 [使用 Microsoft 身分識別平臺註冊應用程式](../../active-directory/develop/quickstart-register-app.md)中的步驟。
2. 在 Azure AD 中，選取 [ **公開應用程式**]。
3. 選取 [**應用程式識別碼 URI**的**設定**]。

   [![設定我的應用程式 U R I D 的選項螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. 選取 [儲存]。

## <a name="create-a-secure-webhook-action-group"></a>建立安全 Webhook 動作群組

在您的應用程式註冊 Azure AD 之後，您可以使用動作群組中的安全 Webhook 動作，根據 Azure 警示在 ITSM 工具中建立工作專案。

動作群組提供模組化且可重複使用的方式來觸發 Azure 警示的動作。 您可以在 Azure 入口網站中使用具有計量警示、活動記錄警示和 Azure Log Analytics 警示的動作群組。
若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](./action-groups.md)。

若要將 webhook 新增至動作，請依照下列指示進行安全 Webhook：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，搜尋並選取 [監視器]。 [監視器] 頁面會將您的所有監視設定與資料合併在一個檢視中。
2. 選取 [**警示**  >  **管理動作**]。
3. 選取 [新增動作群組]，並填寫各欄位。
4. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。
5. 選取 [ **安全 Webhook**]。
6. 選取下列詳細資料：
   1. 選取您所註冊 Azure Active Directory 實例的物件識別碼。
   2. 針對 URI，貼上您從 [ITSM 工具環境](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#configure-the-partner-environment)複製的 webhook URL。
   3. 將 **[啟用一般警示架構** ] 設定為 **[是]**。 

   下圖顯示範例安全 Webhook 動作的設定：

   ![顯示安全 Webhook 動作的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>設定 ITSM 工具環境

此設定包含兩個步驟：
1. 取得安全匯出定義的 URI。
2. 以 ITSM 工具的流程為依據的定義。

### <a name="connect-bmc-helix-to-azure-monitor"></a>將 BMC Helix 連接到 Azure 監視器

下列各節提供有關如何在 Azure 中連接您的 BMC Helix 產品和安全匯出的詳細資料。

### <a name="prerequisites"></a>先決條件

確定您符合下列必要條件：

* Azure AD 已註冊。
* 您有支援的 BMC Helix (19.08 版或更新版本) 的多雲端服務管理版本。

### <a name="configure-the-bmc-helix-connection"></a>設定 BMC Helix 連接

1. 在 BMC Helix 環境中使用下列程式，以便取得安全匯出的 URI：

   1. 登入 Integration Studio。
   2. 搜尋 **從 Azure 警示流程建立事件** 。
   3. 複製 webhook URL。
   
   ![Integration Studio 中 webhook U R L 的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. 依照下列版本的指示進行：
   * [針對20.02 版的 Azure 監視器啟用預建整合](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * [針對19.11 版的 Azure 監視器啟用預建整合](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. 在 BMC Helix 中設定連線的一部分，請移至您的整合 BMC 實例，並遵循下列指示：

   1. 選取 [ **目錄**]。
   2. 選取 **Azure 警示**。
   3. 選取 [ **連接器**]。
   4. 選取 **[** 設定]。
   5. 選取 [ **新增連接** 設定]。
   6. 填入設定區段的資訊：
      - **名稱**：組成您自己的。
      - **授權類型**： **無**
      - **描述**：組成您自己的。
      - **網站**： **雲端**
      - **實例數目**： **2**，預設值。
      - **檢查**：依預設選取以啟用使用方式。
      - Azure 租使用者識別碼和 Azure 應用程式識別碼取自您稍早定義的應用程式。

![顯示 BMC 設定的螢幕擷取畫面。](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>後續步驟

* [建立 Azure 警示的 ITSM 工作項目](./itsmc-overview.md)
