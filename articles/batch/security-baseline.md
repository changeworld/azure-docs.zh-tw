---
title: 批次處理的 Azure 安全基線
description: 批次處理的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fef1afb9748fd6f2f59b972c451d95ae1204ef70
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011628"
---
# <a name="azure-security-baseline-for-batch"></a>批次處理的 Azure 安全基線

批次處理的 Azure 安全基線包含可説明您改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview),它提供了有關如何使用最佳實務指南在 Azure 上保護雲端解決方案的建議。

有關詳細資訊,請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊,請參閱[安全控制:網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:使用虛擬網路上的網路安全組或 Azure 防火牆保護資源

**指導**:在虛擬網路中部署 Azure 批處理池。 要允許池計算節點與其他虛擬機或本地網路進行安全通信,可以在 Azure 虛擬網路的子網中預配池。 此外,在虛擬網路中部署池可以控制用於保護各個節點的網路介面 (NIC) 以及子網的網路安全組 (NSG)。 設定 NSG 以僅允許來自 Internet 上受信任的 IP/位置的流量。

如何在虛擬網路中建立 Azure 批次處理池:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: 監視和記錄 Vnet、子網和 NICS 的配置和流量

**指南**:使用 Azure 安全中心並修復與批次處理池關聯的虛擬網路/網路安全組 (NSG) 相關的網路保護建議。 在用於保護批處理池的 NSG 上啟用流日誌,並將日誌發送到 Azure 存儲帳戶以進行流量審核。 您還可以將 NSG 串流日誌發送到 Azure 日誌分析工作區,並使用 Azure 流量分析提供有關 Azure 雲中的流量流的見解。 Azure 流量分析的一些優點是能夠可視化網路活動並識別熱點、識別安全威脅、瞭解流量流模式和查明網路錯誤配置。

如何啟用 NSG 串流紀錄:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用與使用流量分析:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

瞭解 Azure 安全中心提供的網路安全:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

**指南**:在虛擬網路上啟用 Azure DDoS(分散式拒絕服務)標準保護,保護 Azure Batch 池,防止 DDoS 攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通訊。

如何設定 DDoS 保護:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 安全中心整合威脅情報:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 記錄網路封包與串流紀錄

**指南**:在用於保護 Azure 批處理池的網路安全組 (NSG) 上啟用流日誌,並將日誌發送到 Azure 存儲帳戶以進行流量審核。

如何啟用 NSG 串流紀錄:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統

**指南**:如果需要符合性,請從 Azure 應用商店中選擇一個網路虛擬設備,該設備支援具有有效負載檢查功能的入侵檢測系統 (IDS) 和入侵防護系統 (IPS) 功能。

如果不需要基於有效負載檢查的入侵檢測和/或預防,則可以使用具有威脅智慧的 Azure 防火牆。 Azure 防火牆威脅基於智慧的篩選可以警報和拒絕來自已知惡意 IP 位址和域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

使用公共 IP 位址在與 Azure 批次處理池節點相同的虛擬網路中部署具有公共 IP 位址的 Azure 防火牆。 在 Internet 上的受信任位置和單個池節點的專用 IP 位址之間設定網路位址轉換 (NAT) 規則。 在 Azure 防火牆上,在「威脅智慧」下,配置「警報和拒絕」以阻止以警報和阻止來自已知惡意 IP 位址和域的流量。 IP 位址和網域來自 Microsoft 威脅情報源,僅包含最高置信記錄。 

如何在虛擬網路中建立 Azure 批次處理池:

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何部署 Azure 防火牆:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: 管理 Web 應用程式的流量

**指南**:不適用,基準測試適用於在 Azure 應用服務或 IaaS 實例上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

**指南**:使用虛擬網路服務標記定義與 Azure 批處理池關聯的網路安全組或 Azure 防火牆上的網路存取控制件。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱(例如 ApiManagement),可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

瞭解並使用服務標記:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

**指南**:定義和實現與 Azure 批次處理池關聯的網路資源與 Azure 策略關聯的標準安全配置。 在「Microsoft.Batch」和「Microsoft.Network」命名空間中使用 Azure 策略別名來創建自訂策略以審核或強制執行 Azure 批處理池的網路配置。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

**指南**:對與 Azure 批處理池關聯的網路安全和流量流相關的網路服務組 (NSG) 和其他資源使用標記。 對於單個 NSG 規則,使用「描述」欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間(等)。

使用與標記相關的任何內置 Azure 策略定義(如"要求標記及其值")確保所有資源都使用標記創建,並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立 NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

**指南**:使用 Azure 活動日誌監視網路資源配置並檢測與 Azure 批處理池相關的網路資源的更改。 在 Azure 監視器中創建警報,這些警報將在對關鍵網路資源的更改時觸發。

如何檢視和檢索 Azure 活動日誌事件:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

如何在 Azure 監視器中建立警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊,請參閱[安全控制:日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 使用批准的時間同步來源

**指南**:默認情況下,對於 Azure 批處理,Microsoft 提供時間同步。但是,如果您有特定的時間同步要求,則可以實現這些更改。

**Azure 安全中心監視**:目前不可用

**責任**: 微軟

### <a name="22-configure-central-security-log-management"></a>2.2: 配置中央安全日誌管理

**指南**:將 Azure 批處理帳戶添加到 Azure 監視器,以聚合群集設備生成的安全資料。 利用自定義查詢來檢測和回應環境中的威脅。  對於 Azure 批次處理資源級監視,請使用批次處理 API 監視或查詢資源的狀態,包括作業、任務、節點和池。

如何將 Azure 批次處理帳戶並加入 Azure 監視器:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:為 Azure 資源啟用稽核紀錄記錄

**指南**:對於 Azure 批處理帳戶級別監視,請使用 Azure 監視器的功能監視每個批處理帳戶。 Azure 監視器會收集 Batch 帳戶層級範圍內的資源計量和診斷記錄 (選擇性)，這些資源包括集區、作業和工作等。 以手動方式或程式設計方式收集和使用此資料，以監視您 Batch 帳戶中的活動並診斷問題。

對於 Azure 批次處理資源等級監視,請使用 Azure 批次處理 API 監視或查詢資源的狀態,包括作業、任務、節點和池。

如何設定 Azure 批次處理帳號等級的監視和紀錄記錄:

https://docs.microsoft.com/azure/batch/monitoring-overview

瞭解批次處理資源級監視:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: 從作業系統收集安全日誌

**指南**:Azure 監視器收集 Azure 批處理帳戶中資源的指標和診斷日誌。 以多種方式收集和使用此數據來監視 Azure Batch 帳戶並診斷問題。 您也可以設定計量警示，在計量達到指定值時接收通知。

如果需要,您可以通過安全外殼 (SSH) 或遠端桌面協定 (RDP) 連接到單個池節點,以造訪本地作業系統日誌。

如何從 Azure 批處理帳戶收集診斷日誌:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

如何遠端連線到 Azure 批次處理池節點:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5: 設定安全紀錄儲存保留

**指南**:將 Azure 批處理帳戶添加到 Azure 監視器。 確保使用的 Azure 紀錄分析工作區已根據組織的合規性法規設置了日誌保留期

如何設定 Azure 批次處理監控與紀錄記錄:

https://docs.microsoft.com/azure/batch/monitoring-overview

如何設定 Azure 紀錄分析工作區保留期:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="26-monitor-and-review-logs"></a>2.6: 監視和審閱日誌

**指導**:創建 Azure 批處理指標警報,當指定指標的值超過給定閾值時觸發。

如何設定 Azure 批次處理指標警示:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 啟用例外活動警示

**指導**:創建 Azure 批處理指標警報,當指定指標的值超過給定閾值時觸發。

如何設定 Azure 批次處理指標警示:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8: 集中反惡意軟體紀錄記錄

**指南**:在 Windows 作業系統的情況下,在單個批次處理節點上使用 Windows Defender,或者如果您使用的是 Linux,則提供您自己的反惡意軟體解決方案。

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="29-enable-dns-query-logging"></a>2.9: 啟用 DNS 查詢紀錄記錄

**指南**: 為 dns 紀錄記錄實施第三方解決方案

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10: 啟用命令列稽核紀錄記錄

**指導**:根據每個節點手動配置控制台日誌記錄和 PowerShell 轉錄。

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊,請參閱[安全控制:識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 維持管理帳號清單

**指南**:維護在預配 Azure Batch 池期間創建的本地管理帳戶的記錄以及您創建的任何其他帳戶。 此外,如果使用 Azure 活動目錄 (AAD) 整合,則 AAD 具有必須顯式分配並因此可查詢的內建角色。 使用 AAD PowerShell 模組執行臨時查詢以發現屬於管理組成員的帳戶。

此外,您可以使用 Azure 安全中心標識和存取管理建議。

如何使用 PowerShell 在 AAD 中取得目錄角色:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 取得 AAD 中目錄角色的成員:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

如何使用 Azure 安全中心監視標識和訪問:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

**指南**:預配 Azure 批次處理池時,您可以選擇創建本地電腦帳戶。 沒有要更改的預設密碼,但您可以為安全外殼 (SSH) 和遠端桌面協定 (RDP) 訪問指定不同的密碼。 配置 Azure 批次處理池後,可以為 Azure 門戶中的單個節點或透過 Azure 資源管理器 API 生成隨機使用者。

如何將使用者新增到特定計算節點:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 確保使用專用管理帳戶

**指南**:將 Azure 批處理應用程式的身份驗證與 Azure 活動目錄集成。 圍繞使用專用管理帳戶創建政策和程式。

此外,您可以使用 Azure 安全中心標識和存取管理建議。

如何使用 Azure 活動目錄對批次處理應用程式進行身份驗證:

https://docs.microsoft.com/azure/batch/batch-aad-auth

如何使用 Azure 安全中心監視標識和訪問:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4:使用 Azure 活動目錄的單一登入 (SSO)

**指南**:不適用,雖然 Azure 批處理支援 Azure AD 身份驗證,但不支援單一登錄。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的訪問使用多重身份驗證。

**指南**:將 Azure 批次處理應用程式的身份驗證與 Azure 活動目錄 (AAD) 整合。 啟用 AAD 多重身份驗證 (MFA),並遵循 Azure 安全中心識別和存取管理建議。

 

如何在 Azure 中啟用 MFA:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 安全中心內監視標識和訪問:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

**指南**:使用具有多重身份驗證 (MFA) 的 PAW(特權存取工作站)來登錄和配置 Azure 批處理資源。

瞭解特權存取工作站:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 從管理帳戶記錄和警報可疑活動

**指南**:如果 Azure 批處理應用程式具有 Azure 活動目錄 (AAD) 的整合身份驗證,請使用 Azure 活動目錄安全報告生成日誌和警報,當環境中發生可疑或不安全活動時。 使用 Azure 安全中心監視標識和訪問活動。

如何辨識標記為有風險活動的 Azure AD 使用者:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何監視 Azure 安全中心中的使用者識別和訪問活動:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

**指南**:如果具有 Azure 活動目錄的 Azure 批次處理應用程式整合身份驗證,則可以使用條件存取命名位置僅允許從 IP 位址範圍或國家 / 區域的特定邏輯分組進行存取。

如何在 Azure 中設定命名位置:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="39-use-azure-active-directory"></a>3.9: 使用 Azure 的項目

**指南**:使用 Azure 活動目錄 (AAD) 作為中央身份驗證和授權系統,並將 Azure 批處理應用程式的身份驗證與 AAD 整合。 AAD 對靜態和傳輸中的數據使用強加密來保護數據。 AAD 還對使用者憑據進行鹽漬、哈希和安全地存儲。

如何建立與設定 AAD 實體:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何使用 AAD 對批次處理應用程式進行身份驗證:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期檢視和協調使用者存取

**指南**: Azure 活動目錄 (AAD) 提供日誌以幫助發現陳舊的帳戶。 此外,您可以使用 Azure 標識存取審核有效地管理組成員身份、訪問企業應用程式和角色分配。 可以定期查看使用者的訪問許可權,以確保只有正確的使用者才能繼續訪問。

如何使用 Azure 識別存取稽核:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 監視訪問已停用帳戶的嘗試

**指南**:為 Azure 活動目錄使用者帳戶創建診斷設置,將審核日誌和登錄日誌發送到 Azure 日誌分析工作區。 在 Azure 日誌分析工作區中配置所需的警報。

如何將 Azure 活動紀錄整合到 Azure 監視器中:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

**指南**:使用 Azure 活動目錄 (AAD) 風險檢測和身份保護功能配置自動回應,以識別與使用者身份相關的可疑操作。 此外,您可以將數據引入 Azure Sentinel 以進行進一步調查。

如何檢視 AAD 風險登入:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定與啟用身份保護風險原則:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何登上 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>支援專案的 Microsoft 提供對相關客戶資料的存取<br></div>

**指導**:不可用;Azure 批處理尚不支援客戶密碼箱。
 
客戶密碼箱支援服務清單:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="data-protection"></a>資料保護

*有關詳細資訊,請參閱[安全控制:資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊的清單

**指南**:使用標記來協助追蹤記憶體或處理敏感資訊的 Azure 資源。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

**指導**:為開發、測試和生產實施單獨的訂閱和/或管理組。 Azure 批處理池應由虛擬網路/子網分隔,標記適當,並通過網路安全組 (NSG) 進行保護。 Azure 批次處理數據應包含在安全的 Azure 儲存帳戶中。

如何在虛擬網路中建立 Azure 批次處理池:

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何保護 Azure 儲存帳戶:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊。

**指南**:對於與包含敏感資訊的 Azure 批處理池關聯的 Azure 存儲帳戶,請使用標記將它們標記為敏感,並使用 Azure 最佳做法對其進行保護。

數據識別、分類和損失預防功能尚未可用於 Azure 存儲或計算資源。 如果需要符合性,則實施第三方解決方案。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保護 Azure 儲存帳戶:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心監視**:目前不可用

**責任**: 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

**指南**:加密傳輸中的所有敏感資訊。 默認情況下,Microsoft Azure 資源將協商 TLS 1.2。 確保連接到 Azure 批次處理池或資料儲存(Azure 儲存帳戶)的任何用戶端能夠協商 TLS 1.2 或更高。

確保存取包含 Azure 批次處理資料的儲存帳戶需要 HTTPS。

瞭解傳輸中的 Azure 儲存帳戶加密:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 安全中心監視**:是

**責任**: 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 使用活動探索工具辨識敏感資料

**指南**:對於與包含敏感資訊的 Azure 批處理池關聯的 Azure 存儲帳戶,請使用標記將它們標記為敏感,並使用 Azure 最佳做法對其進行保護。

數據識別、分類和損失預防功能尚未可用於 Azure 存儲或計算資源。 如果需要符合性,則實施第三方解決方案。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保護 Azure 儲存帳戶:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 安全中心監視**:目前不可用

**責任**: 共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: 使用 Azure RBAC 控制對資源的存取

**指導**:使用 Azure 活動目錄 (AAD) 基於角色的訪問控制 (RBAC) 來控制對 Azure 資源管理層(包括批處理帳戶、批處理池)和存儲帳戶的訪問。

瞭解 Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

如何在 Azure 中設定 RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

**指南**:資料識別、分類和損失預防功能尚未可用於 Azure 存儲或計算資源。 如果需要符合性,則實施第三方解決方案。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 安全中心監視**:目前不可用

**責任**: 共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

**指南**:對於與 Azure Batch 帳戶關聯的儲存帳戶,建議允許 Microsoft 管理加密金鑰,但是,如果需要,您可以選擇管理自己的密鑰。

如何管理 Azure 儲存帳戶的加密金鑰:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

**指南**:將 Azure 監視器與 Azure 活動日誌一起用於建立警報,用於何時對與 Azure 批處理帳戶/池相關或關聯的關鍵 Azure 資源進行更改。

為與 Azure 批次處理池關聯的儲存帳戶配置診斷設置,以監視和記錄針對池資料的所有 CRUD 操作。

如何為 Azure 活動紀錄事件建立警報:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何為 Azure 儲存帳戶啟用其他紀錄記錄/審核:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊,請參閱[安全控制:漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 執行自動漏洞掃描工具

**指南**:對於 Azure 批處理池節點,您負責管理漏洞管理解決方案。

或者,如果您有 Rapid7、Qualys 或任何其他漏洞管理平臺訂閱,則可以在 Batch 池節點上手動安裝漏洞評估代理,並通過相應的門戶管理節點。

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:部署自動化作業系統修補程式管理解決方案

**指南**:微軟維護和更新基本 Azure 批處理池節點映射。 確保 Azure Batch 池節點的作業系統在群集生存期內保持修補程式,這可能需要啟用自動更新、監視節點或執行定期重新啟動。


**Azure 安全中心監視**:是

**責任**: 共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:部署自動第三方軟體修補程式管理解決方案

**指南**:確保 Azure 批處理池節點的第三方應用程式在群集生存期內保持修補程式,這可能需要啟用自動更新、監視節點或執行定期重新啟動。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 比較背對背漏洞掃描

**指南**:如果您有 Rapid7、Qualys 或任何其他漏洞管理平臺訂閱,則可以使用該供應商的門戶查看和比較背對背的漏洞掃描。

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 使用風險評級流程確定修復已發現漏洞的優先順序。

**指南**:使用常見的風險評分程式(例如常見漏洞評分系統)或第三方掃描工具提供的默認風險評級。

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊,請參閱[安全控制:庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1: 使用 Azure 資產發現

**指南**:使用 Azure 資源圖查詢/發現訂閱中的所有資源(如計算、存儲、網路等)。 確保租戶中具有適當的(讀取)許可權,並能夠枚舉訂閱中的所有 Azure 訂閱和資源。

儘管可以通過資源圖發現經典 Azure 資源,但強烈建議今後創建和使用 Azure 資源管理員 (ARM) 資源。

如何使用 Azure 圖形建立查詢:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂閱:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

**指南**:將標記應用於 Azure 資源,使元數據以邏輯方式將它們組織到分類中。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

**指南**:在適當情況下,使用標記、管理組和單獨的訂閱來組織和跟蹤資產。 定期協調庫存,確保及時從訂閱中刪除未經授權的資源。

如何建立其他 Azure 訂閱:

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用者標記:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:維護已批准的 Azure 資源和軟體標題的清單。

**指南**:定義已批准的 Azure 資源和已批准的計算資源軟體的清單


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型
- 允許的資源類型

使用 Azure 資源圖查詢/發現訂閱中的資源。 確保環境中的所有 Azure 資源都獲得批准。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 圖形建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中的未經批准的軟體應用程式

**指南**:對於 Azure 批處理池節點,實現第三方解決方案來監視未經批准的軟體應用程式的群集節點。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

**指南**:對於 Azure 批處理池節點,實現第三方解決方案來監視未經批准的軟體應用程式的群集節點。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="68-use-only-approved-applications"></a>6.8: 僅使用已批准的應用程式

**指南**:對於 Azure 批處理池節點,實現第三方解決方案以防止未經授權的軟體執行。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="69-use-only-approved-azure-services"></a>6.9: 僅使用已批准的 Azure 服務

**指南**:使用 Azure 政策對客戶訂閱中可建立的資源類型使用以下內建策略定義進行限制:

- 不允許的資源類型
- 允許的資源類型

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 政策拒絕特定資源類型:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="610-implement-approved-application-list"></a>6.10: 實施已批准的申請清單

**指南**:對於 Azure 批處理池節點,實現第三方解決方案以防止執行未經授權的文件類型。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>限制使用者透過文稿與 Azure 資源管理員互動的能力</div>

**指南**:使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止存取"來限制使用者與 Azure 資源管理員互動的能力。

如何設定條件存取以封鎖對 Azure 資源管理員的存取:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

**指南**:不適用,

這不適用於 Azure 批處理,因為 Azure Batch 池的使用者(非管理員)不需要存取各個節點來運行作業。 群集管理員已對所有節點具有根訪問許可權。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯隔離高風險應用程式

**指南**:不適用,基準測試適用於在 Azure 應用服務或 IaaS 實例上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊,請參閱[安全控制:安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

**指南**:使用「Microsoft.Batch」命名空間中的 Azure 策略別名創建自訂策略以審核或強制執行 Azure 批處理帳戶和池的配置。

如何檢視可用的 Azure 政策別名:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: 為您的作業系統建立安全性設定

**指南**:為批處理池節點的操作系統建立安全配置。

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3:維護所有 Azure 資源的安全設定

**指導**:使用 Azure 策略 [拒絕] 和 [部署(如果不存在))對與批處理帳戶和池相關的 Azure 資源(如虛擬網路、子網、Azure 防火牆、Azure 存儲帳戶等)強制實施安全設置。 您可以使用以下命名空間中的 Azure 政策別名來建立自訂政策:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 政策效果:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: 維護作業系統的安全設定

**指南**: 由 Microsoft 管理和維護的 Azure 批次處理池操作系統映射。 您負責實現作業系統級狀態配置。

**Azure 安全中心監視**:目前不可用

**責任**: 共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源配置

**指南**:如果對 Azure 批處理帳戶、池或相關資源使用自定義 Azure 策略定義,請使用 Azure 儲存庫安全地存儲和管理代碼。

如何在 Azure DevOps 中儲存代碼:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 儲存函式庫文件:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

**指南**:如果對 Azure 批次處理池使用自訂映射,請使用基於角色的存取控制 (RBAC) 以確保只有經過授權的使用者才能存取映射。

瞭解 Azure 中的 RBAC:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

如何在 Azure 中設定 RBAC:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 部署系統設定管理工具

**指南**:使用內置 Azure 策略定義來警報、審核和強制執行與 Azure 批處理相關的資源配置。  在「Microsoft.Batch」命名空間中使用 Azure 策略別名,為 Azure 批處理帳戶和池創建自定義策略。 此外,開發用於管理策略異常的流程和管道。

如何設定與管理 Azure 政策:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:部署作業系統的系統設定管理工具

**指南**:實現第三方解決方案,以保持 Azure 批處理池節點操作系統所需的狀態。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: 實現 Azure 服務的自動設定監視

**指南**:使用「Microsoft.Batch」命名空間中的 Azure 策略別名創建自訂策略以審核或強制執行 Azure Batch 實例的配置。 您還可以使用專為 Azure 批次產生的任何內建策略或 Azure 批次的資源,例如:

- 子網路應該與網路安全性群組建立關聯

-儲存帳戶應使用虛擬網路服務終結點

- 應啟用 Batch 帳戶中的診斷記錄

如何檢視可用的 Azure 政策別名:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 實現作業系統的自動設定監控

**指南**:實施第三方解決方案來監視 Azure 批處理池節點操作系統的狀態。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="711-securely-manage-azure-secrets"></a>7.11: 安全管理 Azure 機密

**指南**:Azure 密鑰保管庫可與 Azure 批處理部署一起使用,以管理 Azure 儲存帳戶中池存儲的密鑰。

如何與 Azure 託管識別整合:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Azure 金鑰保存庫:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用託管識別提供金鑰保管庫身份驗證:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: 安全自動管理身份

**指南**: 無法用,Azure 批次不支援託管服務識別


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 消除意外的認證

**指南**:實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置,如 Azure 密鑰保管庫。 

如何設定認證程式:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊,請參閱[安全控制:惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 使用集中管理的反惡意軟體

**指南**:在 Windows 作業系統的情況下,在單獨的 Azure 批處理池節點上使用 Windows 保護者,或者如果您使用的是 Linux,則提供您自己的反惡意軟體解決方案。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:要上傳到非計算 Azure 資源的預入檔案

**指南**:在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,Azure Batch),但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源(如應用服務、資料湖存儲、Blob 存儲等)的任何檔。在這些情況下,Microsoft 無法訪問客戶數據。

瞭解適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意軟體:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 安全中心監視**:不適用

**責任**: 共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 確保更新反惡意軟體和簽名

**指南**:在 Windows 作業系統的情況下,在單獨的 Azure 批處理池節點上使用 Windows 保護程式,並確保啟用了自動更新。 如果您使用的是 Linux,請提供您自己的反惡意軟體解決方案。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="data-recovery"></a>資料復原

*有關詳細資訊,請參閱[安全控制:資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

**指南**:在 Azure 批次處理池資料儲存中使用 Azure 儲存帳戶時,請選擇適當的冗餘選項(LRS、ZRS、GRS、RA-GRS)。 

如何為 Azure 儲存帳戶設定儲存冗餘:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

**指南**:在 Azure 批次處理池資料儲存中使用 Azure 儲存帳戶時,請選擇適當的冗餘選項(LRS、ZRS、GRS、RA-GRS)。  如果對 Azure 批次處理部署的任何部分使用 Azure 密鑰保管庫,請確保備份密鑰。

如何為 Azure 儲存帳戶設定儲存冗餘:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何在 Azure 中備份金鑰保管庫金鑰:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

**指南**:如果要為 Azure 儲存帳戶或任何其他與 Azure Batch 實現相關的資源管理自己的密鑰,請定期測試備份密鑰的還原。

如何在 Azure 中備份金鑰保管庫金鑰:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

如何使用 PowerShell 回復客戶託管金鑰:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

**指南**:如果 Azure 密鑰保管庫用於保存與 Azure 批處理池存儲帳戶相關的任何密鑰,請在 Azure 金鑰保管庫中啟用軟刪除,以保護密鑰免遭意外或惡意刪除。

如何在 Azure 金鑰保存庫中開啟軟刪除:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊,請參閱[安全控制:事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1:建立事件回應指南

**指導**:確保有書面的事件回應計劃,定義人員的角色以及事件處理/管理的各個階段。

如何在 Azure 安全中心內配置工作流自動化:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2:建立事件評分和優先順序處理過程

**指南**:安全中心為警報分配嚴重性,以説明您確定處理每個警報的順序,以便在資源受到威脅時,您可以馬上到達它。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

**指導**:進行練習,以常規節奏測試系統的事件回應能力。 找出薄弱環節和差距,並根據需要修訂計劃。

請參閱 NIST 出版物:IT 計劃和功能測試、培訓和鍛煉計劃指南:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: 提供安全事件連絡人詳細資訊,並&nbsp;配置安全 事件的警報通知

**指南**:如果 Microsoft 安全回應中心 (MSRC) 發現您的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。

如何設置 Azure 安全中心安全連絡人:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

**指南**:使用「連續匯出」功能匯出 Azure 安全中心警報和建議。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心數據連接器將警報流式傳輸到 Azure 哨兵。

如何設定連續匯出:

https://docs.microsoft.com/azure/security-center/continuous-export

如何將警報流式傳輸到 Azure 哨兵:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

**指南**:使用 Azure 安全中心的工作流自動化功能,通過安全警報和建議上的「邏輯應用」自動觸發回應。

如何設定工作流自動化和邏輯應用:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊,請參閱[安全控制:滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1:對 Azure 資源進行定期滲透測試,並確保在 60 天內修復所有關鍵安全發現。

**指南**:請遵循 Microsoft 的接用規則,以確保您的滲透測試不違反 Microsoft 策略:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

您可以在此處找到有關 Microsoft 針對 Microsoft 託管雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的策略和實施的詳細資訊: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**:不適用

**責任**: 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
