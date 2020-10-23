---
title: '以 Kerberos 為基礎的單一登入 (Azure Active Directory 中具有應用程式 Proxy 的 SSO) '
description: 涵蓋如何使用 Azure Active Directory 應用程式 Proxy 提供單一登入。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperfq2
ms.openlocfilehash: 860d29d3fff2187e770a5ff00b7145fc188a497c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426485"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>使用應用程式 Proxy 進行單一登入 (SSO) 至您應用程式的 Kerberos 限制委派

您可以針對透過應用程式 Proxy 發佈並使用整合式 Windows 驗證保護的內部部署應用程式，提供單一登入。 這些應用程式需要 Kerberos 票證以進行存取。 應用程式 Proxy 會使用 Kerberos 限制委派 (KCD) 來支援這些應用程式。 

您可以在 Active Directory 中提供應用程式 Proxy 連接器權限來模擬使用者，以使用「整合式 Windows 驗證」(IWA) 啟用應用程式的單一登入。 連接器會使用此權限來代表其傳送和接收權杖。

## <a name="how-single-sign-on-with-kcd-works"></a>使用 KCD 單一登入的運作方式
此圖說明當使用者嘗試存取使用 IWA 的內部部署應用程式時的流程。

![Microsoft AAD 驗證流程圖](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. 使用者輸入 URL，以透過應用程式 Proxy 存取內部部署應用程式。
2. 「應用程式 Proxy」將要求重新導向至 Azure AD 驗證服務，以進行預先驗證。 此時，Azure AD 會套用任何適用的驗證和授權原則，例如多重要素驗證。 若使用者通過驗證，Azure AD 會建立權杖並將它傳送給使用者。
3. 使用者將權杖傳遞給「應用程式 Proxy」。
4. 應用程式 Proxy 會驗證權杖，並從該權杖抓取 (UPN) 的使用者主體名稱，然後連接器會透過雙重驗證的安全通道，將服務主體名稱 (SPN) 。
5. 連接器會執行 Kerberos 限制委派 (KCD) 與內部部署 AD 的協商，模擬使用者以取得應用程式的 Kerberos 權杖。
6. Active Directory 會將應用程式的 Kerberos 權杖傳送至「連接器」。
7. 「連接器」會使用從 AD 接收的 Kerberos 權杖，將原始要求傳送至應用程式伺服器。
8. 應用程式會傳送回應至「連接器」，然後再傳回至「應用程式 Proxy」服務，最後再傳回給使用者。

## <a name="prerequisites"></a>先決條件
開始使用 IWA 應用程式的單一登入之前，請確定您的環境已完成下列設定和組態︰

* 您的應用程式 (例如 SharePoint Web 應用程式) 已設為使用「整合式 Windows 驗證」。 如需詳細資訊，請參閱 [啟用 Kerberos 驗證的支援](https://technet.microsoft.com/library/dd759186.aspx)，或針對 SharePoint 參閱 [sharepoint 2013 中的 kerberos 驗證計畫](https://technet.microsoft.com/library/ee806870.aspx)。
* 您所有的應用程式都有 [服務主體名稱](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)。
* 執行「連接器」的伺服器與執行應用程式的伺服器，皆已加入網域且屬於相同網域或信任網域。 如需有關加入網域的詳細資訊，請參閱 [將電腦加入網域](https://technet.microsoft.com/library/dd807102.aspx)。
* 執行「連接器」的伺服器有權限讀取使用者的 TokenGroupsGlobalAndUniversal 屬性。 這個預設設定可能已受到環境強化安全性所影響。

### <a name="configure-active-directory"></a>設定 Active Directory
根據您的「應用程式 Proxy 連接器」和應用程式伺服器是否位於相同的網域，Active Directory 組態會有所不同。

#### <a name="connector-and-application-server-in-the-same-domain"></a>連接器和應用程式伺服器位於相同網域
1. 在 Active Directory 中，移至 [**工具**  >  **使用者和電腦**]。
2. 選取正在執行連接器的伺服器。
3. 以滑鼠右鍵按一下並選取 [**屬性**  >  **委派**]。
4. 選取 [信任這台電腦，但只委派指定的服務]。 
5. 選取 [使用任何驗證通訊協定]。
6. 在 [這個帳戶可以呈送委派認證的服務]**** 下方，新增應用程式伺服器的 SPN 身分識別值。 這可讓「應用程式 Proxy 連接器」針對清單中所定義的應用程式，在 AD 中模擬使用者。

   ![[連接器 SVR 屬性] 視窗螢幕擷取畫面](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>連接器和應用程式伺服器位於不同網域
1. 如需跨網域使用 KCD 的先決條件清單，請參閱 [跨網域的 Kerberos 限制委派](https://technet.microsoft.com/library/hh831477.aspx)。
2. 使用 `principalsallowedtodelegateto` web 應用程式 (電腦或私人網路域使用者帳戶) 的服務帳戶屬性，從應用程式 Proxy (連接器) 啟用 Kerberos 驗證委派。 應用程式伺服器是在的內容中執行 `webserviceaccount` ，而委派的伺服器則是 `connectorcomputeraccount` 。 在的網域中執行 Windows Server 2012 R2 或更新版本) 的網域控制站 (執行下列命令 `webserviceaccount` 。 針對這兩個帳戶使用一般名稱 (非 UPN) 。

   如果 `webserviceaccount` 是電腦帳戶，請使用下列命令：

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   如果 `webserviceaccount` 是使用者帳戶，請使用下列命令：

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>設定單一登入 
1. 根據 [使用應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)中的所述指示來發佈您的應用程式。 請務必選取 [Azure Active Directory]**** 做為 [預先驗證方法]****。
2. 應用程式出現於企業應用程式清單後，將其選取並按一下 [單一登入]****。
3. 將單一登入模式設定為 [整合式 Windows 驗證]****。  
4. 輸入應用程式伺服器的 [內部應用程式 SPN]****。 在此範例中，已發佈應用程式的 SPN 為 http/www.contoso.com。 此 SPN 必須在連接器可以呈送委派認證的服務清單中。 
5. 針對要代表使用者使用的連接器選擇 [委派的登入身分識別]。 如需詳細資訊，請參閱[使用不同的內部部署和雲端](#working-with-different-on-premises-and-cloud-identities)身分識別

   ![進階應用程式組態](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>非 Windows 應用程式的 SSO

Azure AD 應用程式 Proxy 的 Kerberos 委派流程會在 Azure AD 在雲端驗證使用者開始。 一旦要求到達內部部署，Azure AD 應用程式 Proxy 連接器會利用與本機 Active Directory 互動，代表使用者發出 Kerberos 票證。 此程序稱為「Kerberos 限制委派 (KCD)」。 

在下一個階段中，要求會傳送至具有此 Kerberos 票證的後端應用程式。 

有幾個機制定義如何傳送這類要求中的 Kerberos 票證。 大部分的非 Windows 伺服器預期會以 SPNEGO token 的形式來接收。 Azure AD 的應用程式 Proxy 支援此機制，但預設為停用。 連接器可以針對 SPNEGO 或標準 Kerberos 權杖進行設定，但不能同時設定兩者。

如果您為 SPNEGO 設定連接器電腦，請確定該連接器群組中的所有其他連接器也都已採用 SPNEGO 進行設定。 預期標準 Kerberos 權杖的應用程式應該透過未設定 SPNEGO 的其他連接器來路由傳送。 有些 web 應用程式會接受這兩種格式，而不需要任何設定變更。 
 

若要啟用 SPNEGO：

1. 開啟以系統管理員身分執行的命令提示字元。
2. 透過命令提示字元，在需要 SPNEGO 的連接器伺服器中執行下列命令。

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

非 Windows 應用程式通常會使用使用者名稱或 SAM 帳戶名稱，而不是網域的電子郵件地址。 如果這種情況適用於您的應用程式，就必須設定指定的登入身分識別欄位，將您的雲端身分識別連線到您的應用程式身分識別。 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>使用不同的內部部署和雲端身分識別
應用程式 Proxy 會假設使用者在雲端與內部部署中具有完全相同的身分識別。 但在某些環境中，由於公司原則或應用程式相依性，組織可能必須使用替代的識別碼進行登入。 在這種情況下，您仍然可以使用 KCD 進行單一登入。 為每個應用程式設定 [委派的身分識別登入]****，以指定在執行單一登入時所應使用的身分識別。  

此功能可讓具有不同內部部署與雲端身分識別的許多組織，可從雲端單一登入到內部部署應用程式，而不需要使用者輸入不同的使用者名稱與密碼。 這包括下列組織：

* 在內部有多個網域 (joe@us.contoso.com、joe@eu.contoso.com)，並且在雲端有單一網域 (joe@contoso.com)。
* 在內部有無法路由傳送的網域名稱 (joe@contoso.usa)，並且在雲端有合法網域名稱。
* 請勿在內部使用網域名稱 (joe)
* 在內部部署和雲端中使用不同的別名。 例如：joe-johns@contoso.com 對上 joej@contoso.com  

使用應用程式 Proxy，您就可以選取要用來取得 Kerberos 票證的身分識別。 這項設定會因應用程式而異。 其中的一些選項適合不接受電子郵件地址格式的系統，另外的選項則設計用於替代登入。

![[委派的登入身分識別] 參數螢幕擷取畫面](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

如果使用委派的登入身分識別，則組織的所有網域或樹系中的這個值可能不是唯一。 您可以藉由使用兩個不同的連接器群組發佈這些應用程式兩次來避免此問題。 因為每個應用程式有不同的使用者對象，您可以將其「連接器」加入不同的網域。

如果用於登入身分識別的 **內部部署 SAM 帳戶名稱** ，則必須將裝載連接器的電腦新增至使用者帳戶所在的網域。

### <a name="configure-sso-for-different-identities"></a>設定不同身分識別的 SSO
1. 設定 Azure AD Connect 設定，讓主要的身分識別會是電子郵件地址 (郵件)。 這是在自訂程序中完成 (透過變更同步設定中的 [使用者主體名稱]**** 欄位)。 這些設定也決定使用者如何登入 Office 365、Windows 10 裝置與其他使用 Azure AD 作為其身分識別存放區的應用程式。  
   ![識別使用者螢幕擷取畫面 - [使用者主體名稱] 下拉式清單](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 在您想要修改之應用程式的應用程式組態設定中，選取要使用的 [委派的登入識別] **** ：

   * 使用者主體名稱 (例如 joe@contoso.com)
   * 替代使用者主體名稱 (例如 joed@contoso.local)
   * 使用者主體名稱的使用者名稱部分 (例如 joe)
   * 替代使用者主體名稱的使用者名稱部分 (例如 joed)
   * 內部部署 SAM 帳戶名稱 (視網域控制站組態而定)

### <a name="troubleshooting-sso-for-different-identities"></a>疑難排解不同身分識別的 SSO
如果 SSO 程式中發生錯誤，它會顯示在連接器電腦事件記錄檔中，如 [疑難排解](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)中所述。
但在某些情況下，要求會成功傳送至後端應用程式，同時此應用程式會以各種其他 HTTP 回應來回覆。 疑難排解這些情況應該要從檢查連接器電腦上應用程式 Proxy 工作階段事件記錄中的事件編號 24029 開始。 用於委派的使用者身分識別會出現在事件詳細資料的 [使用者] 欄位內。 若要開啟工作階段記錄，請選取事件檢視器檢視功能表中的 [顯示分析與偵錯記錄]****。

## <a name="next-steps"></a>後續步驟

* [如何設定應用程式 Proxy 應用程式以使用 Kerberos 限制委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](application-proxy-troubleshoot.md)

