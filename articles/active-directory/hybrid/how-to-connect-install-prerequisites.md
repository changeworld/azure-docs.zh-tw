---
title: Azure AD Connect：必要條件與硬體 | Microsoft Docs
description: 本文說明 Azure AD Connect 的必要條件和硬體需求。
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
ms.openlocfilehash: 1fa96d6bd0032f675ffaeabc58c62c13312039dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662169"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect 的必要條件
本文說明 Azure Active Directory (Azure AD) Connect 的必要條件和硬體需求。

## <a name="before-you-install-azure-ad-connect"></a>安裝 Azure AD Connect 之前
安裝 Azure AD Connect 之前，您需要注意一些事項。

### <a name="azure-ad"></a>Azure AD
* 您需要 Azure AD 的租使用者。 您可以透過 [Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)取得一個租用戶。 您可以使用下列其中一個入口網站來管理 Azure AD Connect：
  * [Azure 入口網站](https://portal.azure.com)。
  * [Office 入口網站](https://portal.office.com)。
* [新增並驗證](../fundamentals/add-custom-domain.md) 您計畫使用於 Azure AD 中的網域。 例如，如果您打算為使用者使用 contoso.com，請確定此網域已經過驗證，而且您不只使用 contoso.onmicrosoft.com 預設網域。
* Azure AD 的租使用者預設允許50000物件。 當您驗證您的網域時，限制會增加至300000物件。 如果您在 Azure AD 中需要更多物件，請開啟支援案例，讓限制增加更多。 如果您需要500000個以上的物件，您需要授權，例如 Microsoft 365、Azure AD Premium 或 Enterprise Mobility + Security。

### <a name="prepare-your-on-premises-data"></a>準備您的內部部署資料
* 在您同步處理至 Azure AD 和 Microsoft 365 之前，請使用 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) 來識別目錄中的錯誤，例如重複和格式化問題。
* 查看 [您可以在 Azure AD 中啟用的選擇性同步處理功能](how-to-connect-syncservice-features.md)，並評估應該啟用的功能。

### <a name="on-premises-active-directory"></a>內部部署 Active Directory
* Active Directory 架構版本和樹系功能等級必須是 Windows Server 2003 或更新版本。 只要符合架構版本和樹系層級的需求，網域控制站就可以執行任何版本。
* 如果您打算使用功能 *密碼回寫*，則網域控制站必須在 Windows Server 2008 R2 或更新版本上。
* Azure AD 使用的網域控制站必須為可寫入。 *不支援*使用唯讀網域控制站 (RODC) ，而且 Azure AD Connect 不會遵循任何寫入重新導向。
* 使用內部部署樹系或網域（使用「點點」 (名稱包含句點 "."）。 *不支援*) NetBIOS 名稱。
* 建議您 [啟用 Active Directory 回收站](how-to-connect-sync-recycle-bin.md)。

### <a name="azure-ad-connect-server"></a>Azure AD Connect 伺服器
Azure AD Connect 伺服器包含重要的身分識別資料。 此伺服器的系統管理存取權必須受到適當保護。 遵循保護特殊許可權 [存取](/windows-server/identity/securing-privileged-access/securing-privileged-access)的指導方針。 

Azure AD Connect 伺服器必須視為[Active Directory 系統管理層級模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)中記載的第0層元件 

若要深入瞭解如何保護您的 Active Directory 環境，請參閱 [保護 Active Directory 的最佳作法](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)。

#### <a name="installation-prerequisites"></a>安裝必要條件

- Azure AD Connect 必須安裝在已加入網域的 Windows Server 2012 或更新版本上。 
- Azure AD Connect 無法安裝在2019之前的 Small Business Server 或 Windows Server Essentials () 支援 Windows Server Essentials 2019。 伺服器必須使用 Windows Server Standard 或以上版本。 
- Azure AD Connect 伺服器必須已安裝完整的 GUI。 不支援在 Windows Server Core 上安裝 Azure AD Connect。 
- 如果您使用 Azure AD Connect wizard 來管理 Active Directory 同盟服務 (AD FS 設定，Azure AD Connect 伺服器就不能群組原則啟用 PowerShell 轉譯。 如果您使用 Azure AD Connect wizard 來管理同步處理設定，則可以啟用 PowerShell 轉譯。 
- 如果正在部署 AD FS： 
    - 安裝 AD FS 或 Web 應用程式 Proxy 的伺服器必須是 Windows Server 2012 R2 或更新版本。 Windows 遠端管理 ，才能執行遠端安裝。 
    - 您必須設定 TLS/SSL 憑證。 如需詳細資訊，請參閱[在 AD FS 中管理 AD FS 和管理 ssl 憑證](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)[的 ssl/TLS 通訊協定和加密套件](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)。
    - 您必須設定名稱解析。 
- 如果您的全域管理員已啟用 MFA，則 URL https://secure.aadcdn.microsoftonline-p.com *必須* 在 [信任的網站] 清單中。 當系統提示您提供 MFA 挑戰，但尚未新增之前，系統會提示您將此網站新增至 [信任的網站] 清單。 您可以使用 Internet Explorer 將它新增到信任的網站。

#### <a name="harden-your-azure-ad-connect-server"></a>強化您的 Azure AD Connect 伺服器 
建議您強化 Azure AD Connect 伺服器，以減少 IT 環境中這個重要元件的安全性攻擊面。 遵循這些建議將有助於減輕組織的一些安全性風險。

- 將 Azure AD Connect 視為與網域控制站和其他第0層資源相同。 如需詳細資訊，請參閱 [Active Directory 系統管理層模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)。
- 僅限網域系統管理員或其他嚴格控制的安全性群組，限制對 Azure AD Connect 伺服器的系統管理存取權。
- [為具有特殊許可權存取權的所有人員建立專用帳戶](/windows-server/identity/securing-privileged-access/securing-privileged-access)。 系統管理員不應流覽網頁、檢查其電子郵件，以及使用具有高許可權的帳戶來執行日常生產力工作。
- 遵循保護特殊許可權 [存取](/windows-server/identity/securing-privileged-access/securing-privileged-access)時所提供的指導方針。 
- 拒絕對 AADConnect 伺服器使用 NTLM 驗證。 以下是一些執行此動作的方法： [限制 AADConnect 伺服器上的 ntlm](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) ，以及 [限制網域上的 ntlm](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- 確定每部電腦都有唯一的本機系統管理員密碼。 如需詳細資訊，請參閱 [區域系統管理員密碼解決方案 (LAPS) ](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) 可以在每部工作站上設定唯一的隨機密碼，並將它們儲存在 ACL 保護的 Active Directory 中。 只有合格的授權使用者才可讀取或要求重設這些本機系統管理員帳戶密碼。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.)取得要在工作站和伺服器上使用的 LAPS。 使用 LAPS 和特殊許可權存取工作站來操作環境的其他指導方針 (Paw) 可在以「 [完全來源」原則為基礎的操作標準](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)中找到。 
- 針對具有貴組織資訊系統的特殊許可權存取權的所有人員，執行專用的特殊許可權 [存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 。 
- 遵循這些 [額外的指導方針](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) ，以降低 Active Directory 環境的受攻擊面。


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect 使用的 SQL Server
* Azure AD Connect 需要 SQL Server 資料庫來儲存身分識別資料。 根據預設，SQL Server 2012 Express LocalDB (安裝) 的輕 SQL Server Express 量版本。 SQL Server Express 有 10 GB 的大小限制，可讓您管理大約100000的物件。 如果您需要管理更大量的目錄物件，請將安裝精靈指向不同的 SQL Server 安裝。 SQL Server 安裝的類型可能會影響 [Azure AD Connect 的效能](./plan-connect-performance-factors.md#sql-database-factors)。
* 如果您使用不同的 SQL Server 安裝，則適用下列需求：
  * Azure AD Connect 支援 SQL Server 2012 (的所有版本，並以最新的 service pack) 到 SQL Server 2019。 Azure SQL Database *不支援* 做為資料庫。
  * 您必須使用不區分大小寫的 SQL 定序。 這些定序是在其名稱中使用 \_CI_ 來識別。 \_*不支援*在名稱中使用 CS_ 所識別的區分大小寫定序。
  * 每個 SQL 實例只能有一個同步處理引擎。 *不支援*使用 FIM/MIM Sync、DirSync 或 AZURE AD 同步共用 SQL 實例。

### <a name="accounts"></a>帳戶
* 您必須具有您想要整合之 Azure AD 租使用者的全域系統管理員帳戶 Azure AD。 此帳戶必須是 *學校或組織帳戶* ，且不能是 *Microsoft 帳戶*。
* 如果您使用 [快速設定](reference-connect-accounts-permissions.md#express-settings-installation) 或從 DirSync 升級，您必須具有內部部署 Active Directory 的企業系統管理員帳戶。
* 如果您使用自訂設定安裝路徑，您會有更多選項。 如需詳細資訊，請參閱 [自訂安裝設定](reference-connect-accounts-permissions.md#custom-installation-settings)。

### <a name="connectivity"></a>連線能力
* Azure AD Connect 伺服器需要內部網路和網際網路的 DNS 解析。 DNS 伺服器必須能夠將名稱解析成您的內部部署 Active Directory 和 Azure AD 端點。
* 如果您的內部網路有防火牆，而您需要開啟 Azure AD Connect 伺服器與網域控制站之間的埠，請參閱 [Azure AD Connect 埠](reference-connect-ports.md) 以取得詳細資訊。
* 如果您的 proxy 或防火牆限制可以存取的 Url，則必須開啟 [Office 365 url 和 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 中記載的 url。
  * 如果您是在德國或 Microsoft Azure Government 雲端中使用 Microsoft cloud，請參閱 [Azure AD Connect 同步處理服務實例](reference-connect-instances.md) 的 url 考慮。
* Azure AD Connect (1.1.614.0 和更高版本) 預設會使用 TLS 1.2 來加密同步引擎與 Azure AD 之間的通訊。 若 TLS 1.2 無法在基礎作業系統上使用，Azure AD Connect 會逐步回到較舊的通訊協定 (TLS 1.1 和 TLS 1.0)。
* 在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎與 Azure AD 之間的通訊。 若要變更為 TLS 1.2，請依照[啟用 Azure AD Connect 的 TLS 1.2](#enable-tls-12-for-azure-ad-connect) 中的步驟。
* 如果您要使用輸出 proxy 來連線至網際網路，則必須為安裝精靈新增 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 檔案中的下列設定，並 Azure AD Connect 同步處理，以連線到網際網路並 Azure AD。 必須在檔案底部輸入此文字。 在此程式碼中， * &lt; PROXYADDRESS &gt; *代表實際的 proxy IP 位址或主機名稱。

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

* 如果您的 proxy 伺服器需要驗證，則 [服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account) 必須位於網域中。 使用自訂設定安裝路徑來指定 [自訂的服務帳戶](how-to-connect-install-custom.md#install-required-components)。 您也需要不同的 machine.config 變更。在 machine.config 的這項變更中，安裝精靈和同步處理引擎會回應來自 proxy 伺服器的驗證要求。 在所有安裝精靈頁面中（[ **設定** ] 頁面除外），會使用已登入使用者的認證。 在安裝精靈結尾的 [ **設定** ] 頁面上，內容會切換到您建立的 [服務帳戶](reference-connect-accounts-permissions.md#adsync-service-account) 。 machine.config 區段看起來應該像這樣：

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

* 如果在現有的安裝程式中完成 proxy 設定，就必須重新開機 **Microsoft Azure AD 同步處理服務** ，Azure AD Connect 才能讀取 proxy 設定並更新行為。 
* 當 Azure AD Connect 在同步處理目錄的過程中對 Azure AD 傳送了 Web 要求，Azure AD 最多可能需要 5 分鐘的時間才會回應。 Proxy 伺服器通常會有連接閒置超時設定。 請確定設定至少設定為6分鐘以上。

如需詳細資訊，請參閱 MSDN 關於 [預設 proxy 元素](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)。
如需連線問題的詳細資訊，請參閱[針對連線問題進行疑難排解](tshoot-connect-connectivity.md)。

### <a name="other"></a>其他
選擇性：使用測試使用者帳戶來驗證同步處理。

## <a name="component-prerequisites"></a>元件的必要條件
### <a name="powershell-and-net-framework"></a>PowerShell 和 .NET Framework
Azure AD Connect 需要 Microsoft PowerShell 和 .NET Framework 4.5.1。 您需要在伺服器上安裝此版本或更新版本。 視您的 Windows Server 版本而定，採取下列動作：

* Windows Server 2012 R2
  * 預設會安裝 Microsoft PowerShell。 您不需要執行任何動作。
  * .NET Framework 4.5.1 和更新版本會透過 Windows Update 提供。 請確定您已在主控台中安裝最新的 Windows Server 更新。
* Windows Server 2012
  * 您可以從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得的 Windows Management Framework 4.0 提供最新版本的 microsoft PowerShell。
  * 您可以從 [Microsoft 下載中心](https://www.microsoft.com/downloads)取得 .NET Framework 4.5.1 和更新版本。


### <a name="enable-tls-12-for-azure-ad-connect"></a>啟用 Azure AD Connect 的 TLS 1.2
在 1.1.614.0 版之前的版本中，Azure AD Connect 預設會使用 TLS 1.0 來加密同步引擎伺服器與 Azure AD 之間的通訊。 您可以設定 .NET 應用程式在伺服器上預設使用 TLS 1.2。 如需 TLS 1.2 的詳細資訊，請參閱 [Microsoft 資訊安全諮詢 2960358](/security-updates/SecurityAdvisories/2015/2960358)。

1. 請確定您已為您的作業系統安裝 .NET 4.5.1 的修正程式。 如需詳細資訊，請參閱 [Microsoft 資訊安全諮詢 2960358](/security-updates/SecurityAdvisories/2015/2960358)。 您的伺服器上可能已經安裝此 Hotfix 或更新版本。

1. 請針對所有作業系統設定此登錄機碼並重新啟動伺服器。
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. 如果您也想要在同步處理引擎伺服器與遠端 SQL Server 之間啟用 TLS 1.2，請確定您已安裝必要的版本，以供 [Microsoft SQL Server 的 TLS 1.2 支援](https://support.microsoft.com/kb/3135244)。

## <a name="prerequisites-for-federation-installation-and-configuration"></a>同盟安裝和組態的必要條件
### <a name="windows-remote-management"></a>Windows 遠端管理
當您使用 Azure AD Connect 部署 AD FS 或 Web 應用程式 Proxy (WAP) 時，請檢查這些需求：

* 如果目標伺服器已加入網域，請確定已啟用 Windows 遠端系統管理。
  * 在提高許可權的 PowerShell 命令視窗中，使用命令 `Enable-PSRemoting –force` 。
* 如果目標伺服器是未加入網域的 WAP 電腦，則有幾個額外的需求：
  * 在目標電腦 (WAP 電腦) 上：
    * 確認 Windows 遠端管理/WS-MANAGEMENT (WinRM) 服務正在透過 [服務] 嵌入式管理單元執行。
    * 在提高許可權的 PowerShell 命令視窗中，使用命令 `Enable-PSRemoting –force` 。
  * 在執行嚮導的電腦上 (如果目的電腦未加入網域，或) 不受信任的網域：
    * 在提高許可權的 PowerShell 命令視窗中，使用命令 `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 。
    * 在 [伺服器管理員] 中：
      * 將 DMZ WAP 主機新增至電腦集區。 在 [伺服器管理員] 中，選取 [**管理**  >  **新增伺服器**]，然後使用 [ **DNS** ] 索引標籤。
      * 在 [ **伺服器管理員所有伺服器** ] 索引標籤上，以滑鼠右鍵按一下 WAP 伺服器，然後選取 [ **管理為**]。 輸入 WAP 電腦的本機 (非網域) 認證。
      * 若要驗證遠端 PowerShell 連線性，請在 [ **伺服器管理員所有伺服器** ] 索引標籤上，以滑鼠右鍵按一下 WAP 伺服器，然後選取 [ **Windows PowerShell**]。 應該會開啟遠端 PowerShell 會話，以確保可以建立遠端 PowerShell 會話。

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 憑證需求
* 建議您在 AD FS 伺服陣列的所有節點以及所有 Web 應用程式 Proxy 伺服器上，使用相同的 TLS/SSL 憑證。
* 此憑證必須是 X509 憑證。
* 您可以在測試實驗室環境中的同盟伺服器上使用自我簽署的憑證。 在生產環境中，我們建議您從公開憑證授權單位單位取得憑證。
  * 如果您使用不是公開信任的憑證，請確定本機伺服器和所有同盟伺服器上都信任安裝在每個 Web 應用程式 Proxy 伺服器上的憑證。
* 憑證的身分識別必須與同盟服務名稱相符 (例如 sts.contoso.com)。
  * 身分識別為 dNSName 類型之 SAN) 延伸 (的主體別名，或者，如果沒有 SAN 專案，則會將主體名稱指定為一般名稱。
  * 有多個 SAN 專案可以存在於憑證中，前提是其中一個符合 federation service 名稱。
  * 如果您打算使用 Workplace Join，enterpriseregistration 值需要額外的 SAN **。** 然後，在您的組織中 (UPN) 尾碼的使用者主體名稱，例如，enterpriseregistration.contoso.com。
* 以 CryptoAPI 新一代 (CNG) 金鑰和金鑰儲存提供者為基礎的憑證 (Ksp) 不受支援。 因此，您必須使用以密碼編譯服務提供者為基礎的憑證 (CSP) 而不是 KSP。
* 支援萬用字元憑證。

### <a name="name-resolution-for-federation-servers"></a>同盟伺服器的名稱解析
* 設定 AD FS 名稱的 DNS 記錄 (例如，sts.contoso.com) 內部 DNS 伺服器的內部網路 (，以及透過網域註冊機構) 的外部網路 (公用 DNS。 請確認內部網路 DNS 記錄是使用 A 記錄而非 CNAME 記錄。 您必須使用記錄才能讓 Windows 驗證正確地從已加入網域的電腦上運作。
* 如果您要部署一個以上的 AD FS 伺服器或 Web 應用程式 Proxy 伺服器，請確定您已設定負載平衡器，而且 AD FS 名稱的 DNS 記錄 (例如，sts.contoso.com) 指向負載平衡器。
* 若要讓 Windows 整合式驗證適用于在您的內部網路中使用 Internet Explorer 的瀏覽器應用程式，請確定 AD FS 名稱 (例如，將 sts.contoso.com) 新增至 Internet Explorer 的內部網路區域。 您可以透過群組原則來控制這項需求，並將其部署至所有已加入網域的電腦。

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 支援元件
Azure AD Connect 在安裝 Azure AD Connect 的伺服器上安裝下列元件。 此清單適用於基本快速安裝。 如果您在 [ **安裝同步處理服務** ] 頁面上選擇使用不同的 SQL Server，則不會在本機安裝 SQL Express LocalDB。

* Azure AD Connect Health
* Microsoft SQL Server 2012 命令列公用程式
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect 的硬體需求
下表顯示 Azure AD Connect 同步電腦的最低需求。

| Active Directory 中的物件數目 | CPU | 記憶體 | 硬碟大小 |
| --- | --- | --- | --- |
| 少於 10,000 個 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 個 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 個 |1.6 GHz |16 GB |100 GB |
| 若為100000或更多物件，則需要 SQL Server 的完整版本 | | | |
| 100,000–300,000 個 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 個 |1.6 GHz |32 GB |450 GB |
| 超過 600,000 個 |1.6 GHz |32 GB |500 GB |

執行 AD FS 或 Web 應用程式 Proxy 伺服器之電腦的最低需求如下：

* CPU：雙核心 1.6 GHz 以上
* 記憶體： 2 GB 或更高
* Azure VM：A2 組態或更高等級

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。