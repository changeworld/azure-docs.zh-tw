---
title: 在 Azure Active Directory 中安裝 Connect Health 代理程式
description: 本 Azure AD Connect Health 文章說明 Active Directory 同盟服務 (AD FS) 和同步處理的代理程式安裝。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 365c9a990f16e9077450ae15f6677ab716fc45ba
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900445"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health 代理程式安裝

在本文中，您將瞭解如何安裝及設定 Azure Active Directory (Azure AD) Connect Health 代理程式。 若要下載代理程式，請參閱 [這些指示](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)。

## <a name="requirements"></a>規格需求

下表列出使用 Azure AD Connect Health 的需求。

| 需求 | 描述 |
| --- | --- |
| 已安裝 Azure AD Premium。 |Azure AD Connect Health 是 Azure AD Premium 的功能。 如需詳細資訊，請參閱 [註冊 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)。 <br /><br />若要開始30天的免費試用版，請參閱 [開始試用](https://azure.microsoft.com/trial/get-started-active-directory/)。 |
| 您是 Azure AD 中的全域管理員。 |依預設，只有全域系統管理員可以安裝和設定健康情況代理程式、存取入口網站，以及在 Azure AD Connect Health 內進行任何作業。 如需詳細資訊，請參閱[管理您的 Azure AD 目錄](../fundamentals/active-directory-whatis.md)。 <br /><br /> 藉由使用 Azure 角色型存取控制 (Azure RBAC) ，您可以讓組織中的其他使用者存取 Azure AD Connect Health。 如需詳細資訊，請參閱 [適用于 Azure AD Connect Health 的 AZURE RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。 <br /><br />**重要**：使用工作或學校帳戶來安裝代理程式。 您無法使用 Microsoft 帳戶。 如需詳細資訊，請參閱 [以組織方式註冊 Azure](../fundamentals/sign-up-organization.md)。 |
| Azure AD Connect Health 代理程式安裝在每部目標伺服器上。 | 您必須在目標伺服器上安裝和設定健康情況代理程式，才能接收資料及提供監視和分析功能。 <br /><br />例如，若要從 Active Directory 同盟服務 (AD FS) 基礎結構取得資料，您必須在 AD FS 伺服器和 Web 應用程式 Proxy 伺服器上安裝代理程式。 同樣地，若要從內部部署 Azure AD Domain Services (Azure AD DS) 基礎結構取得資料，您必須在網域控制站上安裝代理程式。  |
| Azure 服務端點有輸出連線能力。 | 在安裝期間和執行階段，代理程式需要連線至 Azure AD Connect Health 服務端點。 如果防火牆封鎖輸出連線能力，請將 [輸出連線端點](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) 新增至允許清單。 |
|輸出連線能力是以 IP 位址為基礎。 | 如需以 IP 位址為基礎的防火牆篩選的相關資訊，請參閱 [AZURE IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。|
| 輸出流量的 TLS 檢查已篩選或停用。 | 如果網路層上的輸出流量有 TLS 檢查或終止，則代理程式註冊步驟或資料上傳作業可能會失敗。 如需詳細資訊，請參閱 [設定 TLS 檢查](/previous-versions/tn-archive/ee796230(v=technet.10))。 |
| 伺服器上的防火牆埠正在執行代理程式。 |代理程式需要開啟下列防火牆埠，才能與 Azure AD Connect Health 服務端點進行通訊： <br /><li>TCP 通訊埠 443</li><li>TCP 通訊埠 5671</li> <br />最新版本的代理程式不需要端口5671。 升級至最新版本，僅需要端口443。 如需詳細資訊，請參閱混合式身分 [識別所需的埠和通訊協定](./reference-connect-ports.md)。 |
| 如果已啟用 Internet Explorer 增強式安全性，則允許指定的網站。  |如果已啟用 Internet Explorer 增強式安全性，則在您安裝代理程式的伺服器上允許下列網站：<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>HTTPs： \/ /aadcdn.msftauth.net</li><li>受 Azure AD (所信任之組織的同盟伺服器，例如 HTTPs： \/ /sts.contoso.com) </li> <br />如需詳細資訊，請參閱 [如何設定 Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)。 如果您的網路中有 proxy，請查看出現在此表格結尾的附注。|
| 已安裝 PowerShell 4.0 版或更新版本。 | Windows Server 2012 包含 PowerShell 3.0 版。 此 *版本不足以* 供代理程式之用。</br></br> Windows Server 2012 R2 和更新版本包含足夠的最新版本 PowerShell。|
|已停用 FIPS (聯邦資訊處理標準) 。|Azure AD Connect Health 代理程式不支援 FIPS。|

> [!IMPORTANT]
> Windows Server Core 不支援安裝 Azure AD Connect Health 代理程式。


> [!NOTE]
> 如果您有高度鎖定和受限的環境，您需要新增比資料表列出的 Url 更多的 Url，以 Internet Explorer 增強式安全性。 此外，請新增下一節中的表格所列的 Url。  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure 服務端點的輸出連線

在安裝和執行時間期間，代理程式需要連線至 Azure AD Connect Health 服務端點。 如果防火牆封鎖輸出連線能力，請確定下表中的 Url 預設不會遭到封鎖。 

請勿停用這些 Url 的安全性監視或檢查。 相反地，請允許它們，因為您允許其他網際網路流量。 

這些 Url 可讓您與 Azure AD Connect Health 服務端點進行通訊。 稍後在本文中，您將瞭解如何使用來 [檢查輸出](#test-connectivity-to-azure-ad-connect-health-service) 連線能力 `Test-AzureADConnectHealthConnectivity` 。

| 網域環境 | 必要 Azure 服務端點 |
| --- | --- |
| 一般公用 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>在最新版的代理程式中不需要此端點 &#42; servicebus.windows.net-埠： 5671 (。 ) </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>HTTPs： \/ /www.office.com (此端點僅用於註冊期間的探索用途。 ) </li> |
| Azure Germany | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>HTTPs： \/ /www.office.de (此端點僅用於註冊期間的探索用途。 ) </li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>HTTPs： \/ /www.office.com (此端點僅用於註冊期間的探索用途。 ) </li> |


## <a name="install-the-agent"></a>安裝代理程式

若要下載並安裝 Azure AD Connect Health 代理程式： 

* 請確定您符合 Azure AD Connect Health 的 [需求](how-to-connect-health-agent-install.md#requirements) 。
* 開始使用適用于 AD FS 的 Azure AD Connect Health：
    * [下載 AD FS 的 Azure AD Connect Health 代理程式](https://go.microsoft.com/fwlink/?LinkID=518973)。
    * 請參閱 [安裝指示](#install-the-agent-for-ad-fs)。
* 開始使用 Azure AD Connect Health 進行同步處理：
    * [下載並安裝最新版的 Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771)。 適用于同步處理的健康狀態代理程式會安裝為 Azure AD Connect 安裝 (1.0.9125.0 版或更新版本的) 的一部分。
* 開始使用適用于 Azure AD DS 的 Azure AD Connect Health：
    * [下載 AZURE AD DS 的 Azure AD Connect Health 代理程式](https://go.microsoft.com/fwlink/?LinkID=820540)。
    * 請參閱 [安裝指示](#install-the-agent-for-azure-ad-ds)。

## <a name="install-the-agent-for-ad-fs"></a>安裝 AD FS 的代理程式

> [!NOTE]
> 您的 AD FS 伺服器應該與您的同步伺服器不同。 請勿在同步伺服器上安裝 AD FS 代理程式。
>

安裝代理程式之前，請確定您的 AD FS server 主機名稱是唯一的，而且不存在於 AD FS 服務中。
若要啟動代理程式安裝，請按兩下您所下載的 *.exe* 檔案。 在第一個視窗中，選取 [ **安裝**]。

![顯示 Azure AD Connect Health AD FS 代理程式之安裝視窗的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/install1.png)

安裝完成之後，請選取 [ **立即設定**]。

![顯示 Azure AD Connect Health AD FS 代理程式安裝確認訊息的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/install2.png)

PowerShell 視窗隨即開啟，以啟動代理程式註冊程式。 當系統提示您時，請使用具有註冊代理程式許可權的 Azure AD 帳戶登入。 根據預設，全域系統管理員帳戶具有許可權。

![顯示 Azure AD Connect Health AD FS 登入視窗的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/install3.png)

在您登入之後，PowerShell 會繼續進行。 完成時，您可以關閉 PowerShell。 設定已完成。

此時，代理程式服務應該會自動啟動，以允許代理程式將所需的資料安全地上傳至雲端服務。

如果您還沒有符合所有必要條件，則會在 PowerShell 視窗中顯示警告。 在安裝代理程式之前，請務必先完成 [需求](how-to-connect-health-agent-install.md#requirements) 。 下列螢幕擷取畫面顯示這些警告的範例。

![顯示 Azure AD Connect Health AD FS 設定腳本的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/install4.png)

若要確認代理程式是否已安裝，請在伺服器上尋找下列服務。 如果您已完成設定，這些服務應已在執行中。 否則，它們會停止，直到設定完成為止。

* Azure AD Connect Health AD FS 診斷服務
* Azure AD Connect Health AD FS Insights 服務
* Azure AD Connect Health AD FS 監視服務

![顯示 Azure AD Connect Health AD FS 服務的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>啟用 AD FS 的審核

> [!NOTE]
> 本節只適用于 AD FS 的伺服器。 您不需要在 Web 應用程式 Proxy 伺服器上執行這些步驟。
>

使用方式分析功能需要收集和分析資料。 因此 Azure AD Connect Health 代理程式需要 AD FS audit 記錄檔中的資訊。 預設不會啟用這些記錄。 您可以使用下列程式來啟用 AD FS 的審核，並找出 AD FS 伺服器上的 AD FS audit 記錄檔。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>在 Windows Server 2012 R2 上啟用 AD FS 的稽核

1. 在 [開始畫面上，開啟 **伺服器管理員**，然後開啟 [ **本機安全性原則**]。 在工作列上，開啟 **伺服器管理員**]，然後選取 [ **工具/本機安全性原則**]。
2. 移至 [安全性設為本機的原則 *\ 許可權指派* ] 資料夾。 然後按兩下 [ **產生安全性審核**]。
3. 在 [本機安全性設定]索引標籤上，確認 AD FS 服務帳戶已列出。 如果未列出，請選取 [ **新增使用者或群組**]，並將它新增至清單。 然後選取 [確定]。
4. 若要啟用審核，請以較高的許可權開啟 [命令提示字元] 視窗。 然後，執行下列命令： 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. 關閉 [本機安全性原則]。
    >[!Important]
    >只有主要 AD FS 伺服器才能執行下列步驟。 
1. 開啟 [AD FS 管理] 嵌入式管理單元。  (在 **伺服器管理員** 中，選取 [**工具**]  >  **AD FS 管理**]。 ) 
1. 在 [ **動作** ] 窗格中，選取 [ **編輯同盟服務屬性**]。
1. 在 [ **同盟服務屬性** ] 對話方塊中，選取 [ **事件** ] 索引標籤。
1. 選取 [ **成功審核] 和 [失敗的審核** ] 核取方塊，然後選取 **[確定]**。
1. 若要透過 PowerShell 啟用詳細資訊記錄，請使用下列命令： 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>在 Windows Server 2016 上啟用 AD FS 的稽核

1. 在 [開始畫面上，開啟 **伺服器管理員**，然後開啟 [ **本機安全性原則**]。 在工作列上，開啟 **伺服器管理員**]，然後選取 [ **工具/本機安全性原則**]。
2. 移至 [安全性設為本機的原則 *\ 許可權指派* ] 資料夾，然後按兩下 [ **產生安全性審核**]。
3. 在 [本機安全性設定]索引標籤上，確認 AD FS 服務帳戶已列出。 如果未列出，請選取 [ **新增使用者或群組**]，然後將 AD FS 服務帳戶新增至清單。 然後選取 [確定]。
4. 若要啟用審核，請以較高的許可權開啟 [命令提示字元] 視窗。 然後，執行下列命令： 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. 關閉 [本機安全性原則]。
    >[!Important]
    >只有主要 AD FS 伺服器才能執行下列步驟。
1. 開啟 [AD FS 管理] 嵌入式管理單元。  (在 **伺服器管理員** 中，選取 [**工具**]  >  **AD FS 管理**]。 ) 
1. 在 [ **動作** ] 窗格中，選取 [ **編輯同盟服務屬性**]。
1. 在 [ **同盟服務屬性** ] 對話方塊中，選取 [ **事件** ] 索引標籤。
1. 選取 [ **成功審核] 和 [失敗的審核** ] 核取方塊，然後選取 **[確定]**。 成功的審核和失敗的審核應預設為啟用。
1. 開啟 PowerShell 視窗並執行下列命令： 

    `Set-AdfsProperties -AuditLevel Verbose`

預設會啟用「基本」審核等級。 如需詳細資訊，請參閱 [Windows Server 2016 中的 AD FS audit 增強功能](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)。


#### <a name="to-locate-the-ad-fs-audit-logs"></a>找出 AD FS 稽核記錄

1. 開啟 [事件檢視器]。
2. 移至 [ **Windows 記錄** 檔]，然後選取 [ **安全性**]。
3. 選取右側的 [ **篩選目前的記錄**]。
4. 針對 [ **事件來源**]，選取 AD FS 的 [ **審核**]。

    如需有關 audit 記錄檔的詳細資訊，請參閱 [操作問題](reference-connect-health-faq.md#operations-questions)。

    ![顯示 [篩選目前的記錄] 視窗的螢幕擷取畫面。 在 [事件來源] 欄位中，已選取 [AD FS 審核]。](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 群組原則可以停用 AD FS 稽核。 如果 AD FS 審核已停用，則無法使用有關登入活動的使用方式分析。 確定您沒有可停用 AD FS 審核的群組原則。
>


## <a name="install-the-agent-for-sync"></a>安裝代理程式進行同步處理

同步的 Azure AD Connect Health 代理程式會自動安裝在最新版的 Azure AD Connect 中。 若要使用 Azure AD Connect 進行同步，請 [下載最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 並進行安裝。

若要確認已安裝代理程式，請在伺服器上尋找下列服務。 如果您已完成設定，服務應該已在執行中。 否則，服務會停止，直到設定完成為止。

* Azure AD Connect Health Sync Insights 服務
* Azure AD Connect Health Sync Monitoring 服務

![顯示伺服器上同步處理服務執行 Azure AD Connect Health 的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> 請記住，您必須有 Azure AD Premium 才能使用 Azure AD Connect Health。 如果您沒有 Azure AD Premium，就無法在 Azure 入口網站中完成設定。 如需詳細資訊，請參閱 [需求](how-to-connect-health-agent-install.md#requirements)。
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>手動註冊 Azure AD Connect Health 以進行同步處理

如果在成功安裝 Azure AD Connect 之後，同步代理程式註冊的 Azure AD Connect Health 失敗，則您可以使用 PowerShell 命令手動註冊代理程式。

> [!IMPORTANT]
> 只有在您安裝 Azure AD Connect 後，代理程式註冊失敗時，才使用此 PowerShell 命令。
>
>

使用下列 PowerShell 命令，手動註冊 Azure AD Connect Health 代理程式以進行同步處理。 Azure AD Connect Health 服務會在代理程式註冊成功後啟動。

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

此命令採用下列參數：

* **AttributeFiltering**： `$true` 如果 Azure AD Connect 未同步處理預設屬性集，且已自訂為使用已篩選的屬性集，則 (預設) 。 否則，使用 `$false`。
* **StagingMode**： `$false` 如果 Azure AD Connect 伺服器 *不* 在預備模式中， (預設) 。 如果伺服器設定為預備模式，請使用 `$true` 。

當系統提示您進行驗證時，請使用相同的全域系統管理員帳戶 (例如 admin@domain.onmicrosoft.com 您用來設定 Azure AD Connect 的) 。

## <a name="install-the-agent-for-azure-ad-ds"></a>安裝 Azure AD DS 的代理程式

若要啟動代理程式安裝，請按兩下您所下載的 *.exe* 檔案。 在第一個視窗中，選取 [ **安裝**]。

![顯示 AD DS 安裝視窗之 Azure AD Connect Health 代理程式的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

當安裝完成時，請選取 [ **立即設定**]。

![螢幕擷取畫面，顯示完成 Azure AD DS 的 Azure AD Connect Health 代理程式安裝的視窗。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

[命令提示字元] 視窗隨即開啟。 PowerShell 會執行 `Register-AzureADConnectHealthADDSAgent` 。 當系統提示您時，請登入 Azure。

![顯示 Azure AD DS 之 Azure AD Connect Health 代理程式登入視窗的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

在您登入之後，PowerShell 會繼續進行。 完成時，您可以關閉 PowerShell。 設定已完成。

此時，應該會自動啟動服務，讓代理程式監視和收集資料。 如果您未符合前幾節所述的所有必要條件，則會在 PowerShell 視窗中顯示警告。 在安裝代理程式之前，請務必先完成 [需求](how-to-connect-health-agent-install.md#requirements) 。 下列螢幕擷取畫面顯示這些警告的範例。

![顯示 Azure AD DS 設定的 Azure AD Connect Health 代理程式警告的螢幕擷取畫面。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

若要確認已安裝代理程式，請在網域控制站上尋找下列服務：

* Azure AD Connect Health AD DS Insights 服務
* Azure AD Connect Health AD DS 監視服務

如果您已完成設定，這些服務應已在執行中。 否則，它們會停止，直到設定完成為止。

![螢幕擷取畫面，顯示網域控制站上的執行中服務。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>在多部伺服器上快速安裝代理程式

1. 在 Azure AD 中建立使用者帳戶。 請使用密碼來保護它。
2. 使用入口網站，在 Azure AD Connect Health 中為這個本機 Azure AD 帳戶指派 **擁有** 者角色。 請遵循下列 [步驟](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。 將角色指派給所有服務實例。 
3. 下載本機網域控制站中的 *.Exe* MSI 檔案以進行安裝。
4. 執行下列指令碼。 將參數取代為新的使用者帳戶及其密碼。 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

當您完成時，您可以執行下列一項或多項工作，以移除本機帳戶的存取權： 
* 針對 Azure AD Connect Health 移除本機帳戶的角色指派。
* 輪替本機帳戶的密碼。 
* 停用 Azure AD 的本機帳戶。
* 刪除 Azure AD 的本機帳戶。  

## <a name="register-the-agent-by-using-powershell"></a>使用 PowerShell 註冊代理程式

在您安裝適當的代理程式 *setup.exe* 檔案之後，您可以使用下列 PowerShell 命令來註冊代理程式，視角色而定。 開啟 PowerShell 視窗並執行適當的命令：

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> 若要對主權雲端進行註冊，請使用下列命令列：
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


這些命令接受 `Credential` 做為參數來完成註冊 noninteractively，或在執行 Server Core 的電腦上完成註冊。 請注意下列事項：
* 您可以 `Credential` 在以參數形式傳遞的 PowerShell 變數中捕捉。
* 您可以提供有權註冊代理程式，且 *未* 啟用多重要素驗證的任何 Azure AD 身分識別。
* 根據預設，全域系統管理員具有註冊代理程式的許可權。 您也可以允許較低許可權的身分識別來執行此步驟。 如需詳細資訊，請參閱 [AZURE RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)。

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>設定 Azure AD Connect Health 代理程式使用 HTTP proxy

您可以設定 Azure AD Connect Health 代理程式使用 HTTP proxy。

> [!NOTE]
> * 不支援 `Netsh WinHttp set ProxyServerAddress`。 代理程式會使用 System.Net 而非 Windows HTTP 服務來提出 web 要求。
> * 設定的 HTTP proxy 位址會用來傳遞已加密的 HTTPS 訊息。
> * 不支援已驗證的 Proxy (使用 HTTPBasic)。
>
>

### <a name="change-the-agent-proxy-configuration"></a>變更代理程式 proxy 設定

若要設定 Azure AD Connect Health 代理程式使用 HTTP proxy，您可以：
* 匯入現有的 proxy 設定。
* 手動指定 proxy 位址。
* 清除現有的 proxy 設定。

> [!NOTE]
> 若要更新 proxy 設定，您必須重新開機所有 Azure AD Connect Health 代理程式服務。 執行以下命令：
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>匯入現有的 proxy 設定

您可以匯入 Internet Explorer 的 HTTP proxy 設定，讓 Azure AD Connect Health 代理程式可以使用這些設定。 在執行健康情況代理程式的每部伺服器上，執行下列 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

您可以匯入 WinHTTP proxy 設定，讓 Azure AD Connect Health 代理程式可以使用它們。 在執行健康情況代理程式的每部伺服器上，執行下列 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>手動指定 proxy 位址

您可以手動指定 proxy 伺服器。 在執行健康情況代理程式的每部伺服器上，執行下列 PowerShell 命令：

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

以下為範例： 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

在此範例中：
* `address`設定可以是 DNS 可解析的伺服器名稱或 IPv4 位址。
* 您可以省略 `port` 。 如果您這麼做，則443是預設埠。

#### <a name="clear-the-existing-proxy-configuration"></a>清除現有的 proxy 設定

您可以執行下列命令來清除現有的 Proxy 設定：

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>讀取目前的 Proxy 設定

您可以藉由執行下列命令來讀取目前的 proxy 設定：

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>測試 Azure AD Connect Health 服務的連線能力

有時候，Azure AD Connect Health 代理程式可能會失去與 Azure AD Connect Health 服務的連線能力。 造成此連線中斷的原因可能包括網路問題、許可權問題和其他各種問題。

如果代理程式無法將資料傳送至 Azure AD Connect Health 服務超過兩小時，入口網站中會出現下列警示：「健全狀況服務資料不是最新狀態」。 

您可以藉由執行下列 PowerShell 命令，找出受影響的 Azure AD Connect Health 代理程式是否可以將資料上傳至 Azure AD Connect Health 服務：

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

角色參數目前可接受下列值：

* ADFS
* 同步
* ADDS

> [!NOTE]
> 若要使用 [連線能力] 工具，您必須先註冊代理程式。 如果您無法完成代理程式註冊，請確定您已符合 Azure AD Connect Health 的所有 [需求](how-to-connect-health-agent-install.md#requirements) 。 預設會在代理程式註冊期間測試連線能力。
>
>

## <a name="next-steps"></a>後續步驟

請參閱下列相關文章：

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 作業](how-to-connect-health-operations.md)
* [使用 Azure AD Connect Health 搭配 AD FS](how-to-connect-health-adfs.md)
* [使用適用於同步處理的 Azure AD Connect Health](how-to-connect-health-sync.md)
* [使用 Azure AD Connect Health 搭配 Azure AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
* [Azure AD Connect Health 版本記錄](reference-connect-health-version-history.md)
