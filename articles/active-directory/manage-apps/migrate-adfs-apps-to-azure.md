---
title: 將應用程式身份驗證從 AD FS 移至 Azure 的活動目錄
description: 本文旨在協助組織了解如何將應用程式移至 Azure AD (內容著重於同盟 SaaS 應用程式)。
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891890"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>將應用程式身份驗證從活動目錄來建立 Azure 的目錄

[Azure 活動目錄 (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)提供了一個通用識別平臺,可為人員、合作夥伴和客戶提供單個標識,以便從任何平臺和設備存取應用程式和協作。 Azure AD 具有[全套識別管理功能](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 標準化應用程式(應用)身份驗證和授權到 Azure AD 可實現這些功能提供的好處。 

> [!NOTE]
> 本文重點介紹將應用程式身份驗證從本地活動目錄和活動目錄聯合身份驗證服務移動到 Azure AD。 有關規劃此移動的概述,請參閱將[應用程式身份驗證遷移到 Azure AD](https://aka.ms/migrateapps/whitepaper)的白皮書。 白皮書討論了如何規劃遷移、測試和見解。

## <a name="introduction"></a>簡介

如果您有一個包含使用者帳戶的本地目錄,則可能有許多使用者對其進行身份驗證的應用程式。 每個應用都配置為使用者使用其身份進行訪問。 


使用者還可以直接使用本地活動目錄進行身份驗證。 活動目錄聯合服務 (AD FS) 是基於本地標識服務的標準。 AD FS 擴展了在受信任的業務合作夥伴之間使用單一登錄 (SSO) 功能的能力,而無需使用者單獨登錄到每個應用程式。 這稱為"聯合"。

許多組織具有軟體即服務 (SaaS) 或自定義業務線 (LOB) 應用,這些應用與 AD FS 直接聯合,以及基於 Office 365 和基於 Azure AD 的應用。 

![直接連線本地的應用程式](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**為了提高應用程式安全性,您的目標是在本地和雲端環境中擁有一組存取控制項和策略**。 

![透過 Azure AD 連線的應用程式](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>要移轉的應用程式

將所有應用程式身份驗證遷移到 Azure AD 是最佳選擇,因為它為您提供了用於標識和存取管理的單個控制平面。

您的應用程式可能使用現代協定或舊協議進行身份驗證。 請考慮首先遷移使用現代身份驗證協定(如 SAML 和打開 ID 連接)的應用程式。 這些應用可以通過應用庫中的內建連接器或通過在 Azure AD 中註冊應用程式來重新配置為使用 Azure AD 進行身份驗證。 使用較舊的協定的應用程式可以使用[應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)進行集成。 

有關詳細資訊,請參閱與[Azure AD 整合哪些型態的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)?

如果[啟用了 Azure AD 連接執行狀況](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs),則可以使用[AD FS 應用程式活動報表將應用程式遷移到 Azure AD。](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 

### <a name="the-migration-process"></a>移轉程序

在將應用身份驗證移動到 Azure AD 的過程中,請充分測試應用和配置。 我們建議您繼續使用現有的測試環境進行遷移到生產環境的遷移測試。 如果測試環境當前不可用,則可以使用[Azure 應用服務](https://azure.microsoft.com/services/app-service/)或[Azure 虛擬機器](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)設置一個環境,具體取決於應用程式的體系結構。

您可以選擇設置單獨的測試 Azure AD 租戶,以便在開發應用配置時使用。 

您的遷移過程可能如下所示:

**階段 1 = 目前狀態:使用 AD FS 進行生產應用認證**

![移轉階段 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**階段 2 = 選擇:指向測試 Azure 租戶的應用測試實體**

更新配置以將應用的測試實例指向測試 Azure AD 租戶,並進行任何必要的更改。 應用可以與測試 Azure AD 租戶中的使用者一起進行測試。 在開發過程中,您可以使用[Fiddler](https://www.telerik.com/fiddler)等工具來比較和驗證請求和回應。

如果設置單獨的測試租戶不可行,請跳過此階段並啟動應用的測試實例,並將其指向生產 Azure AD 租戶,如下階段 3 所述。

![移移階段 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**階段 3 = 指向生產 Azure 租戶的測試應用**

更新配置以將應用的測試實例指向 Azure 的生產實例。 您現在可以與生產實例中的使用者一起進行測試。 如有必要,請查看本文中有關轉換使用者的部分。

![移轉階段 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**階段 4 = 指向生產 AD 租戶的生產應用**

更新生產應用程式的配置以指向生產 Azure 租戶。

![移轉階段 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 使用 AD FS 進行身份驗證的應用可能會使用活動目錄組獲得許可權。 在開始遷移之前,使用[Azure AD 連接同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)在本地環境和 Azure AD 之間同步識別資料。 在遷移之前驗證這些組和成員資格,以便在遷移應用程式時向同一使用者授予訪問許可權。

### <a name="line-of-business-lob-apps"></a>商務線 (LOB) 應用

LOB 應用由您的組織在內部開發,或作為安裝在資料中心的標準打包產品提供。 範例包括基於 Windows 識別基礎和 SharePoint 應用建構的應用(不是線上共享點)。 

使用 OAuth 2.0、OpenID 連接或 WS-聯合的 LOB 應用可以作為[應用註冊](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)與 Azure AD 整合。 在[Azure 門戶](https://portal.azure.com/)中的企業應用程式頁上將使用 SAML 2.0 或 WS 聯合作為[非庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)的自定義應用集成。

## <a name="saml-based-single-sign-on"></a>基於 SAML 的單登入

可以使用 SAML 2.0 進行身份驗證的應用可以配置為[基於 SAML 的單登錄](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)(基於 SAML 的 SSO)。 使用[基於 SAML 的 SSO,](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)您可以基於您在 SAML 聲明中定義的規則將使用者映射到特定的應用程式角色。 

要為基於 SAML 的單登入設定 SaaS 應用程式,請參考[設定基於 SAML 的單登入](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)。 

![SSO SAML 使用者螢幕截圖 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
許多 SaaS 應用程式都有[特定於應用程式的教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list),該教程將逐步完成基於 SAML 的單登錄的配置。

![應用程式教學](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些應用程式可以輕鬆地遷移。 具有更複雜需求 (例如自訂宣告) 的應用程式則可能需要在 Azure AD 及/或 Azure AD Connect 中額外進行設定。 有關受支援的宣告映射的資訊,請參閱[Azure 活動目錄中的宣告映射](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。

映射屬性時請記住以下限制:

* 並非所有可以在 AD FS 中發出的屬性都會在 Azure AD 中作為屬性顯示,以向 SAML 令牌發出,即使這些屬性是同步的。 編輯屬性時,「值」下拉清單將顯示 Azure AD 中可用的不同屬性。 檢查[Azure AD 連線同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)設定,以確保所需的屬性(例如 samAccountName)正在同步到 Azure AD。 可以使用延伸屬性發出不屬於 Azure AD 中標準用戶架構的任何聲明。

* 在大多數的常見情況下，應用程式只需要 NameID 宣告和其他常見的使用者識別碼宣告。 要確定是否需要任何其他聲明,請檢查您從 AD FS 發出哪些聲明。

* 並非所有聲明都可能是問題,因為某些聲明在 Azure AD 中受到保護。 

* 使用加密 SAML 權杖的能力現已處於預覽狀態。 請參考[如何:自訂在企業應用程式的 SAML 權杖中發出的聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

 

### <a name="software-as-a-service-saas-apps"></a>軟體即服務 (SaaS) 應用

如果用戶登錄到 SaaS 應用(如 Salesforce、ServiceNow 或 Workday)並與 AD FS 整合,則您使用的是 SaaS 應用的聯合登錄。 

大多數 SaaS 應用程式已在 Azure AD 中配置。 Microsoft 在[Azure AD 應用庫中](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)有許多與 SaaS 應用的預配置連接,這將使您的轉換更容易。 SAML 2.0 應用程式可以通過 Azure AD 應用庫或非[庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)與 Azure AD 整合。 

使用 OAuth 2.0 或 OpenID Connect 的應用程式可與 Azure AD 整合 (類似於「應用程式註冊」[](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users))。 使用舊協定的應用可以使用 Azure [AD 應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)對 Azure AD 進行身份驗證。

對於加入 SaaS 應用程式的任何問題,您可以聯絡[SaaS 應用程式整合支援別名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

**SSO 的 SAML 簽署證書**:簽署證書是任何 SSO 部署的重要組成部分。 Azure AD 建立簽署證書,以建立基於 SAML 的聯合 SSO 到 SaaS 應用程式。 添加庫或非庫應用程式後,將使用聯合 SSO 選項配置添加的應用程式。 請參考[Azure 的目錄中管理聯合單一登入的憑證](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>可移動的應用程式和設定

您目前可以輕鬆移動的應用程式包括使用標準設定元素和宣告集的 SAML 2.0 應用:

* 使用者主體名稱

* 電子郵件地址

* 指定的名稱

* Surname

* 作為 SAML **NameID** 的替代屬性，包括 Azure AD 郵件屬性、郵件前置詞、員工識別碼、擴充屬性 1-15 或內部部署 **SamAccountName** 屬性。 如需詳細資訊，請參閱[編輯 NameIdentifier 宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

* 自訂宣告。

以下需要其他設定步驟才能移到 Azure AD:

* AD FS 中的自定義授權或多重身份驗證 (MFA) 規則。 您可以使用 Azure AD[條件存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)功能設定它們。

* 具有多個「答覆 URL」終結點的應用。 通過使用 PowerShell 或在 Azure 門戶介面中配置它們。

* WS-同盟應用程式，例如需要 SAML 1.1 版權杖的 SharePoint 應用程式。 您可以使用 PowerShell 手動配置它們。 您還可以為庫中的 SharePoint 和 SAML 1.1 應用程式添加預整合的通用範本。 我們支援 SAML 2.0 協定。

* 複雜的聲明頒發將轉換規則。 有關支援的聲明映射的資訊,請參閱:
   *  [Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [針對 Azure Active Directory 中的企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD 目前不支援的應用程式和設定

當前無法遷移需要以下功能的應用。

**協定功能**

* 支援 WS-信任 ActAs 模式

* SAML 成品解析

* 簽署 SAML 要求的簽章驗證  
請注意,已簽名的請求已接受,但簽名未驗證。  
鑒於 Azure AD 將僅將權杖返回到應用程式中預先配置的終結點,在大多數情況下可能不需要簽名驗證。

**權杖功能中的聲明**

* 來自屬性的聲明儲存 Azure AD 目錄以外的其他聲明,除非該資料同步到 Azure AD。 有關詳細資訊,請參閱 Azure [AD 同步 API 概述](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)。

* 發佈目錄多值屬性。 例如,此時無法為代理地址發出多值聲明。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>將應用程式設定從 AD FS 映射到 Azure AD

移轉一開始會評估應用程式在內部部署環境中的設定方式，然後將該設定對應至 Azure AD。 AD FS 和 Azure AD 的工作方式類似,因此配置信任、登錄和登出 URL 以及識別符的概念在這兩種情況下都適用。 記錄應用程式的 AD FS 配置設定,以便在 Azure AD 中輕鬆配置它們。

### <a name="map-app-configuration-settings"></a>對應設定設定

下表描述了 AD FS 依賴方信任 Azure 企業應用程式之間的一些最常見的設定映射:

* AD FS – 在 AD FS 依賴方信任中尋找應用的設定。 右鍵單擊依賴方並選擇"屬性" 

* Azure AD= 該設定在每個應用程式的單一登錄屬性中的[Azure 門戶](https://portal.azure.com/)中配置。

| 組態設定| AD FS| 如何在 Azure AD 中設定| SAML 權杖 |
| - | - | - | - |
| **應用程式登入 URL** <p>使用者登錄服務提供者 (SP) 啟動的 SAML 流中的應用的 URL。| N/A| 從 SAML 的登入開啟基本 SAML 設定| N/A |
| **應用程式回覆 URL** <p>從標識提供者 (IdP) 的角度進行應用的 URL。 使用者登錄到 IdP 後,IdP 會在此處將使用者和令牌發送到此處。  這也稱為**SAML 斷言使用者終結點**。| 選擇 **「端點」** 選項卡| 從 SAML 的登入開啟基本 SAML 設定| SAML 權杖中的目標元素。 範例值:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **應用程式登出 URL** <p>這是當使用者從應用程式註銷時向其發送"註銷清理"請求的 URL。 IdP 也發送從所有其他應用註銷使用者的請求。| 選擇 **「端點」** 選項卡| 從 SAML 的登入開啟基本 SAML 設定| N/A |
| **應用程式識別碼** <p>從 IdP 的角度來看,這是應用標識符。 登錄 URL 值通常用於標識符(但並非始終如此)。  有時,應用將此稱為"實體ID"。| 選擇 **「識別碼」** 選項卡|從 SAML 的登入開啟基本 SAML 設定| 映射到 SAML 權杖中的 **「訪問群體**」元素。 |
| **應用程式同盟中繼資料** <p>這是應用聯合元數據的位置。 IdP 會使用此位置來自動更新特定組態設定，例如端點或加密憑證。| 選擇「**監視」** 選項卡| N/A。 Azure AD 不支援直接使用應用程式聯合元數據。 您可以手動匯入聯合元資料。| N/A |
| **使用者識別碼/名稱識別碼** <p>此屬性用來唯一表示由 Azure AD 或 AD FS 提供給應用程式的使用者識別。  此屬性通常是 UPN 或使用者的電子郵件位址。| 聲明規則。 在大多數情況下,聲明規則發出以Name標識符結尾的類型的聲明。| 您可以在標題 **「使用者屬性」 與「聲明」** 找到識別碼。 預設情況下,使用 UPN| 映射到 SAML 權杖中的**NameID**元素。 |
| **其他索賠** <p>通常從 IdP 發送到應用的其他聲明資訊的範例包括名字、姓氏、電子郵寄地址和組成員身份。| 在 AD FS 中，您可以在信賴憑證者上的其他宣告規則中找到此項目。| 您可以在標題下找到識別子 **,使用者屬性&宣告**。 選取 [檢視]**** 並編輯所有其他使用者屬性。| N/A |


### <a name="map-identity-provider-idp-settings"></a>映射識別提供者 (IdP) 設定

將應用程式設定為指向 SSO 的 Azure AD 和 AD FS。 在這裡,我們重點關注使用 SAML 協定的 SaaS 應用。 但是,此概念也適用於自定義 LOB 應用。

> [!NOTE]
> Azure AD 的設定值遵循 Azure 租戶 ID 替換 [租戶 id] 和應用程式 ID 取代 [應用程式 id] 的模式。 您可以在 Azure 活動目錄>屬性下的[Azure 門戶](https://portal.azure.com/)中找到此資訊: 

* 選擇目錄 ID 以查看租戶 ID。 

* 選擇應用程式識別碼以查看應用程式 ID。

 在高級別上,將以下關鍵 SaaS 應用配置元素映射到 Azure AD。 

 

| 元素| 設定值 |
| - | - |
| 身份提供者發行者| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| 身份提供者登入網址| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 識別提供者登出網址| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 同一個同資料位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>為 SaaS 應用映射 SSO 設定

SaaS 應用需要知道向何處發送身份驗證請求以及如何驗證收到的權杖。 下表描述了在應用中設定 SSO 設定的元素,以及它們在 AD FS 和 Azure AD 中的值或位置

| 組態設定| AD FS| 如何在 Azure AD 中設定 |
| - | - | - |
| **IdP 登入網址** <p>從應用的角度登錄 IdP 的登錄 URL(使用者被重定向以登錄)。| AD FS 登入 URL 是 AD FS 聯合服務名稱,後跟"/adfs/ls/"。 <p>例如:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| 將 [租戶 ID] 替換為租戶 ID。 <p> 對使用 SAML-P 協定的應用:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>對於使用 WS-聯合協定的應用:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 登出網址**<p>從應用的角度註銷 IdP 的登出網址(當用戶選擇註銷應用時,使用者被重定向)。| 登出網址 與登入 URL 相同,或者與附加" wa_wsignout1.0" 的 URL 相同。 例如:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| 將 [租戶 ID] 替換為租戶 ID。<p>對使用 SAML-P 協定的應用:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> 對於使用 WS-聯合協定的應用:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **權杖簽署憑證**<p>IdP 使用憑證的私密金鑰對已頒發的權杖進行簽名。 它會驗證權杖是否來自應用程式設定要信任的相同 IdP。| 在 AD FS 管理的 [憑證]**** 之下可找到 AD FS 權杖簽署憑證。| 在標題**SAML 簽署證書**下的應用程式的**單一登錄屬性**中的 Azure 門戶中找到它。 您可以在該處下載憑證以便上傳至應用程式。  <p>如果應用程式有多個證書,則可以在聯合元資料 XML 檔中找到所有證書。 |
| **標識符/"頒發者"**<p>從應用的角度來看 IdP 的標識符(有時稱為"頒發者 ID")。<p>在 SAML 權杖中,該值顯示為頒發者元素。| AD FS 的識別碼通常是在 **「編輯聯合服務屬性」** 下,AD FS 管理中的聯合服務識別符>。 例如:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| 將 [租戶 ID] 替換為租戶 ID。<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **IdP 聯合中繼資料**<p>IdP 公開可用的聯合元數據的位置。 (有些應用程式會使用同盟中繼資料，作為系統管理員個別設定 URL、識別碼和權杖簽署憑證的替代方式)。| 在「**服務>終結點>元數據>類型:聯合元數據**」下,在 AD FS 管理中尋找 AD FS 聯合元資料 URL。 例如:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Azure AD 的相對值[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)遵循模式 。 將 [租戶功能變數名稱] 替換為租戶的名稱,格式為"contoso.onmicrosoft.com"。   <p>如需詳細資訊，請參閱[同盟中繼資料](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD 表示 AD FS 安全原則

將應用身份驗證移動到 Azure AD 時,請從現有安全策略創建映射到 Azure AD 中可用的等效或替代變體。 確保在滿足應用擁有者要求的安全標準的同時可以完成這些映射,將使應用的其餘部分遷移更加輕鬆。

對於每種規則類型及其示例,我們在此處建議該規則在 AD FS、AD FS 規則語言等效代碼中的外觀,以及此映射在 Azure AD 中的方式。

### <a name="map-authorization-rules"></a>對應規則

以下是 AD FS 中授權規則類型的範例,以及如何將它們映射到 Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>範例 1: 允許所有使用者存取

允許存取所有使用者類似於 AD FS: 

![移轉階段 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


這會以以下方式之一對應到 Azure AD:

在[Azure 門戶](https://portal.azure.com/)中 :
* 選項 1:將所需的使用者分配設置為"否" ![編輯 SaaS 應用的存取控制原則 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    請注意,將「使用者分配」設置為「是」要求將使用者分配到應用程式以獲得訪問許可權。 當設置為"否"時,所有使用者都有權訪問。 此開關不控制"我的應用"體驗中針對用戶的顯示內容。 

 
* 選項 2:在「使用者和組」選項卡中,將應用程式分配給「所有使用者」自動組。 <p>
您必須在 Azure AD 租戶中[啟用動態群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule),預設的「所有使用者」群組才能可用。

   ![Azure AD 中的 SaaS 應用 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>範例 2:顯式允許組

AD FS 中的顯示式群組授權:


![頒發授權規則 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


這是規則映射到 Azure AD 的方式:

在[Azure 門戶](https://portal.azure.com/)中,將首先建立對應於 AD FS 中的使用者組[的使用者組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal),然後將應用許可權分配給該組:

![新增配置 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>範例 3: 授權特定使用者

AD FS 中的顯示式使用者授權:

![頒發授權規則 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

這是規則映射到 Azure AD 的方式:

在[Azure 門戶](https://portal.azure.com/)中,透過應用的「添加分配」選項卡將使用者添加到應用,如下所示:

![Azure 的 SaaS 應用 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>映射多重身份驗證規則 

[多因素身份驗證 (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)和 AD FS 的本地部署在遷移後仍將工作,因為您與 AD FS 聯合。 但是,請考慮遷移到綁定到 Azure AD 的條件訪問工作流的 Azure 內建 MFA 功能。 

以下是 AD FS 中 MFA 規則類型的範例,以及如何根據不同的條件將它們映射到 Azure AD:

AD FS 中的 MFA 規則設定:

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>範例 1:根據使用者/組強制實施 MFA

使用者/組選擇器是一個規則,允許您在每個組(組 SID)或每個使用者(主要 SID)的基礎上強制實施 MFA。 除了使用者/組分配之外,AD FS MFA 配置 UI 中的所有附加複選框將作為在強制執行使用者/ 組規則後計算的其他規則。 


為 Azure AD 的使用者或群組指定 MFA 規則:

1. 建立新的[的條件存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 選擇**配置**。 添加要強制實施 MFA 的使用者或組。

3. 設定 **「存取」控制項**選項,如下所示:  
‎

![AAD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>範例 2:對未註冊的設備強制實施 MFA

為 Azure AD 中未註冊的裝置指定 MFA 規則:

1. 建立新的[的條件存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 將 **「分配給****所有使用者**」

3. 設定 **「存取」控制項**選項,如下所示:  
‎

![AAD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
當您將「For 多個控制件」選項設定為「需要其中一個選定的控制件」時,這意味著如果使用者滿足該複選框指定的任何條件,則這些控制項將被授予對應用的存取許可權。

#### <a name="example-3-enforce-mfa-based-on-location"></a>範例 3:根據位置強制實施 MFA

依使用者在 Azure AD 中的位置指定 MFA 規則:

1. 建立新的[的條件存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

1. 將 **「分配給****所有使用者**」

1. [在 Azure AD 中配置命名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations),否則信任從公司網路內部進行聯合。 

1. 配置**條件規則**以指定要為其強制實施 MFA 的位置。

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 設定 **「存取」控制項**選項,如下所示:


![對應控制原則](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>將 Emit 屬性映射為宣告規則

下面是如何在 AD FS 中映射屬性的範例:

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


這是規則映射到 Azure AD 的方式:

在[Azure 門戶](https://portal.azure.com/)中,選擇**企業應用程式**,**單一登**入或添加**SAML 權杖屬性**,如下所示:

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>映射內置存取控制原則

AD FS 2016 具有多種內置存取控制策略,您可以選擇:

![內置存取控制的 Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
要在 Azure AD 中實現內建策略,可以使用[新的條件存取策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)並設定存取控制項,也可以使用 AD FS 2016 中的自訂策略設計器設定存取控制策略。 規則編輯器有一個詳盡的許可證和例外選項的清單,可説明您進行各種排列。

![Azure AD 存取控制原則](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



在此表中,我們列出了一些有用的"允許"和"例外"選項,以及它們如何映射到 Azure AD。 


| | 如何在 Azure AD 中設定「允許」選項?| 如何在 Azure AD 中配置「除」選項? |
| - | - | - |
| 從特定網路| 映射到 Azure AD 的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)| 對[信任位置](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)使用 **「排除**」選項 |
| 來自特定群組| [設定使用者/組派](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 使用使用者與群組中使用 **「排除**」選項 |
| 來自具有特定信任等級的裝置| 在配置 ->条件下的裝置狀態「控制件設定| 在裝置狀態條件「**使用 」排除**」選項,並包括**所有裝置** |
| 要求指定的聲明| 無法移轉此設定| 無法移轉此設定 |


如何為 Azure 門戶中的信任位置設定「排除」選項的範例:

![對應控制策略的螢幕擷取](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>將使用者從 AD FS 轉換為 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD 中同步 AD FS 群組

映射授權規則時,使用 AD FS 進行身份驗證的應用可能會使用 Active Directory 組來授予許可權。 在這種情況下,在遷移應用程式之前,使用[Azure AD 連接](https://go.microsoft.com/fwlink/?LinkId=615771)將這些組與 Azure AD 同步。 請確保在遷移之前驗證這些組和成員資格,以便在遷移應用程式時向同一使用者授予訪問許可權。

有關詳細資訊,請參閱[使用從活動目錄 同步的組屬性的先決條件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)。

### <a name="setup-user-self-provisioning"></a>設定使用者自預先 

某些 SaaS 應用程式支援使用者首次登錄應用程式時自行預配功能。 在 Azure 活動目錄 (Azure AD) 中,術語應用預配是指在使用者需要存取的雲[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)應用程式中自動創建使用者識別和角色。 遷移的使用者已在 SaaS 應用程式中具有帳戶。 遷移後添加的任何新使用者都需要預配。 移動應用程式後測試[SaaS 的預先](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)配 。

### <a name="sync-external-users-in-azure-ad"></a>在 Azure AD 中同步外部使用者

您的現有外部使用者可能在 AD FS 中以兩種主要方式進行設定:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>在組織內具有本地帳戶的外部使用者

您將繼續能夠以與內部使用者帳戶相同的方式使用這些帳戶。 這些外部使用者帳戶在組織內具有原則名稱,儘管該帳戶的電子郵件可能指向外部。 在遷移中,可以通過遷移這些使用者以在此類標識可用時使用自己的公司標識來利用[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)提供的優勢。 這簡化了這些用戶的登錄過程,因為他們通常使用自己的公司登錄登錄。 由於不再需要管理外部使用者的帳戶,組織的管理也將得到緩解。

#### <a name="federated-external-identities"></a>聯際外部識別

如果您目前正在與外部組織聯合,則有幾種方法可以採用:

* [在 Azure 門戶中加入 Azure 的目錄 B2B 協作使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。 您可以主動將 B2B 協作邀請從 Azure AD 管理門戶發送到合作夥伴組織,以便各個成員繼續使用他們習慣的應用和資產。 

* [創建自助服務 B2B 註冊工作流](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal),該工作流使用 B2B 邀請 API 為合作夥伴組織的個人使用者生成請求。

無論現有外部使用者的配置方式如何,他們都可能具有與其帳戶關聯的許可權,包括組成員身份或特定許可權。 評估是否需要遷移或清理這些許可權。 在使用者遷移到外部標識后,需要禁用組織中表示外部使用者的帳戶。 遷移過程應與您的業務合作夥伴討論,因為他們連接到資源的能力可能會中斷。

## <a name="migrate-and-test-your-apps"></a>移轉及測試應用程式

請遵循本文中詳述的遷移過程。

然後轉到[Azure 門戶](https://aad.portal.azure.com/)以測試遷移是否成功。 請遵循下列指示：
1. 選擇**企業應用程式** > **所有應用程式**,並從清單中找到你的應用。

1. 選擇 **「管理** > **使用者和組**」 以至少向應用分配一個使用者或群組。

1. 選擇 **「管理** > **條件存取**」。 查看策略清單,並確保不會使用[條件存取策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)阻止對應用程式的訪問。

根據配置應用的方式,驗證 SSO 是否正常工作。 

| 驗證類型| 測試 |
| - | - |
| OAuth / 開 ID 連線| 選擇**企業應用程式>許可權**,並確保您已同意在應用的用戶設置中使用在您的組織中使用的應用程式。  
‎ |
| 以 SAML 為基礎的 SSO| 使用 **「單一登入**」下的[「測試 SAML 設定」](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues)按鈕。  
‎ |
| 基於密碼的 SSO| 下載並安裝[MyApps 安全登入](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[擴充](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)。 此擴展可説明您啟動組織的任何需要您使用 SSO 流程的雲端應用。  
‎ |
| 應用程式 Proxy| 確保連接器正在運行並分配給應用程式。 請造[訪應用程式代理故障排除指南](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)以獲得進一步説明。  
‎ |

> [!NOTE]
> 來自舊 AD FS 環境的 Cookie 仍將在使用者的電腦上持久。 這些 Cookie 可能會導致遷移問題,因為使用者可能會定向到舊的 AD FS 登錄環境,而不是新的 Azure AD 登錄。 您可能需要手動清除使用者瀏覽器 Cookie 或使用文稿。 您還可以使用系統中心配置管理器或類似平臺。

### <a name="troubleshoot"></a>疑難排解

如果遷移的應用程式測試有任何錯誤,則故障排除可能是回到現有 AD FS 依賴方之前的第一步。 請參考[如何除錯 Azure 的應用程式的應用程式的 KDEL 的單一登入](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)。

### <a name="rollback-migration"></a>移轉移

如果遷移失敗,我們建議您將現有的依賴方留在 AD FS 伺服器上,並取消對依賴方的訪問。 這將允許在部署期間根據需要快速回退。

### <a name="end-user-communication"></a>最終使用者通訊

雖然計劃的中斷視窗本身可能很小,但您仍應計劃主動向員工傳達這些時間幀,同時從 AD FS 到 Azure AD 進行截止。 確保應用體驗具有"反饋"按鈕,或指向説明台解決問題的指標。

部署完成後,您可以發送通信,通知使用者成功部署,並提醒使用者他們需要執行的任何新步驟。

* 指示使用者使用[訪問面板](https://myapps.microsoft.com.com/)訪問所有遷移的應用程式。 

* 提醒使用者可能需要更新其 MFA 設置。 

* 如果部署了自助服務密碼重置,使用者可能需要更新或驗證其身份驗證方法。 請參閱[MFA](https://aka.ms/mfatemplates)和[SSPR](https://aka.ms/ssprtemplates)最終使用者通信範本。

與外部使用者的通信:在出現問題時,這組使用者通常是受影響最嚴重的。 如果安全狀態為外部合作夥伴規定了一組不同的條件訪問規則或風險配置檔,則尤其如此。 確保外部合作夥伴瞭解雲遷移計劃,並有一個時間範圍,鼓勵他們參與測試外部協作獨有的所有流的試驗部署。 最後,確保他們有一種方法訪問您的幫助台,以防出現問題。

## <a name="next-steps"></a>後續步驟
讀取[應用程式身分驗證移至 Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
設定[條件存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)與[MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
