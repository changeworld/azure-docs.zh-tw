---
title: 使用 Azure AD 應用程式代理啟用對 Power BI 的遠端存取
description: 介紹有關如何將本地 Power BI 與 Azure AD 應用程式代理集成的基礎知識。
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111583"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 Power BI 行動版的遠端存取

本文討論如何使用 Azure AD 應用程式代理使 Power BI 移動應用連接到 Power BI 報表服務器 （PBIRS） 和 SQL 伺服器報表服務 （SSRS） 2016 及更高版本。 通過此集成，遠離公司網路的使用者可以從 Power BI 移動應用訪問其 Power BI 報告，並受 Azure AD 身份驗證的保護。 此保護包括[安全優勢](application-proxy-security.md#security-benefits)，如條件訪問和多重要素驗證。  

## <a name="prerequisites"></a>Prerequisites

本文假定您已部署報表服務和[啟用了應用程式代理](application-proxy-add-on-premises-application.md)。

- 啟用應用程式代理需要在 Windows 伺服器上安裝連接器並完成[先決條件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)，以便連接器可以與 Azure AD 服務進行通信。  
- 發佈 Power BI 時，我們建議您使用相同的內部和外部域。 要瞭解有關自訂域的更多資訊，請參閱[在應用程式代理 中使用自訂域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)。
- 此集成可用於 Power **BI 移動 iOS 和 Android**應用程式。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>第 1 步：配置 Kerberos 限制委派 （KCD）

對於使用 Windows 驗證的內部部署應用程式來說，您可以使用 Kerberos 驗證通訊協定和稱為 Kerberos 限制委派 (KCD) 的功能來達成單一登入 (SSO)。 配置後，KCD 允許應用程式代理連接器為使用者獲取 Windows 權杖，即使使用者尚未直接登錄到 Windows。 要瞭解有關 KCD 的詳細資訊，請參閱[Kerberos 限制委派概述](https://technet.microsoft.com/library/jj553400.aspx)和[Kerberos 限制委派，以便使用應用程式代理對應用進行單一登入](application-proxy-configure-single-sign-on-with-kcd.md)。

在 Reporting Services 端上不需要進行太多設定。 只需確保具有有效的服務主體名稱 （SPN），以啟用正確的 Kerberos 身份驗證。 還要確保已啟用報表服務伺服器進行協商身份驗證。

要為報表服務設置 KCD，請繼續執行以下步驟。

### <a name="configure-the-service-principal-name-spn"></a>配置服務主體名稱 （SPN）

SPN 是使用 Kerberos 驗證之服務的唯一識別碼。 您需要確保報表伺服器具有正確的 HTTP SPN。 如需如何設定報表伺服器之適當服務主體名稱 (SPN) 的資訊，請參閱[為報表伺服器註冊服務主體名稱 (SPN)](https://msdn.microsoft.com/library/cc281382.aspx)。
您可以執行 Setspn 命令並搭配 -L 選項，來確認是否已新增 SPN。 若要深入了解此命令，請參閱 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)。

### <a name="enable-negotiate-authentication"></a>啟用協商身份驗證

要使報表伺服器使用 Kerberos 身份驗證，請將報表伺服器的身份驗證類型配置為 RSWindowsNegotiate。 使用 rsreportserver.config 檔配置此設置。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

如需詳細資訊，請參閱[修改 Reporting Services 設定檔](https://msdn.microsoft.com/library/bb630448.aspx)和[設定報表伺服器上的 Windows 驗證](https://msdn.microsoft.com/library/cc281253.aspx)。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>確保連接器受信任，以便委派到添加到報表服務應用程式池帳戶的 SPN
配置 KCD，以便 Azure AD 應用程式代理服務可以將使用者標識委派給報表服務應用程式池帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後，在這種情況下，該伺服器將上下文傳遞給目標應用程式或報表服務。

要配置 KCD，請對每台連接器電腦重複以下步驟：

1. 以域管理員身份登錄到網域控制站，然後打開**活動目錄使用者和電腦**。
2. 尋找連接器執行所在的電腦。  
3. 按兩下電腦，然後選擇"**委派**"選項卡。
4. 將委派設定設置為**信任此電腦，以便僅委派到指定的服務**。 然後，選取 [使用任何驗證通訊協定]****。
5. 選擇 **"添加**"，然後選擇 **"使用者"或"電腦**"。
6. 輸入用於報表服務的服務帳戶。 這是您在 Reporting Services 設定內新增 SPN 的帳戶。
7. 按一下 [確定]****。 要保存更改，請再次按一下 **"確定**"。

有關詳細資訊，請參閱[Kerberos 限制委派，以便使用應用程式代理對應用進行單一登入](application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>第 2 步：通過 Azure AD 應用程式代理髮布報表服務

現在，您已準備好配置 Azure AD 應用程式代理。

1. 使用以下設置通過應用程式代理髮布報表服務。 有關如何通過應用程式代理髮布應用程式的分步說明，請參閱[使用 Azure AD 應用程式代理髮布應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   - **內部 URL**：輸入連接器可以在公司網路中到達的報表服務器的 URL。 確保可以從安裝連接器的伺服器聯繫到此 URL。 最佳做法是使用頂層網域，例如`https://servername/`避免通過應用程式代理髮布的子路徑的問題。 例如，使用`https://servername/`和`https://servername/reports/`而不是`https://servername/reportserver/`或 。
     > [!NOTE]
     > 我們建議使用與報表伺服器的安全 HTTPS 連接。 有關如何[配置本地模式報表伺服器上](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)的 SSL 連線，請參閱配置 SSL 連線。
   - **外部 URL**： 輸入 Power BI 移動應用將連接到的公共 URL。 例如，如果使用了自訂域，`https://reports.contoso.com`則可能如下所示。 要使用自訂域，請上載域的證書，並將 DNS 記錄指向應用程式的預設msappproxy.net域。 有關詳細步驟，請參閱[在 Azure AD 應用程式代理 中使用自訂域](application-proxy-configure-custom-domain.md)。

   - **預身份驗證方法**： Azure 活動目錄

2. 發佈您的應用程式之後，請按照下列步驟設定單一登入設定：

   a. 在入口網站的應用程式頁面上，選取 [單一登入]****。

   b. 對於**單一登入模式**，請選擇**集成 Windows 身份驗證**。

   c. 將**內部應用程式 SPN**設置為之前設置的值。  

   d. 針對要代表使用者使用的連接器選擇 [委派的登入身分識別]****。 有關詳細資訊，請參閱[使用不同的本地和雲標識](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)。

   e. 按一下 [儲存]**** 以儲存您的變更。

要完成應用程式的設置，請轉到 **"使用者和組"** 部分並分配使用者以訪問此應用程式。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>第 3 步：修改應用程式的回復 URI

在 Power BI 移動應用可以連接和訪問報表服務之前，必須配置步驟 2 中自動為您創建的應用程式註冊。 

1. 在 Azure 活動目錄**概述頁上**，選擇**應用註冊**。
2. 在"**所有應用程式**"選項卡下搜索您在步驟 2 中創建的應用程式。
3. 選擇應用程式，然後選擇**身份驗證**。
4. 根據正在使用的平臺添加以下重定向 URI。

   為 Power BI 移動**iOS**配置應用時，添加以下公共用戶端類型的重定向 URI（移動&桌面）：
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   為 Power BI 移動**Android**配置應用時，添加以下公共用戶端類型的重定向 URI（移動&桌面）：
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 必須添加重定向 URI，應用程式才能正常工作。 如果要為 Power BI 移動 iOS 和 Android 配置應用，則將以下公共用戶端類型的重定向 URI（移動&桌面）添加到為 iOS 配置的重定向 URI`urn:ietf:wg:oauth:2.0:oob`清單中： 。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>第 4 步：從 Power BI 移動應用連接

1. 在 Power BI 移動應用中，連接到報表服務實例。 為此，請輸入通過應用程式代理髮布的應用程式的**外部 URL。**

   ![使用外部 URL 為 BI 移動應用供電](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. 選取 [連接]****。 您將被定向到 Azure 活動目錄登錄頁。

3. 輸入使用者的有效憑據，然後選擇 **"登錄**"。 您將看到來自報表服務伺服器的元素。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>第 5 步：為託管設備配置 Intune 策略（可選）

您可以使用 Microsoft Intune 管理公司員工使用的用戶端應用。 Intune 允許您使用資料加密和其他訪問要求等功能。 要通過 Intune 瞭解有關應用管理的更多詳細資訊，請參閱 Intune 應用管理。 要使 Power BI 移動應用程式使用 Intune 策略，請使用以下步驟。

1. 轉到**Azure 活動目錄**，然後**應用註冊**。
2. 註冊本機用戶端應用程式時，請選擇步驟 3 中配置的應用程式。
3. 在應用程式的頁面上，選擇 API**許可權**。
4. 按一下 **"添加許可權**"。 
5. **在我的組織使用的 API**下，搜索"微軟移動應用程式管理"並選擇它。
6. 將**裝置管理管理應用.ReadWrite**許可權添加到應用程式
7. 按一下 **"授予管理員同意"** 以授予對應用程式的許可權存取權限。
8. 通過引用[如何創建和分配應用保護原則來](https://docs.microsoft.com/intune/app-protection-policies)配置所需的 Intune 策略。

## <a name="troubleshooting"></a>疑難排解

如果應用程式在嘗試載入報表超過幾分鐘後返回錯誤頁，則可能需要更改超時設置。 預設情況下，應用程式代理支援最多需要 85 秒才能回應請求的應用程式。 要將此設置延長到 180 秒，請在應用程式的"應用代理設置"頁中選擇後端超時為**Long。** 有關如何創建快速可靠的報表的提示，請參閱[Power BI 報告最佳實踐](https://docs.microsoft.com/power-bi/power-bi-reports-performance)。

## <a name="next-steps"></a>後續步驟

- [使本機用戶端應用程式能夠與代理應用程式進行交互](application-proxy-configure-native-client-application.md)
- [在 Power BI 行動裝置應用程式中檢視內部部署報表伺服器報表和 KPI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
