---
title: Azure AD Connect：必要條件與硬體 | Microsoft Docs
description: 本主題描述 Azure AD Connect 的必要條件和硬體需求。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79741557e6eea1b4252e5ab4d9976b124cea1169
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346895"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect 的必要條件
本主題描述 Azure AD Connect 的必要條件和硬體需求。

## <a name="before-you-install-azure-ad-connect"></a>安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

### <a name="azure-ad"></a>Azure AD
* Azure AD 租用戶。 您可以透過 [Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)取得一個租用戶。 您可以使用下列其中一個入口網站來管理 Azure AD Connect：
  * [Azure 門戶](https://portal.azure.com)。
  * [Office 入口網站](https://portal.office.com)。  
* [新增並驗證](../active-directory-domains-add-azure-portal.md) 您計畫使用於 Azure AD 中的網域。 例如，如果您計畫讓使用者使用 contoso.com，請確定此網域已經過驗證，而且您不是只使用 contoso.onmicrosoft.com 預設網域。
* Azure AD 租用戶預設允許 5 萬個物件。 當您驗證網域後，此限額會增加到 30 萬個物件。 如果您在 Azure AD 中需要更多的物件，您必須洽詢支援人員以增加此限額。 如果您需要 50 萬個以上的物件，您需要如 Office 365、Azure AD Basic、Azure AD Premium 或 Enterprise Mobility + Security 等授權。

### <a name="prepare-your-on-premises-data"></a>準備您的內部部署資料
* 在同步至 Azure AD 和 Office 365 之前，使用 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) 來識別目錄中如重複項目和格式問題等的錯誤。
* 檢閱 [您可在 Azure AD 中啟用的選用同步處理功能](how-to-connect-syncservice-features.md) ，並評估您應該啟用哪些功能。

### <a name="on-premises-active-directory"></a>內部部署 Active Directory
* AD 結構描述版本與樹系功能等級必須是 Windows Server 2003 或更新版本。 只要符合結構描述和樹系層級需求，網域控制站就能執行任何版本。
* 如果您打算使用**密碼回寫**功能，網域控制站必須是 Windows Server 2008 R2 或更新版本。
* Azure AD 使用的網域控制站必須為可寫入。 **不**支援使用 RODC (唯讀網域控制站)，且 Azure AD Connect 不會追蹤任何寫入重新導向。
* **不**支援使用內部部署樹系/帶點 (名稱包含句點 ".") 的 NetBios 名稱的網域。
* 建議您[啟用 Active Directory 資源回收筒](how-to-connect-sync-recycle-bin.md)。

### <a name="azure-ad-connect-server"></a>Azure AD Connect 伺服器
>[!IMPORTANT]
>Azure AD Connect 伺服器包含關鍵標識資料，應視為在[Active Directory 管理層模型中記錄的](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)0 層元件

* Azure AD Connect 無法安裝至 2019 以前的 Small Business Server 或 Windows Server Essentials (支援 Windows Server Essentials 2019)。 伺服器必須使用 Windows Server Standard 或以上版本。
* 由於安全實踐和限制設置可能阻止 Azure AD Connect 正確安裝，因此不建議在網域控制站上安裝 Azure AD 連接。
* Azure AD Connect 伺服器必須已安裝完整的 GUI。 **不**支援在伺服器核心上安裝。
>[!IMPORTANT]
>不支援在小型企業伺服器、伺服器必需品或伺服器核心上安裝 Azure AD 連接。

* Azure AD 連接必須安裝在 Windows 伺服器 2012 或更高版本中。 此伺服器必須加入域，並且可能是網域控制站或成員伺服器。
* 如果使用 Azure AD 連接嚮導來管理 ADFS 配置，則 Azure AD 連接伺服器不得啟用 PowerShell 轉錄群組原則。 如果您使用 Azure AD Connect 精靈來管理同步處理組態，您可以啟用 PowerShell 轉譯。
* 如果部署的是 Active Directory Federation Services，則安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。 [Windows 遠端管理](#windows-remote-management) ，才能執行遠端安裝。
* 如果正在部署活動目錄聯合服務，則需要[TLS/SSL 憑證](#tlsssl-certificate-requirements)。
* 如果部署的是 Active Directory 同盟服務，您就需要設定 [名稱解析](#name-resolution-for-federation-servers)。
* 如果您的全域管理員啟用了 MFA，則 URL**https://secure.aadcdn.microsoftonline-p.com**必須位於受信任的網站清單中。 在顯示 MFA 挑戰提示時，如果您尚未將此 URL 新增到信任的網站清單，系統會先提示您將它新增到清單。 您可以使用 Internet Explorer 將它新增到信任的網站。
* Microsoft 建議強化 Azure AD 連接伺服器，以減少 IT 環境此關鍵元件的安全攻擊面。  遵循以下建議將減少組織的安全風險。

* 在加入的域伺服器上部署 Azure AD 連接，並限制對域管理員或其他嚴格控制的安全性群組進行管理訪問。

若要深入了解，請參閱： 

* [保護管理員組](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [保護內置管理員帳戶](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [通過減少攻擊面改善和維持安全性](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [減少活動目錄攻擊面](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect 使用的 SQL Server
* Azure AD Connect 需要 SQL Server 資料庫來儲存身分識別資料。 預設會安裝 SQL Server 2012 Express LocalDB (SQL Server Express 的精簡版)。 SQL Server Express 有 10 GB 的大小限制，可讓您管理大約 100,000 個物件。 如果您需要管理更多數量的目錄物件，則必須將安裝精靈指向不同的 SQL Server 安裝。 SQL 伺服器安裝的類型可能會影響 Azure [AD 連接的性能](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)。
* 如果使用 SQL Server 的不同安裝，則這些要求適用：
  * Azure AD 連接支援從 2012 年（使用最新服務包）到 SQL Server 2019 的所有版本的 Microsoft SQL 伺服器。 **不支援** 使用 Microsoft Azure SQL Database 作為資料庫。
  * 您必須使用不區分大小寫的 SQL 定序。 這些定序是在其名稱中使用 \_CI_ 來識別。 **不支援**使用區分大小寫的定序 (在其名稱中以 \_CS_ 來識別)。
  * 您在每個 SQL 執行個體中只能有一個同步引擎。 **不支援** 使用 FIM/MIM Sync、DirSync 或 Azure AD Sync 來共用 SQL 執行個體。

### <a name="accounts"></a>帳戶
* 想要與其整合之 Azure AD 租用戶的 Azure AD 全域管理員帳戶。 此帳戶必須是**學校或組織帳戶**，不能是 **Microsoft 帳戶**。
* 如果使用[快速設置](reference-connect-accounts-permissions.md#express-settings-installation)或從 DirSync 升級，則必須為本地活動目錄提供企業管理員帳戶。
* 如果使用自訂設置安裝路徑，則有更多的選項，請參閱[活動目錄中的帳戶](reference-connect-accounts-permissions.md#custom-installation-settings)

### <a name="connectivity"></a>連線能力
* Azure AD Connect 伺服器需要內部網路和網際網路的 DNS 解析。 DNS 伺服器必須能夠將名稱解析成您的內部部署 Active Directory 和 Azure AD 端點。
* 如果您的內部網路有防火牆，而您需要開放 Azure AD Connect 伺服器與網域控制站之間的連接埠，請參閱 [Azure AD Connect 連接埠](reference-connect-ports.md)以了解詳細資訊。
* 如果您的 Proxy 或防火牆會限制可以存取的 URL，則必須開啟 [Office 365 URL 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中記載的 URL。
  * 如果您是使用 Microsoft Cloud Germany，或是使用 Microsoft Azure Government 雲端，則請參閱 [Azure AD Connect：執行個體的特殊考量](reference-connect-instances.md) 中的 URL。
* Azure AD Connect (1.1.614.0 和更高版本) 預設會使用 TLS 1.2 來加密同步引擎與 Azure AD 之間的通訊。 若 TLS 1.2 無法在基礎作業系統上使用，Azure AD Connect 會逐步回到較舊的通訊協定 (TLS 1.1 和 TLS 1.0)。
* 在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎與 Azure AD 之間的通訊。 若要變更為 TLS 1.2，請依照[啟用 Azure AD Connect 的 TLS 1.2](#enable-tls-12-for-azure-ad-connect) 中的步驟。
* 如果您使用連出 Proxy 來連線到網際網路，就必須在 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 檔案中新增下列設定，安裝精靈和 Azure AD Connect 同步處理才能夠連線到網際網路和 Azure AD。 必須在檔案底部輸入此文字。 在此程式碼中，&lt;PROXYADDRESS&gt; 代表實際的 Proxy IP 位址或主機名稱。

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* 如果您的 Proxy 伺服器需要驗證，則[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)必須位於網域中，且您必須使用自訂的設定安裝路徑來指定[自訂服務帳戶](how-to-connect-install-custom.md#install-required-components)。 您還需要對機器進行不同的更改。在電腦.config 中進行此更改後，安裝精靈和同步引擎會回應來自代理伺服器的身份驗證請求。 在所有安裝精靈頁面中 ([設定] **** 頁面除外)，都會使用已登入之使用者的認證。 在安裝精靈結尾的 [設定]**** 頁面上，內容會切換到您建立的[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)。 Machine.config 區段應該看起來像這樣。

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* 當 Azure AD Connect 在同步處理目錄的過程中對 Azure AD 傳送了 Web 要求，Azure AD 最多可能需要 5 分鐘的時間才會回應。 Proxy 伺服器常會有連線閒置逾時設定。 請確定此設定至少設為 6 分鐘以上。

如需詳細資訊，請參閱關於[預設 Proxy 元素 (英文)](https://msdn.microsoft.com/library/kd3cf2ex.aspx) 的 MSDN。  
如需連線問題的詳細資訊，請參閱[針對連線問題進行疑難排解](tshoot-connect-connectivity.md)。

### <a name="other"></a>其他
* 選用：測試使用者帳戶來驗證同步處理。

## <a name="component-prerequisites"></a>元件的必要條件
### <a name="powershell-and-net-framework"></a>PowerShell 和 .NET 框架
Azure AD Connect 需要 Microsoft PowerShell 和 .NET Framework 4.5.1。 您需要在伺服器上安裝此版本或更新版本。 依您的 Windows Server 版本來執行下列作業：

* Windows Server 2012R2
  * 預設會安裝 Microsoft PowerShell。 您不需要執行任何動作。
  * .NET Framework 4.5.1 和更新版本會透過 Windows Update 提供。 請確定您已在控制台安裝 Windows Server 的最新更新。
* Windows Server 2012
  * **Windows Management Framework 4.0**中包含最新的 Microsoft PowerShell 版本，可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。
  * .NET Framework 4.5.1 和更新版本可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。


### <a name="enable-tls-12-for-azure-ad-connect"></a>啟用 Azure AD Connect 的 TLS 1.2
在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎伺服器與 Azure AD 之間的通訊。 您可以通過配置 .NET 應用程式來更改此情況，以便預設情況下在伺服器上使用 TLS 1.2。 您可以在 [Microsoft 資訊安全摘要報告 2960358](https://technet.microsoft.com/security/advisory/2960358) 中找到 TLS 1.2 的相關詳細資訊。

1.  請確保您已為作業系統安裝了 .NET 4.5.1 修補程式，請參閱 Microsoft[安全通報 2960358](https://technet.microsoft.com/security/advisory/2960358)。 您的伺服器上可能已經安裝此 Hotfix 或更新版本。
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE_SOFTWARE_微軟\.NETFramework_v4.0.30319 "SchUseStrongCrypto"=dword：00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It’s strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
