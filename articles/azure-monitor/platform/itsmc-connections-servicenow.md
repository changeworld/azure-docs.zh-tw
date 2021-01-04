---
title: 使用 IT 服務管理連接器連接 ServiceNow
description: 本文提供有關如何使用 IT 服務管理連接器 (ITSMC) 在 Azure 監視器中集中監視和管理 ITSM 工作專案的資訊。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729624"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>使用 IT 服務管理連接器連接 ServiceNow

本文提供有關如何設定 ServiceNow 實例與 IT 服務管理連接器之間的連線 (ITSMC) Log Analytics 中的連線，以集中管理您的工作專案。

下列各節提供有關如何將 ServiceNow 產品連線到 Azure 中的 ITSMC 之詳細資料。

## <a name="prerequisites"></a>Prerequisites
請確保已符合下列必要條件︰
- 已安裝 ITSMC。 詳細資訊：[新增 IT 服務管理連接器解決方案](./itsmc-definition.md#add-it-service-management-connector)。
- ServiceNow 支援的版本：奧蘭多、紐約、馬德里、倫敦、Kingston、雅加達、伊斯坦布爾、赫爾辛基、Geneva。
- 現在，從 Azure 監視器傳送的警示可在 ServiceNow 中建立下列其中一個元素：事件、事件或警示。
> [!NOTE]
> ITSMC 僅支援 Service Now 提供的官方 SaaS 供應項目。 目前不支援 Service Now 的私人部署。 

**ServiceNow 管理員必須在 ServiceNow 執行個體中執行下列動作**：
- 產生 ServiceNow 產品的用戶端識別碼和用戶端密碼。 如需如何產生用戶端識別碼和祕密的相關資訊，請視需要參閱下列資訊：

    - [針對奧蘭多設定 OAuth](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 New York 設定 OAuth](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Madrid 設定 OAuth](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 London 設定 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Kingston 設定 OAuth](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Jakarta 設定 OAuth](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Istanbul 設定 OAuth](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Helsinki 設定 OAuth](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [針對 Geneva 設定 OAuth](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> 在定義「設定 OAuth」時，我們建議：
>
> 1) **將重新整理權杖有效期更新為 90 天 (7776000秒)：** 在第 2 階段中 [設定 OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) 時：[建立讓用戶端存取執行個體的端點](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) 在定義端點之後，於 ServiceNow 刀鋒視窗中搜尋系統 OAuth，而非選取 [應用程式登錄]。 挑選已定義的 OAuth 名稱，並將 [重新整理權杖有效期] 的欄位更新為 7776000 (90 天的秒數)。
> 結束時，按一下 [更新]。
> 2) **我們建議您建立內部程序，以確保連線保持運作：** 根據重新整理權杖有效期重新整理權杖。 請務必在重新整理權杖預期的到期時間之前執行下列作業 (我們建議在重新整理權杖有效期到期的前幾天這樣做)：
>
>     1. [完成 ITSM 連接器組態的手動同步程序](./itsmc-resync-servicenow.md)
>     2. 撤銷舊的重新整理權杖，因為基於安全考慮，不建議保留舊的金鑰。 在 ServiceNow 刀鋒視窗中搜尋系統 OAuth，而非選取 [管理權杖]。 根據 OAuth 名稱和到期日，從清單中挑選舊的權杖。
> ![SNOW 系統 OAuth 定義](media/itsmc-connections/snow-system-oauth.png)
>     3. 按一下 [撤銷存取權]，而非按一下 [撤銷]。

- 安裝適用於 Microsoft Log Analytics 整合的使用者應用程式 (ServiceNow 應用程式)。 [深入了解](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )。
> [!NOTE]
> ITSMC 僅支援從 ServiceNow 存放區下載的 Microsoft Log Analytics 整合官方使用者應用程式。 ITSMC 不支援在 ServiceNow 端或不屬於官方 ServiceNow 解決方案的應用程式中內嵌任何程式碼。 
- 為安裝的使用者應用程式建立整合使用者角色。 關於如何建立整合使用者角色的資訊在[這裡](#create-integration-user-role-in-servicenow-app)。

## <a name="connection-procedure"></a>**連線程序**
請使用下列程序來建立 ServiceNow 連線：


1. 在 Azure 入口網站中，移至 [所有資源]，然後尋找 **ServiceDesk(YourWorkspaceName)**

2.  在 [工作區資料來源] 下方，按一下 [ITSM 連線]。
    ![新增連線](media/itsmc-connections/add-new-itsm-connection.png)

3. 在右窗格頂端按一下 [新增]。

4. 提供下表中所述的資訊，然後按一下 [確定] 來建立連線。


> [!NOTE]
> 這些全部都是必要參數。

| **欄位** | **說明** |
| --- | --- |
| **連接名稱**   | 輸入您想要與 ITSMC 連線之 ServiceNow 執行個體的名稱。  稍後當您在 Log Analytics 中設定這個 ITSM 的工作項目/檢視詳細的記錄分析時，會使用這個名稱。 |
| **夥伴類型**   | 選取 **ServiceNow**。 |
| **使用者名稱**   | 輸入您在 ServiceNow 應用程式中建立的整合使用者名稱，以支援 ITSMC 的連線。 詳細資訊：[建立 ServiceNow 應用程式使用者角色](#create-integration-user-role-in-servicenow-app)。|
| **密碼**   | 將與此使用者名稱與相關聯的密碼輸入。 **注意**：使用者名稱和密碼僅用來產生驗證權杖，並不會儲存在 ITSMC 服務內。  |
| **伺服器 URL**   | 輸入您想要連線到 ITSMC 之 ServiceNow 執行個體的 URL。 此 URL 應指向支援的 SaaS 版本，其尾碼為 ".servicenow.com"。|
| **用戶端識別碼**   | 將您想要用於先前產生之 OAuth2 驗證的用戶端識別碼輸入。  如需產生用戶端識別碼和祕密的資訊： [OAuth 設定](https://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
| **用戶端祕密**   | 輸入針對此識別碼產生的用戶端祕密。   |
| **資料同步範圍**   | 選取您想要透過 ITSMC 同步處理到 Azure Log Analytics 的 ServiceNow 工作項目。  系統會將這些值匯入記錄分析。   **選項︰** 事件和變更要求。|
| **同步資料** | 輸入您想要起算資料的過去天數。 **上限**：120 天。 |
| **在 ITSM 解決方案中建立新的設定項目** | 如果您想要在 ITSM 產品中建立設定項目，請選取此選項。 選取時，ITSMC 會在支援的 ITSM 系統中建立受影響的 CI 作為設定項目 (如果 CI 不存在)。 **預設**︰停用。 |

![ServiceNow 連線](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**順利連線並同步處理時**︰

- 從 ServiceNow 執行個體選取的工作項目會匯入到 Azure **Log Analytics**。 您可以在 [IT 服務管理連接器] 圖格上檢視這些工作項目的摘要。

- 您可以在這個 ServiceNow 執行個體中建立來自 Log Analytics 警示、記錄檔記錄或 Azure 警示的事件。

> [!NOTE]
> 在 ServiceNow 中，每小時的要求都有速率限制。 若要設定此限制，請在 ServiceNow 實例中定義「輸入 REST API 速率限制」來設定限制。

## <a name="create-integration-user-role-in-servicenow-app"></a>在 ServiceNow 應用程式中建立整合使用者角色

請使用下列程序：

1. 請瀏覽 [ServiceNow 存放區](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)，並將 **ServiceNow 和 Microsoft OMS 整合的使用者應用程式** 安裝到 ServiceNow 執行個體。
   
   >[!NOTE]
   >因屬於 Microsoft Operations Management Suite (OMS) 轉換為 Azure 監視器的一環，OMS 現在稱為 Log Analytics。     
2. 安裝完成後，請瀏覽左側導覽列中的 [ServiceNow 執行個體]、[搜尋] 並選取 [Microsoft OMS 整合器]。  
3. 按一下 [安裝檢查清單]。

   如果尚未建立使用者角色，則狀態會顯示為 [未完成]。

4. 在 [建立整合使用者] 旁邊的文字方塊中，輸入可連線到 Azure 中 ITSMC 之使用者的使用者名稱。
5. 輸入這個使用者的密碼，然後按一下 [確定]。  

> [!NOTE]
> 您可以使用這些認證，在 Azure 中進行 ServiceNow 連線。

會使用預設的角色指派來顯示新建立的使用者。

**預設角色**︰
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

成功建立使用者之後，[檢查安裝檢查清單] 狀態會改變為 [已完成]，並列出針對應用程式所建立的使用者角色詳細資料。

> [!NOTE]
> ITSM 連接器可以將事件傳送給 ServiceNow，完全不需要在您的 ServiceNow 執行個體上安裝其他任何模組。 如果您在 ServiceNow 執行個體中使用 EventManagement 模組，而且想要使用連接器在 ServiceNow 中建立事件或警示，請將下列角色加入至整合使用者：
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>後續步驟

* [ITSM 連接器總覽](itsmc-overview.md)
* [建立 Azure 警示的 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)