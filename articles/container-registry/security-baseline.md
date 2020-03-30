---
title: Azure 容器註冊表的 Azure 安全基線
description: Azure 容器註冊表的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244285"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure 容器註冊表的 Azure 安全基線

Azure 容器註冊表的 Azure 安全基線包含有助於改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：Azure 虛擬網路為 Azure 和本地資源提供安全的私人網路絡。 通過限制從 Azure 虛擬網路訪問專用 Azure 容器註冊表，可確保只有虛擬網路中的資源訪問註冊表。 對於跨界方案，還可以配置防火牆規則，以便僅允許從特定 IP 位址進行註冊表訪問。 從防火牆後面配置防火牆訪問規則和服務標記以訪問容器註冊表。

使用 Azure 虛擬網路或防火牆規則限制對 Azure 容器註冊表的訪問：https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

配置規則以訪問防火牆後面的 Azure 容器註冊表：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：使用 Azure 安全中心並修復網路保護建議，以説明保護 Azure 中的網路資源。 啟用 NSG 流日誌並將日誌發送到存儲帳戶以進行流量審核。

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

保護您的網路資源：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指南**：不適用。 基準測試適用于託管 Web 應用程式的 Azure 應用服務或計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：在虛擬網路上啟用 DDoS 標準保護，以保護免受 DDoS 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通信。  在啟用威脅智慧後，在每個組織的網路邊界部署 Azure 防火牆，並將其配置為針對惡意網路流量"警報和拒絕"。

您可以使用 Azure 安全中心"及時網路"訪問來配置 NSG，以在有限時間內將終結點暴露到已批准的 IP 位址。 此外，使用 Azure 安全中心自我調整網路強化來建議基於實際流量和威脅情報限制埠和源 IP 的 NSG 配置。

如何配置 DDoS 保護：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


如何部署 Azure 防火牆：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

瞭解 Azure 安全中心集成威脅情報：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

瞭解 Azure 安全中心自我調整網路強化：https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 安全中心正好及時進行網路存取控制：https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：為附加到子網的 NSG 啟用網路安全性群組 （NSG） 流日誌，這些日誌用於保護 Azure 容器註冊表。 您可以將 NSG 流日誌記錄到 Azure 存儲帳戶中，以生成流記錄。 如果需要調查異常活動，則啟用 Azure 網路觀察程式資料包捕獲。

如何啟用 NSG 流日誌：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路觀察程式：https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指南**：從 Azure 應用商店中選擇支援 IDS/IPS 功能的具有有效負載檢查功能的產品/ 如果不需要基於有效負載檢查的入侵偵測和/或預防，則可以使用具有威脅智慧的 Azure 防火牆。 Azure 防火牆威脅基於智慧的篩選可以警報和拒絕來自已知惡意 IP 位址和域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路邊界部署您選擇的防火牆解決方案，以檢測和/或拒絕惡意流量。

Azure 應用商店：https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

如何部署 Azure 防火牆：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆配置警報：https://docs.microsoft.com/azure/firewall/threat-intel


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指南**：不適用。 基準測試適用于在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：對於需要訪問容器註冊表的資源，請使用 Azure 容器註冊表服務的虛擬網路服務標記在網路安全性群組或 Azure 防火牆上定義網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱"AzureContainerRegistry"，可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。

允許按服務標記訪問：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：使用 Azure 策略定義和實施與 Azure 容器註冊表關聯的網路資源的標準安全配置。 在"Microsoft.ContainerRegistry"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行容器註冊表的網路設定。 

可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器範本、RBAC 控制項和策略）來簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱，並通過版本控制微調控制和管理。

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何創建 Azure 藍圖：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：客戶可以使用 Azure 藍圖在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器範本、RBAC 控制項和策略），從而簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱，並通過版本控制微調控制和管理。

如何創建 Azure 藍圖：https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指南**：使用 Azure 活動日誌監視網路資源配置並檢測與容器註冊表相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。

如何查看和檢索 Azure 活動日誌事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心監視**：是

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指南**：Microsoft 維護 Azure 資源的時間源，但是，您可以選擇管理計算資源的時間同步設置。

如何為 Azure 計算資源配置時間同步：https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure 安全中心監視**：當前不可用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指南**：通過 Azure 監視器引入日誌以聚合 Azure 容器註冊表生成的安全資料。 在 Azure 監視器中，使用日誌分析工作區來查詢和執行分析，並使用 Azure 存儲帳戶進行長期/存檔存儲。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指南**：Azure 監視器收集注冊表中使用者驅動事件的資源日誌（以前稱為診斷日誌）。 收集和使用此資料以審核註冊表身份驗證事件，並提供登錄機碼目（如拉取和推送事件）的完整活動跟蹤，以便您可以診斷註冊表的安全問題。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指南**：不適用。 基準測試適用于計算資源。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：在 Azure 監視器中，根據組織的合規性法規設置日誌分析工作區保留期。 使用 Azure 存儲帳戶進行長期/存檔存儲。

如何為日誌分析工作區設置日誌保留參數：https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指導**：分析和監視 Azure 容器註冊表日誌是否存在異常行為，並定期查看結果。 使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌資料的查詢。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

瞭解日誌分析工作區：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**：使用 Azure 日誌分析工作區監視和警報安全性記錄檔中與 Azure 容器註冊表相關的異常事件。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

如何對日誌分析日誌資料發出警報：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指南**：不適用。 Azure 容器註冊表不處理或生成與惡意軟體相關的日誌。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指南**：不適用。 Azure 容器註冊表是終結點，不啟動通信，服務不查詢 DNS。

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

**指南**：不適用。 基準測試適用于計算資源。


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊，請參閱[安全控制：標識和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

**指南**： Azure 活動目錄 （Azure AD） 具有必須顯式分配且可查詢的內置角色。 使用 Azure AD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

對於每個 Azure 容器註冊表，跟蹤是啟用還是禁用內置管理帳戶。 不使用時禁用帳戶。

如何使用 PowerShell 在 Azure AD 中獲取目錄角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 獲取 Azure AD 中的目錄角色成員：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure 容器註冊表管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

**指南**： Azure 活動目錄 （Azure AD） 沒有預設密碼的概念。 需要密碼的其他 Azure 資源強制創建具有複雜性要求和最小密碼長度的密碼，具體取決於服務。 您負責可能使用預設密碼的協力廠商應用程式和應用商店服務。

如果啟用了 Azure 容器註冊表的預設管理者帳戶，則會自動創建複雜密碼，並應輪換。 不使用時禁用帳戶。

Azure 容器註冊表管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

**指導**：圍繞使用專用管理帳戶創建標準操作程式。 使用 Azure 安全中心標識和訪問管理監視管理帳戶數。

此外，創建過程以啟用容器註冊表的內置管理帳戶。 不使用時禁用帳戶。

瞭解 Azure 安全中心標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure 容器註冊表管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

**指南**：在可能的情況下，請使用 Azure 活動目錄 SSO，而不是配置每個服務的各個獨立憑據。 使用 Azure 安全中心標識和訪問管理建議。

對於對容器註冊表的單獨訪問，請使用與 Azure 活動目錄集成的單個登錄名。

使用 Azure AD 瞭解 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

個人登錄到容器註冊表：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure 活動目錄 （Azure AD） 多重要素驗證 （MFA）， 並遵循 Azure 安全中心標識和訪問管理建議。

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用具有 MFA 的 PAW（特權訪問工作站）來登錄和配置 Azure 資源。

瞭解特權訪問工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA：https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：當環境中發生可疑或不安全活動時，請使用 Azure 活動目錄 （Azure AD） 安全報告生成日誌和警報。 使用 Azure 安全中心監視標識和訪問活動。

如何識別標記為有風險活動的 Azure AD 使用者：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何監視 Azure 安全中心中的使用者身份和訪問活動：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：使用條件訪問命名位置僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中配置命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （Azure AD） 作為中央身份驗證和授權系統。 Azure AD 對靜態和傳輸中的資料使用增強式加密來保護資料。 Azure AD 還會對使用者憑據進行鹽漬、雜湊和安全地存儲。

如何創建和配置 Azure AD 實例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指南**： Azure 活動目錄 （Azure AD） 提供日誌以説明發現陳舊的帳戶。 此外，使用 Azure 標識訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。 可以定期查看使用者存取權限，以確保只有正確的使用者才能繼續訪問。

瞭解 Azure AD 報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 標識訪問審核：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：您可以訪問 Azure 活動目錄 （Azure AD） 登錄活動、審核和風險事件日誌源，這允許您與任何安全資訊和事件管理 （SIEM） /監視工具集成。

您可以通過為 Azure 活動目錄使用者帳戶創建診斷設置並將稽核記錄和登錄日誌發送到日誌分析工作區來簡化此過程。 您可以在日誌分析工作區中配置所需的警報。

如何將 Azure 活動日誌集成到 Azure 監視器中：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**：使用 Azure 活動目錄 （Azure AD） 風險和身份保護功能配置自動回應，以檢測與使用者身份相關的可疑操作。 

如何查看 Azure AD 風險登錄：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何配置和啟用身份保護風險策略：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指導**：不可用;Azure 容器註冊表當前不支援客戶密碼箱。

客戶密碼箱支援服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用資源標記説明跟蹤存儲或處理敏感資訊的 Azure 容器註冊表。

在註冊表中標記和版本容器映射或其他專案，並鎖定圖像或存儲庫，以説明跟蹤存儲或處理敏感資訊的圖像。

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

標記和版本控制容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

在 Azure 容器註冊表中鎖定容器映射：https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的容器註冊表、訂閱和/或管理組。 存儲或處理敏感性資料的資源應足夠隔離。

資源應由虛擬網路或子網分隔，標記得當，並由網路安全性群組 （NSG） 或 Azure 防火牆保護。

如何創建其他 Azure 訂閱：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何創建管理組：https://docs.microsoft.com/azure/governance/management-groups/create

如何創建和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

使用 Azure 虛擬網路或防火牆規則限制對 Azure 容器註冊表的訪問：https://docs.microsoft.com/azure/container-registry/container-registry-vnet

如何創建具有安全配置的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆配置警報或警報和拒絕：

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指南**：在網路週邊部署自動工具，用於監視敏感資訊的未授權傳輸，並阻止此類傳輸，同時提醒資訊安全專業人員。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**：確保連接到 Azure 容器註冊表的任何用戶端能夠協商 TLS 1.2 或更高。 預設情況下，Microsoft Azure 資源協商 TLS 1.2。

請按照 Azure 安全中心的建議，瞭解靜態加密和傳輸中的加密（如果適用）。

瞭解使用 Azure 傳輸中的加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure 安全中心監視**：是

**責任**： 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：Azure 容器註冊表尚不可用資料標識、分類和損失預防功能。 如果需要符合性，則實施協力廠商解決方案。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指南**：使用 Azure 活動目錄 （Azure AD） RBAC 控制對 Azure 容器註冊表中的資料和資源的訪問。 

如何在 Azure 中配置 RBAC：https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure 容器註冊表角色和許可權：https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指南**：如果需要符合計算資源，則實施協力廠商工具，如基於主機的自動資料丟失防護解決方案，以便強制訪問資料，即使資料從系統複製。

對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**：對所有 Azure 資源使用靜態加密。 預設情況下，Azure 容器註冊表中的所有資料都使用 Microsoft 管理的金鑰進行靜態加密。

瞭解 Azure 中靜態加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure 容器註冊表中的客戶管理金鑰：https://aka.ms/acr/cmk



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指南**：Azure 監視器收集注冊表中使用者驅動事件的資源日誌（以前稱為診斷日誌）。 收集和使用此資料以審核註冊表身份驗證事件，並提供登錄機碼目（如拉取和拉取事件）的完整活動跟蹤，以便您可以診斷註冊表的操作問題。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指南**：按照 Azure 安全中心關於對容器映射執行漏洞評估的建議進行操作。 可以選擇從 Azure 應用商店部署協力廠商解決方案以執行映射漏洞評估。

如何實施 Azure 安全中心漏洞評估建議：https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure 容器註冊表與安全中心集成（預覽）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化作業系統修補程式管理解決方案

**指南**：Microsoft 對支援 Azure 容器註冊表的基礎系統執行修補程式管理。

當檢測到作業系統和其他修補程式對基本映射的更新時，自動執行容器映射更新。

關於 Azure 容器註冊表任務的基本映射更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指南**：您可以使用協力廠商解決方案來修補應用程式映射。  此外，還可以運行 Azure 容器註冊表任務，以便根據安全修補程式或基本映射中的其他更新自動更新容器註冊表中的應用程式映射。

關於 ACR 任務的基本映射更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比較背對背漏洞掃描

**指南**：將 Azure 容器註冊表 （ACR） 與 Azure 安全中心集成，以便定期掃描容器映射以訪問漏洞。 可以選擇從 Azure 應用商店部署協力廠商解決方案以執行定期映射漏洞掃描。

Azure 容器註冊表與安全中心集成（預覽）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**：將 Azure 容器註冊表 （ACR） 與 Azure 安全中心集成，以便定期掃描容器映射以尋找漏洞並對風險進行分類。 可以選擇從 Azure 應用商店部署協力廠商解決方案，以執行定期映射漏洞掃描和風險分類。

Azure 容器註冊表與安全中心集成（預覽）：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 資源圖查詢/發現訂閱中的所有資源（如計算、存儲、網路、埠和協定等）。  請確保租戶中的適當（讀取）許可權，並枚舉訂閱中的所有 Azure 訂閱以及資源。

儘管可以通過資源圖發現經典 Azure 資源，但強烈建議今後創建和使用 Azure 資源管理器資源。

如何使用 Azure 資源圖創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 訂閱：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

瞭解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：Azure 容器註冊表維護中繼資料，包括註冊表中圖像的標記和清單。 遵循標記工件的建議做法。

關於註冊表、存儲庫和圖像：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

標記和版本控制容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：Azure 容器註冊表維護中繼資料，包括註冊表中圖像的標記和清單。 遵循標記工件的建議做法。

關於註冊表、存儲庫和圖像：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

標記和版本控制容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已批准的 Azure 資源和軟體標題的清單

**指導**：您需要根據組織需要創建已批准的 Azure 資源的清單。  

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 監視未經批准的 Azure 資源

**指南**：使用 Azure 策略對可在訂閱中創建的資源類型施加限制。

使用 Azure 資源圖查詢/發現其訂閱中的資源。  確保環境中的所有 Azure 資源都獲得批准。

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 監控計算資源中未經批准的軟體應用程式

**指導**：分析和監視 Azure 容器註冊表日誌是否存在異常行為，並定期查看結果。 使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌資料的查詢。

用於診斷評估和審核的 Azure 容器註冊表日誌：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

瞭解日誌分析工作區：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 刪除未經批准的 Azure 資源和軟體應用程式

**指南**：Azure 自動化在工作負載和資源的部署、操作和停用期間提供完全控制。  您可以實現自己的解決方案來刪除未經授權的 Azure 資源。 Azure 自動化簡介：https://docs.microsoft.com/azure/automation/automation-intro


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="68-use-only-approved-applications"></a>6.8： 僅使用已批准的應用程式

**指南**：不適用。 基準測試是為計算資源設計的。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="69-use-only-approved-azure-services"></a>6.9： 僅使用已批准的 Azure 服務

**指南**：利用 Azure 策略來限制可以在環境中預配哪些服務。

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒絕特定資源類型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="610-implement-approved-application-list"></a>6.10： 實施已批准的申請清單

**指南**：不適用。 基準測試是為計算資源設計的。



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者通過腳本與 AzureResource Manager 交互的能力

**指南**：使用特定于作業系統的配置或協力廠商資源來限制使用者在 Azure 計算資源中執行腳本的能力。

如何配置條件訪問以阻止對 Azure 資源管理器的訪問：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制使用者在計算資源中執行腳本的能力

**指南**：使用作業系統特定的配置或協力廠商資源來限制使用者在 Azure 計算資源中執行腳本的能力。

例如，如何控制 Windows 環境中的 PowerShell 腳本執行：https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或邏輯上隔離高風險應用程式

**指南**：業務操作所需的軟體，但可能會給組織帶來更高的風險，應隔離在自己的虛擬機器和/或虛擬網路中，並且使用 Azure 防火牆或網路安全性群組進行充分保護。

如何創建虛擬網路：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何創建具有安全配置的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊，請參閱[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全配置

**指南**：使用 Azure 策略或 Azure 安全中心維護所有 Azure 資源的安全配置。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

**指南**：利用 Azure 安全中心建議"修復虛擬機器上的安全配置中的漏洞"來維護所有計算資源的安全配置。

如何監視 Azure 安全中心建議：https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何修復 Azure 安全中心建議：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

**指導**：使用 Azure 策略 [拒絕] 和 [部署（如果不存在））在整個 Azure 資源中強制實施安全設置。

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 策略效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

**指南**：不適用。 基準測試適用于計算資源。

**Azure 安全中心監視**：不適用

**責任**： 共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

**指南**：如果使用自訂 Azure 策略定義，請使用 Azure 存儲庫安全地存儲和管理代碼。

如何在 Azure DevOps 中存儲代碼：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 存儲庫文檔：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指南**：不適用。 基準測試適用于計算資源。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指導**：使用 Azure 策略來警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指南**：不適用。 基準測試適用于計算資源。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：使用 Azure 安全中心對 Azure 資源執行基線掃描。

使用 Azure 策略對可在訂閱中創建的資源類型施加限制。

如何修復 Azure 安全中心中的建議：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

使用 Azure 策略審核 Azure 容器註冊表的合規性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指南**：不適用。 基準測試適用于計算資源。


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**：將託管服務標識與 Azure 金鑰保存庫結合使用，以簡化和保護雲應用程式的秘密管理。

如何與 Azure 託管標識集成：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何創建金鑰保存庫：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管標識提供金鑰保存庫身份驗證：https://docs.microsoft.com/azure/key-vault/managed-identity

在 Azure 容器註冊表任務中使用 Azure 託管標識：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**：使用託管標識在 Azure AD 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務（包括金鑰保存庫）進行身份驗證，而無需在代碼中進行任何憑據。

如何配置託管標識：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

使用託管標識對 Azure 容器註冊表進行身份驗證：https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的憑據暴露

**指南**：實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置，如 Azure 金鑰保存庫。

如何設置憑據掃描程式：https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊，請參閱[安全控制：惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反惡意軟體

**指南**：將 Microsoft 反惡意軟體用於 Azure 雲服務和虛擬機器，以持續監視和防禦資源。 對於 Linux，請使用協力廠商反惡意軟體解決方案。

如何為雲服務和虛擬機器配置 Microsoft 反惡意軟體：https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上載到非計算 Azure 資源的預掃描檔

**指南**：在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體（例如，Azure 容器註冊表），但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源（如應用服務、資料湖存儲、Blob 存儲等）的任何檔。


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指南**：不適用。 基準測試適用于計算資源。 Microsoft 處理底層平臺的反惡意軟體。


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指南**：Microsoft Azure 容器註冊表中的資料始終會自動複製，以確保持久性和高可用性。 Azure 容器註冊表複製資料，以便資料免受計畫內和計畫外事件

可選地異地複製容器註冊表，以在多個 Azure 區域中維護註冊表副本。 

Azure 容器註冊表中的異地複製：https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指南**：通過從一個註冊表導入另一個註冊表，可以選擇備份容器映射。

使用 Azure 命令列工具或 SDK 在 Azure 金鑰保存庫中備份客戶管理的金鑰。

將容器映射導入容器註冊表：https://docs.microsoft.com/azure/container-registry/container-registry-import-images

如何在 Azure 中備份金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**：使用 Azure 命令列工具或 SDK 測試 Azure 金鑰保存庫中備份的客戶託管金鑰的恢復。

如何在 Azure 中還原 Azure 金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**：您可以在 Azure 金鑰保存庫中啟用虛刪除，以保護金鑰免受意外或惡意刪除。

如何在金鑰保存庫中啟用虛刪除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：為您的組織構建事件回應指南。 確保有書面事件回應計畫，定義人員的所有角色以及事件處理/管理階段，從檢測到事件後審查。

如何在 Azure 安全中心內配置工作流自動化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

有關構建您自己的安全事件回應流程的指導：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

微軟安全回應中心的事件剖析：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客戶還可以利用 NIST 的電腦安全性事件處理指南來説明創建他們自己的事件回應計畫：https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：Azure 安全中心為每個警報分配嚴重性，以説明您優先處理應首先調查哪些警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析，以及導致警報的活動背後存在惡意的置信度。

此外，清楚地標記訂閱（對於前 生產，非 prod），並創建命名系統以明確識別和分類 Azure 資源。


**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="103-test-security-response-procedures"></a>10.3： 測試安全回應程式

**指導**：進行練習，以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距，並根據需要修訂計畫。

請參閱 NIST 出版物：IT 計畫和功能測試、培訓和鍛煉計劃指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件連絡人詳細資訊，並配置安全事件的警報通知

**指南**：如果 Microsoft 安全回應中心 （MSRC） 發現客戶的資料已被非法或未經授權的方訪問，Microsoft 將使用安全事件聯繫資訊與您聯繫。  事後查看事件，以確保問題得到解決。

如何設置 Azure 安全中心安全連絡人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 將安全警報納入事件回應系統

**指南**：使用"連續匯出"功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心資料連線器來資料流警報哨兵。

如何配置連續匯出：https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報資料流到 Azure 哨兵：https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自動回應安全警報

**指南**：使用 Azure 安全中心的工作流自動化功能，通過安全警報和建議上的"邏輯應用"自動觸發回應。

如何配置工作流自動化和邏輯應用：https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊，請參閱[安全控制：滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：對 Azure 資源進行定期滲透測試，並確保在 60 天內補救所有關鍵安全發現

**指南**： 遵循 Microsoft 參與規則，確保您的滲透測試不違反 Microsoft 策略：https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以找到有關 Microsoft 針對 Microsoft 管理的雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和執行的詳細資訊，如下所示：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure 安全中心監視**：不適用

**責任**： 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
