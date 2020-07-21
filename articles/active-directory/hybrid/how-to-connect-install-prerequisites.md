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
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11d5c1bb133f0aea241fbc55f96ab5f8818e5ed6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518108"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect 的必要條件
本主題描述 Azure AD Connect 的必要條件和硬體需求。

## <a name="before-you-install-azure-ad-connect"></a>安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

### <a name="azure-ad"></a>Azure AD
* Azure AD 租用戶。 您可以透過 [Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)取得一個租用戶。 您可以使用下列其中一個入口網站來管理 Azure AD Connect：
  * [Azure 入口網站](https://portal.azure.com)。
  * [Office 入口網站](https://portal.office.com)。  
* [新增並驗證](../active-directory-domains-add-azure-portal.md) 您計畫使用於 Azure AD 中的網域。 例如，如果您計畫讓使用者使用 contoso.com，請確定此網域已經過驗證，而且您不是只使用 contoso.onmicrosoft.com 預設網域。
* Azure AD 租用戶預設允許 5 萬個物件。 當您驗證網域後，此限額會增加到 30 萬個物件。 如果您在 Azure AD 中需要更多的物件，您必須洽詢支援人員以增加此限額。 如果您需要的物件超過500k，則需要授權，例如 Office 365、Azure AD Premium 或企業行動力和安全性。

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
Azure AD Connect 伺服器包含重要的身分識別資料。 請務必妥善保護此伺服器的系統管理存取權，遵循保護特殊許可權[存取](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)中所述的指導方針。 

Azure AD Connect 伺服器必須視為第0層元件，如[Active Directory 管理層模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)中所述。  

若要深入瞭解如何保護您的 Active Directory 環境，請參閱[保護 Active Directory 的最佳做法](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)。

#### <a name="installation-prerequisites"></a>安裝先決條件 

- Azure AD Connect 必須安裝在已加入網域的 Windows Server 2012 或更新版本上。 
- Azure AD Connect 無法安裝至 2019 以前的 Small Business Server 或 Windows Server Essentials (支援 Windows Server Essentials 2019)。 伺服器必須使用 Windows Server Standard 或以上版本。  
- Azure AD Connect 伺服器必須已安裝完整的 GUI。 不支援在 Windows Server Core 上安裝 Azure AD Connect。 
- 如果您使用 Azure AD Connect wizard 來管理 ADFS 設定，Azure AD Connect 伺服器就不能群組原則啟用 PowerShell 轉譯。 如果您使用 Azure AD Connect 精靈來管理同步處理組態，您可以啟用 PowerShell 轉譯。 
- 如果正在部署 Active Directory 同盟服務，則： 
    - 安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。 Windows 遠端管理 ，才能執行遠端安裝。 
    - 您必須設定 TLS/SSL 憑證。  請參閱[管理 ssl/TLS 通訊協定和加密套件，以](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)[在 AD FS 中 AD FS 和管理 ssl 憑證](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)。
    - 您必須設定名稱解析。 
- 如果您的全域系統管理員已啟用 MFA，則 URL https://secure.aadcdn.microsoftonline-p.com **必須**位於信任的網站清單中。 在顯示 MFA 挑戰提示時，如果您尚未將此 URL 新增到信任的網站清單，系統會先提示您將它新增到清單。 您可以使用 Internet Explorer 將它新增到信任的網站。  

#### <a name="hardening-your-azure-ad-connect-server"></a>強化您的 Azure AD Connect 伺服器 
Microsoft 建議強化您的 Azure AD Connect 伺服器，以降低 IT 環境中這項重要元件的安全性受攻擊面。 遵循下列建議可協助降低貴組織的一些安全性風險。

- 您必須將 Azure AD Connect 視為網域控制站和其他第0層資源：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material 
- 您應該將 Azure AD Connect 伺服器的系統管理存取許可權制為只有網域系統管理員或其他嚴格控制的安全性群組。
- 您應該[為具有特殊許可權存取權的所有人員建立專用帳戶](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)。 系統管理員不應以具備高特殊權限的帳戶瀏覽網頁、查看其電子郵件，以及執行日常生產力工作。
- 您應遵循保護特殊許可權[存取](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts)中所提供的指導方針。 
- 您應該確定每部電腦都有唯一的本機系統管理員密碼。 [本機系統管理員密碼解決方案（LAPS）](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps)可以在每個工作站上設定唯一的隨機密碼，而伺服器則會將它們儲存在 ACL 所保護的 ACTIVE DIRECTORY （AD）中。 只有合格的授權使用者才可讀取或要求重設這些本機系統管理員帳戶密碼。 您可以從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.)取得要在工作站和伺服器上使用的 LAPS。 如需使用 LAPS 和 Paw 操作環境的其他指引，請參閱以[乾淨來源原則為基礎的操作標準](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)。 
- 您應針對具有貴組織資訊系統特殊許可權存取權的所有人員，執行專用的特殊[許可權存取工作站（PAW）](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) 。 
- 您應該遵循這些[額外的指導方針](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)，減少 Active Directory 環境的受攻擊面。


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect 使用的 SQL Server
* Azure AD Connect 需要 SQL Server 資料庫來儲存身分識別資料。 預設會安裝 SQL Server 2012 Express LocalDB (SQL Server Express 的精簡版)。 SQL Server Express 有 10 GB 的大小限制，可讓您管理大約 100,000 個物件。 如果您需要管理更多數量的目錄物件，則必須將安裝精靈指向不同的 SQL Server 安裝。 SQL Server 安裝的類型可能會影響[Azure AD Connect 的效能](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)。
* 如果您使用不同的 SQL Server 安裝，則適用下列需求：
  * Azure AD Connect 支援2012（含最新的 Service Pack）到 SQL Server 2019 的所有 Microsoft SQL Server 版本。 **不支援** 使用 Microsoft Azure SQL Database 作為資料庫。
  * 您必須使用不區分大小寫的 SQL 定序。 這些定序是在其名稱中使用 \_CI_ 來識別。 **不支援**使用區分大小寫的定序 (在其名稱中以 \_CS_ 來識別)。
  * 您在每個 SQL 執行個體中只能有一個同步引擎。 **不支援** 使用 FIM/MIM Sync、DirSync 或 Azure AD Sync 來共用 SQL 執行個體。

### <a name="accounts"></a>帳戶
* 想要與其整合之 Azure AD 租用戶的 Azure AD 全域管理員帳戶。 此帳戶必須是**學校或組織帳戶**，不能是 **Microsoft 帳戶**。
* 如果您使用[快速設定](reference-connect-accounts-permissions.md#express-settings-installation)或從 DirSync 升級，則您必須擁有內部部署 Active Directory 的企業系統管理員帳戶。
* 如果您使用自訂設定安裝路徑，則會有更多選項。 如需詳細資訊，請參閱[自訂安裝設定](reference-connect-accounts-permissions.md#custom-installation-settings)。

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

* 如果您的 Proxy 伺服器需要驗證，則[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)必須位於網域中，且您必須使用自訂的設定安裝路徑來指定[自訂服務帳戶](how-to-connect-install-custom.md#install-required-components)。 您也需要對 machine.config 進行不同的變更。隨著 machine.config 中的這項變更，安裝精靈和同步處理引擎會回應來自 proxy 伺服器的驗證要求。 在所有安裝精靈頁面中 ([設定] **** 頁面除外)，都會使用已登入之使用者的認證。 在安裝精靈結尾的 [設定]**** 頁面上，內容會切換到您建立的[服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account)。 Machine.config 區段應該看起來像這樣。

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
### <a name="powershell-and-net-framework"></a>PowerShell 和 .NET Framework
Azure AD Connect 需要 Microsoft PowerShell 和 .NET Framework 4.5.1。 您需要在伺服器上安裝此版本或更新版本。 依您的 Windows Server 版本來執行下列作業：

* Windows Server 2012R2
  * 預設會安裝 Microsoft PowerShell。 您不需要執行任何動作。
  * .NET Framework 4.5.1 和更新版本會透過 Windows Update 提供。 請確定您已在控制台安裝 Windows Server 的最新更新。
* Windows Server 2012
  * **Windows Management Framework 4.0**中包含最新的 Microsoft PowerShell 版本，可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。
  * .NET Framework 4.5.1 和更新版本可從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得。


### <a name="enable-tls-12-for-azure-ad-connect"></a>啟用 Azure AD Connect 的 TLS 1.2
在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎伺服器與 Azure AD 之間的通訊。 您可以設定 .NET 應用程式在伺服器上預設使用 TLS 1.2，藉此進行變更。 您可以在 [Microsoft 資訊安全摘要報告 2960358](https://technet.microsoft.com/security/advisory/2960358) 中找到 TLS 1.2 的相關詳細資訊。

1.  請確定您已安裝適用于您作業系統的 .NET 4.5.1 修補程式，請參閱[Microsoft 安全性諮詢 2960358](https://technet.microsoft.com/security/advisory/2960358)。 您的伺服器上可能已經安裝此 Hotfix 或更新版本。
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "SchUseStrongCrypto" = dword：00000001
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
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
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
