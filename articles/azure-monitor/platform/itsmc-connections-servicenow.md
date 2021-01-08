---
title: 使用 IT 服務管理連接器連接 ServiceNow
description: 瞭解如何在 Azure 監視器中將 ServiceNow 與 IT 服務管理連接器連線 (ITSMC) ，以集中監視和管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 7d1b4b3542f6914d413a5e29e57baa15e7a53346
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012779"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>使用 IT 服務管理連接器連接 ServiceNow

本文說明如何設定 ServiceNow 實例與 IT 服務管理連接器之間的連線 (ITSMC) 在 Log Analytics 中，讓您可以集中管理您的 IT 服務管理 (ITSM) 工作專案。

## <a name="prerequisites"></a>Prerequisites
確定您符合下列連接必要條件。

### <a name="itsmc-installation"></a>ITSMC 安裝

如需安裝 ITSMC 的相關資訊，請參閱 [新增 IT 服務管理連接器解決方案](./itsmc-definition.md#add-it-service-management-connector)。

> [!NOTE]
> ITSMC 僅支援來自 ServiceNow 的官方軟體即服務 (SaaS) 供應專案。 不支援 ServiceNow 的私人部署。

### <a name="oauth-setup"></a>OAuth 設定

ServiceNow 支援的版本包括奧蘭多、紐約、馬德里、倫敦、Kingston、雅加達、伊斯坦布爾、赫爾辛基和 Geneva。

ServiceNow 管理員必須為其 ServiceNow 實例產生用戶端識別碼和用戶端密碼。 如有需要，請參閱下列資訊：

- [針對奧蘭多設定 OAuth](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 New York 設定 OAuth](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Madrid 設定 OAuth](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 London 設定 OAuth](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Kingston 設定 OAuth](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Jakarta 設定 OAuth](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Istanbul 設定 OAuth](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Helsinki 設定 OAuth](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [針對 Geneva 設定 OAuth](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

在設定 OAuth 的過程中，我們建議您：

1. [建立用戶端用來存取實例的端點](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html)。

1. 更新重新整理權杖的存留期：

   1. 在 [ **ServiceNow** ] 窗格上，搜尋 **系統 OAuth**，然後選取 [ **應用程式** 登錄]。 
   1. 選取已定義的 OAuth 名稱，並將重新整理 **權杖生命週期** 變更為 **7776000 秒** (90 天) 。 
   1. 選取 [更新]。 

1. 建立內部程式以確保連接保持運作。 在重新整理權杖存留期的預期到期前幾天，請執行下列作業：

   1. [完成 ITSM 連接器設定的手動同步處理常式](./itsmc-resync-servicenow.md)。

   1. 撤銷舊的重新整理權杖。 基於安全性考慮，我們不建議保留舊的金鑰。 
   
      1. 在 [ **ServiceNow** ] 窗格上，搜尋 **系統 OAuth**，然後選取 [ **管理權杖**]。 
      
      1. 根據 OAuth 名稱和到期日，從清單中選取舊的權杖。

         ![顯示 OAuth 權杖清單的螢幕擷取畫面。](media/itsmc-connections/snow-system-oauth.png)
      1. 選取 **[撤銷存取**  >  **撤銷]**。

## <a name="install-the-user-app-and-create-the-user-role"></a>安裝使用者應用程式並建立使用者角色

使用下列程式，立即安裝服務的使用者應用程式，並為其建立整合使用者角色。 您將使用這些認證，在 Azure 中建立 ServiceNow 連線。

> [!NOTE]
> ITSMC 僅支援從 ServiceNow 存放區下載的 Microsoft Log Analytics 整合官方使用者應用程式。 ITSMC 不支援在 ServiceNow 端或任何不屬於官方 ServiceNow 解決方案的應用程式上內嵌任何程式碼。 

1. 造訪 servicenow [存放區](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) ，並在您的 servicenow 實例中安裝 **SERVICENOW 和 Microsoft OMS 整合的使用者應用程式** 。
   
   >[!NOTE]
   >從 Microsoft Operations Management Suite (OMS) 到 Azure 監視器的持續轉換過程中，OMS 現在稱為 Log Analytics。     
2. 安裝之後，請移至 ServiceNow 實例的左側導覽列，然後搜尋並選取 [ **MICROSOFT OMS** 整合程式]。  
3. 選取 **安裝檢查清單**。

   狀態會顯示為 [  **未完成** ]，因為尚未建立使用者角色。

4. 在 [ **建立整合使用者**] 旁的文字方塊中，輸入可以連線到 AZURE 中 ITSMC 的使用者名稱。
5. 輸入此使用者的密碼，然後選取 **[確定]**。  

新建立的使用者會顯示為已指派預設角色：

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

當您成功建立使用者之後， **檢查安裝檢查清單** 的狀態會移至 [ **已完成** ]，並列出為應用程式所建立之使用者角色的詳細資料。

> [!NOTE]
> ITSMC 可將事件傳送至 ServiceNow，而不需要在 ServiceNow 實例上安裝任何其他模組。 如果您是在 ServiceNow 實例中使用 EventManagement 模組，而且想要使用連接器在 ServiceNow 中建立事件或警示，請將下列角色新增至整合使用者：
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>建立連接
使用下列程式建立 ServiceNow 連線。

> [!NOTE]
> 從 Azure 監視器傳送的警示可在 ServiceNow 中建立下列其中一個元素：事件、事件或警示。 

1. 在 Azure 入口網站中，移至 [ **所有資源** ]，並尋找 **>servicedesk (YourWorkspaceName)**。

2. 在 [ **工作區資料來源**] 底下，選取 [ **ITSM 連接**]。

   ![顯示資料來源選取範圍的螢幕擷取畫面。](media/itsmc-connections/add-new-itsm-connection.png)

3. 選取右窗格頂端的 [ **新增**]。

4. 提供下表所述的資訊，然後選取 **[確定]**。

   | **欄位** | **說明** |
   | --- | --- |
   | **連接名稱**   | 輸入您想要與 ITSMC 連接的 ServiceNow 實例名稱。 您稍後會在 Log Analytics 中，于設定 ITSM 工作專案及查看詳細分析時使用此名稱。 |
   | **夥伴類型**   | 選取 **ServiceNow**。 |
   | **伺服器 Url**   | 輸入您想要連線至 ITSMC 之 ServiceNow 實例的 URL。 URL 應指向支援的 SaaS 版本，其 servicenow.com 為尾碼 *。*|
   | **使用者名稱**   | 輸入您在 ServiceNow 應用程式中建立的整合使用者名稱，以支援 ITSMC 的連接。|
   | **密碼**   | 輸入與此使用者名稱相關聯的密碼。 **注意**：使用者名稱和密碼僅用來產生驗證權杖。 它們不會儲存在 ITSMC 服務內的任何位置。  |
   | **用戶端識別碼**   | 輸入您想要用於 OAuth2 authentication 的用戶端識別碼，這是您稍早產生的識別碼。 如需產生用戶端識別碼和秘密的詳細資訊，請參閱 [設定 OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup)。 |
   | **用戶端祕密**   | 輸入為此識別碼產生的用戶端密碼。   |
   | **資料同步範圍 (（以天為單位）)** | 輸入您想要資料的過去天數。 限制為120天。 |
   | **要同步處理的工作專案**   | 透過 ITSMC 選取您想要同步至 Azure Log Analytics 的 ServiceNow 工作專案。 選取的值會匯入至 Log Analytics。 選項包括事件和變更要求。|
   | **在 ITSM 產品中建立新的設定專案** | 如果您想要在 ITSM 產品中建立設定項目，請選取此選項。 選取此選項時，ITSMC 會建立設定專案 (如果在支援的 ITSM 系統中沒有任何) 存在的話。 此功能預設為停用。 |

![新增 ServiceNow 連線的方塊和選項螢幕擷取畫面。](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

當您成功連線並同步處理時：

- 從 ServiceNow 實例選取的工作專案會匯入至 Log Analytics。 您可以在 [ **IT 服務管理連接器** ] 圖格上，查看這些工作專案的摘要。

- 您可以從 Log Analytics 警示或記錄檔記錄，或從這個 ServiceNow 實例中的 Azure 警示建立事件。

> [!NOTE]
> ServiceNow 具有每小時要求的速率限制。 若要設定限制，請在 ServiceNow 實例中定義 **輸入 REST API 速率限制** 。

## <a name="next-steps"></a>後續步驟

* [ITSM 連接器總覽](itsmc-overview.md)
* [建立 Azure 警示的 ITSM 工作項目](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)