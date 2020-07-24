---
title: Atlassian Jira/Confluence 管理員指南 - Azure Active Directory| Microsoft Docs
description: 搭配 Azure Active Directory (Azure AD) 使用 Atlassian Jira 和 Confluence 的管理員指南。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5884685f12c4697558a008287f446e52e5105d3b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016780"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>適用於 Azure Active Directory 的 Atlassian Jira 和 Confluence 管理員指南

## <a name="overview"></a>概觀

Azure Active Directory (Azure AD) 單一登入 (SSO) 外掛程式可讓 Microsoft Azure AD 客戶使用工作或學校帳戶登入 Atlassian Jira 和 Confluence 伺服器架構的產品。 它會實作以 SAML 2.0 為基礎的 SSO。

## <a name="how-it-works"></a>運作方式

當使用者要登入 Atlassian Jira 或 Confluence 應用程式時，他們會在登入頁面上看到 [以 Azure AD 登入]**** 按鈕。 使用者選取此按鈕後，系被會要求他們使用 Azure AD 組織登入頁面 (也就是工作或學校帳戶) 登入。

使用者經過驗證後，應該就能登入應用程式。 如果他們已經使用工作或學校帳戶的識別碼與密碼驗證過，便可以直接登入應用程式。 

此登入在 Jira 和 Confluence 皆有效。 如果使用者登入 Jira 應用程式，而且在相同瀏覽器視窗中開啟 Confluence，就不需要再對其他應用程式提供認證。 

使用者也可以使用工作或學校帳戶透過 [我的應用程式] 存取 Atlassian 產品。 系統應該會讓他們登入，不會再要求認證。

> [!NOTE]
> 使用者佈建不是透過此外掛程式進行。

## <a name="audience"></a>對象

可使用此外掛程式啟用 Azure AD SSO 的 Jira 和 Confluence 管理員。

## <a name="assumptions"></a>假設

* Jira 和 Confluence 執行個體已啟用 HTTPS。
* 已在 Jira 或 Confluence 中建立使用者。
* 已在 Jira 或 Confluence 中指派使用者角色。
* 管理員可以取得設定外掛程式所需的資訊。
* 從公司網路外部也可以使用 Jira 或 Confluence。
* 外掛程式只能在內部部署版本的 Jira 和 Confluence 中運作。

## <a name="prerequisites"></a>先決條件

安裝外掛程式之前，請注意下列資訊：

* Jira 和 Confluence 是安裝在 64 位元版本的 Windows 上。
* Jira 和 Confluence 版本已啟用 HTTPS。
* Jira 和 Confluence 都可以在網際網路上取得。
* Jira 和 Confluence 中已設定管理員認證。
* Azure AD 中已設定管理員認證。
* Jira 和 Confluence 已停用 WebSudo。

## <a name="supported-versions-of-jira-and-confluence"></a>支援的 Jira 和 Confluence 版本

此外掛程式支援下列 Jira 和 Confluence 版本：

* JIRA Core 和 JIRA Software：6.0 到 7.12
* JIRA Service Desk：3.0.0 到 3.5.0
* JIRA 也支援 5.2。 如需詳細資訊，請按一下[適用於 JIRA 5.2 的 Microsoft Azure Active Directory 單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence：5.0 到 5.10
* Confluence：6.0.1
* Confluence：6.1.1
* Confluence：6.2.1
* Confluence：6.3.4
* Confluence：6.4.0
* Confluence：6.5.0
* Confluence：6.6.2
* Confluence：6.7.0
* Confluence：6.8.1
* Confluence：6.9.0
* Confluence：6.10.0
* Confluence：6.11.0
* Confluence：6.12.0

## <a name="installation"></a>安裝

若要安裝外掛程式，請遵循下列步驟：

1. 以管理員身分登入 Jira 或 Confluence 執行個體。

2. 移至 Jira/Confluence 管理主控台，選取 [附加元件]****。

3. 從 microsoft 下載中心下載適用于 Jira 的 microsoft [saml sso 外掛程式](https://www.microsoft.com/download/details.aspx?id=56506) /  [Confluence 的 microsoft saml sso 外掛程式](https://www.microsoft.com/download/details.aspx?id=56503)。

   搜尋結果中會顯示適當的外掛程式版本。

4. 選取外掛程式，然後通用外掛程式管理員 (UPM) 會安裝它。

安裝外掛程式之後，它會出現在 [管理附加元件]**** 的 [使用者安裝的附加元件]**** 區段。

## <a name="plug-in-configuration"></a>外掛程式設定

您必須先設定外掛程式，才能開始使用。 選取外掛程式，選取 [設定]**** 按鈕，並提供組態詳細資料。

下圖是 Jira 和 Confluence 中的設定畫面：

![外掛程式設定畫面](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **中繼資料 URL**：用來從 Azure AD 取得同盟中繼資料的 URL。

* **識別碼**：Azure AD 用來驗證要求來源的 URL。 此識別碼對應至 Azure AD 中的 **Identifier** 元素。 外掛程式會自動將此 URL 衍生為 HTTPs:// *\<domain:port>* /。

* **回覆 URL**：識別提供者 (IdP) 中的回覆 URL，用於起始 SAML 登入。 此回覆 URL 對應至 Azure AD 中的 **Reply URL** 元素。 外掛程式會自動將此 URL 衍生為 HTTPs:// *\<domain:port>* /plugins/servlet/saml/auth。

* **登入 URL**：識別提供者 (IdP) 中的登入 URL，用於起始 SAML 登入。 此登入 URL 對應至 Azure AD 中的 **Sign On** 元素。 外掛程式會自動將此 URL 衍生為 HTTPs:// *\<domain:port>* /plugins/servlet/saml/auth。

* **IdP 實體識別碼**：IdP 所使用的實體識別碼。 系統解析中繼資料 URL 時就會填入這個方塊。

* **登入 URL**：來自 IdP 的登入 URL。 系統解析中繼資料 URL 時，Azure AD 就會填入這個方塊。

* **登出 URL**：來自 IdP 的登出 URL。 系統解析中繼資料 URL 時，Azure AD 就會填入這個方塊。

* **X.509 憑證**：IdP 的 X.509 憑證。 系統解析中繼資料 URL 時，Azure AD 就會填入這個方塊。

* **登入按鈕名稱**：輸入您組織要讓使用者在登入頁面上看到的登入按鈕名稱。

* **SAML 使用者識別碼位置**：SAML 回應中預期的 Jira 或 Confluence 使用者識別碼位置。 可能是在 **NameID** 或自訂屬性名稱中。

* **屬性名稱**：預期出現使用者識別碼的屬性名稱。

* **啟用主領域探索**：如果公司使用 Active Directory 同盟服務 (AD FS) 式登入，就要選擇此選項。

* **網域名稱**：採用 AD FS 式登入時的網域名稱。

* **啟用單一登出**：如果您想要在使用者登出 Jira 或 Confluence 時登出 Azure AD，就要選擇此選項。

## <a name="troubleshooting"></a>疑難排解

* **您遇到多個憑證錯誤**：登入 Azure AD，移除應用程式可使用的多個憑證。 確定只剩一個憑證。

* **Azure AD 中的憑證即將到期**：附加元件負責憑證的自動變換。 當憑證快到期時，應將新的憑證標示為使用中，並刪除未使用的憑證。 當使用者嘗試在此情況下登入 Jira 時，外掛程式會擷取並儲存新的憑證。

* **您想要停用 WebSudo (停用安全的系統管理員工作階段)**：

  * 針對 Jira，預設會啟用安全的系統管理員工作階段 (也就是確認密碼後才能存取管理功能)。 如果您想要在 Jira 執行個體中移除這個功能，在 jira-config.propertie 檔案中指定以下這一行：`jira.websudo.is.disabled = true`

  * 針對 Confluence，請遵循 [Confluence支援網站](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)上的步驟。

* **中繼資料 URL 應該填入的欄位未填入資料**：

  * 檢查 URL 是否正確。 檢查是否已對應正確的租用戶和應用程式識別碼。

  * 在瀏覽器中輸入 URL，看看是否收到同盟中繼資料 XML。

* **發生內部伺服器錯誤**：檢閱安裝記錄目錄中的記錄。 如果您是在使用者嘗試使用 Azure AD SSO 登入時遇到錯誤，您可以將記錄提供給支援小組。

* **使用者嘗試登入時發生「找不到使用者識別碼」錯誤**：在 Jira 或 Confluence 中建立使用者識別碼。

* **Azure AD 中發生「找不到應用程式」錯誤**：檢查是否以正確 URL 對應至 Azure AD 中的應用程式。

* **您需要支援**：請連繫 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 小組會在營業時間 24-48 小時內回應。

  您也可以透過 Azure 入口網站管道向 Microsoft 發出支援票證。

## <a name="plug-in-faq"></a>外掛程式常見問題集

如果您有關於此外掛程式的任何查詢，請參閱以下的常見問題集。

### <a name="what-does-the-plug-in-do"></a>這個外掛程式能做什麼？

此外掛程式提供適用於 Atlassian Jira (包括 Jira 核心、Jira 軟體、Jira 服務台) 和 Confluence 內部部署軟體的單一登入 (SSO) 功能。 此外掛程式搭配 Azure Active Directory (Azure AD) 使用，成為識別提供者 (IdP)。

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>此外掛程式與哪些 Atlassian 產品搭配使用？

此外掛程式與內部部署版本的 Jira 和 Confluence 搭配使用。

### <a name="does-the-plug-in-work-on-cloud-versions"></a>此外掛程式可否在雲端版本上運作？

不可以。 此外掛程式僅支援內部部署版本的 Jira 和 Confluence。

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>此外掛程式支援哪些 Jira 和 Confluence 版本？

此外掛程式支援這些版本：

* JIRA Core 和 JIRA Software：6.0 到 7.12
* JIRA Service Desk：3.0.0 到 3.5.0
* JIRA 也支援 5.2。 如需詳細資訊，請按一下[適用於 JIRA 5.2 的 Microsoft Azure Active Directory 單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence：5.0 到 5.10
* Confluence：6.0.1
* Confluence：6.1.1
* Confluence：6.2.1
* Confluence：6.3.4
* Confluence：6.4.0
* Confluence：6.5.0
* Confluence：6.6.2
* Confluence：6.7.0
* Confluence：6.8.1
* Confluence：6.9.0
* Confluence：6.10.0
* Confluence：6.11.0
* Confluence：6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>此外掛程式是免費還是需付費？

是免費的附加元件。

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>部署此外掛程式之後是否需要重新啟動 Jira 或 Confluence？

不需要重新啟動。 您可以立即開始使用此外掛程式。

### <a name="how-do-i-get-support-for-the-plug-in"></a>如何取得此外掛程式的支援？

您可以連絡 [Azure AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)以取得此外掛程式所需的任何支援。 小組會在營業時間 24-48 小時內回應。

您也可以透過 Azure 入口網站管道向 Microsoft 發出支援票證。

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>此外掛程式是否可用於安裝在 Mac 或 Ubuntu 上的 Jira 和 Confluence？

我們只在安裝於 64 位元 Windows 伺服器的 Jira 和 Confluence 上測試過這個外掛程式。

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>此外掛程式是否可與 Azure AD 以外的 IdP 搭配運作？

不可以。 它只能與 Azure AD 搭配運作。

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>此外掛程式可以與哪個版本的 SAML 搭配運作？

可以與 SAML 2.0 搭配運作。

### <a name="does-the-plug-in-do-user-provisioning"></a>此外掛程式會進行使用者佈建嗎？

不可以。 此外掛程式只提供以 SAML 2.0 為基礎的 SSO。 必須在使用 SSO 登入之前，於應用程式中佈建使用者。

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>此外掛程式支援叢集版本的 Jira 和 Confluence 嗎？

不可以。 此外掛程式與內部部署版本的 Jira 和 Confluence 搭配使用。

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>此外掛程式能與 HTTP 版本的 Jira 和 Confluence 搭配使用嗎？

不可以。 此外掛程式只能與已啟用 HTTPS 的安裝搭配運作。
