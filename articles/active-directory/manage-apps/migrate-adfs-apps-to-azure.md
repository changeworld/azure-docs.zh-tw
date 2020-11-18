---
title: 將應用程式驗證從 AD FS 移至 Azure Active Directory
description: 本文旨在協助組織了解如何將應用程式移至 Azure AD (內容著重於同盟 SaaS 應用程式)。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1012ae32f679d23f16a7483415657596d027cc01
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658820"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>將應用程式驗證從 Active Directory 同盟服務移至 Azure Active Directory

[Azure Active Directory (Azure AD) ](../fundamentals/active-directory-whatis.md) 提供通用身分識別平臺，可讓您的人員、合作夥伴和客戶擁有單一身分識別，以存取應用程式，並從任何平臺和裝置共同作業。 Azure AD 有一 [套完整的身分識別管理功能](../fundamentals/active-directory-whatis.md)。 將應用程式的 (應用程式) 驗證和授權標準化，Azure AD 可提供這些功能所提供的優點。

> [!TIP]
> 本文是針對開發人員物件撰寫的。 規劃應用程式移至 Azure AD 的專案經理和系統管理員應考慮閱讀我們將 [應用程式驗證移至 Azure AD](https://aka.ms/migrateapps/whitepaper) 白皮書 (PDF) 。

## <a name="introduction"></a>簡介

如果您有包含使用者帳戶的內部部署目錄，則您可能會有許多使用者驗證的應用程式。 每個應用程式都設定為讓使用者使用其身分識別進行存取。


使用者也可以直接使用您的內部部署 Active Directory 進行驗證。 Active Directory 同盟服務 (AD FS) 是以標準為基礎的內部部署身分識別服務。 AD FS 擴充了在信任的商業夥伴之間使用單一登入 (SSO) 功能，而不需要使用者個別登入每個應用程式的功能。 這就是所謂的同盟。

許多組織都有軟體即服務 (SaaS) 或自訂的企業營運 (LOB) 應用程式，與 AD FS 和 Microsoft 365 架構的應用程式直接同盟 Azure AD。

![直接連線至內部部署的應用程式](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**為了提高應用程式安全性，您的目標是在內部部署和雲端環境中擁有一組存取控制和原則**。

![透過 Azure AD 連接的應用程式](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>要遷移的應用程式類型

將您的所有應用程式驗證遷移至 Azure AD 是最理想的做法，因為它提供您單一控制平面來進行身分識別和存取管理。

您的應用程式可能會使用新式或舊版通訊協定進行驗證。 請考慮先將使用新式驗證通訊協定的應用程式遷移 (例如 SAML 和 Open ID Connect) 。 這些應用程式可以透過應用程式資源庫中的內建連接器，或在 Azure AD 中註冊應用程式，重新設定為透過 Azure AD 進行驗證。 使用舊版通訊協定的應用程式可以使用 [應用程式 Proxy](./what-is-application-proxy.md)來整合。

如需詳細資訊，請參閱 [我可以與 Azure AD 整合哪些類型的應用程式](./what-is-application-management.md)？

您可以使用 [AD FS 應用程式活動報表，將應用程式遷移至 Azure AD](./migrate-adfs-application-activity.md) （如果您已 [啟用 Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)）。

### <a name="the-migration-process"></a>遷移程式

在將應用程式驗證移至 Azure AD 的過程中，請充分測試您的應用程式和設定。 建議您繼續使用現有的測試環境，以移至生產環境的遷移測試。 如果目前無法使用測試環境，您可以根據應用程式的架構，使用 [Azure App Service](https://azure.microsoft.com/services/app-service/) 或 [Azure 虛擬機器](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)設定一個。

您可以選擇設定個別的測試 Azure AD 租使用者，以在開發您的應用程式設定時使用。

您的遷移程式可能如下所示：

**階段1–目前狀態：使用 AD FS 驗證生產應用程式**

![遷移階段1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**第2階段–選用：測試指向測試 Azure 租使用者的應用程式實例**

更新設定，以將應用程式的測試實例指向測試 Azure AD 租使用者，並進行任何必要的變更。 您可以使用測試 Azure AD 租使用者中的使用者測試應用程式。 在開發過程中，您可以使用 [Fiddler](https://www.telerik.com/fiddler) 之類的工具來比較並驗證要求和回應。

如果無法設定個別的測試租使用者，請略過此階段並建立應用程式的測試實例，並將其指向您的生產環境 Azure AD 租使用者，如下階段3所述。

![遷移階段2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**階段3–指向生產 Azure 租使用者的測試應用程式**

更新設定，以將應用程式的測試實例指向 Azure 的生產實例。 您現在可以使用生產實例中的使用者進行測試。 如有必要，請參閱本文中有關轉換使用者的章節。

![遷移階段3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**階段4–指向生產 AD 租使用者的生產應用程式**

更新生產環境應用程式的設定，以指向您的生產 Azure 租使用者。

![遷移階段4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 使用 AD FS 進行驗證的應用程式可能會使用 Active Directory 群組來取得許可權。 在開始進行遷移之前，請使用 [Azure AD Connect 同步](../hybrid/how-to-connect-sync-whatis.md) 處理，在內部部署環境和 Azure AD 之間同步處理身分識別資料。 請先確認這些群組和成員資格再進行遷移，讓您可以在應用程式遷移時授與存取權給相同的使用者。

### <a name="line-of-business-lob-apps"></a>企業營運 (LOB) 應用程式

LOB 應用程式是由您的組織在內部開發，或作為安裝在您資料中心內的標準封裝產品來提供。 範例包括建立在 Windows Identity Foundation 和 SharePoint 應用程式上的應用程式， (不是 SharePoint Online) 。

使用 OAuth 2.0、OpenID Connect 或 WS-Federation 的 LOB 應用程式，可以與 Azure AD 作為 [應用程式註冊](../develop/quickstart-register-app.md)來整合。 在[Azure 入口網站](https://portal.azure.com/)的 [企業應用程式] 頁面上，將使用 SAML 2.0 或 WS-Federation 的自訂應用程式整合為[非資源庫應用程式](./add-application-portal.md)。

## <a name="saml-based-single-sign-on"></a>以 SAML 為基礎的單一登入

使用 SAML 2.0 進行驗證的應用程式可以設定 (SAML 型 SSO) 的 [saml 型單一登入](./what-is-single-sign-on.md) 。 使用 [saml 型 SSO](./what-is-single-sign-on.md)，您可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。

若要為 SaaS 應用程式設定 SAML 式單一登入，請參閱[設定 SAML 式單一登入](./view-applications-portal.md)。

![SSO SAML 使用者螢幕擷取畫面 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


許多 SaaS 應用程式都有一個 [應用程式特定的教學](../saas-apps/tutorial-list.md) 課程，可逐步引導您完成 SAML 型單一登入的設定。

![應用程式教學課程](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些應用程式可以輕鬆地遷移。 具有更複雜需求 (例如自訂宣告) 的應用程式則可能需要在 Azure AD 及/或 Azure AD Connect 中額外進行設定。 如需支援的宣告對應相關資訊，請參閱 [Azure Active Directory 中的宣告對應](../develop/active-directory-claims-mapping.md)。

對應屬性時請記住下列限制：

* 並非所有可在 AD FS 中發出的屬性都會在 Azure AD 中顯示為要發出至 SAML 權杖的屬性，即使這些屬性已同步。 當您編輯屬性時，[值] 下拉式清單會顯示 Azure AD 中可用的不同屬性。 檢查 [Azure AD Connect 同步](../hybrid/how-to-connect-sync-whatis.md) 處理設定，以確保必要屬性（例如 samAccountName）正在同步處理到 Azure AD。 您可以使用擴充屬性來發出 Azure AD 中不屬於標準使用者架構的任何宣告。

* 在大多數的常見情況下，應用程式只需要 NameID 宣告和其他常見的使用者識別碼宣告。 若要判斷是否需要任何其他宣告，請檢查您要從 AD FS 發出哪些宣告。

* 並非所有宣告都可能會有問題，因為某些宣告在 Azure AD 中受到保護。

* 使用加密 SAML 權杖的功能現已進入預覽階段。 請參閱 [如何：針對企業應用程式自訂在 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)。



### <a name="software-as-a-service-saas-apps"></a>軟體即服務 (SaaS) 應用程式

如果您的使用者登入 SaaS 應用程式（例如 Salesforce、ServiceNow 或 Workday），並與 AD FS 整合，則您會使用 SaaS 應用程式的同盟登入。

大部分的 SaaS 應用程式都可以在 Azure AD 中設定。 Microsoft 在  [Azure AD 應用程式資源庫](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中有許多與 SaaS 應用程式的預先設定連線，可讓您更輕鬆地進行轉換。 您可以透過 Azure AD 應用程式資源庫或 [非資源庫應用程式](./add-application-portal.md)，將 SAML 2.0 應用程式與 Azure AD 整合。

使用 OAuth 2.0 或 OpenID Connect 的應用程式可與 Azure AD 整合 (類似於「應用程式註冊」[](../develop/quickstart-register-app.md))。 使用舊版通訊協定的應用程式可以使用 [Azure AD 應用程式 Proxy](./application-proxy.md) 來向 Azure AD 進行驗證。

針對將 SaaS 應用程式上架的任何問題，您可以聯絡 [Saas 應用程式整合支援別名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

**Sso 的 SAML 簽署憑證**：簽署憑證是任何 sso 部署的重要部分。 Azure AD 會建立簽署憑證，以針對您的 SaaS 應用程式建立以 SAML 為基礎的同盟 SSO。 當您新增資源庫或非資源庫應用程式之後，您將使用同盟 SSO 選項設定新增的應用程式。 請參閱 [在 Azure Active Directory 中管理同盟單一登入的憑證](./manage-certificates-for-federated-single-sign-on.md)。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>可以立即移動的應用程式和設定

您現在可以輕鬆移動的應用程式包括使用一組標準設定元素和宣告的 SAML 2.0 應用程式：

* 使用者主體名稱

* 電子郵件地址

* 指定的名稱

* Surname

* 作為 SAML **NameID** 的替代屬性，包括 Azure AD 郵件屬性、郵件前置詞、員工識別碼、擴充屬性 1-15 或內部部署 **SamAccountName** 屬性。 如需詳細資訊，請參閱[編輯 NameIdentifier 宣告](../develop/active-directory-saml-claims-customization.md)。

* 自訂宣告。

以下需要額外的設定步驟，才能遷移至 Azure AD：

* 自訂授權或 Multi-Factor Authentication (MFA) AD FS 中的規則。 您可以使用 [Azure AD 條件式存取](../conditional-access/overview.md) 功能來設定它們。

* 具有多個回復 URL 端點的應用程式。 您可以使用 PowerShell 或 Azure 入口網站介面，在 Azure AD 中進行設定。

* WS-同盟應用程式，例如需要 SAML 1.1 版權杖的 SharePoint 應用程式。 您可以使用 PowerShell 以手動方式進行設定。 您也可以從資源庫新增適用于 SharePoint 和 SAML 1.1 應用程式的預先整合一般範本。 我們支援 SAML 2.0 通訊協定。

* 複雜宣告發行轉換規則。 如需支援的宣告對應相關資訊，請參閱：
   *  [Azure Active Directory 中的宣告對應](../develop/active-directory-claims-mapping.md)
   * [針對 Azure Active Directory 中的企業應用程式自訂 SAML 權杖中發出的宣告](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Azure AD 目前不支援的應用程式和設定

目前無法遷移需要下列功能的應用程式。

**通訊協定功能**

* 支援 WS-Trust ActAs 模式

* SAML 成品解析

* 簽署之 SAML 要求的簽章驗證，請注意已簽署的要求已被接受，但未驗證簽章。
假設 Azure AD 只會將權杖傳回給應用程式中預先設定的端點，在大部分情況下，可能不需要簽章驗證。

**權杖功能中的宣告**

* 從 Azure AD 目錄以外的屬性存放區宣告，除非該資料已同步處理到 Azure AD。 如需詳細資訊，請參閱 [Azure AD 同步處理 API 總覽](/graph/api/resources/synchronization-overview?view=graph-rest-beta)。

* 發行目錄多重值屬性。 例如，我們目前無法為 proxy 位址發出多重值宣告。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>將應用程式設定從 AD FS 對應到 Azure AD

移轉一開始會評估應用程式在內部部署環境中的設定方式，然後將該設定對應至 Azure AD。 AD FS 和 Azure AD 的運作方式相同，因此在這兩種情況下都適用設定信任、登入及登出 Url 和識別碼的概念。 記錄應用程式的 AD FS 設定，讓您可以在 Azure AD 中輕鬆進行設定。

### <a name="map-app-configuration-settings"></a>地圖應用程式設定

下表說明 AD FS 信賴憑證者信任與 Azure AD 企業應用程式之間，最常見的設定對應：

* AD FS –在 AD FS 的信賴憑證者信任中尋找應用程式的設定。 以滑鼠右鍵按一下信賴憑證者，然後選取 [屬性]。

* Azure AD –設定是在每個應用程式的單一登入屬性 [Azure 入口網站](https://portal.azure.com/) 內進行設定。

| 組態設定| AD FS| 如何在 Azure AD 中設定| SAML 權杖 |
| - | - | - | - |
| **應用程式登入 URL** <p>使用者用來登入服務提供者中應用程式的 URL， (SP) 起始的 SAML 流程。| N/A| 從 SAML 型登入開啟基本 SAML 設定| N/A |
| **應用程式回覆 URL** <p>從身分識別提供者 (IdP 的) 觀點來看，應用程式的 URL。 IdP 會在使用者登入 IdP 之後，將使用者和權杖傳送到此處。  這也稱為 SAML 判斷提示取用 **者端點**。| 選取 [ **端點** ] 索引標籤| 從 SAML 型登入開啟基本 SAML 設定| SAML 權杖中的 Destination 元素。 值範例： `https://contoso.my.salesforce.com` |
| **應用程式登出 URL** <p>這是使用者登出應用程式時，會傳送「登出清除」要求的 URL。 IdP 會傳送要求，以將使用者從其他所有應用程式登出。| 選取 [ **端點** ] 索引標籤| 從 SAML 型登入開啟基本 SAML 設定| N/A |
| **應用程式識別碼** <p>這是來自 IdP 觀點的應用程式識別碼。 登入 URL 值經常用於識別碼 (但不一定是) 。  有時候，應用程式會將此稱為「實體識別碼」。| 選取 [ **識別碼** ] 索引標籤|從 SAML 型登入開啟基本 SAML 設定| 對應至 SAML 權杖中的 **物件** 元素。 |
| **應用程式同盟中繼資料** <p>這是應用程式同盟中繼資料的位置。 IdP 會使用此位置來自動更新特定組態設定，例如端點或加密憑證。| 選取 [ **監視** ] 索引標籤| N/A。 Azure AD 不支援直接取用應用程式同盟中繼資料。 您可以手動匯入同盟中繼資料。| N/A |
| **使用者識別碼/名稱識別碼** <p>此屬性用來唯一表示由 Azure AD 或 AD FS 提供給應用程式的使用者識別。  這個屬性通常是 UPN 或使用者的電子郵件地址。| 宣告規則。 在大部分情況下，宣告規則會發出具有以 NameIdentifier 結尾之類型的宣告。| 您可以在標頭 **使用者屬性和宣告** 下找到識別碼。 預設會使用 UPN| 對應至 SAML 權杖中的 **NameID** 元素。 |
| **其他宣告** <p>通常會從 IdP 傳送至應用程式的其他宣告資訊範例包括名字、姓氏、電子郵件地址和群組成員資格。| 在 AD FS 中，您可以在信賴憑證者上的其他宣告規則中找到此項目。| 您可以在 & 宣告的標頭 **使用者屬性** 下找到識別碼。 選取 [檢視] 並編輯所有其他使用者屬性。| N/A |


### <a name="map-identity-provider-idp-settings"></a>對應識別提供者 (IdP) 設定

設定您的應用程式，使其指向 SSO 的 Azure AD 與 AD FS。 在這裡，我們將著重于使用 SAML 通訊協定的 SaaS 應用程式。 不過，此概念也會延伸至自訂 LOB 應用程式。

> [!NOTE]
> Azure AD 的設定值會遵循您的 Azure 租使用者識別碼取代 {租使用者識別碼} 的模式，而應用程式識別碼會取代 {application-id}。 您可以在 [Azure Active Directory > 屬性] 下的 [Azure 入口網站](https://portal.azure.com/) 中找到這項資訊：

* 選取 [目錄識別碼] 以查看您的租使用者識別碼。

* 選取 [應用程式識別碼] 以查看您的應用程式識別碼。

 在高階中，將下列重要的 SaaS 應用程式設定元素對應至 Azure AD。



| 項目| 設定值 |
| - | - |
| 身分識別提供者簽發者| HTTPs： \/ /sts.windows.net/{tenant-id}/ |
| 身分識別提供者登入 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 身分識別提供者登出 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 同盟中繼資料位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>針對 SaaS 應用程式對應 SSO 設定

SaaS 應用程式必須知道傳送驗證要求的位置，以及如何驗證所收到的權杖。 下表說明在應用程式中設定 SSO 設定的元素，以及其在 AD FS 和中的值或位置 Azure AD

| 組態設定| AD FS| 如何在 Azure AD 中設定 |
| - | - | - |
| **IdP 登入 URL** <p>從應用程式的觀點 (來看，IdP 的登入 URL，使用者會被重新導向以進行登入) 。| AD FS 登入 URL 是 AD FS federation service 名稱後面接著 "/adfs/ls/." <p>例如：`https://fs.contoso.com/adfs/ls/`| 將 {租使用者識別碼} 取代為您的租使用者識別碼。 <p> 針對使用 SAML-P 通訊協定的應用程式： [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>針對使用 WS-Federation 通訊協定的應用程式： [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 登出 URL**<p>從應用程式的觀點來看，IdP 的登出 URL (當使用者選擇登出應用程式) 時重新導向。| 登出 URL 與登入 URL 相同，或附加 "wa = wsignout1.0 1.0" 的相同 URL。 例如：`https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| 將 {租使用者識別碼} 取代為您的租使用者識別碼。<p>針對使用 SAML-P 通訊協定的應用程式：<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> 針對使用 WS-Federation 通訊協定的應用程式： [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **權杖簽署憑證**<p>IdP 會使用憑證的私密金鑰來簽署已發行的權杖。 它會驗證權杖是否來自應用程式設定要信任的相同 IdP。| 在 AD FS 管理的 [憑證] 之下可找到 AD FS 權杖簽署憑證。| 在應用程式的 [ **單一登入] 屬性** 的 [標頭 **SAML 簽署憑證**] 底下的 Azure 入口網站中找到它。 您可以在該處下載憑證以便上傳至應用程式。  <p>如果應用程式有一個以上的憑證，您可以在同盟中繼資料 XML 檔案中找到所有憑證。 |
| **識別碼/「簽發者」**<p>從應用程式觀點來看，IdP 的識別碼 (有時稱為「簽發者識別碼」 ) 。<p>在 SAML 權杖中，此值會顯示為簽發者元素。| AD FS 的識別碼通常是 [服務] 下的 [AD FS 管理] 中的同盟服務識別碼， **> 編輯同盟服務屬性**。 例如：`http://fs.contoso.com/adfs/services/trust`| 將 {租使用者識別碼} 取代為您的租使用者識別碼。<p>HTTPs： \/ /sts.windows.net/{tenant-id}/ |
| **IdP 同盟中繼資料**<p>IdP 公開可用同盟中繼資料的位置。 (有些應用程式會使用同盟中繼資料，作為系統管理員個別設定 URL、識別碼和權杖簽署憑證的替代方式)。| 在 [服務 > 端點] 下的 [AD FS 管理] 中，尋找 AD FS 同盟中繼資料 URL **> 中繼資料 > 類型：同盟中繼資料**。 例如：`https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD 的對應值會遵循此模式 [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) 。 將 {TenantDomainName} 取代為您租使用者的名稱，格式為 "contoso.onmicrosoft.com"。   <p>如需詳細資訊，請參閱[同盟中繼資料](../azuread-dev/azure-ad-federation-metadata.md)。 |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>代表 Azure AD 中 AD FS 安全性原則

將您的應用程式驗證移至 Azure AD 時，請從現有的安全性原則建立對應到 Azure AD 中可用的相等或替代變數。 確保這些對應可以完成，同時符合您的應用程式擁有者所需的安全性標準，會讓其餘的應用程式遷移作業變得更容易。

針對每個規則類型和其範例，我們建議您在這裡建議規則在 AD FS、AD FS 規則語言對等程式碼中的外觀，以及 Azure AD 中的對應。

### <a name="map-authorization-rules"></a>對應授權規則

以下是 AD FS 中授權規則類型的範例，以及如何將它們對應至 Azure AD：

#### <a name="example-1-permit-access-to-all-users"></a>範例1：允許存取所有使用者

允許對所有使用者的存取權在 AD FS 中看起來像這樣：

![螢幕擷取畫面顯示 [以 SAML 設定單一 Sign-On] 對話方塊。](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


這會以下列其中一種方式對應至 Azure AD：

在 [ [Azure 入口網站](https://portal.azure.com/)：
* 選項1：設定不需要的使用者指派 ![編輯 SaaS 應用程式的存取控制原則 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    請注意，將 [需要使用者指派] 參數設定為 [是] 時，需要將使用者指派給應用程式才能取得存取權。 當設定為 [否] 時，所有使用者都有存取權。 此參數不會控制我的應用程式體驗中的使用者所顯示的內容。


* 選項2：在 [使用者和群組] 索引標籤中，將您的應用程式指派給「所有使用者」自動群組。 <p>
您必須在 Azure AD 租使用者中 [啟用動態群組](../enterprise-users/groups-create-rule.md) ，才能使用預設的 [所有使用者] 群組。

   ![Azure AD 中的 SaaS 應用程式 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>範例2：明確允許群組

AD FS 中的明確群組授權：


![螢幕擷取畫面：顯示 [允許網域管理員宣告] 規則的 [編輯規則] 對話方塊。](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


這是規則對應至 Azure AD 的方式：

在 [Azure 入口網站](https://portal.azure.com/)中，您會先 [建立一個](../fundamentals/active-directory-groups-create-azure-portal.md) 對應至 AD FS 使用者群組的使用者群組，然後將應用程式許可權指派給該群組：

![新增指派 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>範例3：授權特定使用者

AD FS 中的明確使用者授權：

![螢幕擷取畫面顯示 [允許網域系統管理員宣告] 規則的 [編輯規則] 對話方塊，其中包含 [主要 S I D] 的 [輸入宣告類型]。](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

這是規則對應至 Azure AD 的方式：

在 [Azure 入口網站](https://portal.azure.com/)中，透過應用程式的 [新增指派] 索引標籤，將使用者新增至應用程式，如下所示：

![Azure 中的 SaaS 應用程式 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>對應 Multi-Factor Authentication 規則

Multi-Factor Authentication 的內部部署 [ (MFA) ](../authentication/concept-mfa-howitworks.md) 和 AD FS 在遷移之後仍會運作，因為您已與 AD FS 同盟。 不過，請考慮遷移至 Azure 的內建 MFA 功能，這些功能系結至 Azure AD 的條件式存取工作流程。

以下是 AD FS 中的 MFA 規則類型範例，以及如何根據不同條件將它們對應至 Azure AD：

AD FS 中的 MFA 規則設定：

![螢幕擷取畫面顯示 Azure 入口網站中的 Azure A D 的條件。](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>範例1：根據使用者/群組強制執行 MFA

使用者/群組選取器是一項規則，可讓您在每個群組上強制執行 MFA (群組 SID) 或每位使用者 (主要 SID) 基礎。 除了使用者/群組指派以外，AD FS MFA 設定 UI 中的所有其他核取方塊，都是在強制執行使用者/群組規則之後評估的額外規則。


在 Azure AD 中指定使用者或群組的 MFA 規則：

1. 建立 [新的條件式存取原則](../authentication/tutorial-enable-azure-mfa.md?bc=%252fazure%252factive-directory%252fconditional-access%252fbreadcrumb%252ftoc.json&toc=%252fazure%252factive-directory%252fconditional-access%252ftoc.json)。

2. 選取 [指派]  。 將您想要在其上強制執行 MFA 的使用者 (s) 或群組)  (。

3. 設定 **存取控制** 選項，如下所示：

![螢幕擷取畫面顯示可授與存取權的 [授與] 窗格。](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>範例2：針對未註冊的裝置強制執行 MFA

在 Azure AD 中指定未註冊裝置的 MFA 規則：

1. 建立 [新的條件式存取原則](../authentication/tutorial-enable-azure-mfa.md?bc=%252fazure%252factive-directory%252fconditional-access%252fbreadcrumb%252ftoc.json&toc=%252fazure%252factive-directory%252fconditional-access%252ftoc.json)。

2. 將 **指派** 設定為 [ **所有使用者**]。

3. 設定 **存取控制** 選項，如下所示：

![螢幕擷取畫面顯示 [授與] 窗格，您可以在其中授與存取權並指定其他限制。](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


當您將 [針對多個控制項] 選項設定為需要其中一個選取的控制項時，就表示如果使用者完成了核取方塊所指定的任一個條件，他們就會被授與您應用程式的存取權。

#### <a name="example-3-enforce-mfa-based-on-location"></a>範例3：根據位置強制執行 MFA

根據使用者在 Azure AD 中的位置指定 MFA 規則：

1. 建立 [新的條件式存取原則](../authentication/tutorial-enable-azure-mfa.md?bc=%252fazure%252factive-directory%252fconditional-access%252fbreadcrumb%252ftoc.json&toc=%252fazure%252factive-directory%252fconditional-access%252ftoc.json)。

1. 將 **指派** 設定為 [ **所有使用者**]。

1. [在 Azure AD 中設定指定的位置](../reports-monitoring/quickstart-configure-named-locations.md) ，否則會信任您公司網路內部的同盟。

1. 設定 **條件規則** ，以指定您想要強制執行 MFA 的位置。

![螢幕擷取畫面：顯示條件規則的 [位置] 窗格。](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 設定 **存取控制** 選項，如下所示：


![地圖存取控制原則](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>對應發出屬性作為宣告規則

以下是在 AD FS 中對應屬性的範例：

![螢幕擷取畫面顯示 [編輯規則] 對話方塊，可發出屬性作為宣告。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


這是規則對應至 Azure AD 的方式：

在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [ **企業應用程式**]、[ **單一登入**]，然後新增 **SAML 權杖屬性** ，如下所示：

![螢幕擷取畫面顯示您企業應用程式的單一登入頁面。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>對應內建存取控制原則

AD FS 2016 有數個內建存取控制原則，您可以從中選擇：

![Azure AD 內建的存取控制](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


若要在 Azure AD 中執行內建原則，您可以使用 [新的條件式存取原則](../authentication/tutorial-enable-azure-mfa.md?bc=%252fazure%252factive-directory%252fconditional-access%252fbreadcrumb%252ftoc.json&toc=%252fazure%252factive-directory%252fconditional-access%252ftoc.json) 並設定存取控制，也可以使用 AD FS 2016 中的自訂原則設計工具來設定存取控制原則。 「規則編輯器」有一份完整的「允許」和「除外」選項，可協助您進行所有種類的排列。

![Azure AD 存取控制原則](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



在此表中，我們列出了一些實用的 [允許] 和 [除外] 選項，以及它們如何對應至 Azure AD。


| 選項 | 如何在 Azure AD 中設定允許選項？| 如何在 Azure AD 中設定 Except 選項？ |
| - | - | - |
| 從特定網路| 對應至 Azure AD 中的[命名位置](../reports-monitoring/quickstart-configure-named-locations.md)| 針對 [信任的位置](../conditional-access/location-condition.md)使用 [**排除**] 選項 |
| 從特定群組| [設定使用者/群組指派](./assign-user-or-group-access-portal.md)| 在 [使用者和群組] 中使用 [ **排除** ] 選項 |
| 從具有特定信任層級的裝置| 從 [指派-> 條件] 下的 [裝置狀態] 控制項設定此設定| 使用 [裝置狀態條件] 下的 [ **排除** ] 選項，並包含 **所有裝置** |
| 使用要求中的特定宣告| 無法遷移此設定| 無法遷移此設定 |


如何在 Azure 入口網站中設定受信任位置的排除選項的範例：

![對應存取控制原則的螢幕擷取畫面](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>將使用者從 AD FS 轉換為 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>同步 Azure AD 中的 AD FS 群組

當您對應授權規則時，使用 AD FS 進行驗證的應用程式可能會使用 Active Directory 群組來取得許可權。 在這種情況下，請使用 [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) 將這些群組與 Azure AD 同步處理，然後再遷移應用程式。 在遷移之前，請務必先驗證這些群組和成員資格，讓您可以在應用程式遷移時授與相同使用者的存取權。

如需詳細資訊，請參閱 [使用從 Active Directory 同步處理的群組屬性的必要條件](../hybrid/how-to-connect-fed-group-claims.md)。

### <a name="setup-user-self-provisioning"></a>設定使用者自我布建

某些 SaaS 應用程式支援在使用者第一次登入應用程式時自行布建使用者的能力。 在 Azure Active Directory (Azure AD) 中，應用程式佈建一詞是指在使用者需要存取的雲端 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式中，自動建立使用者身分識別和角色。 已遷移的使用者在 SaaS 應用程式中已經有一個帳戶。 您必須布建在遷移後新增的任何新使用者。 在應用程式遷移之後，測試 [SaaS 應用](../app-provisioning/user-provisioning.md) 程式布建。

### <a name="sync-external-users-in-azure-ad"></a>同步處理 Azure AD 中的外部使用者

您現有的外部使用者可能會以兩種主要方式在 AD FS 內進行設定：

#### <a name="external-users-with-a-local-account-within-your-organization"></a>您組織內具有本機帳戶的外部使用者

您將繼續使用這些帳戶的方式，與您的內部使用者帳戶運作方式相同。 這些外部使用者帳戶在您的組織內有一個主體名稱，雖然帳戶的電子郵件可能指向外部。 當您進行遷移時，可以透過在有這類身分識別可供使用時，藉由將這些使用者遷移至使用自己的公司身分識別，來利用 [AZURE AD B2B](../external-identities/what-is-b2b.md) 提供的優點。 這可簡化這些使用者的登入程式，因為他們通常是使用自己的公司登入登入。 您組織的系統管理也會分階段減緩，而不再需要管理外部使用者的帳戶。

#### <a name="federated-external-identities"></a>同盟外部身分識別

如果您目前正在與外部組織聯盟，您有幾種方法可以採取：

* [在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../external-identities/add-users-administrator.md)共同作業使用者。 您可以主動將 B2B 共同作業邀請從 Azure AD 系統管理入口網站傳送給合作夥伴組織，讓個別成員繼續使用他們所用的應用程式和資產。

* [建立自助式 b2b 註冊工作流程，此工作流程](../external-identities/self-service-portal.md) 會使用 B2B 邀請 API 來為您的夥伴組織中的個別使用者產生要求。

無論您現有的外部使用者的設定方式為何，他們可能會有與其帳戶相關聯的許可權，不論是群組成員資格或特定許可權。 評估是否需要遷移或清除這些許可權。 您組織內代表外部使用者的帳戶必須在使用者遷移至外部身分識別之後停用。 遷移程式應與您的商務合作夥伴討論，因為它們可能會中斷連接到您資源的能力。

## <a name="migrate-and-test-your-apps"></a>遷移及測試您的應用程式

遵循本文中詳述的遷移程式。

然後移至 [Azure 入口網站](https://aad.portal.azure.com/) ，以測試遷移是否成功。 請遵循下列指示：
1. 從清單中選取 [**企業應用** 程式  >  **所有應用程式**]，並尋找您的應用程式。

1. 選取 [**管理**  >  **使用者和群組**]，將至少一個使用者或群組指派給應用程式。

1. 選取 [**管理**  >  **條件式存取**]。 檢查您的原則清單，並確保您不會封鎖具有 [條件式存取原則](../conditional-access/overview.md)的應用程式存取。

根據您設定應用程式的方式，確認 SSO 是否正常運作。

| 驗證類型| 測試 |
| - | - |
| OAuth/OpenID Connect| 選取 [ **企業應用程式] > 許可權** ，並確定您已同意應用程式在您的組織中用於應用程式的使用者設定。
‎ |
| 以 SAML 為基礎的 SSO| 使用 [**單一登入**] 下的 [[測試 SAML 設定](./debug-saml-sso-issues.md)] 按鈕。
‎ |
| Password-Based SSO| 下載並安裝[MyApps Secure Sign](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [in 擴充](../user-help/my-apps-portal-end-user-access.md)功能。 此延伸模組可協助您啟動任何組織的雲端應用程式，而這些應用程式需要您使用 SSO 進程。
‎ |
| 應用程式 Proxy| 確定您的連接器正在執行，並已指派給您的應用程式。 請造訪 [應用程式 Proxy 疑難排解指南](./application-proxy-troubleshoot.md) ，以取得進一步的協助。
‎ |

> [!NOTE]
> 舊 AD FS 環境中的 cookie 仍會持續存在於使用者的電腦上。 這些 cookie 可能會導致遷移問題，因為使用者可能會被導向至舊的 AD FS 登入環境，而不是新的 Azure AD 登入。 您可能需要手動或使用腳本來清除使用者瀏覽器 cookie。 您也可以使用 System Center Configuration Manager 或類似的平臺。

### <a name="troubleshoot"></a>疑難排解

如果已遷移的應用程式測試有任何錯誤，則可能會先進行疑難排解，再回到現有的 AD FS 信賴憑證者。 瞭解 [如何對 Azure Active Directory 中的應用程式進行 SAML 型單一登入](./debug-saml-sso-issues.md)的偵測。

### <a name="rollback-migration"></a>復原遷移

如果遷移失敗，建議您將現有的信賴憑證者保留在 AD FS 伺服器上，並移除對信賴憑證者的存取權。 這可讓您在部署期間視需要快速回復。

### <a name="end-user-communication"></a>終端使用者通訊

雖然規劃的停機時間範圍本身可能很短，但您仍應規劃將這些時間範圍主動傳達給員工，同時讓 AD FS 剪下 Azure AD。 確定您的應用程式體驗有意見反應按鈕，或您的技術服務人員的指標有問題。

部署完成後，您就可以傳送通訊，通知使用者部署成功，並提醒他們必須採取的任何新步驟。

* 指示使用者使用 [我的應用程式](https://myapps.microsoft.com) 來存取所有已遷移的應用程式。

* 提醒使用者可能需要更新其 MFA 設定。

* 如果已部署 Self-Service 密碼重設，使用者可能需要更新或驗證其驗證方法。 請參閱 [MFA](https://aka.ms/mfatemplates) 和 [SSPR](https://aka.ms/ssprtemplates) 終端使用者通訊範本。

與外部使用者的通訊：此使用者群組通常會在問題發生時受到最嚴重的影響。 如果您的安全性狀態為外部夥伴指定一組不同的條件式存取規則或風險設定檔，則更是如此。 請確定外部夥伴知道雲端遷移排程，並有時間範圍，建議他們參與試驗部署，以測試外部共同作業的獨特流程。 最後，請確定他們有辦法在發生中斷問題時存取您的技術服務人員。

## <a name="next-steps"></a>後續步驟

讀取  [將應用程式驗證遷移至 Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
設定 [條件式存取](../conditional-access/overview.md) 和 [MFA](../authentication/concept-mfa-howitworks.md)

試用逐步程式碼範例：[適用于開發人員的 Azure AD 應用程式遷移腳本 AD FS](https://aka.ms/adfsplaybook)