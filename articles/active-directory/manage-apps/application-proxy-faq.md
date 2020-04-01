---
title: Azure AD 應用程式代理常見問題 |微軟文件
description: 瞭解有關使用 Azure AD 應用程式代理向遠端使用者發佈內部本地應用程式的常見問題 (FAQ) 的答案。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 31587777ba22dd8b4cebf81f0ff98979bb30fade
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410725"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>活動目錄 (Azure AD) 應用程式代理常見問題

此頁回答了有關 Azure 活動目錄 (Azure AD) 應用程式代理的常見問題。

## <a name="enabling-azure-ad-application-proxy"></a>啟用 Azure AD 應用程式 Proxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>使用 Azure AD 應用程式代理需要哪些授權?

要使用 Azure AD 應用程式代理,必須具有 Azure AD 進階 P1 或 P2 許可證。 有關許可的詳細資訊,請參閱[Azure 活動目錄定價](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>為什麼「啟用應用程式代理」按鈕灰顯?

確保至少安裝了 Azure AD 高級 P1 或 P2 許可證,並安裝了 Azure AD 應用程式代理連接器。 成功安裝第一個連接器後,將自動啟用 Azure AD 應用程式代理服務。

## <a name="connector-configuration"></a>連接器設定

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>應用程式代理連接器服務是否可以在不同於預設值的使用者上下文中運行?

不,不支援此方案。 預設設定值為：

- 微軟AAD應用程式代理連線器 ─WAPCSvc - 網路服務
- 微軟AAD應用程式代理連接器更新程式 - WAPC更新器Svc - NT權威_系統

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>我的後端應用程式託管在多個 Web 伺服器上,需要使用者會話持久性(粘性)。 如何實現會話持久性? 

有關建議,請參閱[應用程式代理連接器和應用程式的高可用性和負載平衡](application-proxy-high-availability-load-balancing.md)。

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>是否可以在連接器伺服器和後端應用程式伺服器之間放置轉發代理設備?

不,不支援此方案。 只能將連接器和更新服務配置為使用轉發代理來向 Azure 的出站流量。 請參考[使用現有本地代理伺服器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>是否支援從連接器伺服器到 Azure 的流量上的 TLS 終止(TLS/HTTPS 檢查或加速)?

應用程式代理連接器對 Azure 執行基於證書的身份驗證。 TLS 終止(TLS/HTTPS 檢查或加速)會破壞此身份驗證方法,不受支援。 從連接器到 Azure 的流量必須繞過執行 TLS 終止的任何設備。  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>我應該創建專用帳戶以將連接器註冊到 Azure AD 應用程式代理?

沒有理由. 任何全域管理員或應用程式管理員帳戶都將工作。 安裝過程中輸入的憑據在註冊過程后不使用。 相反,證書頒發給連接器,該連接器從該點開始用於身份驗證。

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>如何監視 Azure AD 應用程式代理連接器的性能?

還有與連接器一起安裝的性能監視器計數器。 檢視方法：  

1. 選擇 **「開始**」,鍵入"Perfmon",然後按 ENTER。
2. 選擇**性能監視器**「並按**+** 下綠色圖示」。
3. 添加要監視的**Microsoft AAD 應用程式代理連接器**計數器。

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 應用程式代理連接器是否必須與資源位於同一子網上?

連接器不需要位於同一子網上。 但是,它需要對資源的名稱解析(DNS、主機檔)和必要的網路連接(路由到資源、在資源上打開的埠等)。 有關建議,請參閱[使用 Azure 活動目錄應用程式代理時的網路拓撲注意事項](application-proxy-network-topology.md)。

## <a name="application-configuration"></a>應用程式設定

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>默認超時和「長」後端超時的長度是多少? 超時可以延長嗎?

默認長度為 85 秒。 "長"設置為 180 秒。 無法延長超時限制。

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>如何更改應用程式載入的著陸頁?

在"應用程式註冊"頁中,您可以將主頁 URL 更改為著陸頁所需的外部 URL。 當從"我的應用"或 Office 365 門戶啟動應用程式時,將載入指定的頁面。 有關設定步驟,請參閱[使用 Azure AD 應用程式代理為已發表應用設定自訂主頁](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>只能發佈基於 IIS 的應用程式嗎? 在非 Windows Web 伺服器上執行的 Web 應用程式如何? 連接器是否必須安裝在安裝了 IIS 的伺服器上?

否,對於發佈的應用程式沒有 IIS 要求。 您可以發表在 Windows 伺服器以外的伺服器上執行的 Web 應用程式。 但是,您可能無法使用非 Windows 伺服器的預身份驗證,具體取決於 Web 伺服器是否支援協商(Kerberos 身份驗證)。 安裝連接器的伺服器上不需要 IIS。

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>在設置 Kerberos 約束委派 (KCD) 時,我應該何時使用「委託委派給帳戶」方法?

當連接器伺服器與 Web 應用程式服務帳戶位於不同的域中時,將使用委託委託委託到帳戶方法。 它要求使用基於資源的約束委派。
如果連接器伺服器和 Web 應用程式服務帳戶位於同一域中,則可以使用 Active Directory 使用者和電腦在每個連接器電腦帳戶上配置委派設置,從而允許它們委派給目標 SPN。

如果連接器伺服器和 Web 應用程式服務帳戶位於不同的域中,則使用基於資源的委派。 委派許可權在目標 Web 伺服器和 Web 應用程式服務帳戶上配置。 這種約束委派的方法相對較新。 該方法在 Windows Server 2012 中引入,它允許資源(Web 服務)擁有者控制哪些計算機和服務帳戶可以委託給它,從而支援跨域委派。 沒有 UI 可幫助處理此設定,因此您需要使用 PowerShell。
有關詳細資訊,請參閱使用[應用程式代理瞭解 Kerberos 約束委派](https://aka.ms/kcdpaper)的白皮書。

## <a name="pass-through-authentication"></a>傳遞驗證

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>對於通過直通身份驗證發佈的應用程式,是否可以使用條件訪問策略?

條件訪問策略僅適用於 Azure AD 中成功預身份驗證的使用者。 直通身份驗證不會觸發 Azure AD 身份驗證,因此無法強制執行條件存取策略。 使用直通身份驗證,必須在本地伺服器上實現 MFA 策略(如果可能)或通過使用 Azure AD 應用程式代理啟用預身份驗證。

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>是否可以發佈具有客戶端憑證身份驗證要求的 Web 應用程式?

不,此方案不受支持,因為應用程式代理將終止 TLS 流量。  

## <a name="remote-desktop-gateway-publishing"></a>遠端桌面閘道發佈

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>如何透過 Azure AD 應用程式代理發佈遠端桌面閘道?

請參考[Azure AD 應用程式代理伺服器的遠端桌面](application-proxy-integrate-with-remote-desktop-services.md)。

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>我可以在遠端桌面閘道發佈方案中使用 Kerberos 受限委派(單登錄 - Windows 整合身份驗證)嗎?

不,不支援此方案。  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>我的使用者不使用 Internet Explorer 11,預身份驗證方案不適合他們。 這是預期行為嗎？

是的,這是預料之中的。 預身份驗證方案需要 ActiveX 控制項,第三方瀏覽器不支援該控制項。

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>是否支援遠端桌面 Web 用戶端 (HTML5)?

否,此方案當前不受支援。 請關注我們的[用戶語音](https://aka.ms/aadapuservoice)反饋論壇,瞭解此功能的更新。

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>配置預身份驗證方案后,我意識到使用者必須進行兩次身份驗證:首先在 Azure AD 登錄窗體上,然後在 RDWeb 登錄窗體上進行身份驗證。 這是預期行為嗎？ 如何將此減少到一個登錄?

是的,這是預料之中的。 如果用戶的電腦已加入 Azure AD,則使用者將自動登錄到 Azure AD。 使用者只需僅在 RDWeb 登錄窗體上提供其認證。

## <a name="sharepoint-publishing"></a>共用點發佈

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>如何通過 Azure AD 應用程式代理發佈共享點?

請參考[Azure AD 應用程式代理啟用對 SharePoint 的遠端存取](application-proxy-integrate-with-sharepoint-server.md)。

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>我能否使用 SharePoint 移動應用 (iOS/ Android) 訪問已發布的 SharePoint 伺服器?

[SharePoint 移動應用](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises)當前不支援 Azure 活動目錄預身份驗證。

## <a name="active-directory-federation-services-ad-fs-publishing"></a>活動目錄 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>是否可以使用 Azure AD 應用程式代理作為 AD FS 代理(如 Web 應用程式代理)?

否。 Azure AD 應用程式代理旨在與 Azure AD 配合使用,並且不符合作為 AD FS 代理的要求。

## <a name="websocket"></a>網路通訊接字

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 支援是否適用於 QlikSense 以外的應用程式?

目前,WebSocket 協定支援仍處於公共預覽狀態,它可能不適用於其他應用程式。 一些客戶使用 WebSocket 協定與其他應用程式取得了喜憂參半的成功。 如果您測試此類方案,我們很樂意聽到您的結果。 請在上aadapfeedback@microsoft.com向我們發送您的反饋。

Windows 管理中心 (WAC) 或遠端桌面 Web 用戶端 (HTML5) 中的功能(事件紀錄、PowerShell 和遠端桌面服務)目前無法透過 Azure AD 應用程式代理工作。

## <a name="link-translation"></a>連結翻譯

### <a name="does-using-link-translation-affect-performance"></a>使用連結轉換會影響性能嗎?

是。 連結轉換會影響性能。 應用程式代理服務掃描應用程式以尋找硬編碼連結,並將其替換為各自的已發佈的外部 URL,然後再將其呈現給使用者。 

為了獲得最佳性能,我們建議通過配置[自定義域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)使用相同的內部和外部 URL。 如果無法使用自定義域,則可以在行動裝置上使用「我的應用安全登錄擴展」或「Microsoft 邊緣瀏覽器」來提高連結翻譯性能。 有關[使用 Azure AD 應用程式代理發布的應用程式,請參閱重定向硬編碼連結](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="wildcards"></a>萬用字元

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>如何使用通配符發佈兩個具有相同自定義功能變數名稱但具有不同協定的應用程式,一個用於 HTTP,一個用於 HTTPS?

此方案不受直接支援。 這個專案的選項包括:

1. 將 HTTP 和 HTTPS URL 發布為具有通配符的單獨應用程式,但為每個 URL 提供不同的自訂域。 此配置將工作,因為它們具有不同的外部 URLS。

2. 通過通配符應用程式發佈 HTTPS URL。 使用應用程式代理 PowerShell cmdlet 分別發佈 HTTP 應用程式:
   - [應用程式代理程式管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [應用程式代理連線器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
