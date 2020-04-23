---
title: 將應用程式驗證從 AD FS 移至 Azure Active Directory
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
ms.openlocfilehash: e104fa26d6657c06a0df31bad8c773fb444a1105
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100867"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>將應用程式驗證從 Active Directory 同盟服務移至 Azure Active Directory

[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)提供通用身分識別平臺，可讓您的人員、合作夥伴和客戶使用單一身分識別來存取應用程式，並從任何平臺和裝置進行共同作業。 Azure AD 具有一[組完整的身分識別管理功能](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 將您的應用程式（app）驗證和授權標準化，Azure AD 可提供這些功能所提供的優點。 

> [!NOTE]
> 本文著重于將應用程式驗證從內部部署 Active Directory 和 Active Directory 同盟服務移至 Azure AD。 如需規劃這項移動的總覽，請參閱將[應用程式驗證遷移到 Azure AD](https://aka.ms/migrateapps/whitepaper)白皮書。 本白皮書將討論如何規劃遷移、測試和深入解析。

## <a name="introduction"></a>簡介

如果您有包含使用者帳戶的內部部署目錄，您可能會有許多使用者進行驗證的應用程式。 每個應用程式都會設定為讓使用者使用其身分識別來存取。 


使用者也可以直接使用您的內部部署 Active Directory 進行驗證。 Active Directory 同盟服務（AD FS）是以標準為基礎的內部部署身分識別服務。 AD FS 擴充了在信任的商業夥伴之間使用單一登入（SSO）功能的能力，而不需要使用者分別登入每個應用程式。 這就是所謂的同盟。

許多組織都具有軟體即服務（SaaS）或自訂的企業營運（LOB）應用程式，可直接與 Office 365 和 Azure AD 型應用程式一起 AD FS。 

![直接連線到內部部署的應用程式](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**若要提高應用程式安全性，您的目標是在您的內部部署和雲端環境中擁有單一的存取控制和原則集合**。 

![透過 Azure AD 連線的應用程式](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>要遷移的應用程式類型

將您的所有應用程式驗證遷移至 Azure AD 是最佳做法，因為它提供單一控制平面來進行身分識別和存取管理。

您的應用程式可能會使用新式或舊版通訊協定進行驗證。 請考慮先遷移使用新式驗證通訊協定的應用程式（例如 SAML 和 Open ID Connect）。 這些應用程式可以重新設定為透過應用程式庫中的內建連接器，或在 Azure AD 中註冊應用程式，以透過 Azure AD 進行驗證。 使用舊版通訊協定的應用程式可以使用[應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)進行整合。 

如需詳細資訊，請參閱[我可以將哪些類型的應用程式與 Azure AD 整合](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)？

如果您已[啟用 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)，您可以使用 [ [AD FS 應用程式活動] 報告將應用程式遷移至 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 。 

### <a name="the-migration-process"></a>遷移程式

在將您的應用程式驗證移至 Azure AD 的過程中，會適當地測試您的應用程式和設定。 我們建議您繼續使用現有的測試環境，以移至生產環境進行遷移測試。 如果目前無法使用測試環境，您可以根據應用程式的架構，使用[Azure App Service](https://azure.microsoft.com/services/app-service/)或[Azure 虛擬機器](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)設定一個。

您可以選擇設定個別的測試 Azure AD 租使用者，以在開發應用程式設定時使用。 

您的遷移程式可能如下所示：

**階段1–目前狀態：使用 AD FS 驗證生產應用程式**

![遷移階段1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**第2階段–選擇性：測試指向測試 Azure 租使用者的應用程式實例**

將設定更新為將應用程式的測試實例指向測試 Azure AD 租使用者，並進行任何必要的變更。 應用程式可以在測試 Azure AD 租使用者中，使用使用者進行測試。 在開發過程中，您可以使用[Fiddler](https://www.telerik.com/fiddler)之類的工具來比較和驗證要求和回應。

如果不可行地設定個別的測試租使用者，請略過此階段並建立應用程式的測試實例，並將其指向您的生產 Azure AD 租使用者，如下面的階段3所述。

![遷移階段2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**第3階段–指向生產環境 Azure 租使用者的測試應用程式**

更新設定，將應用程式的測試實例指向 Azure 的生產實例。 您現在可以在生產實例中測試使用者。 如有必要，請參閱本文中關於轉換使用者的章節。

![遷移階段3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**第4階段–指向實際執行 AD 租使用者的生產應用程式**

更新生產應用程式的設定，以指向生產環境的 Azure 租使用者。

![遷移階段1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 使用 AD FS 進行驗證的應用程式可能會使用 Active Directory 群組來取得許可權。 開始進行遷移之前，請使用[Azure AD Connect 同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)處理內部部署環境與 Azure AD 之間的身分識別資料。 請先確認這些群組和成員資格再進行遷移，讓您可以在遷移應用程式時，將存取權授與給相同的使用者。

### <a name="line-of-business-lob-apps"></a>企業營運（LOB）應用程式

LOB 應用程式是由您的組織在內部開發，或以已安裝在您資料中心的標準封裝產品形式提供。 範例包括以 Windows Identity Foundation 和 SharePoint 應用程式（而非 SharePoint Online）為基礎的應用程式。 

使用 OAuth 2.0、OpenID Connect 或 WS-同盟的 LOB 應用程式可以與 Azure AD 做為[應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)進行整合。 在[Azure 入口網站](https://portal.azure.com/)的 [企業應用程式] 頁面上，將使用 SAML 2.0 或 WS-同盟的自訂應用程式整合為[非資源庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)。

## <a name="saml-based-single-sign-on"></a>以 SAML 為基礎的單一登入

使用 SAML 2.0 進行驗證的應用程式，可以設定為以[saml 為基礎的單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)（SAML 型 SSO）。 透過[saml 型 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)，您可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。 

若要設定 SAML 型單一登入的 SaaS 應用程式，請參閱[設定 saml 型單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)。 

![SSO SAML 使用者螢幕擷取畫面 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
許多 SaaS 應用程式都有[應用程式特定的教學](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)課程，可逐步引導您完成設定 SAML 型單一登入。

![應用程式教學課程](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些應用程式可以輕鬆地遷移。 具有更複雜需求 (例如自訂宣告) 的應用程式則可能需要在 Azure AD 及/或 Azure AD Connect 中額外進行設定。 如需支援的宣告對應的詳細資訊，請參閱[Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。

對應屬性時，請記住下列限制：

* 並非所有可在 AD FS 中發出的屬性都會在 Azure AD 中顯示為要發出至 SAML token 的屬性，即使這些屬性已同步處理也一樣。 當您編輯屬性時，[值] 下拉式清單會顯示 Azure AD 中可用的不同屬性。 請檢查[Azure AD Connect 同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)設定，以確保必要屬性（例如 samAccountName--）正在同步處理至 Azure AD。 您可以使用擴充屬性，在 Azure AD 中發出不屬於標準使用者架構的任何宣告。

* 在大多數的常見情況下，應用程式只需要 NameID 宣告和其他常見的使用者識別碼宣告。 若要判斷是否需要任何其他宣告，請檢查您要從 AD FS 發出的宣告。

* 並非所有宣告都可能是問題，因為 Azure AD 中的某些宣告受到保護。 

* 使用加密 SAML 權杖的功能現已開放預覽。 請參閱[如何：針對企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

 

### <a name="software-as-a-service-saas-apps"></a>軟體即服務（SaaS）應用程式

如果您的使用者登入 SaaS 應用程式（例如 Salesforce、ServiceNow 或 Workday），並已與 AD FS 整合，您就會使用 SaaS 應用程式的同盟登入。 

大部分的 SaaS 應用程式都可以在 Azure AD 中設定。 Microsoft 在[Azure AD 應用程式庫](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中有許多與 SaaS 應用程式的預先設定連線，可讓您更輕鬆地進行轉換。 SAML 2.0 應用程式可以透過 Azure AD 應用程式庫或[非資源庫應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)，與 Azure AD 整合。 

使用 OAuth 2.0 或 OpenID Connect 的應用程式可與 Azure AD 整合 (類似於「應用程式註冊」[](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users))。 使用傳統通訊協定的應用程式可以使用[Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)來向 Azure AD 進行驗證。

對於將 SaaS 應用程式上架的任何問題，您可以聯繫[Saas 應用程式整合支援別名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

**Sso 的 SAML 簽署憑證**：簽署憑證是任何 SSO 部署的重要部分。 Azure AD 會建立簽署憑證，以針對您的 SaaS 應用程式建立 SAML 型同盟 SSO。 新增資源庫或非資源庫應用程式之後，您將使用 [同盟 SSO] 選項設定新增的應用程式。 請參閱[在 Azure Active Directory 中管理同盟單一登入的憑證](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>可以立即移動的應用程式和設定

您現在可以輕鬆移動的應用程式包括使用一組標準設定元素和宣告的 SAML 2.0 應用程式：

* 使用者主體名稱

* 電子郵件地址

* 指定的名稱

* Surname

* 作為 SAML **NameID** 的替代屬性，包括 Azure AD 郵件屬性、郵件前置詞、員工識別碼、擴充屬性 1-15 或內部部署 **SamAccountName** 屬性。 如需詳細資訊，請參閱[編輯 NameIdentifier 宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

* 自訂宣告。

以下需要額外的設定步驟，才能遷移至 Azure AD：

* AD FS 中的自訂授權或多重要素驗證（MFA）規則。 您可以使用[Azure AD 的條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)功能來設定它們。

* 具有多個回復 URL 端點的應用程式。 您可以使用 PowerShell 或 Azure 入口網站介面，在 Azure AD 中設定它們。

* WS-同盟應用程式，例如需要 SAML 1.1 版權杖的 SharePoint 應用程式。 您可以使用 PowerShell 手動設定它們。 您也可以從資源庫新增適用于 SharePoint 和 SAML 1.1 應用程式的預先整合一般範本。 我們支援 SAML 2.0 通訊協定。

* 複雜宣告發行轉換規則。 如需支援的宣告對應的相關資訊，請參閱：
   *  [Azure Active Directory 中的宣告對應](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [針對 Azure Active Directory 中的企業應用程式自訂 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD 目前不支援的應用程式和設定

目前無法遷移需要下列功能的應用程式。

**通訊協定功能**

* WS-TRUST ActAs 模式的支援

* SAML 成品解析

* 已簽署 SAML 要求的簽章驗證  
請注意，已簽署的要求已被接受，但未驗證簽章。  
假設 Azure AD 只會將權杖傳回給在應用程式中預先設定的端點，在大多數情況下，可能不需要簽章驗證。

**權杖功能中的宣告**

* 來自 Azure AD 目錄以外之屬性存放區的宣告，除非該資料已同步處理至 Azure AD。 如需詳細資訊，請參閱[Azure AD 同步處理 API 總覽](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)。

* 發行目錄多重值屬性。 例如，我們目前無法發出 proxy 位址的多重值宣告。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>將應用程式設定從 AD FS 對應到 Azure AD

移轉一開始會評估應用程式在內部部署環境中的設定方式，然後將該設定對應至 Azure AD。 AD FS 和 Azure AD 的工作方式類似，因此在這兩種情況下，設定信任、登入及登出 Url 和識別碼的概念都適用。 記錄應用程式的 AD FS 設定，讓您可以輕鬆地在 Azure AD 中設定它們。

### <a name="map-app-configuration-settings"></a>對應應用程式設定

下表描述在 AD FS 信賴憑證者信任與 Azure AD 企業應用程式之間，最常見的設定對應：

* AD FS –在應用程式的 AD FS 信賴憑證者信任中尋找設定。 以滑鼠右鍵按一下信賴憑證者，然後選取 [屬性]。 

* Azure AD –設定會在每個應用程式的 [單一登入] 屬性中的[Azure 入口網站](https://portal.azure.com/)內設定。

| 組態設定| AD FS| 如何在 Azure AD 中設定| SAML 權杖 |
| - | - | - | - |
| **應用程式登入 URL** <p>使用者在服務提供者（SP）起始的 SAML 流程中，用來登入應用程式的 URL。| N/A| 從以 SAML 為基礎的登入開啟基本 SAML 設定| N/A |
| **應用程式回覆 URL** <p>來自識別提供者（IdP）之觀點的應用程式 URL。 IdP 會在使用者登入 IdP 之後，于此處傳送使用者和權杖。  這也稱為 SAML 判斷提示取用**者端點**。| 選取 [**端點**] 索引標籤| 從以 SAML 為基礎的登入開啟基本 SAML 設定| SAML 權杖中的 Destination 元素。 範例值：[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **應用程式登出 URL** <p>這是當使用者登出應用程式時，會傳送「登出清除」要求的 URL。 IdP 也會傳送要求，以將使用者從其他所有應用程式登出。| 選取 [**端點**] 索引標籤| 從以 SAML 為基礎的登入開啟基本 SAML 設定| N/A |
| **應用程式識別碼** <p>這是從 IdP 的觀點來看的應用程式識別碼。 [登入 URL] 值通常用於識別碼（但不一定）。  有時候應用程式會將此稱為「實體識別碼」。| 選取 [**識別碼**] 索引標籤|從以 SAML 為基礎的登入開啟基本 SAML 設定| 對應到 SAML 權杖中的**物件**元素。 |
| **應用程式同盟中繼資料** <p>這是應用程式的同盟中繼資料的位置。 IdP 會使用此位置來自動更新特定組態設定，例如端點或加密憑證。| 選取 [**監視**] 索引標籤| N/A。 Azure AD 不支援直接取用應用程式同盟中繼資料。 您可以手動匯入同盟中繼資料。| N/A |
| **使用者識別碼/名稱 ID** <p>此屬性用來唯一表示由 Azure AD 或 AD FS 提供給應用程式的使用者識別。  這個屬性通常是 UPN 或使用者的電子郵件地址。| 宣告規則。 在大部分情況下，宣告規則會發出具有以 NameIdentifier 結尾之類型的宣告。| 您可以在標頭**使用者屬性和宣告**底下找到識別碼。 根據預設，會使用 UPN| 對應到 SAML 權杖中的**NameID**元素。 |
| **其他宣告** <p>通常會從 IdP 傳送至應用程式的其他宣告資訊範例包括名字、姓氏、電子郵件地址和群組成員資格。| 在 AD FS 中，您可以在信賴憑證者上的其他宣告規則中找到此項目。| 您可以在標頭**使用者屬性 & 宣告**底下找到識別碼。 選取 [檢視]**** 並編輯所有其他使用者屬性。| N/A |


### <a name="map-identity-provider-idp-settings"></a>對應識別提供者（IdP）設定

將您的應用程式設定為指向 SSO 的 Azure AD 與 AD FS。 在這裡，我們會將焦點放在使用 SAML 通訊協定的 SaaS 應用程式。 不過，此概念也會延伸到自訂 LOB 應用程式。

> [!NOTE]
> Azure AD 的設定值會遵循您的 Azure 租使用者識別碼取代 {tenant id} 的模式，而應用程式識別碼會取代 {應用程式識別碼}。 您可以在 [Azure Active Directory > 屬性] 底下的 [ [Azure 入口網站](https://portal.azure.com/)中找到這項資訊： 

* 選取 [目錄識別碼] 以查看您的租使用者識別碼。 

* 選取 [應用程式識別碼] 以查看您的應用程式識別碼。

 在高階中，將下列重要 SaaS 應用程式設定元素對應至 Azure AD。 

 

| 元素| 設定值 |
| - | - |
| 識別提供者簽發者| HTTPs：\//sts.windows.net/{tenant-id}/ |
| 識別提供者登入 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 識別提供者登出 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 同盟中繼資料位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>對應 SaaS 應用程式的 SSO 設定

SaaS 應用程式必須知道要將驗證要求傳送到何處，以及如何驗證收到的權杖。 下表描述在應用程式中設定 SSO 設定的元素，以及其在 AD FS 和 Azure AD 中的值或位置。

| 組態設定| AD FS| 如何在 Azure AD 中設定 |
| - | - | - |
| **IdP 登入 URL** <p>從應用程式的觀點來看，IdP 的登入 URL （使用者會被重新導向以進行登入）。| AD FS 登入 URL 是 AD FS federation service 名稱後面接著 "/adfs/ls/." <p>例如：[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| 以您的租使用者識別碼取代 {tenant-id}。 <p> 針對使用 SAML-P 通訊協定的應用程式：[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>針對使用 WS-同盟通訊協定的應用程式：[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 登出 URL**<p>從應用程式的觀點來看，IdP 的登出 URL （使用者選擇登出應用程式時會重新導向）。| 「登出 URL」與「登入 URL」相同，或附加 "wa = wsignout1.0 1.0" 的相同 URL。 例如：[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| 以您的租使用者識別碼取代 {tenant-id}。<p>針對使用 SAML-P 通訊協定的應用程式：<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> 針對使用 WS-同盟通訊協定的應用程式：[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **權杖簽署憑證**<p>IdP 會使用憑證的私密金鑰來簽署已發行的權杖。 它會驗證權杖是否來自應用程式設定要信任的相同 IdP。| 在 AD FS 管理的 [憑證]**** 之下可找到 AD FS 權杖簽署憑證。| 在 [ **SAML 簽署憑證**] 標頭底下，于應用程式的 [**單一登入] 屬性**的 Azure 入口網站中找到它。 您可以在該處下載憑證以便上傳至應用程式。  <p>如果應用程式有一個以上的憑證，您可以在同盟中繼資料 XML 檔案中找到所有憑證。 |
| **識別碼/「簽發者」**<p>從應用程式觀點來看的 IdP 識別碼（有時稱為「簽發者識別碼」）。<p>在 SAML 權杖中，值會顯示為 Issuer 元素。| AD FS 的識別碼通常是**服務 > 編輯同盟服務屬性**] 下 AD FS 管理中的同盟服務識別碼。 例如：[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| 以您的租使用者識別碼取代 {tenant-id}。<p>HTTPs：\//sts.windows.net/{tenant-id}/ |
| **IdP 同盟中繼資料**<p>IdP 的公開可用同盟中繼資料位置。 (有些應用程式會使用同盟中繼資料，作為系統管理員個別設定 URL、識別碼和權杖簽署憑證的替代方式)。| 在 [服務 > 端點] 下的 AD FS 管理中，尋找 AD FS 的同盟中繼資料 URL **> 中繼資料 > 類型：同盟中繼資料**。 例如：[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Azure AD 的對應值會遵循模式[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)。 將 {TenantDomainName} 取代為您的租使用者名稱，格式為 "contoso.onmicrosoft.com"。   <p>如需詳細資訊，請參閱[同盟中繼資料](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>代表 Azure AD 中 AD FS 安全性原則

將您的應用程式驗證移至 Azure AD 時，請建立從現有安全性原則到 Azure AD 中可用的對等或替代變體的對應。 確保這些對應可以在符合應用程式擁有者所需的安全性標準時完成，讓其餘的應用程式遷移變得更容易。

針對每個規則類型及其範例，我們建議在此說明規則在 AD FS、AD FS 規則語言對等程式碼中的外觀，以及如何 Azure AD 中的對應。

### <a name="map-authorization-rules"></a>對應授權規則

以下是 AD FS 中授權規則類型的範例，以及如何將它們對應至 Azure AD：

#### <a name="example-1-permit-access-to-all-users"></a>範例1：允許所有使用者的存取權

允許所有使用者的存取看起來像 AD FS： 

![遷移階段1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


這會以下列其中一種方式對應至 Azure AD：

在 [ [Azure 入口網站](https://portal.azure.com/)：
* 選項1：將 [需要使用者指派] 設定為 [否] ![編輯 SaaS 應用程式的存取控制原則 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    請注意，設定 [需要使用者指派] 切換為 [是] 時，需要將使用者指派給應用程式以取得存取權。 當設定為 [否] 時，所有使用者都有存取權。 此參數不會控制針對我的應用程式體驗中的使用者所顯示的內容。 

 
* 選項2：在 [使用者和群組] 索引標籤中，將您的應用程式指派給「所有使用者」自動群組。 <p>
您必須在 Azure AD 租使用者中[啟用動態群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)，才能使用預設的 [所有使用者] 群組。

   ![我在 Azure AD 的 SaaS 應用程式 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>範例2：明確允許群組

AD FS 中的明確群組授權：


![發佈授權規則 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


這是規則對應到 Azure AD 的方式：

在[Azure 入口網站](https://portal.azure.com/)中，您會先[建立一個](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)對應至 AD FS 使用者群組的使用者群組，然後將應用程式許可權指派給該群組：

![新增指派 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>範例3：授權特定使用者

AD FS 中的明確使用者授權：

![發佈授權規則 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

這是規則對應到 Azure AD 的方式：

在[Azure 入口網站](https://portal.azure.com/)中，透過應用程式的 [新增指派] 索引標籤，將使用者新增至應用程式，如下所示：

![我在 Azure 中的 SaaS 應用程式 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>對應多重要素驗證規則 

在遷移之後，內部部署的[多重要素驗證（MFA）](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)和 AD FS 仍會生效，因為您已與 AD FS 同盟。 不過，請考慮遷移至 Azure 的內建 MFA 功能，並將其系結至 Azure AD 的條件式存取工作流程。 

以下是 AD FS 中的 MFA 規則類型範例，以及如何根據不同的條件將它們對應至 Azure AD：

AD FS 中的 MFA 規則設定：

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>範例1：根據使用者/群組強制執行 MFA

[使用者/群組] 選取器是一種規則，可讓您針對每個群組（群組 SID）或每一使用者（主要 SID）強制執行 MFA。 除了使用者/群組指派以外，AD FS MFA 設定 UI 函式中的所有其他核取方塊，做為強制執行使用者/群組規則之後所評估的其他規則。 


在 Azure AD 中指定使用者或群組的 MFA 規則：

1. 建立[新的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 選取 [**指派**]。 新增您想要在其上強制執行 MFA 的使用者或群組。

3. 設定**存取控制**選項，如下所示：  
‎

![AAD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>範例2：針對未註冊的裝置強制執行 MFA

在 Azure AD 中指定未註冊裝置的 MFA 規則：

1. 建立[新的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

2. 將 [**指派**] 設定為 [**所有使用者**]。

3. 設定**存取控制**選項，如下所示：  
‎

![AAD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
當您將 [針對多個控制項] 選項設定為 [需要其中一個選取的控制項] 時，這表示如果使用者已滿足核取方塊所指定的任何條件，則會授與您應用程式的存取權。

#### <a name="example-3-enforce-mfa-based-on-location"></a>範例3：根據位置強制執行 MFA

根據使用者在 Azure AD 中的位置指定 MFA 規則：

1. 建立[新的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

1. 將 [**指派**] 設定為 [**所有使用者**]。

1. [在 Azure AD 中設定已命名的位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)，否則您公司網路內的同盟會受到信任。 

1. 設定**條件規則**，以指定您想要強制執行 MFA 的位置。

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 設定**存取控制**選項，如下所示：


![對應存取控制原則](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>對應發出屬性作為宣告規則

以下是如何在 AD FS 中對應屬性的範例：

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


這是規則對應到 Azure AD 的方式：

在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [**企業應用程式**]、[**單一登入**]，然後新增 [ **SAML 權杖屬性**]，如下所示：

![Azure AD MFA 設定](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>對應內建的存取控制原則

AD FS 2016 有數個內建的存取控制原則可供您選擇：

![Azure AD 內建的存取控制](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
若要在 Azure AD 中執行內建原則，您可以使用[新的條件式存取原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)並設定存取控制，或者您也可以使用 AD FS 2016 中的自訂原則設計工具來設定存取控制原則。 「規則編輯器」有一份完整的「允許」和「除外」選項，可協助您進行所有類型的排列。

![Azure AD 存取控制原則](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



在此表中，我們列出了一些實用的 [允許] 和 [例外] 選項，以及它們如何對應到 Azure AD。 


| | 如何在 Azure AD 中設定允許選項？| 如何在 Azure AD 中設定 Except 選項？ |
| - | - | - |
| 從特定網路| 對應至 Azure AD 中的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)| 針對[信任的位置](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)使用**排除**選項 |
| 從特定群組| [設定使用者/群組指派](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 在使用者和群組中使用**排除**選項 |
| 從具有特定信任層級的裝置| 從 [指派-> 條件] 底下的 [裝置狀態] 控制項加以設定| 使用 [裝置狀態條件] 底下的 [**排除**] 選項，並包含 [**所有裝置**] |
| 在要求中使用特定宣告| 此設定無法遷移| 此設定無法遷移 |


範例說明如何在 Azure 入口網站中設定信任位置的排除選項：

![對應存取控制原則的螢幕擷取畫面](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>將使用者從 AD FS 轉換成 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>同步 Azure AD 中的 AD FS 群組

當您對應授權規則時，使用 AD FS 進行驗證的應用程式可能會使用 Active Directory 群組來取得許可權。 在這種情況下，請使用[Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)來同步處理這些群組與 Azure AD，然後再遷移應用程式。 請務必先確認這些群組和成員資格，然後再進行遷移，讓您可以在應用程式遷移時授與存取權給相同的使用者。

如需詳細資訊，請參閱[使用從 Active Directory 同步處理的群組屬性的必要條件](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)。

### <a name="setup-user-self-provisioning"></a>設定使用者自我布建 

某些 SaaS 應用程式支援在第一次登入應用程式時自行布建使用者的能力。 在 Azure Active Directory （Azure AD）中，應用程式布建一詞指的是在使用者需要存取的雲端（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）應用程式中自動建立使用者身分識別和角色。 已遷移的使用者在 SaaS 應用程式中將會有一個帳戶。 在遷移後新增的任何新使用者都必須布建。 在應用程式遷移之後，測試[SaaS 應用](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)程式布建。

### <a name="sync-external-users-in-azure-ad"></a>同步 Azure AD 中的外部使用者

您現有的外部使用者可能會在 AD FS 內以兩種主要方式設定：

#### <a name="external-users-with-a-local-account-within-your-organization"></a>在您的組織內具有本機帳戶的外部使用者

您將能以內部使用者帳戶的相同方式，繼續使用這些帳戶。 這些外部使用者帳戶在您的組織內具有主體名稱，不過帳戶的電子郵件可能會指向外部。 隨著您進行遷移的進度，您可以藉由將這些使用者遷移到這類身分識別時，使用自己的公司身分識別，來利用[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)供應專案的優點。 這可簡化這些使用者的登入程式，因為他們通常是使用自己的公司登入來登入。 您的組織管理也會分階段減緩，而不必再管理外部使用者的帳戶。

#### <a name="federated-external-identities"></a>同盟外部身分識別

如果您目前正在與外部組織同盟，您有幾種方法可以採取下列動作：

* [在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)共同作業使用者。 您可以主動將 B2B 共同作業邀請從 Azure AD 系統管理入口網站傳送到合作夥伴組織，以供個別成員繼續使用其所用的應用程式和資產。 

* [建立自助 b2b 註冊工作流程](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)，其會使用 B2B 邀請 API，為您的夥伴組織的個別使用者產生要求。

無論您現有的外部使用者如何設定，他們可能會有與其帳戶相關聯的許可權，不論是在群組成員資格或特定許可權中。 評估這些許可權是否需要遷移或清除。 貴組織內代表外部使用者的帳戶，必須在使用者遷移至外部身分識別後停用。 應該與您的商務夥伴討論遷移程式，因為它們連線到您的資源的能力可能會中斷。

## <a name="migrate-and-test-your-apps"></a>遷移及測試您的應用程式

請遵循這篇文章中詳述的遷移程式。

然後移至 [ [Azure 入口網站](https://aad.portal.azure.com/)]，測試遷移是否成功。 請遵循下列指示：
1. 從清單中選取 [**企業應用程式** > ] [**所有應用程式**] 並尋找您的應用程式。

1. 選取 [**管理** > **使用者和群組**]，將至少一個使用者或群組指派給應用程式。

1. 選取 [**管理** > **條件式存取**]。 檢查您的原則清單，並確定您不會封鎖具有[條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)的應用程式存取。

根據您設定應用程式的方式，確認 SSO 運作正常。 

| 驗證類型| 測試 |
| - | - |
| OAuth/OpenID Connect| 選取 [**企業應用程式] [> 許可權**]，並確定您已同意應用程式在您的應用程式的 [使用者設定] 中用於組織。  
‎ |
| 以 SAML 為基礎的 SSO| 使用在 [**單一登入**] 下找到的 [[測試 SAML 設定](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues)] 按鈕。  
‎ |
| 以密碼為基礎的 SSO| 下載並安裝[MyApps Secure](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)登[入延伸](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)模組。 此延伸模組可協助您啟動貴組織的任何雲端應用程式，並要求您使用 SSO 進程。  
‎ |
| 應用程式 Proxy| 請確定您的連接器正在執行，並已指派給您的應用程式。 請流覽[應用程式 Proxy 疑難排解指南](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)以取得進一步的協助。  
‎ |

> [!NOTE]
> 舊 AD FS 環境中的 cookie 仍會持續存在於使用者的電腦上。 這些 cookie 可能會造成遷移問題，因為使用者可能會被導向至舊的 AD FS 登入環境，而不是新的 Azure AD 登入。 您可能需要手動或使用腳本來清除使用者瀏覽器 cookie。 您也可以使用 System Center Configuration Manager 或類似的平臺。

### <a name="troubleshoot"></a>疑難排解

如果測試已遷移的應用程式有任何錯誤，則可能是在回到現有的 AD FS 信賴憑證者之前的第一步進行疑難排解。 請參閱[如何在 Azure Active Directory 中，對應用程式進行以 SAML 為基礎的單一登入](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)。

### <a name="rollback-migration"></a>復原遷移

如果遷移失敗，建議您將現有的信賴憑證者保留在 AD FS 伺服器上，並移除對信賴憑證者的存取權。 如果需要，您可以在部署期間快速回復。

### <a name="end-user-communication"></a>使用者通訊

雖然規劃的中斷時段本身可能很小，但您仍應計畫主動將這些時間範圍傳達給員工，同時從 AD FS 到 Azure AD 進行切換。 請確定您的應用程式體驗具有意見反應按鈕，或您的技術服務人員的指標有問題。

部署完成之後，您可以傳送通訊來通知使用者部署成功，並提醒他們需要採取的任何新步驟。

* 指示使用者使用[存取面板](https://myapps.microsoft.com)來存取所有已遷移的應用程式。 

* 提醒使用者他們可能需要更新其 MFA 設定。 

* 如果已部署自助式密碼重設，使用者可能需要更新或驗證其驗證方法。 請參閱[MFA](https://aka.ms/mfatemplates)和[SSPR](https://aka.ms/ssprtemplates)終端使用者通訊範本。

與外部使用者的通訊：在發生問題時，此使用者群組通常會受到最嚴重的影響。 如果您的安全性狀態規定了一組不同的條件式存取規則，或外部合作夥伴的風險設定檔，這就更是如此。 確保外部合作夥伴知道雲端遷移排程，並在一段時間內鼓勵他們參與試驗部署，以測試外部共同作業獨有的所有流程。 最後，請確定他們有辦法在發生重大問題時存取您的技術服務人員。

## <a name="next-steps"></a>後續步驟
閱讀[將應用程式驗證遷移至 Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
設定[條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)和[MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
