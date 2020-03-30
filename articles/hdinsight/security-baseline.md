---
title: HDInsight 的 Azure 安全基線
description: HDInsight 的 Azure 安全基線
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: eddfcacd01a67fffa8e3e992e021ed1771d25944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471331"
---
# <a name="azure-security-baseline-for-hdinsight"></a>HDInsight 的 Azure 安全基線

HDInsight 的 Azure 安全基線包含可説明您改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，它提供了有關如何使用最佳實踐指南在 Azure 上保護雲解決方案的建議。

有關詳細資訊，請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊，請參閱[安全控制：網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

**指南**：Azure HDInsight 中的週邊安全是通過虛擬網路實現的。 企業管理員可以在虛擬網路內創建群集，並使用網路安全性群組 （NSG） 限制對虛擬網路的訪問。 只有入站網路安全性群組規則中允許的 IP 位址才能與 Azure HDInsight 群集通信。 此設定可提供周邊安全性。 部署在虛擬網路中的所有群集也將具有專用終結點，該終結點解析為虛擬網路內的私人 IP 位址，以便對群集閘道進行專用 HTTP 訪問。


如何在虛擬網路中部署 Azure HDInsight 並借助網路安全性群組進行安全保護：

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

**指南**：使用 Azure 安全中心並修復用於保護 Azure HDInsight 群集的虛擬網路、子網和網路安全性群組的網路保護建議。 啟用網路安全性群組 （NSG） 流日誌，並將日誌發送到 Azure 存儲帳戶以進行流量審核。 您還可以將 NSG 流日誌發送到 Azure 日誌分析工作區，並使用 Azure 流量分析提供有關 Azure 雲中的流量流的見解。 Azure 流量分析的一些優點是能夠視覺化網路活動並識別熱點、識別安全威脅、瞭解流量流模式和查明網路錯誤配置。


如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


如何啟用和使用 Azure 流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


瞭解 Azure 安全中心提供的網路安全：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

**指導**：不適用;基準測試適用于託管 Web 應用程式的 Azure 應用服務或計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

**指南**：為了保護免受 DDoS 攻擊，請在部署 Azure HDInsight 的虛擬網路上啟用 Azure DDoS 標準保護。 使用 Azure 安全中心集成的威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址通信。


如何配置 DDoS 保護：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


瞭解 Azure 安全中心集成威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

**指南**：為連接到用於保護 Azure HDInsight 群集的子網的 NSG 啟用網路安全性群組 （NSG） 流日誌。 將 NSG 流日誌記錄到 Azure 存儲帳戶中，以生成流記錄。 如果需要調查異常活動，則啟用 Azure 網路觀察程式資料包捕獲。


如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


如何啟用網路觀察程式：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

**指導**：要求可滿足 Azure 安全控制 ID 1.1;將 Azure HDInsight 群集部署到虛擬網路中，並借助網路安全性群組 （NSG） 進行安全保護。

Azure HDInsight 有幾個依賴項需要入站流量。 無法通過防火牆設備發送入站管理流量。 已知併發布了所需管理流量的源位址。 使用此資訊創建網路安全性群組規則，以僅允許來自受信任位置的流量，從而保護群集的入站流量。

如何在虛擬網路中部署 HDInsight，以及如何使用網路安全性群組進行安全保護：https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

瞭解 HDInsight 依賴項和防火牆使用方式：https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight 管理 IP 位址：https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

**指導**：不適用;基準測試適用于託管 Web 應用程式的 Azure 應用服務或計算資源。

**Azure 安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

**指南**：使用虛擬網路服務標記定義連接到 Azure HDInsight 群集部署的子網的網路安全性群組 （NSG） 上的網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱（例如 ApiManagement），可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。


瞭解和使用 Azure HDInsight 的服務標記：

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

**指南**：為與 Azure HDInsight 群集相關的網路資源定義和實施標準安全配置。 在"Microsoft.HDInsight"和"Microsoft.Network"命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行 Azure HDInsight 群集的網路設定。


您還可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器範本、RBAC 控制項和策略）來簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱和環境，並通過版本控制微調控制和管理。


如何查看可用的 Azure 策略別名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何創建 Azure 藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

**指南**：對網路安全性群組 （NSG） 以及與 Azure HDInsight 群集關聯的網路安全和流量流相關的其他資源使用標記。 對於單個 NSG 規則，使用"描述"欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間（等）。


使用與標記相關的任何內置 Azure 策略定義（如"要求標記及其值"）確保所有資源都使用標記創建，並通知您現有的未標記資源。


您可以使用 Azure PowerShell 或 Azure 命令列介面 （CLI） 來查找資源或根據資源標記執行操作。


如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


如何創建虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


如何創建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

**指南**：使用 Azure 活動日誌監視網路資源配置，並檢測與 Azure HDInsight 部署相關的網路資源的更改。 在 Azure 監視器中創建警報，這些警報將在對關鍵網路資源的更改時觸發。


如何查看和檢索 Azure 活動日誌事件：

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


如何在 Azure 監視器中創建警報：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊，請參閱[安全控制：日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1： 使用批准的時間同步源

**指南**：Microsoft 維護 Azure HDInsight 群集元件的時間源，您可以更新計算部署的時間同步。


如何為 Azure 計算資源配置時間同步：

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 安全中心監視**：當前不可用

**責任**： 微軟

### <a name="22-configure-central-security-log-management"></a>2.2： 配置中央安全性記錄檔管理

**指南**：您可以將 Azure HDInsight 群集並聯到 Azure 監視器，以聚合群集生成的安全資料。 利用自訂查詢來檢測和回應環境中的威脅。 


如何將 Azure HDInsight 群集並聯到 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


如何為 Azure HDInsight 群集創建自訂查詢：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：為 Azure 資源啟用稽核記錄記錄

**指南**：為 HDInsight 群集啟用 Azure 監視器，將其定向到日誌分析工作區。 這將記錄所有 Azure HDInsight 叢集節點的相關群集資訊和 OS 指標。


如何為 HDInsight 群集啟用日誌記錄：

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


如何查詢 HDInsight 日誌：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4： 從作業系統收集安全性記錄檔

**指南**：將 Azure HDInsight 群集添加到 Azure 監視器。 確保使用的日誌分析工作區已根據組織的合規性法規設置了日誌保留期。


如何將 Azure HDInsight 群集並聯到 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


如何配置日誌分析工作區保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5： 配置安全性記錄檔存儲保留

**指南**：將 Azure HDInsight 群集添加到 Azure 監視器。 確保使用的 Azure 日誌分析工作區已根據組織的合規性法規設置了日誌保留期。


如何將 Azure HDInsight 群集並聯到 Azure 監視器：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


如何配置日誌分析工作區保留期：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="26-monitor-and-review-logs"></a>2.6： 監視和查看日誌

**指南**： 使用 Azure 日誌分析工作區查詢查詢 Azure HDInsight 日誌：


如何為 Azure HDInsight 群集創建自訂查詢：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7： 啟用異常活動的警報

**指南**：使用 Azure 日誌分析工作區監視和警報安全性記錄檔中與 Azure HDInsight 群集相關的異常事件。


如何管理 Azure 安全中心的警報：

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


如何對日誌分析日誌資料發出警報：

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8： 集中反惡意軟體日誌記錄

**指南**： Azure HDInsight 附帶了為叢集節點映射預先安裝並啟用的 Clamscan，但您必須管理軟體並手動聚合/監視 Clamcan 生成的任何日誌。


瞭解夾子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="29-enable-dns-query-logging"></a>2.9： 啟用 DNS 查詢日誌記錄

**指南**：為 dns 日誌記錄實施協力廠商解決方案。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10： 啟用命令列稽核記錄記錄

**指導**：根據每個節點手動設定主控台日誌記錄。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊，請參閱[安全控制：標識和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 維持管理帳戶的清單

**指南**：維護在 Azure HDInsight 群集群集的群集預配期間創建的本地管理帳戶的記錄以及您創建的任何其他帳戶。 此外，如果使用 Azure AD 集成，Azure AD 具有必須顯式分配並因此可查詢的內置角色。 使用 Azure AD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。


此外，您可以使用 Azure 安全中心標識和訪問管理建議。


如何使用 PowerShell 在 Azure AD 中獲取目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


如何使用 PowerShell 獲取 Azure AD 中的目錄角色成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


如何使用 Azure 安全中心監視標識和訪問：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2： 在適用的情況下更改預設密碼

**指南**：預配群集時，Azure 要求您為 Web 門戶創建新密碼，並保護命令程式 （SSH） 訪問。 沒有要更改的預設密碼，但您可以為 SSH 和 Web 門戶訪問指定不同的密碼。


在預配 Azure HDInsight 群集時如何設置密碼：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用專用管理帳戶

**指南**：將 Azure HDInsight 群集的身份驗證與 Azure 活動目錄集成。 圍繞使用專用管理帳戶創建政策和程式。


此外，您可以使用 Azure 安全中心標識和訪問管理建議。


如何將 Azure HDInsight 身份驗證與 Azure 活動目錄集成：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


如何使用 Azure 安全中心監視標識和訪問：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活動目錄的單一登入 （SSO）

**指南**：使用 Azure HDInsight ID 代理使用多重要素驗證登錄到企業安全包 （ESP） 群集，而無需提供任何密碼。 如果您已登錄到其他 Azure 服務（如 Azure 門戶），則可以使用單個登錄 （SSO） 體驗登錄到 Azure HDInsight 群集。


如何啟用 Azure HDInsight ID 代理：

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有基於 Azure 活動目錄的訪問使用多重要素驗證

**指南**：啟用 Azure AD MFA 並遵循 Azure 安全中心標識和訪問管理建議。 配置了企業安全包的 Azure HDInsight 群集可以連接到域，以便域使用者可以使用其域憑據對群集進行身份驗證並運行大資料作業。 啟用多重要素驗證 （MFA） 進行身份驗證時，使用者將面臨提供第二個身份驗證因數的挑戰。


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


如何在 Azure 安全中心內監視標識和訪問：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 對所有管理工作使用專用電腦（特權訪問工作站）

**指南**：使用具有多重要素驗證 （MFA） 的 PAW（特權訪問工作站）來登錄和配置 Azure HDInsight 群集和相關資源。


瞭解特權訪問工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7： 從管理客戶紀錄和警報可疑活動

**指南**：配置了企業安全包的 Azure HDInsight 群集可以連接到域，以便域使用者可以使用其域憑據進行身份驗證。 當 AAD 環境中發生可疑或不安全活動時，可以使用 Azure 活動目錄 （AAD） 安全報告生成日誌和警報。 使用 Azure 安全中心監視標識和訪問活動。


如何識別標記為有風險活動的 AAD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


如何監視 Azure 安全中心中的使用者標識和訪問活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從已批准的位置管理 Azure 資源

**指南**：配置了企業安全包的 Azure HDInsight 群集可以連接到域，以便域使用者可以使用其域憑據進行身份驗證。 使用條件訪問命名位置僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。


如何在 Azure 中配置命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活動目錄

**指南**：使用 Azure 活動目錄 （AAD） 作為中央身份驗證和授權系統。 AAD 對靜態和傳輸中的資料使用增強式加密來保護資料。 AAD 還對使用者憑據進行鹽漬、雜湊和安全地存儲。

配置了企業安全包 （ESP） 的 Azure HDInsight 群集可以連接到域，以便域使用者可以使用其域憑據對群集進行身份驗證。


如何創建和配置 AAD 實例：

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


如何在 Azure HDInsight 中使用 Azure 活動目錄域服務配置企業安全包：

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期審查和協調使用者訪問

**指南**：使用 Azure HDInsight 群集的 Azure 活動目錄 （AAD） 身份驗證。 AAD 提供日誌來説明發現陳舊的帳戶。 此外，使用 Azure 標識訪問審核可高效地管理組成員身份、對企業應用程式的訪問和角色指派。 可以定期查看使用者的存取權限，以確保只有正確的使用者才能繼續訪問。 


如何使用 Azure 標識訪問審核：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11： 監視訪問已停用帳戶的嘗試

**指南**：使用 Azure 活動目錄 （AAD） 登錄和稽核記錄監視訪問已停用帳戶的嘗試;這些日誌可以集成到任何協力廠商 SIEM/監視工具中。


您可以通過為 AAD 使用者帳戶創建診斷設置、將稽核記錄和登錄日誌發送到 Azure 日誌分析工作區來簡化此過程。 在 Azure 日誌分析工作區中配置所需的警報。


如何將 Azure 活動日誌集成到 Azure 監視器中：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帳戶登錄行為偏差警報

**指南**：配置了企業安全包 （ESP） 的 Azure HDInsight 群集可以連接到域，以便域使用者可以使用其域憑據對群集進行身份驗證。  使用 Azure 活動目錄 （AAD） 風險檢測和身份保護功能配置自動回應，以檢測與使用者身份相關的可疑操作。 此外，您可以將資料引入 Azure Sentinel 以進行進一步調查。


如何查看 AAD 風險登錄：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


如何配置和啟用身份保護風險策略：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支援方案期間向 Microsoft 提供對相關客戶資料的訪問

**指導**：不可用;Azure HDInsight 尚不支援客戶密碼箱。

客戶密碼箱支援服務清單：https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="data-protection"></a>資料保護

*有關詳細資訊，請參閱[安全控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 維護敏感資訊的清單

**指南**：使用與 Azure HDInsight 部署相關的資源上的標記，以説明跟蹤存儲或處理敏感資訊的 Azure 資源。


如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔離存儲或處理敏感資訊的系統

**指導**：為開發、測試和生產實施單獨的訂閱和/或管理組。 Azure HDInsight 群集和任何關聯的存儲帳戶應通過虛擬網路/子網分隔，在網路安全性群組 （NSG） 或 Azure 防火牆中正確標記和保護。 群集資料應包含在安全的 Azure 存儲帳戶或 Azure 資料湖存儲（第 1 代或第 2 代）中。


為 Azure HDInsight 群集選擇存儲選項：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


如何保護 Azure 資料湖存儲：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


如何保護 Azure 存儲帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 監控和阻止未經授權傳輸敏感資訊

**指南**：對於存儲或處理敏感資訊的 Azure HDInsight 群集，請使用標記將群集和相關資源標記為敏感。 要降低通過外滲來降低資料丟失的風險，請使用 Azure 防火牆限制 Azure HDInsight 群集的出站網路流量。


對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。


如何使用 Azure 防火牆限制 Azure HDInsight 群集的出站流量：

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感資訊

**指南**：加密傳輸中的所有敏感資訊。 確保連接到 Azure HDInsight 群集或群集資料存儲（Azure 存儲帳戶或 Azure 資料存儲第 1/Gen2）的任何用戶端能夠協商 TLS 1.2 或更高。 預設情況下，Microsoft Azure 資源將協商 TLS 1.2。 


瞭解傳輸中的 Azure 資料湖存儲加密：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


瞭解傳輸中的 Azure 存儲帳戶加密：

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活動發現工具識別敏感性資料

**指南**：資料標識、分類和損失預防功能尚未可用於 Azure 存儲或計算資源。 如果需要符合性，則實施協力廠商解決方案。



對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。



瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制對資源的訪問

**指南**：使用 Azure HDInsight 企業安全包 （ESP），您可以使用 Apache Ranger 為存儲在檔、資料夾、資料庫、表和行/列中的資料創建和管理細細微性的存取控制和資料模糊策略。 hadoop 管理員可以使用 Apache Ranger 中的這些外掛程式配置基於角色的存取控制 （RBAC）， 以保護 Apache Hive、HBase、Kafka 和 Spark。

使用 Apache Ranger 配置 RBAC 策略允許您將許可權與組織中的角色相關聯。 這一層的抽象概念可讓您更輕鬆地確保人員只擁有執行其職責所需的權限。

HDInsight 中具有 Azure 活動目錄域服務的企業安全包配置：https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Azure HDInsight 中的企業安全性概述：https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基於主機的資料丟失防護來實施存取控制

**指南**：對於存儲或處理敏感資訊的 Azure HDInsight 群集，請使用標記將群集和相關資源標記為敏感。 資料標識、分類和損失預防功能尚未可用於 Azure 存儲或計算資源。 如果需要符合性，則實施協力廠商解決方案。


對於由 Microsoft 管理的基礎平臺，Microsoft 將所有客戶內容視為敏感內容，並竭盡全力防止客戶資料丟失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已經實施並維護了一套強大的資料保護控制和功能。


瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 靜態加密敏感資訊

**指南**：如果使用 Azure SQL 資料庫存儲 Apache Hive 和 Apache Oozie 中繼資料，請確保 SQL 資料始終保持加密狀態。 對於 Azure 存儲帳戶和資料存儲湖存儲（第 1 代或第 2 代），建議允許 Microsoft 管理加密金鑰，但是，您可以選擇管理自己的金鑰。



如何管理 Azure 存儲帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



如何使用客戶託管加密金鑰創建 Azure 資料湖存儲：

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



瞭解 Azure SQL 資料庫的加密：

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



如何使用客戶託管金鑰為 SQL 資料庫配置透明資料加密：

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure 安全中心監視**：是

**責任**： 共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 對關鍵 Azure 資源的更改進行日誌和警報

**指南**：為與 Azure HDInsight 群集關聯的 Azure 存儲帳戶配置診斷設置，以監視和記錄針對群集資料的所有 CRUD 操作。 為與 Azure HDInsight 群集關聯的任何存儲帳戶或資料湖存儲啟用審核。


如何為 Azure 存儲帳戶啟用其他日誌記錄/審核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


如何為 Azure 資料湖存儲啟用其他日誌記錄/審核：

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure 安全中心監視**：是

**責任**： 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊，請參閱[安全控制：漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1： 運行自動漏洞掃描工具

**指導**：實施協力廠商漏洞管理解決方案。


或者，如果您有 Rapid7、Qualys 或任何其他漏洞管理平臺訂閱，則可以使用腳本操作在 Azure HDInsight 叢集節點上安裝漏洞評估代理，並通過相應的門戶管理節點。


如何手動安裝 Rapid7 代理：

https://insightvm.help.rapid7.com/docs/azure-security-center


如何手動安裝 Qualys 代理：

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


如何使用腳本操作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化作業系統修補程式管理解決方案

**指南**：已為叢集節點映射啟用了自動系統更新，但必須定期重新開機叢集節點以確保應用更新。


微軟維護和更新基本 Azure HDInsight 節點映射。


如何為 HDInsight 群集配置作業系統修補計畫：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指南**：使用腳本操作或其他機制來修補 Azure HDInsight 群集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。


如何為基於 Linux 的 Azure HDInsight 群集配置作業系統修補計畫：

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


如何使用腳本操作：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4： 比較背對背漏洞掃描

**指南**：實施協力廠商漏洞管理解決方案，該解決方案能夠隨著時間的推移比較漏洞掃描。 如果您有 Rapid7 或 Qualys 訂閱，則可以使用該供應商的門戶查看和比較背對背的漏洞掃描。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5： 使用風險評級流程確定修復已發現漏洞的優先順序

**指南**：使用常見的風險評分程式（例如常見漏洞評分系統）或協力廠商掃描工具提供的預設風險評級。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊，請參閱[安全控制：庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1： 使用 Azure 資產發現

**指南**：使用 Azure 資源圖查詢/發現訂閱中的所有資源（如計算、存儲、網路、埠和協定等），包括 Azure HDInsight 群集。  確保租戶中具有適當的（讀取）許可權，並能夠枚舉訂閱中的所有 Azure 訂閱和資源。


儘管可以通過 Azure 資源圖發現經典 Azure 資源，但強烈建議今後創建和使用 Azure 資源管理器資源。


如何使用 Azure 資源圖創建查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


如何查看 Azure 訂閱：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="62-maintain-asset-metadata"></a>6.2： 維護資產中繼資料

**指南**：將標記應用於 Azure 資源，使中繼資料以邏輯方式將它們組織到分類中。


如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3： 刪除未經授權的 Azure 資源

**指南**：在適當情況下，使用標記、管理組和單獨的訂閱來組織和跟蹤資產。 定期協調庫存，確保及時從訂閱中刪除未經授權的資源。


如何創建其他 Azure 訂閱：

https://docs.microsoft.com/azure/billing/billing-create-subscription


如何創建管理組：

https://docs.microsoft.com/azure/governance/management-groups/create


如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已批准的 Azure 資源和軟體標題的清單

**指南**：為計算資源定義已批准的 Azure 資源和已批准的軟體的清單

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 監視未經批准的 Azure 資源

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

- 不允許的資源類型
- 允許的資源類型

使用 Azure 資源圖查詢/發現訂閱中的資源。 確保環境中的所有 Azure 資源都獲得批准。

如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 資源圖創建查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 監控計算資源中未經批准的軟體應用程式

**指南**：實施協力廠商解決方案，以監視未經批准的軟體應用程式的叢集節點。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 刪除未經批准的 Azure 資源和軟體應用程式

**指南**：使用 Azure 資源圖查詢/發現訂閱中的所有資源（如計算、存儲、網路、埠和協定等），包括 Azure HDInsight 群集。  刪除發現的任何未經批准的 Azure 資源。 對於 Azure HDInsight 叢集節點，請實施協力廠商解決方案，以在未經批准的軟體上刪除或發出警報。


如何使用 Azure 資源圖創建查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="68-use-only-approved-applications"></a>6.8： 僅使用已批准的應用程式

**指南**：對於 Azure HDInsight 叢集節點，實現協力廠商解決方案以防止未經授權的軟體執行。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="69-use-only-approved-azure-services"></a>6.9： 僅使用已批准的 Azure 服務

**指南**：使用 Azure 策略對客戶訂閱中可創建的資源類型使用以下內置策略定義進行限制：

- 不允許的資源類型
- 允許的資源類型


如何配置和管理 Azure 策略：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何使用 Azure 策略拒絕特定資源類型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="610-implement-approved-application-list"></a>6.10： 實施已批准的申請清單

**指南**：對於 Azure HDInsight 叢集節點，實現協力廠商解決方案以防止執行未經授權的檔案類型。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者通過腳本與 Azure 資源管理器交互的能力

**指南**：使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止訪問"來限制使用者與 Azure 資源管理器交互的能力。


如何配置條件訪問以阻止對 Azure 資源管理器的訪問：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制使用者在計算資源中執行腳本的能力

**指導**：不適用;這不適用於 Azure HDInsight，因為群集的使用者（非管理員）不需要訪問各個節點來運行作業。 群集管理員對所有叢集節點具有根存取權限。

**Azure 安全中心監視**：當前不可用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或邏輯上隔離高風險應用程式

**指導**：不適用;基準測試適用于託管 Web 應用程式的 Azure 應用服務或計算資源。

**Azure 安全中心監視**：當前不可用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊，請參閱[安全控制：安全配置](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全配置

**指南**：在"Microsoft.HDInsight"命名空間中使用 Azure 策略別名創建自訂策略以審核或強制執行 HDInsight 群集的網路設定。


如何查看可用的 Azure 策略別名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的作業系統配置

**指南**： 由微軟管理和維護的 Azure HDInsight 作業系統映射。 負責為叢集節點的作業系統實施安全配置的客戶。 

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源配置

**指南**：使用 Azure 策略 [拒絕] 和 [部署（如果不存在））為 Azure HDInsight 群集和相關資源強制實施安全設置。


如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


瞭解 Azure 策略效果：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 維護安全的作業系統配置

**指南**： 由微軟管理和維護的 Azure HDInsight 作業系統映射。 負責實現作業系統級狀態配置的客戶。

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存儲 Azure 資源的配置

**指南**：如果使用自訂 Azure 策略定義，請使用 Azure DevOps 或 Azure 存儲庫安全地存儲和管理代碼。



如何在 Azure DevOps 中存儲代碼：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Azure 存儲庫文檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存儲自訂作業系統映射

**指導**：不適用;自訂映射不適用於 Azure HDInsight。

**Azure 安全中心監視**：當前不可用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7： 部署系統建構管理工具

**指南**：使用"Microsoft.HDInsight"命名空間中的 Azure 策略別名創建自訂策略以警報、審核和強制執行系統組態。 此外，開發用於管理原則異常的流程和管道。



如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：為作業系統部署系統建構管理工具

**指南**：實現協力廠商解決方案，以保持叢集節點作業系統所需的狀態。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實現自動化配置監視

**指南**：在"Microsoft.HDInsight"命名空間中使用 Azure 策略別名創建自訂策略以審核或強制執行 HDInsight 群集的配置。


如何查看可用的 Azure 策略別名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 對作業系統實施自動設定監控

**指南**：實施協力廠商解決方案來監視叢集節點作業系統的狀態。

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 機密

**指南**： Azure HDInsight 包括自帶金鑰 （BYOK） 支援 Apache Kafka。 這項功能可讓您擁有及管理用來加密待用資料的金鑰。


Azure HDInsight 中的所有託管磁片都受 Azure 存儲服務加密 （SSE） 的保護。 根據預設，這些磁碟上的資料會使用 Microsoft 所管理的金鑰來加密。 如果啟用 BYOK，則為 Azure HDInsight 提供加密金鑰，以便使用 Azure 金鑰保存庫對其進行使用和管理。


金鑰保存庫也可以與 Azure HDInsight 部署一起使用，以管理群集存儲的金鑰（Azure 存儲帳戶和 Azure 資料湖存儲）


如何在 Azure HDInsight 上為 Apache Kafka 帶來您自己的金鑰：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


如何管理 Azure 存儲帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自動地管理身份

**指南**：託管標識可用於 Azure HDInsight 中，以允許群集訪問 Azure 活動目錄域服務、訪問 Azure 金鑰保存庫或訪問 Azure 資料湖存儲 Gen2 中的檔。


使用 Azure HDInsight 瞭解託管標識：

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的憑據暴露

**指南**：如果使用與 Azure HDInsight 部署相關的任何代碼，則可以實現憑據掃描程式來標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置，如 Azure 金鑰保存庫。 


如何設置憑據掃描程式：

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**：不適用

**責任**： 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊，請參閱[安全控制：惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1： 使用集中管理的反惡意軟體

**指南**： Azure HDInsight 附帶了為叢集節點映射預先安裝並啟用的 Clamscan，但您必須管理軟體並手動聚合/監視 Clamcan 生成的任何日誌。


瞭解 Azure HDInsight 的夾子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：要上載到非計算 Azure 資源的預掃描檔

**指南**：在支援 Azure 服務的基礎主機上啟用了 Microsoft 反惡意軟體，但它不在客戶內容上運行。


預掃描上載到與 Azure HDInsight 群集部署相關的任何 Azure 資源的任何檔，如資料湖存儲、Blob 存儲等。在這些情況下，Microsoft 無法訪問客戶資料。


瞭解適用于 Azure 雲服務和虛擬機器的 Microsoft 反惡意軟體：

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心監視**：當前不可用

**責任**： 共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3： 確保更新反惡意軟體和簽名

**指南**： Azure HDInsight 附帶了為叢集節點映射預先安裝並啟用的 Clamscan。 Clamscan 將自動執行引擎和定義更新，但是，需要手動執行日誌的聚合和管理。


瞭解 Azure Azure HDInsight 的夾子：

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="data-recovery"></a>資料復原

*有關詳細資訊，請參閱[安全控制：資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1： 確保定期自動備份

**指南**：在 HDInsight 群集資料存儲中使用 Azure 存儲帳戶時，請選擇適當的冗余選項（LRS、ZRS、GRS、RA-GRS）。  將 Azure SQL 資料庫用於 Azure HDInsight 群集資料存儲時，請配置活動異地複製。


如何為 Azure 存儲帳戶配置存儲冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy


如何為 Azure SQL 資料庫配置冗余：

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2： 執行完整的系統備份並備份任何客戶託管金鑰

**指南**：在 Azure HDInsight 群集資料存儲中使用 Azure 存儲帳戶時，請選擇適當的冗余選項（LRS、ZRS、GRS、RA-GRS）。 如果對 Azure HDInsight 部署的任何部分使用 Azure 金鑰保存庫，請確保備份金鑰。


為 Azure HDInsight 群集選擇存儲選項：

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


如何為 Azure 存儲帳戶配置存儲冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy


如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶託管金鑰

**指南**：如果 Azure 金鑰保存庫正在與 Azure HDInsight 部署一起使用，則測試備份的客戶託管金鑰的還原。


如何在 Azure HDInsight 上為 Apache Kafka 帶來您自己的金鑰：

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


如何在 Azure 中還原金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保保護備份和客戶管理金鑰

**指南**：如果 Azure 金鑰保存庫正在與 Azure HDInsight 部署一起使用，請在金鑰保存庫中啟用虛刪除，以保護金鑰免遭意外或惡意刪除。


如何在 Azure 金鑰保存庫中啟用虛刪除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊，請參閱[安全控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：創建事件回應指南

**指導**：確保有書面的事件回應計畫，定義人員的角色以及事件處理/管理的各個階段。



如何在 Azure 安全中心內配置工作流自動化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2： 創建事件評分和優先順序處理過程

**指南**：安全中心為警報分配嚴重性，以説明您確定處理每個警報的順序，以便在資源受到威脅時，您可以馬上到達它。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析，以及導致警報的活動背後存在惡意的置信度。

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="103-test-security-response-procedures"></a>10.3： 測試安全回應程式

**指導**：進行練習，以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距，並根據需要修訂計畫。請參閱 NIST 出版物：IT 計畫和功能測試、培訓和鍛煉計劃指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**：不適用

**責任**： 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全事件連絡人詳細資訊，並配置安全事件的警報通知

**指南**：如果 Microsoft 安全回應中心 （MSRC） 發現您的資料已被非法或未經授權的方訪問，Microsoft 將使用安全事件聯繫資訊與您聯繫。



如何設置 Azure 安全中心安全連絡人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**：是

**責任**： 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5： 將安全警報納入事件回應系統

**指南**：使用"連續匯出"功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心資料連線器來資料流警報哨兵。



如何配置連續匯出：

https://docs.microsoft.com/azure/security-center/continuous-export



如何將警報資料流到 Azure 哨兵：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6： 自動回應安全警報

**指南**：使用 Azure 安全中心的工作流自動化功能，通過安全警報和建議上的"邏輯應用"自動觸發回應。



如何配置工作流自動化和邏輯應用：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**：當前不可用

**責任**： 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊，請參閱[安全控制：滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：對 Azure 資源進行定期滲透測試，並確保在 60 天內補救所有關鍵安全發現

**指南**：請遵循 Microsoft 的接用規則，以確保您的滲透測試不違反 Microsoft 策略：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



您可以在此處找到有關 Microsoft 針對 Microsoft 託管雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的戰略和實施的詳細資訊：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**：不適用

**責任**： 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
