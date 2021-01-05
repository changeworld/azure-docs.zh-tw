---
title: 適用于 Azure Stack Edge 的 Azure 安全性基準
description: Azure Stack Edge 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 14e19732423da3842b2f2748a46219691fe222b9
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803148"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>適用于 Azure Stack Edge 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 Microsoft Azure Stack Edge。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Stack Edge 的相關指引來分組。 已排除不適用 Azure Stack Edge 的 **控制項**。

若要查看 Azure Stack Edge 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Stack Edge 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱 [Azure 安全性效能評定：網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針：客戶會將 Microsoft 提供的實體 Azure Stack Edge 裝置部署至其私人網路以供內部存取，並有更多選項可進一步保護。 例如，Azure Stack Edge 裝置可透過客戶的內部網路存取，且需要客戶設定的 IP。 此外，客戶也可以選擇存取密碼來存取裝置的使用者介面。 

透過下列方式進一步保護內部流量：

- 需要傳輸層安全性 (TLS) 1.2 版，才能 Azure 入口網站和 SDK 管理 Azure Stack Edge 裝置。

- 裝置的任何用戶端存取都是透過使用標準 TLS 1.2 作為預設安全通訊協定的本機 web UI。

- 只有授權的 Azure Stack Edge Pro 裝置才能加入客戶在其 Azure 訂用帳戶中建立的 Azure Stack Edge 服務。

您可以在參考的連結中找到其他資訊。
 
- [在存取 Azure Stack Edge Pro GPU 裝置的 Windows 用戶端上設定 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [快速入門-開始使用 Azure Stack Edge Pro 搭配 GPU](azure-stack-edge-gpu-quickstart.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起

**指導** 方針：客戶可以選擇點對站虛擬私人網路 (VPN) 將 Azure Stack Edge 裝置從其內部部署私人網路連線到 Azure 網路。 VPN 提供第二層的加密，可透過傳輸層安全性從客戶的裝置傳輸到 Azure。 

客戶可以透過 Azure 入口網站或透過 Azure PowerShell，在其 Azure Stack Edge 裝置上設定虛擬私人網路。

- [透過適用于 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 的 Azure PowerShell 腳本設定 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在存取 Azure Stack Edge Pro GPU 裝置的 Windows 用戶端上設定 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教學課程：為您 Azure Stack Edge Pro R 設定憑證](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立 Azure 服務的私人網路存取

**指導** 方針：客戶可以選擇點對站虛擬私人網路 (VPN) 將 Azure Stack Edge 裝置從其內部部署私人網路連線到 Azure 網路。 VPN 提供第二層的加密，可透過傳輸層安全性從客戶的裝置傳輸到 Azure。 

- [透過適用于 Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 的 Azure PowerShell 腳本設定 Azure VPN](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [在存取 Azure Stack Edge Pro GPU 裝置的 Windows 用戶端上設定 TLS 1。2](azure-stack-edge-j-series-configure-tls-settings.md)

- [教學課程：為您 Azure Stack Edge Pro R 設定憑證](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

**指導** 方針： Azure Stack Edge 裝置併入標準的 Windows Server 網路保護功能，這些功能無法由客戶進行設定。

客戶可以選擇使用網路虛擬裝置（例如具有 advanced distributed 阻絕服務的防火牆 (DDoS) 保護），來保護與 Azure Stack Edge 裝置連線的私人網路。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針： Azure Stack Edge 裝置使用的端點全都受 Microsoft 管理。 客戶須負責想要部署至其內部部署系統的任何其他控制項。

Azure Stack Edge 裝置使用自己的入侵偵測功能來保護服務。 

- [瞭解 Azure Stack Edge 安全性](azure-stack-edge-security.md)

- [Azure Stack Edge 的埠資訊](azure-stack-edge-gpu-system-requirements.md)

- [取得硬體和軟體入侵偵測記錄](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自動地管理應用程式識別碼

**指導** 方針：所有 Azure Stack Edge 裝置都會在 Azure Active Directory (Azure AD) 中自動擁有系統指派的受控識別。 受控識別目前用於雲端管理 Azure Stack Edge 上裝載的虛擬機器。

Azure Stack Edge 裝置開機進入鎖定狀態以進行本機存取。 針對本機裝置系統管理員帳戶，您必須透過本機 web 使用者介面或 PowerShell 介面連接到您的裝置，並設定強式密碼。 在安全的位置中儲存和管理您的裝置系統管理員認證，例如 Azure Key Vault，並根據您的組織標準輪替系統管理員密碼。

- [透過密碼保護 Azure Stack Edge 裝置](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 進行應用程式存取

**指導** 方針： Azure Stack Edge 端點裝置不支援單一登入。 不過，您可以選擇啟用標準 Azure Active Directory (Azure AD) 型單一登入，以安全地存取您的 Azure 雲端資源。

- [瞭解 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用增強式驗證控制項

**指導** 方針：多重要素驗證是一種強大的驗證控制，但 Azure Stack Edge 服務使用者的加入宣告功能。 它可用於支援的案例，例如在 Azure 入口網站的 Azure Stack Edge 裝置的邊緣管理。 請注意，本機存取 Azure Stack Edge 裝置不支援多重要素驗證。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視及警示帳戶異常

**指導** 方針：啟用 Azure 監視器來收集 Azure Stack Edge 服務的裝置或容器記錄。 監視容器，例如在裝置上的 Kubernetes 叢集上執行多個計算應用程式的容器。

此外，也可以在 Azure Stack Edge 裝置的本機 web 使用者介面上，收集包含 audit 記錄檔的支援封裝。 請注意，Azure 入口網站不提供支援封裝。
 
- [在您的 Azure Stack Edge Pro 裝置上啟用 Azure 監視器](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [收集支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針： Azure Active Directory (Azure AD) 條件式存取是針對 Azure Stack Edge 服務進行驗證的加入宣告功能。 Azure Stack Edge 服務是 Azure 入口網站中的資源，可讓您管理跨不同地理位置的 Azure Stack Edge Pro 裝置。 

- [什麼是條件式存取](../active-directory/conditional-access/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除非預期的認證暴露

**指導** 方針：遵循最佳作法來保護認證，例如：

- 用來透過 Azure 中的 Azure Stack Edge 資源啟用裝置的啟用金鑰。
- 登入認證以存取 Azure Stack Edge 裝置。
- 可加速 Azure Stack Edge 裝置復原的金鑰檔。
- 通道加密金鑰

定期輪替並同步處理您的儲存體帳戶金鑰，以協助保護您的儲存體帳戶免于未經授權的使用者。

- [Azure Stack Edge Pro 安全性和資料保護](azure-stack-edge-security.md)

- [同步儲存體金鑰](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

**指導** 方針： Azure Stack Edge 解決方案有數個具有強式存取控制的元件，可限制對商務關鍵裝置的存取。 您的組織需要 Enterprise 合約 (EA) 或雲端解決方案提供者 (CSP) 或 Microsoft Azure 贊助訂用帳戶來設定及管理裝置： 

Azure Stack Edge 服務會受到 Azure 安全性功能的保護，作為 Azure 中託管的管理服務。 您可以在任何軟體發展工具組管理作業的裝置內容中取得資源的加密金鑰，但只有在您擁有 Resource Graph API 的適當許可權時，才可以看到加密金鑰。

Azure Stack Edge Pro 裝置是內部部署裝置，可在本機處理資料，然後將資料傳送至 Azure，以協助轉換您的資料。 您的裝置：

- 需要啟用金鑰才能存取 Azure Stack Edge 服務。
- 會受到裝置密碼的任何時間保護。
- 已啟用安全開機。

- 是鎖定的裝置。 裝置基礎板管理控制器 (BMC) 和 BIOS 都受到密碼保護。 BIOS 受到受限的使用者存取保護。
- Windows Defender Device Guard 執行。 Device Guard 可讓您只執行您在程式碼完整性原則中定義的受信任應用程式。

遵循最佳作法，以保護用來存取 Azure Stack Edge 的所有認證和密碼。 

- [密碼最佳做法](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱及協調使用者存取

**指導** 方針： Azure Stack Edge 有一個名為 ' EdgeUser ' 的使用者可以設定裝置。 針對裝置上的本機 Azure Resource Manager 功能，它也有 Azure Resource Manager 使用者 ' EdgeArmUser '。 

建議您遵循最佳做法來保護：

- 用來存取內部部署裝置的認證

- SMB 共用認證。

- 存取已設定為使用 NFS 共用的用戶端系統。

- 使用 Blob REST API 時，用來存取本機儲存體帳戶的本機儲存體帳戶金鑰。

您可以在參考的連結中找到其他資訊。

- [Azure Stack Edge 裝置的安全性資訊](azure-stack-edge-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊權限存取工作站

**指導** 方針：安全且隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 使用具有或不具有 Azure 防禦的高度安全使用者工作站來進行系統管理工作。 使用 Azure Active Directory (Azure AD) 、Microsoft Defender 進階威脅防護 (ATP) 和 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 

受保護的工作站可以集中管理以施行安全設定，包括增強式驗證、軟體和硬體基準、受限的邏輯和網路存取。

- [瞭解特殊許可權的存取工作站](/azure/active-directory/devices/concept-azure-managed-workstation) 

- [部署特殊權限存取工作站](/azure/active-directory/devices/howto-azure-managed-workstation) (機器翻譯)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱 [Azure 安全性效能評定：資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保護敏感性資料

**指導** 方針： Azure Stack Edge 會將所有互動的資料視為機密資料，只有獲得授權的使用者可以存取此資料。 您應遵循最佳作法，以保護用來存取 Azure Stack Edge 服務的認證。

- [Azure Stack Edge Pro 安全性和資料保護](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密傳輸中的敏感性資訊

**指導** 方針： Azure Stack Edge 針對航班中的資料使用安全通道。 這些警告是：

- 標準 TLS 1.2 適用于在裝置與 Azure 雲端之間傳輸的資料。 沒有 TLS 1.1 及更早的版本。 如果不支援 TLS 1.2，代理程式通訊將會遭到封鎖。 Azure 入口網站和軟體發展工具組 (SDK) 管理也需要 TLS 1.2。

- 當用戶端透過瀏覽器的本機 web 使用者介面存取您的裝置時，系統會使用標準 TLS 1.2 作為預設安全通訊協定

最佳做法是將您的瀏覽器設定為使用 TLS 1.2。 當您從資料伺服器複製資料時，請使用 SMB 3.0 搭配加密來保護資料。

- [保護傳輸中資料的最佳作法](azure-stack-edge-security.md#protect-data-in-flight)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="asset-management"></a>資產管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指引**：確保安全性小組會獲得 Azure 租用戶和訂用帳戶中的「安全性讀取者」權限，而能夠使用 Azure 資訊安全中心來監視安全性風險。 

根據安全性小組的權責架構，監視安全性風險可能是由中央安全性小組或區域小組負責。 然而，安全性見解和風險務必要在組織內部集中彙總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

請注意，可能需要其他許可權才能看到工作負載和服務。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：只有獲得授權的使用者（例如，「EdgeArmUser」）可以透過本機 Azure Resource Manager 存取 Azure Stack Edge 裝置 api。 使用者帳戶密碼只能在 Azure 入口網站管理。 

- [設定 Azure Resource Manager 密碼](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>上午-6：僅在計算資源中使用已核准的應用程式

**指導** 方針：您可以帶入自己的應用程式，以在任何本機建立的虛擬機器上執行。 使用 PowerShell 腳本在您的 Stack Edge 裝置上建立本機計算虛擬機器。 強烈建議您只帶入受信任的應用程式，以便在本機虛擬機器上執行。 

- [如何在 Windows 環境中控制 PowerShell 腳本執行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1&amp;viewFallbackFrom=powershell-6&amp;preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針： Azure Stack Edge 不提供威脅偵測功能。 不過，客戶可以在支援套件中收集審核記錄，以進行離線威脅偵測和分析。 

- [收集 Azure Stack Edge 裝置的支援套件](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針： Azure Stack Edge 已在可下載的支援套件中啟用網路審核記錄。 您可以剖析這些記錄檔，以針對您的 Azure Stack Edge 裝置執行半即時監視。

- [Azure Stack Edge 收集支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄功能

**指導** 方針： Azure Stack Edge 目前不支援使用記錄檔進行即時監視。 可收集支援封裝的功能，可讓您分析其內含的各種記錄，例如 Azure Stack Edge Pro 裝置的防火牆、軟體、硬體入侵和系統事件記錄檔。 請注意，系統會針對輸入和輸出流量收集軟體入侵或預設防火牆記錄檔。

- [收集 Azure Stack Edge 的支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：將安全性記錄的管理與分析集中

**指導** 方針： Azure Stack Edge 目前不支援使用記錄檔進行即時監視。 不過，您可以收集支援封裝，讓您分析內含的各種記錄。  支援封裝會進行壓縮，並下載至您選擇的路徑。 將套件解壓縮，並查看其中包含的系統記錄檔。 您也可以將這些記錄傳送到安全性資訊事件管理工具或另一個集中儲存位置以進行分析。

- [收集 Azure Stack Edge 的支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存保留期

**指導** 方針：無法在 Azure Stack Edge 裝置變更記錄儲存體保留期限。 系統會視需要清除較舊的記錄檔。 您可以依定期間隔收集來自裝置的支援套件，以保留較長時間的記錄。 

- [收集 Azure Stack Edge 的支援封裝](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7：使用已核准的時間同步處理來源

**指導** 方針： Azure Stack Edge 使用 Microsoft 提供的網路時間提供者伺服器 time.windows.com。  Azure Stack Edge 也可讓客戶設定其選擇的網路時間通訊協定伺服器。

- [設定 Azure Stack Edge 裝置時間設定](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

**指導** 方針：確定您的組織事件回應計畫包含處理常式： 

- 回應安全性事件

- 適用于 Azure 雲端的已更新
 
- 會定期進行以確保就緒

建議您在企業環境中使用標準化事件回應程式來實行安全性。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

**指引**：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 當 Microsoft 安全回應中心 (MSRC) 發現您的資料已遭非法或未經授權的合作對象存取時，Microsoft 會使用此連絡人資訊來與您連絡。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析–根據高品質警示建立事件 

**指導** 方針：確定您有建立高品質警示和測量其品質的流程。 這可讓您學習過去事件的課程，並設定分析師的警示優先順序，以防止浪費時間處理錯誤的正面警示。 根據您過去的事件經驗、經過驗證的社區來源和工具，設計用來產生和清除警示，並提供各種警示來源的記錄和相互關聯，以建立高品質的警示。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用「安全性中心」資料連線器，將警示串流至 Azure Sentinel。 使用 Azure Sentinel 建立 advanced 警示規則，以產生調查的自動事件。 

使用匯出功能將您的資訊安全中心警示和建議匯出至 Azure Sentinel，以協助找出 Azure 資源的風險。 建議您建立一個流程，以自動化方式匯出警示和建議以持續安全性。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測與分析 – 調查事件

**指導** 方針：確定分析師可以查詢和使用不同的資料來源，以建立在調查潛在事件時所發生之活動的完整觀點。 您應收集不同的記錄檔類型，以追蹤潛在攻擊者在終止鏈上的活動，以避免點。  此外，請確定已針對歷程記錄參考捕捉見解和學習。  

調查的資料來源包括從範圍內的服務和執行中的系統收集到的集中式記錄來源，但也可包括：

- 網路資料 – 使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器，擷取網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能，建立執行中系統磁碟的快照集。 

    - 選擇作業系統的原生記憶體傾印功能，以建立執行中系統記憶體的快照。

    - 使用 Azure 服務的快照集功能或協力廠商軟體功能，來建立執行中系統的快照集。

Azure Sentinel 可讓您對絕大多數的記錄來源進行廣泛的資料分析，並提供案例管理入口網站來管理事件的完整生命週期。 調查期間的情報資訊可以與事件相關聯，以供追蹤和報告之用。 

- [為 Windows 機器的磁碟建立快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [為 Linux 機器的磁碟建立快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測與分析 – 設定事件的優先順序

**指引**：根據警示嚴重性和資產敏感度為分析師提供內容，來協助判斷要優先關注的事件。 從 Azure 資訊安全中心使用指派給每個警示的嚴重性，協助您排定應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：圍堵、根除及復原 – 將事件處理自動化

**指引**：將手動重複的工作自動化，以縮短回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行，進而降低每個事件的速度，以及減少分析師可以處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：狀況和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：建立計算資源的安全設定

**指導** 方針： Azure Stack Edge 提供支援來為客戶建立的本機虛擬機器建立安全的設定。 強烈建議您在建立本機虛擬機器時，使用 Microsoft 提供的指導方針來建立安全性基準。

- [下載安全性合規性工具組中包含的安全性基準](/windows/security/threat-protection/windows-security-baselines)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4：維持計算資源的安全設定

**指導** 方針： Azure Stack Edge 不會提供任何支援，以針對客戶所建立的本機虛擬機器維持安全的設定。 強烈建議客戶使用安全性合規性工具組 (SCT) ，以協助維護其計算資源的安全設定。

受 Azure Stack Edge 管理的主機作業系統和虛擬機器會維護其安全性設定。 

- [Windows 安全性基準](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5：安全地儲存自訂作業系統和容器映射

**指導** 方針：系統會安全地儲存由 Azure Stack Edge 管理的主機作業系統和虛擬機器。 

客戶可以攜帶自己的虛擬機器和容器映射，並負責進行安全管理。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自動補救軟體弱點

**指導** 方針：當有這類更新可用來補救弱點時，Azure Stack Edge 提供定期修補程式更新和警示客戶。 客戶必須負責將其裝置和虛擬機器 (建立) 最新修補程式的最新狀態。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行一般攻擊模擬

**指引**：如有需要，請對您的 Azure 資源執行滲透測試或紅隊活動，並確保補救所有重要的安全性結果。
請遵循 Microsoft 雲端滲透測試參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md) (機器翻譯)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="endpoint-security"></a>端點安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：端點安全性](/azure/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用端點偵測和回應 (EDR) 

**指導** 方針： Azure Stack Edge 不支援直接) 的端點偵測和回應 (EDR。 不過，您可以收集支援封裝並取出審核記錄。 然後可以分析這些記錄，以檢查是否有異常的活動。 

- [收集 Azure Stack Edge 裝置的支援套件](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反惡意程式碼軟體

**指導** 方針： Azure Stack Edge 使用 Windows Defender 應用程式控制 (WDAC) 具有嚴格的程式碼完整性 (CI) 原則，只允許執行預先決定的應用程式和腳本。 Windows Defender 的即時保護 (RTP) 反惡意程式碼也已啟用。 客戶可選擇在其所建立的計算 Vm 中執行反惡意程式碼，以在 Azure Stack Edge 裝置本機上執行。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="backup-and-recovery"></a>備份及修復

如需詳細資訊，請參閱 [Azure 安全性效能評定：備份和復原](/azure/security/benchmarks/security-controls-v2-backup-recovery)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：確保定期自動備份

**指導** 方針：建議您 Azure Stack Edge 裝置的定期備份，並可透過 Azure 備份和其他協力廠商資料保護解決方案來執行，以保護在裝置上部署的虛擬機器所包含的資料。 

協力廠商資料保護解決方案（例如 Cohesity、Commvault 和 Veritas）也可以為本機 SMB 或 NFS 共用中的資料提供備份解決方案。 

您可以在參考的連結中找到其他資訊。

- [針對裝置故障進行準備](azure-stack-edge-gpu-prepare-device-failure.md)

- [保護 Vm 上的檔案和資料夾](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密備份資料

**指導** 方針：確定您的備份受到保護，免于遭受攻擊。 這應該包括備份的加密，以防止機密性遺失。  如需詳細資訊，請參閱您選擇的備份解決方案以取得詳細資料。

- [保護 Edge 本機共用中的資料](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [保護虛擬機器中的檔案和資料夾](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：驗證所有備份，包括客戶自控金鑰

**指導** 方針：定期執行備份的資料還原。 

- [Azure Stack Edge 的修復程式](azure-stack-edge-gpu-recover-device-failure.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：降低遺失金鑰的風險

**指導** 方針：確定所有的 Azure Stack Edge 備份（包括任何客戶管理的金鑰）都會根據組織的最佳作法進行保護。 您的 Azure Stack Edge 裝置會與 Azure 儲存體帳戶相關聯，作為 Azure 中資料的目的地儲存機制使用。 

Azure 儲存體帳戶的存取權是由 Azure 訂用帳戶和與該儲存體帳戶相關聯的 2 512 位儲存體存取金鑰所控制。 當 Azure Stack Edge 裝置存取儲存體帳戶時，其中一個存取金鑰會用於驗證用途。 另一個金鑰會保留在保留中，以便定期進行金鑰輪替。 確保金鑰輪替使用安全性最佳作法。

- [保護 Azure 儲存體帳戶中 Azure Stack Edge 的裝置資料](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="governance-and-strategy"></a>控管與策略

如需詳細資訊，請參閱 [Azure 安全性效能評定：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：務必記載並傳達明確的策略，以持續監視及保護系統和資料。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   符合商業風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用的程序 

-   資產在其生命週期中的安全性

-   符合組織資料分類的必要存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全性效能評定 - 資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指引**：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業範圍的策略來分割對產的存取。

審慎權衡安全性區隔的需求，以及需要與彼此通訊並存取資料的啟用每日作業需求。

確保在各種控制項類型上一致地實作分割策略，這些控制項類型包括網路安全性、身分識別和存取模型，以及應用程式權限/存取模型與人力流程控制。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) (機器翻譯)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy) (機器翻譯)

- [使用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理策略

**指引**：持續測量並降低個別資產及其裝載環境的風險。 優先處理高價值資產和高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 狀態和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：協調組織角色、責任及權責

**指引**：務必為安全性組織中的角色和責任記載並傳達清楚的策略。 優先為安全性決策提供清楚的權責、讓每個人熟知共同責任模型，並讓技術團隊熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (機器翻譯)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (機器翻譯)

- [Azure 安全性最佳做法 3 - 流程：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指引**：建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式網路管理和安全性責任

-   與企業分割策略一致的虛擬網路分割模型

-   不同威脅和攻擊案例的補救策略

-   網際網路邊緣和輸入與輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](/azure/security/benchmarks/)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

**指引**：建立 Azure 身分識別和特殊權限存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的增強式驗證方法

-   高權限使用者的保護

-   異常使用者活動監視和處理  

-   使用者身分識別、存取權檢閱與核對流程

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全性效能評定 - 特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指引**：建立記錄和威脅回應策略，以快速偵測威脅並進行補救，同時符合合規性需求。 優先為分析師提供高品質警示和順暢體驗，讓他們能夠專注處理威脅，而非整合和手動步驟。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義且與 NIST 或其他產業架構一致的事件回應流程 

-   記錄擷取和保留，以支援威脅偵測、事件回應及合規性需求

-   使用 SIEM、原生 Azure 功能及其他來源，集中顯示及相互關聯威脅的相關資訊 

-   與客戶、供應商和相關公開合作對象之間的溝通和通知計畫

-   使用 Azure 原生和協力廠商平台進行事件處理，例如記錄和威脅偵測、鑑定，以及攻擊補救和根除

-   處理事件和事件後活動的流程，例如吸取的經驗和證據保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 記錄與威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全性效能評定 - 事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
