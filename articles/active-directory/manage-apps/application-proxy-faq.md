---
title: Azure AD 應用程式 Proxy 的常見問題 |Microsoft Docs
description: 瞭解常見問題的解答 (常見問題) 關於使用 Azure AD 應用程式 Proxy 將內部部署應用程式發佈給遠端使用者。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: aa9a930195908671cc0e772fd9643dcbce9dbb1c
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562407"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) 應用程式 Proxy 的常見問題

此頁面會回答有關 Azure Active Directory (Azure AD) 應用程式 Proxy 的常見問題。

## <a name="enabling-azure-ad-application-proxy"></a>啟用 Azure AD 應用程式 Proxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>需要何種授權才能使用 Azure AD 應用程式 Proxy？

若要使用 Azure AD 應用程式 Proxy，您必須具有 Azure AD Premium P1 或 P2 授權。 如需授權的詳細資訊，請參閱[Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>為什麼 [啟用應用程式 Proxy] 按鈕呈現灰色？

請確定您至少已安裝 Azure AD Premium P1 或 P2 授權和 Azure AD 應用程式 Proxy 連接器。 成功安裝第一個連接器之後，將會自動啟用 Azure AD 應用程式 Proxy 服務。

## <a name="connector-configuration"></a>連接器設定

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>應用程式 Proxy 連接器服務是否可在與預設值不同的使用者內容中執行？

否，不支援此案例。 預設設定值為：

- Microsoft AAD 應用程式 Proxy 連接器-WAPCSvc-網路服務
- Microsoft AAD 應用程式 Proxy 連接器更新程式-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>具有全域管理員或應用程式系統管理員角色的來賓使用者可以為 (來賓) 租使用者註冊連接器嗎？

否，目前無法這麼做。 註冊嘗試一律會在使用者的主租使用者上進行。

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的後端應用程式裝載于多部 web 伺服器上，而且需要使用者會話持續性 (的) 。 如何達到會話持續性？ 

如需建議，請參閱[應用程式 Proxy 連接器和應用程式的高可用性和負載平衡](application-proxy-high-availability-load-balancing.md)。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>TLS 終止是否 (TLS/HTTPS 檢查或加速) 從連接器伺服器到 Azure 支援的流量？

應用程式 Proxy 連接器會對 Azure 執行以憑證為基礎的驗證。  (TLS/HTTPS 檢查或加速) 的 TLS 終止會中斷此驗證方法，而且不受支援。 從連接器到 Azure 的流量必須略過任何正在執行 TLS 終止的裝置。  

### <a name="is-tls-12-required-for-all-connections"></a>所有連接都需要 TLS 1.2 嗎？
是。 為了將頂級的加密提供給客戶，應用程式 Proxy 服務會限制僅接受 TLS 1.2 通訊協定的存取。 這些變更已逐漸推出，並於 2019 年 8 月 31 日起生效。 請確定所有用戶端-伺服器和瀏覽器-伺服器組合都已更新為使用 TLS 1.2，以保持與應用程式 Proxy 服務的連線。 其中包括使用者存取透過應用程式 Proxy 發佈的應用程式時，所使用的用戶端。 請參閱準備 [Office 365 中的 TLS 1.2](https://docs.microsoft.com/microsoft-365/compliance/prepare-tls-1.2-in-office-365)，以取得實用的參考和資源。

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>我可以在連接器伺服器 (s) 和後端應用程式伺服器之間放置正向 proxy 裝置嗎？
是，從連接器版本1.5.1526.0 開始支援此案例。 請參閱使用[現有的內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>我是否應該建立專用帳戶以向 Azure AD 應用程式 Proxy 註冊連接器？

沒有理由。 任何全域管理員或應用程式系統管理員帳戶都可以使用。 在註冊過程中，不會使用在安裝期間輸入的認證。 相反地，會將憑證發行至連接器，這是用來從該點進行驗證。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何監視 Azure AD 應用程式 Proxy 連接器的效能？

其中包含隨連接器一起安裝的效能監視器計數器。 檢視方法：  

1. 選取 [**開始**]，輸入 "Perfmon"，然後按 enter。
2. 選取 [**效能監視器**]，然後按一下綠色 **+** 圖示。
3. 新增您想要監視的**MICROSOFT AAD 應用程式 Proxy 連接器**計數器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 應用程式 Proxy 連接器是否必須與資源位於相同的子網？

連接器不一定要位於相同的子網上。 不過，它需要 (DNS 的名稱解析、主機檔案) 至資源，以及 (路由傳送至資源的必要網路連線、資源上開啟的埠等等 ) 。 如需建議，請參閱[使用 Azure Active Directory 應用程式 Proxy 時的網路拓撲考慮](application-proxy-network-topology.md)。

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>我可以在哪些版本的 Windows Server 上安裝連接器？
應用程式 Proxy 需要 Windows Server 2012 R2 或更新版本。 Windows Server 2019 目前有 HTTP2 的限制。 若要在 Windows Server 2019 上成功使用連接器，您必須新增下列登錄機碼，然後重新開機伺服器：
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```


## <a name="application-configuration"></a>應用程式設定

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>預設值和「長」後端超時時間長度為何？ 可以延長超時時間嗎？

預設長度為85秒。 「長」設定為180秒。 無法擴充超時限制。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何? 變更登陸頁面 [我的應用程式載入？]

在 [應用程式註冊] 頁面上，您可以將首頁 URL 變更為所需的登陸頁面外部 URL。 從我的應用程式或 Office 365 入口網站啟動應用程式時，將會載入指定的頁面。 如需設定步驟，請參閱[使用 Azure AD 應用程式 Proxy 為發佈的應用程式設定自訂首頁](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>只能發行以 IIS 為基礎的應用程式嗎？ 在非 Windows web 伺服器上執行的 web 應用程式呢？ 連接器必須安裝在已安裝 IIS 的伺服器上嗎？

否，發行的應用程式不需要 IIS。 您可以發行在 Windows Server 以外的伺服器上執行的 web 應用程式。 不過，您可能無法使用非 Windows 伺服器的預先驗證，視 web 伺服器是否支援 Negotiate (Kerberos 驗證) 而定。 在安裝連接器的伺服器上，不需要 IIS。

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>我可以設定應用程式 Proxy 來新增 HSTS 標頭嗎？
應用程式 Proxy 不會自動將 HTTP 嚴格傳輸安全性標頭新增至 HTTPS 回應，但如果它是在已發行應用程式所傳送的原始回應中，就會保留標頭。 證明啟用此功能的設定已在藍圖中。 如果您對可將此新增至回應的預覽感興趣，請前往以 aadapfeedback@microsoft.com 取得詳細資料。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>在設定 Kerberos 限制委派時，我應該使用 PrincipalsAllowedToDelegateToAccount 方法 (KCD) 嗎？

當連接器伺服器與 web 應用程式服務帳戶位於不同的網域時，會使用 PrincipalsAllowedToDelegateToAccount 方法。 它需要使用以資源為基礎的限制委派。
如果連接器伺服器和 web 應用程式服務帳戶位於相同的網域中，您可以使用 Active Directory 的使用者和電腦來設定每個連接器電腦帳戶上的委派設定，讓它們可以委派給目標 SPN。

如果連接器伺服器和 web 應用程式服務帳戶位於不同的網域，則會使用以資源為基礎的委派。 委派許可權是在目標 web 伺服器和 web 應用程式服務帳戶上設定。 這種限制委派的方法相當新。 這個方法是在 Windows Server 2012 中引進，它可讓資源 (web 服務) 擁有者控制哪些機器和服務帳戶可以委派給它，藉此支援跨網域委派。 沒有任何 UI 可協助此設定，因此您必須使用 PowerShell。
如需詳細資訊，請參閱白皮書[瞭解使用應用程式 Proxy 的 Kerberos 限制委派](https://aka.ms/kcdpaper)。

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 驗證是否可與 Azure AD 應用程式 Proxy 搭配使用？

NTLM 驗證無法用來做為預先驗證或單一登入方法。 NTLM 驗證只能在用戶端與已發佈的 web 應用程式之間直接進行協商時使用。 使用 NTLM 驗證通常會導致在瀏覽器中出現登入提示。

## <a name="pass-through-authentication"></a>傳遞驗證

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>我可以針對使用傳遞驗證發佈的應用程式使用條件式存取原則嗎？

條件式存取原則只會針對 Azure AD 中已成功預先驗證的使用者強制執行。 傳遞驗證不會觸發 Azure AD 驗證，因此無法強制執行條件式存取原則。 使用傳遞驗證時，MFA 原則必須在內部部署伺服器上執行（如果可能的話），或藉由使用 Azure AD 應用程式 Proxy 來啟用預先驗證。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>我可以發行具有用戶端憑證驗證需求的 web 應用程式嗎？

否，不支援此案例，因為應用程式 Proxy 會終止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>遠端桌面閘道發佈

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何 Azure AD 應用程式 Proxy 發佈遠端桌面閘道？

請參閱[使用 Azure AD 應用程式 Proxy 發佈遠端桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>在遠端桌面閘道發佈案例中，我可以使用 Kerberos 限制委派 (單一登入-Windows 整合式驗證) 嗎？

否，不支援此案例。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的使用者不會使用 Internet Explorer 11，預先驗證案例也不適用。 這是預期行為嗎？

是，這是預期的。 預先驗證案例需要 ActiveX 控制項，這在協力廠商瀏覽器中不受支援。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a> (HTML5) 支援遠端桌面 Web 用戶端嗎？

是，此案例目前處於公開預覽狀態。 請參閱[使用 Azure AD 應用程式 Proxy 發佈遠端桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>在設定預先驗證案例之後，我發現使用者必須驗證兩次：先在 Azure AD 登入表單上，然後在 RDWeb 登入表單上。 這是預期行為嗎？ 如何將此專案減少為一次登入？

是，這是預期的。 如果使用者的電腦已加入 Azure AD，使用者會自動登入 Azure AD。 使用者只需要在 RDWeb 登入表單上提供其認證。

## <a name="sharepoint-publishing"></a>SharePoint 發行

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何將 SharePoint 發行 Azure AD 應用程式 Proxy？

請參閱[使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>我可以使用 SharePoint 行動裝置應用程式 (iOS/Android) 存取已發佈的 SharePoint 伺服器嗎？

SharePoint 行動裝置[應用程式](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)目前不支援 Azure Active Directory 預先驗證。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory 同盟服務 (AD FS) 發佈 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>我可以使用 Azure AD 應用程式 Proxy 作為 AD FS Proxy (，例如 Web 應用程式 Proxy) 嗎？

否。 Azure AD 應用程式 Proxy 是設計用來與 Azure AD 搭配運作，並不符合作為 AD FS Proxy 的需求。

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 支援適用于 QlikSense 以外的應用程式嗎？

目前，WebSocket 通訊協定支援仍處於公開預覽狀態，對其他應用程式可能無法使用。 有些客戶使用 WebSocket 通訊協定與其他應用程式的混合成功。 如果您測試過這類案例，我們很樂意聽到您的結果。 請將您的意見反應傳送給我們 aadapfeedback@microsoft.com 。

Windows 管理中心 (WAC) 或遠端桌面 Web 用戶端 (HTML5) 中 (檢視、PowerShell 和遠端桌面服務) 的功能，目前無法透過 Azure AD 應用程式 Proxy 來處理。

## <a name="link-translation"></a>連結轉譯

### <a name="does-using-link-translation-affect-performance"></a>使用連結轉譯會影響效能嗎？

是。 連結轉譯會影響效能。 應用程式 Proxy 服務會掃描應用程式中是否有硬式編碼的連結，並將它們取代為其各自的已發佈外部 Url，然後才呈現給使用者。 

為了達到最佳效能，我們建議您藉由設定[自訂網域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)來使用相同的內部和外部 url。 如果無法使用自訂網域，您可以在行動裝置上使用我的應用程式安全登入延伸模組或 Microsoft Edge 瀏覽器來改善連結轉譯效能。 請參閱重新[導向使用 Azure AD 應用程式 Proxy 發佈之應用程式的硬式編碼連結](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>萬用字元

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何? 使用萬用字元來發佈兩個具有相同自訂功能變數名稱但具有不同通訊協定的應用程式，一個用於 HTTP，另一個用於 HTTPS？

不直接支援此案例。 此案例的選項如下：

1. 將 HTTP 和 HTTPS Url 同時發佈為具有萬用字元的個別應用程式，但每個都提供不同的自訂網域。 此設定會正常執行，因為它們有不同的外部 URL。

2. 透過萬用字元應用程式發佈 HTTPS URL。 使用下列應用程式 Proxy PowerShell Cmdlet，分別發行 HTTP 應用程式：
   - [應用程式 Proxy 應用程式管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [應用程式 Proxy 連接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
