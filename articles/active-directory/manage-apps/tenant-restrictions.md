---
title: 使用租用戶限制來管理對 SaaS 應用程式的存取 - Azure AD
description: 如何使用租用戶限制以根據使用者的 Azure AD 租用戶來管理可存取應用程式的使用者。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce96eb5e91ccc4cb9f69711f9e6fd8fd59ce65bc
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669935"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租用戶限制來管理對 SaaS 雲端應用程式的存取

強調安全性的大型組織想要移至 Microsoft 365 之類的雲端服務，但必須知道其使用者只能存取核准的資源。 傳統上，當公司想要管理存取時，會限制網域名稱或 IP 位址。 在軟體即服務 (或 SaaS) 應用程式裝載於公用雲端而在共用網域名稱 (例如 [outlook.office.com](https://outlook.office.com/) 和 [login.microsoftonline.com](https://login.microsoftonline.com/)) 上執行的環境中，這個方法行不通。 封鎖這些位址會讓使用者無法存取整個網路上的 Outlook，而不僅是限制他們只能存取已核准的身分識別和資源。

Azure Active Directory (Azure AD) 對此查問所提出的解決方案是稱為租用戶限制的功能。 若使用租用戶限制，組織可以應用程式用於單一登入的 Azure AD 租用戶為基礎，來控制對 SaaS 雲端應用程式的存取。 例如，您可能會想要允許存取組織的 Microsoft 365 應用程式，同時防止存取這些相同應用程式的其他組織實例。  

若使用租用戶限制，組織可以指定允許其使用者存取的租用戶清單。 Azure AD 接著便可只授與對這些已允許之租用戶的存取權。

本文著重于 Microsoft 365 的租使用者限制，但此功能應該適用于使用新式驗證通訊協定搭配 Azure AD 進行單一登入的任何 SaaS 雲端應用程式。 如果您使用的 SaaS 應用程式與 Microsoft 365 所使用的租使用者不同 Azure AD 租使用者，請確定允許所有必要的租使用者。 如需有關 SaaS 雲端應用程式的詳細資訊，請參閱 [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)。

## <a name="how-it-works"></a>運作方式

整體解決方案包含下列元件：

1. **Azure AD** ：如果 `Restrict-Access-To-Tenants: <permitted tenant list>` 存在，Azure AD 便只會針對已允許的租用戶發出安全性權杖。

2. **內部部署 Proxy 伺服器基礎結構** ：此基礎結構是能夠進行傳輸層安全性 (TLS) 檢查的 Proxy 裝置。 您必須將 Proxy 設定為會將包含已允許租用戶清單的標頭插入至目的地為 Azure AD 的流量。

3. **用戶端軟體** ：為了支援租用戶限制，用戶端軟體必須直接從 Azure AD 要求權杖，以便 Proxy 基礎結構能夠攔截流量。 以瀏覽器為基礎的 Microsoft 365 應用程式目前支援租使用者限制，如同使用新式驗證的 Office 用戶端 (例如 OAuth 2.0) 。

4. **新式驗證** ：雲端服務必須使用新式驗證，才能使用租用戶限制並封鎖對所有非允許租用戶的存取。 您必須將 Microsoft 365 的雲端服務設定為預設使用新式驗證通訊協定。 如需有關新式驗證 Microsoft 365 支援的最新資訊，請參閱 [更新的 Office 365 新式驗證](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

下圖說明概略的流量流程。 租使用者限制只需要對 Azure AD 的流量進行 TLS 檢查，而不需要對 Microsoft 365 的雲端服務進行 TLS 檢查。 此區別很重要，因為傳送到 Azure AD 以進行驗證的流量通常比傳送到 SaaS 應用程式 (例如 Exchange Online 和 SharePoint Online) 的流量少很多。

![租用戶限制流量流程 - 圖表](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>設定租用戶限制

開始使用租用戶限制的步驟有兩個。 第一步，確定您的用戶端可以連線到正確的位址。 第二步，設定您的 Proxy 基礎結構。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 位址

若要使用租用戶限制，您的用戶端必須要能夠連線到下列 Azure AD URL 來進行驗證：[login.microsoftonline.com](https://login.microsoftonline.com/)、[login.microsoft.com](https://login.microsoft.com/) 及 [login.windows.net](https://login.windows.net/)。 此外，若要存取 Office 365，您的用戶端必須也要能夠連線到 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所定義的完整網域名稱 (FQDN)、URL 和 IP 位址。 

### <a name="proxy-configuration-and-requirements"></a>Proxy 組態和需求

以下是透過 Proxy 基礎結構啟用租用戶限制的必要設定。 本指導方針是通用的，因此如需了解特定的實作步驟，您應該參考您 Proxy 廠商的文件。

#### <a name="prerequisites"></a>Prerequisites

- Proxy 必須要能夠執行 TLS 攔截、HTTP 標頭插入，以及使用 FQDN/URL 來篩選目的地。

- 用戶端必須信任 Proxy 針對 TLS 通訊所出示的憑證鏈結。 例如，如果使用來自內部[公開金鑰基礎結構 (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) 的憑證，就必須信任內部發行的根憑證授權單位憑證。

- 這項功能包含在 Microsoft 365 訂用帳戶中，但如果您想要使用「租使用者限制」來控制其他 SaaS 應用程式的存取權，則需要 Azure AD Premium 1 個授權。

#### <a name="configuration"></a>組態

針對每個傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的連入要求，請插入兩個 HTTP 標頭： *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 。

> [!NOTE]
> 設定 SSL 攔截和標頭插入時，請確定 https://device.login.microsoftonline.com 已排除流量。 此 URL 用於裝置驗證，且執行 TLS 中斷和檢查可能會干擾用戶端憑證驗證，這可能會導致裝置註冊和裝置型條件式存取的問題。



這些標頭應該包含下列元素︰

- 針對 *限制存取* 租使用者，請使用的值 \<permitted tenant list\> ，這是您想要允許使用者存取的租使用者清單（以逗號分隔）。 任何向租使用者註冊的網域都可以用來識別此清單中的租使用者，以及目錄識別碼本身。 如需描述租使用者的三種方式的範例，允許 Contoso、Fabrikam 和 Microsoft 的名稱/值組看起來像這樣： `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- 就 *Restrict-Access-Context* 而言，請使用單一目錄識別碼的值，用來宣告設定租用戶限制的是哪一個租用戶。 例如，若要將 Contoso 宣告為設定「租使用者限制」原則的租使用者，名稱/值組看起來會像這樣： `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` 。  您 **必須** 在此位置使用您自己的目錄識別碼。

> [!TIP]
> 您可以在 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)中找到您的目錄識別碼。 請以系統管理員身分登入，選取 [Azure Active Directory]，然後選取 [屬性]。 
>
> 若要驗證目錄識別碼或功能變數名稱參考相同的租使用者，請使用該識別碼或網域取代 <tenant> 此 URL： `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` 。  如果具有網域和識別碼的結果相同，則會參考相同的租使用者。 

若要防止使用者插入自己的含有非核准租用戶的 HTTP 標頭，Proxy 就必須在連入要求中已經有 *Restrict-Access-To-Tenants* 標頭時取代此標頭。

必須強制用戶端針對所有傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的要求使用 Proxy。 例如，如果使用 PAC 檔案來指示用戶端使用 Proxy，使用者應該要不能夠編輯或停用 PAC 檔案。

> [!NOTE]
> 請勿在 proxy 設定中的 *. login.microsoftonline.com 下包含子域。 這麼做會包含 device.login.microsoftonline.com，而且可能會干擾在裝置註冊和裝置型條件式存取案例中使用的用戶端憑證驗證。 設定您的 proxy 伺服器，以從 TLS 中斷和檢查和標頭插入排除 device.login.microsoftonline.com。

## <a name="the-user-experience"></a>使用者體驗

此節描述使用者和系統管理員的體驗。

### <a name="end-user-experience"></a>使用者體驗

範例使用者位於 Contoso 網路上，但正在嘗試存取 Fabrikam 的共用 SaaS 應用程式執行個體 (例如 Outlook Online)。 如果 Fabrikam 是 Contoso 執行個體的非允許租用戶，則使用者會看到「拒絕存取」訊息，這表示您嘗試存取的資源屬於您的 IT 部門未核准的組織。

### <a name="admin-experience"></a>管理員體驗

雖然租用戶限制的設定是在公司 Proxy 基礎結構上完成設定，但系統管理員可以直接在 Azure 入口網站中存取租用戶限制報表。 檢視報表：

1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [Azure Active Directory 管理中心] 儀表板隨即出現。

2. 在左窗格中，選取 [Azure Active Directory]。 [Azure Active Directory 概觀] 頁面隨即顯示。

3. 在 [總覽] 頁面上，選取 [ **租使用者限制** ]。

如果將租用戶指定為 Restricted-Access-Context 租用戶，其管理員便可以使用此報表來查看因租用戶限制原則而遭封鎖的登入，包括所使用的身分識別及目標目錄識別碼。 如果設定限制的租用戶為登入的使用者租用戶或資源租用戶，則會包含登入。

> [!NOTE]
> 當位在受限制存取內容租用戶以外租用戶的使用者登入時，此報表可能會包含有限的資訊，例如目標目錄識別碼。 在此情況下，會遮罩使用者識別資訊（例如名稱和使用者主體名稱），以保護其他租使用者中的使用者資料 ( " 00000000-0000-0000-0000-00000000@domain.com " )  

與 Azure 入口網站中的其他報告相同，您可以使用篩選來指定報告的範圍。 您可以根據特定時間間隔、使用者、應用程式、用戶端或狀態進行篩選。 如果您選取 [資料行] 按鈕，則可以選擇使用下列欄位的任何組合來顯示資料：

- **使用者**
- **應用程式**
- **狀態**
- **日期**
- **日期 (UTC)** (其中 UTC 是國際標準時間)
- **MFA 驗證方法** (多重要素驗證方法)
- **MFA 驗證詳細資料** (多重要素驗證詳細資料)
- **MFA 結果**
- **IP 位址**
- **用戶端**
- **使用者名稱**
- **位置**
- **目標租用戶識別碼**

## <a name="microsoft-365-support"></a>Microsoft 365 支援

Microsoft 365 應用程式必須符合兩個準則，才能完全支援租使用者限制：

1. 所使用的用戶端支援新式驗證。
2. 啟用新式驗證作為雲端服務的預設驗證通訊協定。

如需有關哪些 Office 用戶端目前支援新式驗證的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。 該頁面也包含在特定 Exchange Online 和「商務用 Skype Online」租用戶上啟用新式驗證的指示連結。 根據預設，SharePoint Online 已啟用新式驗證。

Microsoft 365 以瀏覽器為基礎的應用程式 (Office 入口網站、Yammer、SharePoint 網站、Outlook 在 Web 上，以及其他) 目前支援租使用者限制。 豐富型用戶端 (Outlook、商務用 Skype、Word、Excel、PowerPoint 等等) 只有在使用新式驗證時才可以強制執行租用戶限制。  

支援新式驗證的 Outlook 和商務用 Skype 用戶端仍然能夠針對未啟用新式驗證的租用戶使用傳統通訊協定，有效地略過租用戶限制。 使用傳統通訊協定的應用程式如果在驗證期間連絡 login.microsoftonline.com、login.microsoft.com 或 login.windows.net，可能會遭到租用戶限制封鎖。

針對 Windows 上的 Outlook，客戶可以選擇實作可防止使用者將非已核准郵件帳戶新增至其設定檔的限制。 例如，請參閱[防止新增非預設 Exchange 帳戶](https://gpsearch.azurewebsites.net/default.aspx?ref=1)群組原則設定。

## <a name="testing"></a>測試

如果您想要先試用租用戶限制，然後才為整個組織實作此功能，則您有兩個選項：運用使用 Fiddler 這類工具的主機型方法，或是分段推出 Proxy 設定。

### <a name="fiddler-for-a-host-based-approach"></a>適用於主機型方法的 Fiddler

Fiddler 是一個免費的 Web 偵錯 Proxy，可用來擷取和修改 HTTP/HTTPS 流量，包括插入 HTTP 標頭。 若要設定 Fiddler 以測試租用戶限制，請執行下列步驟：

1. [下載並安裝 Fiddler](https://www.telerik.com/fiddler)。

2. 依照 [Fiddler 的說明文件 (英文)](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) 操作，設定 Fiddler 以將 HTTPS 流量解密。

3. 設定 Fiddler 以使用自訂規則來插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 標頭：

   1. 在「Fiddler Web 偵錯工具」中，選取 [Rules] \(規則) 功能表，然後選取 [Customize Rules] \(自訂規則) 以開啟 CustomRules 檔案。

   2. 將下列行新增至 `OnBeforeRequest` 函式的開頭。 以 \<tenant domain\> 您的租使用者所註冊的網域取代 (例如 `contoso.onmicrosoft.com`) 。 \<directory ID\>將取代為您租使用者的 AZURE AD GUID 識別碼。

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      如果您需要允許多個租用戶，請使用逗號來分隔租用戶名稱。 例如：

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. 儲存並關閉 CustomRules 檔案。

設定 Fiddler 之後，您可以移至 [File] \(檔案) 功能表並選取 [Capture Traffic] \(擷取流量)，來擷取流量。

### <a name="staged-rollout-of-proxy-settings"></a>分段推出 Proxy 設定

視您 Proxy 基礎結構的功能而定，您可能可以向使用者分段推出設定。 以下是一些可供考量的概略選項：

1. 使用 PAC 檔案將測試使用者指向測試 Proxy 基礎結構，同時又讓一般使用者繼續使用生產環境 Proxy 基礎結構。
2. 有些 Proxy 伺服器可以使用群組來支援不同的組態。

如需具體的詳細資料，請參閱您的 Proxy 伺服器文件。

## <a name="next-steps"></a>後續步驟

- 閱讀[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- 檢閱 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
