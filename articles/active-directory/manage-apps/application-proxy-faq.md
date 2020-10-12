---
title: Azure AD 應用程式 Proxy 的常見問題 |Microsoft Docs
description: 瞭解常見問題的解答 (常見問題) 有關使用 Azure AD 應用程式 Proxy 將內部內部部署應用程式發佈至遠端使用者的常見問題。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: edf51dad768e8d8b5ea5dc6c1eff88f43f0f6b70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88589158"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) 應用程式 Proxy 常見問題

此頁面會回答關於 Azure Active Directory (Azure AD) 應用程式 Proxy 的常見問題。

## <a name="enabling-azure-ad-application-proxy"></a>啟用 Azure AD 應用程式 Proxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 需要什麼授權？

若要使用 Azure AD 應用程式 Proxy，您必須擁有 Azure AD Premium P1 或 P2 授權。 如需授權的詳細資訊，請參閱 [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>如果我的授權過期，我的租使用者中 Azure AD 應用程式 Proxy 會發生什麼事？
如果您的授權過期，則會自動停用應用程式 Proxy。 您的應用程式資訊最多會儲存一年。

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>為什麼 [啟用應用程式 Proxy] 按鈕呈現灰色？

請確定您至少已安裝 Azure AD Premium P1 或 P2 授權和 Azure AD 應用程式 Proxy 連接器。 成功安裝第一個連接器之後，將會自動啟用 Azure AD 的應用程式 Proxy 服務。

## <a name="connector-configuration"></a>連接器設定

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>應用程式 Proxy 連接器服務是否可在與預設值不同的使用者內容中執行？

否，不支援此案例。 預設設定值為：

- Microsoft AAD 應用程式 Proxy 連接器-WAPCSvc-Network Service
- Microsoft AAD 應用程式 Proxy 連接器更新程式-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>具有全域管理員或應用程式系統管理員角色的來賓使用者可以為 (guest) 租使用者註冊連接器嗎？

否，目前無法這麼做。 註冊嘗試一律會在使用者的主租使用者上進行。

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的後端應用程式裝載在多部網頁伺服器上，而且需要使用者會話持續性 () 。 如何達成會話持續性？ 

如需建議，請參閱 [應用程式 Proxy 連接器和應用程式的高可用性和負載平衡](application-proxy-high-availability-load-balancing.md)。

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>TLS 終止 (TLS/HTTPS 檢查或加速) 從連接器伺服器至 Azure 的流量是否支援？

應用程式 Proxy 連接器會對 Azure 執行以憑證為基礎的驗證。 TLS 終止 (TLS/HTTPS 檢查或加速) 會中斷此驗證方法，而且不受支援。 從連接器到 Azure 的流量，必須略過任何正在執行 TLS 終止的裝置。  

### <a name="is-tls-12-required-for-all-connections"></a>所有連接都需要 TLS 1.2 嗎？
是。 為了將頂級的加密提供給客戶，應用程式 Proxy 服務會限制僅接受 TLS 1.2 通訊協定的存取。 這些變更已逐漸推出，並於 2019 年 8 月 31 日起生效。 請確定所有用戶端-伺服器和瀏覽器-伺服器組合都已更新為使用 TLS 1.2，以保持與應用程式 Proxy 服務的連線。 其中包括使用者存取透過應用程式 Proxy 發佈的應用程式時，所使用的用戶端。 請參閱準備 [Office 365 中的 TLS 1.2](https://docs.microsoft.com/microsoft-365/compliance/prepare-tls-1.2-in-office-365)，以取得實用的參考和資源。

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>我可以在連接器伺服器 (s) 和後端應用程式伺服器之間放置轉寄 proxy 裝置嗎？
是，從連接器版本1.5.1526.0 開始支援此案例。 請參閱使用 [現有的內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>我應該建立專用帳戶來向 Azure AD 的應用程式 Proxy 註冊連接器嗎？

沒有原因。 任何全域管理員或應用程式系統管理員帳戶都可以運作。 在註冊過程中，不會使用在安裝期間輸入的認證。 相反地，憑證會發行至連接器，而連接器會用來從該點進行驗證。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何監視 Azure AD 應用程式 Proxy 連接器的效能？

與連接器一起安裝效能監視器的計數器。 檢視方法：  

1. 選取 [ **開始**]，輸入 "Perfmon"，然後按 enter。
2. 選取 **效能監視器** 然後按一下綠色 **+** 圖示。
3. 新增您想要監視的 **MICROSOFT AAD 應用程式 Proxy 連接器** 計數器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 應用程式 Proxy 連接器必須與資源位於相同的子網嗎？

連接器不需要位於相同的子網上。 不過，它需要 (DNS 的名稱解析、主機檔案) 至資源，以及必要的網路連線 (路由至資源、資源上開啟的埠等 ) 。 如需建議，請參閱 [使用 Azure Active Directory 應用程式 Proxy 時的網路拓撲考慮](application-proxy-network-topology.md)。

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>我可以安裝連接器的 Windows Server 版本為何？
應用程式 Proxy 需要 Windows Server 2012 R2 或更新版本。 Windows Server 2019 的 HTTP2 目前有限制。 若要在 Windows Server 2019 上成功使用連接器，您需要新增下列登錄機碼並重新啟動伺服器：
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```


## <a name="application-configuration"></a>應用程式設定

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>我收到無效憑證或密碼可能錯誤的錯誤

上傳 SSL 憑證之後，您會在入口網站上收到「不正確憑證，可能有錯誤的密碼」訊息。

以下是針對此錯誤進行疑難排解的一些秘訣：
- 檢查憑證是否有問題。 將它安裝在您的本機電腦上。 如果您沒有遇到任何問題，則憑證是良好的。
- 請確定密碼不包含任何特殊字元。 針對測試，密碼只能包含字元0-9、a-z 和 a-z。
- 如果是使用 Microsoft 軟體金鑰儲存提供者建立的憑證，則必須使用 RSA 演算法。

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>預設和「長」後端超時的長度為何？ 是否可以延長期限？

預設長度是85秒。 "Long" 設定為180秒。 無法延長超時限制。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何? 變更我的應用程式載入的登陸頁面？

在 [應用程式註冊] 頁面上，您可以將首頁 URL 變更為登陸頁面所需的外部 URL。 從我的應用程式或 Office 365 入口網站啟動應用程式時，將會載入指定的頁面。 如需設定步驟，請參閱 [使用 Azure AD 應用程式 Proxy 為已發佈的應用程式設定自訂首頁](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>只能發行以 IIS 為基礎的應用程式嗎？ 在非 Windows 網頁伺服器上執行的 web 應用程式呢？ 連接器是否必須安裝在已安裝 IIS 的伺服器上？

否，已發行的應用程式不需要 IIS。 您可以發佈在 Windows Server 以外的伺服器上執行的 web 應用程式。 不過，根據 web 伺服器是否支援 Negotiate (Kerberos 驗證) ，您可能無法對非 Windows 伺服器使用預先驗證。 安裝連接器的伺服器上不需要 IIS。

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>是否可以設定應用程式 Proxy 來新增 HSTS 標頭？
應用程式 Proxy 不會自動將 HTTP Strict-Transport-Security 標頭新增至 HTTPS 回應，但如果標頭是由已發佈應用程式傳送的原始回應中，它將會維持標頭。 您可以在藍圖上證明啟用這項功能的設定。 如果您對可將此新增至回應的預覽版感興趣，請聯繫以 aadapfeedback@microsoft.com 取得詳細資料。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>在設定 Kerberos 限制委派時，何時應該使用 >serverc 方法 (KCD) ？

當連接器伺服器與 web 應用程式服務帳戶位於不同的網域時，會使用 >serverc 方法。 它需要使用以資源為基礎的限制委派。
如果連接器伺服器和 web 應用程式服務帳戶位於相同的網域中，您可以使用 Active Directory 消費者和電腦來設定每個連接器電腦帳戶上的委派設定，讓它們可以委派給目標 SPN。

如果連接器伺服器和 web 應用程式服務帳戶位於不同的網域，則會使用以資源為基礎的委派。 系統會在目標 web 伺服器和 web 應用程式服務帳戶上設定委派許可權。 這種限制委派的方法相當新。 方法是在 Windows Server 2012 中引進的，可讓資源 (web 服務) 擁有者控制哪些電腦和服務帳戶可以委派給它，藉此支援跨網域委派。 沒有可協助進行此設定的 UI，因此您需要使用 PowerShell。
如需詳細資訊，請參閱白皮書 [瞭解使用應用程式 Proxy 的 Kerberos 限制委派](https://aka.ms/kcdpaper)。

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 驗證是否與 Azure AD 的應用程式 Proxy 搭配運作？

NTLM 驗證無法用作預先驗證或單一登入方法。 只有當您可以直接在用戶端和已發佈的 web 應用程式之間進行協商時，才可以使用 NTLM 驗證。 使用 NTLM 驗證通常會導致登入提示出現在瀏覽器中。

## <a name="pass-through-authentication"></a>傳遞驗證

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>是否可以針對透過傳遞驗證發佈的應用程式使用條件式存取原則？

條件式存取原則只會在 Azure AD 中成功預先驗證的使用者強制執行。 傳遞驗證不會觸發 Azure AD authentication，因此無法強制執行條件式存取原則。 使用傳遞驗證時，必須盡可能在內部部署伺服器上執行 MFA 原則，或透過 Azure AD 應用程式 Proxy 啟用預先驗證。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>我可以使用用戶端憑證驗證需求發佈 web 應用程式嗎？

否，不支援此案例，因為應用程式 Proxy 將會終止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>遠端桌面閘道發佈

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何 Azure AD 應用程式 Proxy 發佈遠端桌面閘道？

請參閱 [使用 Azure AD 應用程式 Proxy 發佈遠端桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>我可以在遠端桌面閘道發佈案例中，使用 Kerberos 限制委派 (單一 Sign-On-Windows 整合式驗證) 嗎？

否，不支援此案例。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的使用者不會使用 Internet Explorer 11，而預先驗證案例也不適用。 這是預期行為嗎？

是，這是預期的。 預先驗證案例需要 ActiveX 控制項，在協力廠商瀏覽器中不支援。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>是否支援遠端桌面 Web 用戶端 (HTML5) ？

是，此案例目前處於公開預覽狀態。 請參閱 [使用 Azure AD 應用程式 Proxy 發佈遠端桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>在設定好預先驗證案例之後，我發現使用者必須驗證兩次：先在 Azure AD 登入表單上，然後在 RDWeb 登入表單上。 這是預期行為嗎？ 我要如何將此項縮減為單一登入？

是，這是預期的。 如果使用者的電腦已加入 Azure AD，使用者會自動登入 Azure AD。 使用者只需要在 RDWeb 登入表單上提供其認證。

## <a name="sharepoint-publishing"></a>SharePoint 發行

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何 Azure AD 應用程式 Proxy 發佈 SharePoint？

請參閱 [使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>我可以使用 SharePoint 行動應用程式 (iOS/Android) 來存取已發佈的 SharePoint 伺服器嗎？

[SharePoint 行動應用程式](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)目前不支援 Azure Active Directory 預先驗證。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory 同盟服務 (AD FS) 發行 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>我可以使用 Azure AD 應用程式 Proxy 做為 AD FS Proxy (例如 Web 應用程式 Proxy) 嗎？

否。 Azure AD 應用程式 Proxy 是設計用來與 Azure AD 搭配使用，而且不符合作為 AD FS Proxy 的需求。

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 支援是否適用于 QlikSense 以外的應用程式？

目前，WebSocket 通訊協定支援仍處於公開預覽狀態，可能不適用於其他應用程式。 某些客戶已使用 WebSocket 通訊協定與其他應用程式的混合成功。 如果您測試這類案例，我們很樂意聽到您的結果。 請將您的意見反應傳送給我們 aadapfeedback@microsoft.com 。

Windows Admin Center (WAC) 或遠端桌面 Web 用戶端 (HTML5) 中 (檢視、PowerShell 和遠端桌面服務) 的功能目前無法透過 Azure AD 應用程式 Proxy 運作。

## <a name="link-translation"></a>連結轉譯

### <a name="does-using-link-translation-affect-performance"></a>使用連結轉譯會影響效能嗎？

是。 連結轉譯會影響效能。 應用程式 Proxy 服務會掃描應用程式中的硬式編碼連結，並將其取代為其各自發布的外部 Url，然後再呈現給使用者。 

為了達到最佳效能，建議您設定 [自訂網域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)，以使用相同的內部和外部 url。 如果無法使用自訂網域，您可以在行動裝置上使用我的應用程式安全登入延伸模組或 Microsoft Edge 瀏覽器，以改善連結轉譯效能。 請參閱 [針對使用 Azure AD 應用程式 Proxy 發佈的應用程式重新導向硬式編碼連結](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>萬用字元

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何? 使用萬用字元，以相同的自訂功能變數名稱發佈兩個應用程式，但使用不同的通訊協定，一個用於 HTTP，另一個用於 HTTPS？

不直接支援此案例。 此案例的選項如下：

1. 使用萬用字元以個別的應用程式發佈 HTTP 和 HTTPS Url，但為每個 Url 提供不同的自訂網域。 這種設定將會運作，因為它們具有不同的外部 URL。

2. 透過萬用字元應用程式發佈 HTTPS URL。 使用下列應用程式 Proxy PowerShell Cmdlet 來分開發布 HTTP 應用程式：
   - [應用程式 Proxy 應用程式管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [應用程式 Proxy 連接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
