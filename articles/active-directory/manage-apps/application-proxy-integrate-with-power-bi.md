---
title: 使用 Azure AD 應用程式 Proxy 啟用 Power BI 的遠端存取
description: 涵蓋如何將內部部署 Power BI 與 Azure AD 應用程式 Proxy 進行整合的基本概念。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68993a460ba3d6a672a27eb8da5ced85b29d3d12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764548"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 Power BI 行動版的遠端存取

本文討論如何使用 Azure AD 應用程式 Proxy，讓 Power BI 行動應用程式連接到 Power BI 報表伺服器（PBIRS）和 SQL Server Reporting Services （SSRS）2016和更新版本。 透過這項整合，離開公司網路的使用者可以從 Power BI 的行動應用程式存取其 Power BI 報告，並受到 Azure AD 驗證的保護。 這種保護包括條件式存取和多重要素驗證等[安全性優點](application-proxy-security.md#security-benefits)。

## <a name="prerequisites"></a>必要條件

本文假設您已部署報表服務並[啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)。

- 啟用應用程式 Proxy 需要在 Windows server 上安裝連接器，並完成[必要條件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)，讓連接器可以與 Azure AD 服務進行通訊。
- 發佈 Power BI 時，建議您使用相同的內部和外部網域。 若要深入瞭解自訂網域，請參閱[在應用程式 Proxy 中使用自訂網域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)。
- 此整合適用于**Power BI 行動版 iOS 和 Android**應用程式。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>步驟1：設定 Kerberos 限制委派（KCD）

對於使用 Windows 驗證的內部部署應用程式來說，您可以使用 Kerberos 驗證通訊協定和稱為 Kerberos 限制委派 (KCD) 的功能來達成單一登入 (SSO)。 當設定時，KCD 允許應用程式 Proxy 連接器取得使用者的 Windows token，即使使用者未直接登入 Windows。 若要深入瞭解 KCD，請參閱[Kerberos 限制委派總覽](https://technet.microsoft.com/library/jj553400.aspx)和[kerberos 限制委派，以使用應用程式 Proxy 單一登入您的應用程式](application-proxy-configure-single-sign-on-with-kcd.md)。

在 Reporting Services 端上不需要進行太多設定。 只要確定具備有效的服務主體名稱（SPN），即可進行適當的 Kerberos 驗證。 也請確定已啟用 Reporting Services 伺服器進行 Negotiate 驗證。

若要設定 Reporting services 的 KCD，請繼續進行下列步驟。

### <a name="configure-the-service-principal-name-spn"></a>設定服務主體名稱（SPN）

SPN 是使用 Kerberos 驗證之服務的唯一識別碼。 您必須確定您的報表伺服器有適當的 HTTP SPN 存在。 如需如何設定報表伺服器之適當服務主體名稱 (SPN) 的資訊，請參閱[為報表伺服器註冊服務主體名稱 (SPN)](https://msdn.microsoft.com/library/cc281382.aspx)。
您可以執行 Setspn 命令並搭配 -L 選項，來確認是否已新增 SPN。 若要深入了解此命令，請參閱 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)。

### <a name="enable-negotiate-authentication"></a>啟用 Negotiate 驗證

若要讓報表伺服器使用 Kerberos 驗證，請將報表伺服器的驗證類型設定為 RSWindowsNegotiate。 使用 rsreportserver.config 檔案來進行此設定。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

如需詳細資訊，請參閱[修改 Reporting Services 設定檔](https://msdn.microsoft.com/library/bb630448.aspx)和[設定報表伺服器上的 Windows 驗證](https://msdn.microsoft.com/library/cc281253.aspx)。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>請確定連接器受信任，可委派給新增至 Reporting Services 應用程式集區帳戶的 SPN
設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者身分識別委派給 Reporting Services 的應用程式集區帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後，該伺服器會將內容傳遞至目標應用程式，或在此情況下 Reporting Services。

若要設定 KCD，請針對每個連接器電腦重複執行下列步驟：

1. 以網域系統管理員身分登入網域控制站，然後開啟**Active Directory 使用者和電腦**]。
2. 尋找連接器執行所在的電腦。
3. 按兩下該電腦，然後選取 [委派] 索引標籤。
4. 將委派設定為 [信任這台電腦，但只委派指定的服務]。 然後，選取 [**使用任何驗證通訊協定**]。
5. 選取 [新增]，然後選取 [使用者或電腦]。
6. 輸入您要用來 Reporting Services 的服務帳戶。 這是您在 Reporting Services 設定內新增 SPN 的帳戶。
7. 按一下 [確定]。 若要儲存變更，請按一下 [確定]。

如需詳細資訊，請參閱[使用應用程式 Proxy 單一登入應用程式的 Kerberos 限制委派](application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>步驟2：透過 Azure AD 應用程式 Proxy 發行報表服務

現在您已準備好要設定 Azure AD 應用程式 Proxy。

1. 使用下列設定，透過應用程式 Proxy 發行報表服務。 如需如何透過應用程式 Proxy 發佈應用程式逐步指示，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) \(部分機器翻譯\)。
   - **內部 URL**：輸入連接器可以在公司網路中連接之報表伺服器的 URL。 請確定可從安裝連接器的伺服器連線到此 URL。 最佳做法是使用頂層網域 (例如 `https://servername/`)，以避免透過應用程式 Proxy 發佈之子路徑的問題。 例如，使用 `https://servername/`，而不是 `https://servername/reports/` 或 `https://servername/reportserver/`。
     > [!NOTE]
     > 我們建議您對報表伺服器使用安全的 HTTPS 連接。 如需作法資訊，請參閱[在原生模式報表伺服器上設定 SSL 連線](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)。
   - **外部 url**：輸入 Power BI 行動應用程式將連接的公用 URL。 例如，如果使用自訂網域，其看起來可能像 `https://reports.contoso.com`。 若要使用自訂網域，請上傳網域的憑證，並將 DNS 記錄指向您應用程式的預設 msappproxy.net 網域。 如需詳細步驟，請參閱[在 Azure AD 應用程式 Proxy 中使用自訂網域](application-proxy-configure-custom-domain.md) \(部分機器翻譯\)。

   - **預先驗證方法**： Azure Active Directory

2. 發佈您的應用程式之後，請按照下列步驟設定單一登入設定：

   a. 在入口網站的應用程式頁面上，選取 [單一登入]。

   b. 針對 [單一登入模式]，選取 [整合式 Windows 驗證]。

   c. 將**內部應用程式 SPN** 設定為您先前設定的值。

   d. 針對要代表使用者使用的連接器選擇 [委派的登入身分識別]。 如需詳細資訊，請參閱[使用不同的內部部署和雲端身分識別](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities) \(部分機器翻譯\)。

   e. 按一下 [確定] 儲存變更。

若要完成應用程式的設定，請移至 **[使用者和群組**] 區段，並指派使用者以存取此應用程式。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>步驟3：修改應用程式的回復 URI

在 Power BI 的行動應用程式可以連接和存取報表服務之前，您必須先設定在步驟2中為您自動建立的應用程式註冊。

1. 在 Azure Active Directory [概觀] 頁面上，選取 [應用程式註冊]。
2. 在 [**所有應用程式**] 索引標籤底下，搜尋您在步驟2中建立的應用程式。
3. 選取應用程式，然後選取 [驗證]。
4. 根據您所使用的平台，新增下列重新導向 URI。

   為 Power BI 行動版**iOS**設定應用程式時，請新增下列類型的重新導向 Uri：公用用戶端（行動 & 桌面）：
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   為 Power BI 行動版**Android**設定應用程式時，請新增下列類型的重新導向 Uri：公用用戶端（行動 & 桌面）：
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 必須新增重新導向 URI，應用程式才能正常運作。 如果您要為 Power BI 行動版 iOS 和 Android 設定應用程式，請將以下類型的 [公用用戶端（行動 & 桌面）] 重新導向 URI 新增至針對 iOS 所設定的重新導向 Uri 清單： `urn:ietf:wg:oauth:2.0:oob` 。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>步驟4：從 Power BI 行動版應用程式連接

1. 在 Power BI 行動裝置應用程式中，連接到您的 Reporting Services 實例。 若要這麼做，請輸入您透過應用程式 Proxy 發佈之應用程式的**外部 URL** 。

   ![Power BI 具有外部 URL 的行動應用程式](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. 選取 [連接]。 系統會將您導向至 Azure Active Directory 登入] 頁面。

3. 輸入使用者的有效認證，然後選取 [登入]。 您會看到 Reporting Services 伺服器中的元素。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>步驟5：設定受管理裝置的 Intune 原則（選擇性）

您可以使用 Microsoft Intune 來管理公司員工使用的用戶端應用程式。 Intune 可讓您使用資料加密和其他存取需求之類的功能。 若要深入瞭解透過 Intune 管理應用程式，請參閱 Intune 應用程式管理。 若要讓 Power BI 行動應用程式使用 Intune 原則，請使用下列步驟。

1. 前往 [ **Azure Active Directory** ]，然後移至 [**應用程式註冊**]。
2. 在註冊您的原生用戶端應用程式時，選取在步驟3中設定的應用程式。
3. 在應用程式的頁面上，選取 [ **API 許可權**]。
4. 按一下 [**新增許可權**]。
5. 在 [**我的組織使用的 api**] 底下，搜尋「Microsoft 行動應用程式管理」並加以選取。
6. 將**DeviceManagementManagedApps**許可權新增至應用程式
7. 按一下 [授與系統**管理員同意**]，授與應用程式的存取權。
8. 參考[如何建立和指派應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policies)，以設定您想要的 Intune 原則。

## <a name="troubleshooting"></a>疑難排解

如果應用程式在嘗試載入報表超過幾分鐘後傳回錯誤頁面，您可能需要變更 [超時] 設定。 根據預設，應用程式 Proxy 支援最長達85秒的應用程式來回應要求。 若要將這項設定延長為180秒，請在應用程式的 [應用程式 Proxy 設定] 頁面中，選取 [**長期**] 的後端超時。 如需如何建立快速且可靠之報表的秘訣，請參閱[Power BI 報告最佳做法](https://docs.microsoft.com/power-bi/power-bi-reports-performance)。

## <a name="next-steps"></a>後續步驟

- [讓 native client 應用程式與 proxy 應用程式互動](application-proxy-configure-native-client-application.md)
- [在 Power BI 行動裝置應用程式中檢視內部部署報表伺服器報表和 KPI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
