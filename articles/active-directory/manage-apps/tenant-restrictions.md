---
title: 使用租戶限制來管理對 SaaS 應用的訪問 - Azure AD
description: 如何使用租戶限制來管理哪些使用者可以基於其 Azure AD 租戶訪問應用。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481172"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租戶限制管理對 SaaS 雲應用程式的訪問

注重安全性的大型組織想要移到 Office 365 之類的雲端服務，但需要確知其使用者只能存取獲得核准的資源。 傳統上，當公司想要管理存取時，會限制網域名稱或 IP 位址。 在軟體即服務（或 SaaS）應用託管在公共雲中，在共用功能變數名稱（如[outlook.office.com](https://outlook.office.com/)和[login.microsoftonline.com）](https://login.microsoftonline.com/)上運行時，此方法失敗。 封鎖這些位址會讓使用者無法存取整個網路上的 Outlook，而不僅是限制他們只能存取已核准的身分識別和資源。

應對此挑戰的 Azure 活動目錄 （Azure AD） 解決方案是稱為租戶限制的功能。 通過租戶限制，組織可以基於應用程式用於單一登入的 Azure AD 租戶控制對 SaaS 雲應用程式的訪問。 例如，您可能想要允許使用者存取您組織的 Office 365 應用程式，但又防止他們存取其他組織的這些相同應用程式執行個體。  

使用租戶限制，組織可以指定允許其使用者訪問的租戶清單。 Azure AD 接著便可只授與對這些已允許之租用戶的存取權。

本文重點介紹 Office 365 的租戶限制，但該功能應適用于使用 Azure AD 進行單一登入的現代身份驗證協定的任何 SaaS 雲應用。 如果您使用 SaaS 應用程式搭配與 Office 365 所用租用戶不同的 Azure AD 租用戶，請務必核准所有必要的租用戶。 如需有關 SaaS 雲端應用程式的詳細資訊，請參閱 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)。

## <a name="how-it-works"></a>運作方式

整體解決方案包含下列元件：

1. **Azure AD**：`Restrict-Access-To-Tenants: <permitted tenant list>`如果存在，則 Azure AD 僅為允許的租戶頒發安全權杖。

2. **本地代理伺服器基礎結構**：此基礎結構是一種代理設備，能夠進行傳輸層安全 （TLS） 檢查。 必須配置代理，將包含允許租戶清單的標頭插入到發往 Azure AD 的流量中。

3. **用戶端軟體**：為了支援租戶限制，用戶端軟體必須直接從 Azure AD 請求權杖，以便代理基礎結構可以攔截流量。 基於瀏覽器的 Office 365 應用程式當前支援租戶限制，使用現代身份驗證（如 OAuth 2.0）的 Office 用戶端也是如此。

4. **現代身份驗證**：雲服務必須使用現代身份驗證來使用租戶限制並阻止對所有不允許的租戶的訪問。 預設情況下，必須配置 Office 365 雲服務才能使用現代身份驗證協定。 如需有關 Office 365 對新式驗證之支援的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

下圖說明概略的流量流程。 租戶限制僅要求對 Azure AD 的流量進行 TLS 檢查，而不需要對 Office 365 雲服務進行檢查。 這種區別很重要，因為對 Azure AD 進行身份驗證的流量通常比 SaaS 應用程式的流量（如連線交換和共用點連線）低得多。

![租戶限制流量流 - 圖](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>設置租戶限制

有兩個步驟可以開始使用租戶限制。 首先，確保用戶端可以連接到正確的位址。 其次，配置代理基礎結構。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 位址

要使用租戶限制，用戶端必須能夠連接到以下 Azure AD URL 進行身份驗證[：login.microsoftonline.com、login.microsoft.com](https://login.microsoftonline.com/)[login.microsoft.com](https://login.microsoft.com/)和[login.windows.net](https://login.windows.net/)。 此外，要訪問 Office 365，用戶端還必須能夠連接到[Office 365 URL 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中定義的完全限定功能變數名稱 （FQDN）、URL 和 IP 位址。 

### <a name="proxy-configuration-and-requirements"></a>Proxy 組態和需求

通過代理基礎結構啟用租戶限制需要以下配置。 本指導方針是通用的，因此如需了解特定的實作步驟，您應該參考您 Proxy 廠商的文件。

#### <a name="prerequisites"></a>Prerequisites

- 代理必須能夠使用 FQDN/URL 執行 TLS 攔截、HTTP 標頭插入和篩選目標。

- 用戶端必須信任 TLS 通信代理提供的憑證連結。 例如，如果使用來自內部[公開金鑰基礎結構 （PKI） 的](/windows/desktop/seccertenroll/public-key-infrastructure)證書，則必須信任內部頒發根憑證授權單位證書。

- 此功能包含在 Office 365 訂閱中，但如果要使用租戶限制來控制對其他 SaaS 應用的訪問，則需要 Azure AD 高級 1 許可證。

#### <a name="configuration"></a>組態

針對每個傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的連入要求，請插入兩個 HTTP 標頭：*Restrict-Access-To-Tenants* 和 *Restrict-Access-Context*。

這些標頭應該包含下列元素︰

- 對於 *"限制訪問-租戶"，* 請使用允許的\<租戶清單\>的值 ，這是要允許使用者訪問的租戶的逗號分隔清單。 與租用戶一起註冊的任何網域都可用來在此清單中識別該租用戶。 例如，若要允許存取 Contoso 和 Fabrikam 租用戶，名稱/值組會顯示如下： `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- 對於*限制訪問上下文*，使用單個目錄 ID 的值，聲明哪個租戶正在設置租戶限制。 例如，要將 Contoso 聲明為設置租戶限制策略的租戶，名稱/值對如下所示： `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 您可以在[Azure 活動目錄門戶](https://aad.portal.azure.com/)中找到目錄 ID。 請以系統管理員身分登入，選取 [Azure Active Directory]****，然後選取 [屬性]****。

為了防止使用者將自己的 HTTP 標頭插入到未經批准的租戶中，代理需要替換 *"限制訪問到租戶"* 標頭（如果傳入請求中已存在）。

必須強制用戶端針對所有傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的要求使用 Proxy。 例如，如果使用 PAC 檔指示用戶端使用代理，最終使用者不應能夠編輯或禁用 PAC 檔。

## <a name="the-user-experience"></a>使用者體驗

本節介紹最終使用者和管理員的體驗。

### <a name="end-user-experience"></a>使用者體驗

範例使用者位於 Contoso 網路上，但正在嘗試存取 Fabrikam 的共用 SaaS 應用程式執行個體 (例如 Outlook Online)。 如果 Fabrikam 是 Contoso 實例的不允許租戶，則使用者將看到一條訪問拒絕消息，該消息指出您嘗試訪問屬於 IT 部門未批准的組織的資源。

### <a name="admin-experience"></a>管理員體驗

在公司代理基礎結構上完成租戶限制的配置時，管理員可以直接存取 Azure 門戶中的租戶限制報告。 要查看報告：

1. 登錄到 Azure[活動目錄門戶](https://aad.portal.azure.com/)。 將顯示**Azure 活動目錄管理中心**儀表板。

2. 在左窗格中，選取 [Azure Active Directory]****。 將顯示 Azure 活動目錄概覽頁。

3. 在 **"其他功能**"標題中，選擇 **"租戶限制**"。

指定為受限訪問上下文租戶的租戶的管理員可以使用此報表看到由於租戶限制策略（包括使用的標識和目標目錄 ID）而阻止登錄。 如果設定限制的租用戶為登入的使用者租用戶或資源租用戶，則會包含登入。

> [!NOTE]
> 當處於受限訪問上下文租戶以外的租戶中的使用者登錄時，報告可能包含有限的資訊，如目標目錄 ID。 在這種情況下，使用者身份資訊（如名稱和使用者主體名稱）將被遮罩，以保護其他租戶中的使用者資料。

與 Azure 入口網站中的其他報告相同，您可以使用篩選來指定報告的範圍。 您可以篩選特定的時間間隔、使用者、應用程式、用戶端或狀態。 如果選擇 **"列"** 按鈕，則可以選擇顯示具有以下欄位任意組合的資料：

- **使用者**
- **應用程式**
- **狀態**
- **日期**
- **日期 （UTC）** （UTC 是協調通用時間）
- **MFA 身份驗證方法**（多重要素驗證方法）
- **MFA 身份驗證詳細資訊**（多重要素驗證詳細資訊）
- **MFA 結果**
- **IP 位址**
- **用戶端**
- **使用者**
- **位置**
- **目標租戶 ID**

## <a name="office-365-support"></a>Office 365 支援

Office 365 應用程式必須滿足兩個條件才能完全支援租戶限制：

1. 使用的用戶端支援現代身份驗證。
2. 啟用新式驗證作為雲端服務的預設驗證通訊協定。

如需有關哪些 Office 用戶端目前支援新式驗證的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。 該頁面也包含在特定 Exchange Online 和「商務用 Skype Online」租用戶上啟用新式驗證的指示連結。 預設情況下，SharePoint Online 已啟用現代身份驗證。

Office 365 基於瀏覽器的應用程式（Office 門戶、Yammer、SharePoint 網站、Web 上的 Outlook 等）當前支援租戶限制。 粗用戶端（Outlook、業務 Skype、Word、Excel、PowerPoint 等）只能在使用現代身份驗證時強制實施租戶限制。  

支援現代身份驗證的 Outlook 和 Skype 用戶端仍能夠對未啟用現代身份驗證的租戶使用舊協定，從而有效地繞過租戶限制。 如果租戶限制在身份驗證期間與login.microsoftonline.com、login.microsoft.com或login.windows.net聯繫，則可能會阻止使用舊協定的應用程式。

針對 Windows 上的 Outlook，客戶可以選擇實作可防止使用者將非已核准郵件帳戶新增至其設定檔的限制。 例如，請參閱[防止新增非預設 Exchange 帳戶](https://gpsearch.azurewebsites.net/default.aspx?ref=1)群組原則設定。

## <a name="testing"></a>測試

如果要在為整個組織實施租戶限制之前嘗試該限制，有兩個選項：使用 Fiddler 等工具的基於主機的方法，或分階段推出代理設置。

### <a name="fiddler-for-a-host-based-approach"></a>適用於主機型方法的 Fiddler

Fiddler 是一個免費的 Web 偵錯 Proxy，可用來擷取和修改 HTTP/HTTPS 流量，包括插入 HTTP 標頭。 要將 Fiddler 配置為測試租戶限制，請執行以下步驟：

1. [下載並安裝菲德爾](https://www.telerik.com/fiddler)。

2. 依照 [Fiddler 的說明文件 (英文)](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) 操作，設定 Fiddler 以將 HTTPS 流量解密。

3. 設定 Fiddler 以使用自訂規則來插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 標頭：

   1. 在「Fiddler Web 偵錯工具」中，選取 [Rules] \(規則)**** 功能表，然後選取 [Customize Rules] \(自訂規則)**** 以開啟 CustomRules 檔案。

   2. 在`OnBeforeRequest`函數的開頭添加以下行。 將\<租戶域\>替換為已註冊到租戶的域（例如。 `contoso.onmicrosoft.com` 使用您租用戶的 Azure AD GUID 識別碼來取代 \<directory ID\>。

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

設定 Fiddler 之後，您可以移至 [File] \(檔案)**** 功能表並選取 [Capture Traffic] \(擷取流量)****，來擷取流量。

### <a name="staged-rollout-of-proxy-settings"></a>分段推出 Proxy 設定

視您 Proxy 基礎結構的功能而定，您可能可以向使用者分段推出設定。 以下是一些可供考量的概略選項：

1. 使用 PAC 檔案將測試使用者指向測試 Proxy 基礎結構，同時又讓一般使用者繼續使用生產環境 Proxy 基礎結構。
2. 有些 Proxy 伺服器可以使用群組來支援不同的組態。

有關詳細資訊，請參閱代理伺服器文檔。

## <a name="next-steps"></a>後續步驟

- 閱讀[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- 檢閱 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
