---
title: IT 服務管理連接器-Azure 監視器中的安全匯出
description: 本文提供有關如何將 ITSM 產品/服務與 Azure 監視器中的「安全匯出」進行連線，以集中監視和管理 ITSM 工作專案的相關資訊。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568637"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>使用安全匯出將 Azure 連接至 ITSM 工具

本文提供有關如何使用「安全匯出」來設定 ITSM 產品/服務之間連接的資訊。

安全匯出是和更新版本的 [ITSMC](./itsmc-overview.md) (IT 服務管理) 。 這兩個版本可讓您在 Azure 監視器警示引發時，在 ITSM 工具中建立工作專案。 這些功能包括計量、記錄和活動記錄警示。

[ITSMC](./itsmc-overview.md) 會使用使用者和密碼認證，而安全匯出具有更強的驗證，因為它會使用 Azure Active Directory (Azure AD) 。 Azure Active Directory (Azure AD) 是 Microsoft 的雲端式身分識別和存取管理服務。 它可協助使用者登入並存取內部或外部資源。 搭配使用 Azure AD 與 ITSM 有助於使用傳送至外部系統的 Azure AD 應用程式識別碼) ，來識別 (的 Azure 警示。

> [!NOTE]
> 使用安全匯出的 Connect Azure to ITSM tools 現供預覽

## <a name="secure-export-architecture"></a>安全匯出架構

安全匯出架構引進了下列新功能：

* **新的動作群組** -警示會使用安全 webhook 動作群組傳送至 ITSM 工具， (而不是在 ITSMC) 中使用 ITSM 動作群組。
* **Azure AD 驗證** -驗證會使用 Azure AD 而非使用者/密碼認證進行。

## <a name="secure-export-data-flow"></a>安全匯出資料流程

安全的匯出資料流程步驟如下：

1) 已設定為使用安全匯出的警示會在 Azure 監視器中引發
2) 警示承載會以安全 webhook 動作傳送至 ITSM 工具。
3) 如果警示已獲授權進入 ITSM 工具，ITSM 應用程式會檢查 Azure AD。
4) 如果警示已獲授權，應用程式：
    1) 在 ITSM 工具中建立工作專案 (例如事件) 。
    2) 將 (CI) 識別碼的設定專案系結至客戶管理資料庫 (CMDB) 。
![Itsm 圖](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>與 BMC Helix 連接

安全匯出支援 BMC Helix。 整合的一些優點如下：

* **更好的驗證** – Azure AD 可提供更安全的驗證，而不會發生 ITSMC 中經常發生的超時狀況。
* **ITSM 工具中已解決的警示** –計量警示會執行「已引發」和「已解決」狀態。 符合條件時，警示狀態會是「已引發」。 當條件不再符合時，警示狀態會是「已解決」。 在 ITSMC 中，無法自動解決警示。 使用安全匯出時，已解決狀態會流向 ITSM 工具，因此會自動更新。
* **[一般架構允許](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** -在 ITSMC 中，警示承載的架構會根據警示類型而有所不同。 在安全匯出中，我們是所有警示類型的通用架構。 這個新的通用架構包含所有警示類型的 CI。 如此一來，所有的警示類型都能夠將其 CI 系結至 CMDB。

開始使用 ITSM 連接器，請執行下列步驟：

1. 向 Azure Active Directory 註冊您的應用程式。
2. 建立安全 webhook 動作群組。
3. 設定您的夥伴環境。

## <a name="register-with-azure-active-directory"></a>向 Azure Active Directory 註冊

請遵循下列步驟，向 Azure Active Directory 註冊 Azure AD 應用程式

1) [Azure AD 建立](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) 在 Azure Active Directory 選取 [公開應用程式]
3) 選取 [應用程式識別碼 URI] [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)上的 [設定]
4) 按一下 [儲存]。

## <a name="create-a-secure-webhook-action-group"></a>建立安全 Webhook 動作群組

註冊您的 Azure AD 之後，您就可以使用動作群組中的安全 Webhook 動作，根據 Azure 警示，在 ITSM 工具中建立工作專案 (s) 。
動作群組提供以模組化且可重複使用的方式來針對 Azure 警示觸發動作。 您可以在 Azure 入口網站中，搭配計量警示、活動記錄警示及 Azure Log Analytics 警示使用動作群組。
若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。
請使用下列程序：

在 BMC Helix 環境中：

1. 登入 Integration Studio。
2. 搜尋從 Azure 警示流程建立事件。
3. 複製 WebHook URL。
![BMC URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

若要將 webhook 新增至動作，請依照安全 webhook 的指示進行：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，搜尋並選取 [監視器]。 [監視器] 頁面會將您的所有監視設定與資料合併在一個檢視中。
2. 選取 [警示]，然後選取 [管理動作]。
3. 選取 [新增動作群組]，並填寫各欄位。
4. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。
5. 選取 **安全的 Webhook**
6. 選取 [編輯詳細資料]。 下圖顯示範例安全 webhook 動作：
    1. 為您註冊的 Azure Active Directory 選取正確的物件識別碼
    2. 在 [URI] 欄位中，貼上您從 [BMC Helix 環境] 複製的 WebHook URL
    3. 將 **一般警示架構** 設定為 **[是]**。 
7. 下圖顯示範例安全 webhook 動作設定： ![ 安全 webhook](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>設定夥伴環境

### <a name="connect-bmc-helix-to-azure-monitor"></a>將 BMC Helix 連接到 Azure 監視器

下一節提供有關如何在 Azure 中連線到您的 BMC Helix 產品和安全匯出的詳細資料。

### <a name="prerequisites"></a>Prerequisites

請確保已符合下列必要條件︰

* Azure AD 已註冊。
* 您支援的 BMC 版本 Helix 多雲端服務管理：20.02 版或更新版本

設定 BMC Helix 連接：

1) [針對20.2 版的 Azure 監視器啟用預建整合](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) 在 BMC Helix 中設定連線的一部分，請移至您的整合 BMC 實例，並遵循指示進行：

1. 選取 **目錄**
2. 選取 **Azure 警示**
3. 選取 **連接器**
4. 選取 **設定**
5. 選取 [ **新增連接** 設定]
6. 填入設定區段的資訊。
    1. **名稱** -自行組成
    2. **授權類型** -無
    3. **描述**-組成您自己的
    4. **網站**-雲端
    5. **實例數目** -2 –預設值
    6. 依預設選取並啟用**使用方式**
    7. Azure 租使用者識別碼，Azure 應用程式識別碼是取自「建立 Azure Active Directory」步驟中所定義的應用程式。
![BMC 設定](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>後續步驟

* [建立 Azure 警示的 ITSM 工作項目](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
