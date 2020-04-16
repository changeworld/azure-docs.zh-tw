---
title: Azure 安全控制 ─ 網路安全
description: Azure 安全控制網路安全
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408308"
---
# <a name="security-control-network-security"></a>安全控制:網路安全

網路安全建議側重於指定允許或拒絕訪問 Azure 服務的網路協定、TCP/UDP 埠和網路連接的服務。

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:保護虛擬網路中的 Azure 資源

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | 客戶 |

確保所有虛擬網路子網部署都應用了網路安全組,並應用了特定於應用程式的可信埠和源的網路存取控制。 如果可用,請使用專用終結點與專用連結,通過將 VNet 標識擴展到服務,將 Azure 服務資源擴展到虛擬網路。 當專用終結點和專用連結不可用時,請使用服務終結點。 有關服務的特定要求,請參閱該特定服務的安全建議。 

或者,如果您有特定的用例,則可以通過實現 Azure 防火牆來滿足要求。

- [瞭解虛擬網路服務終結點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [瞭解 Azure 專用連結](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何建立具有安全設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [如何部署與設定 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:監視和記錄虛擬網路、子網和 NIC 的配置和流量

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | 客戶 |

使用 Azure 安全中心並遵循網路保護建議來幫助保護 Azure 中的網路資源。 啟用 NSG 串流日誌並將日誌發送到儲存帳戶以進行流量審核。 您還可以將 NSG 串流日誌發送到日誌分析工作區,並使用流量分析提供有關 Azure 雲中的流量流的見解。 流量分析的一些優點是能夠可視化網路活動並識別熱點、識別安全威脅、瞭解流量模式和查明網路錯誤配置。

- [如何開啟 NSG 串流紀錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何開啟及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [瞭解 Azure 安全中心提供的網路安全](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.3 | 9.5 | 客戶 |

在關鍵 Web 應用程式前面部署 Azure Web 應用程式防火牆 (WAF),以便對傳入流量進行額外檢查。 啟用 WAF 的診斷設置,並將日誌引入存儲帳戶、事件中心或日誌分析工作區。

- [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.4 | 12.3 | 客戶 |

在 Azure 虛擬網路上啟用 DDoS 標準保護,以防止 DDoS 攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意 IP 位址的通訊。

在啟用威脅智慧后,在每個組織的網路邊界部署 Azure 防火牆,並將其配置為針對惡意網路流量"警報和拒絕」。。

使用 Azure 安全中心「及時網路」訪問來配置 NSG,以在有限時間內將終結點暴露到已批准的 IP 位址。

使用 Azure 安全中心自適應網路強化來建議基於實際流量和威脅情報限制埠和源 IP 的 NSG 配置。

- [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [瞭解 Azure 安全中心整合威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [瞭解 Azure 安全中心自適應網路強化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [及時瞭解 Azure 安全中心 網路存取控制](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: 記錄網路封包

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.5 | 12.5 | 客戶 |

啟用網路觀察程序數據包捕獲以調查異常活動。

- [如何開啟網路觀察程式](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統(IDS/IPS)

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.6 | 12.6, 12.7 | 客戶 |

從 Azure 應用商店中選擇支援 IDS/IPS 功能以及有效負載檢查功能的產品/服務。  如果不需要基於有效負載檢查的入侵檢測和/或預防,則可以使用具有威脅智慧的 Azure 防火牆。 Azure 防火牆威脅基於智慧的篩選可以警報和拒絕來自已知惡意 IP 位址和域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路邊界部署您選擇的防火牆解決方案,以檢測和/或拒絕惡意流量。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [如何使用 Azure 防火牆配置警示](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: 管理 Web 應用程式的流量

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.7 | 12.9, 12.10 | 客戶 |

為啟用了 HRG 的 Web 應用程式為 Web 應用程式部署 Azure 應用程式閘道,這些憑證適用於受信任的憑證。

- [如何部署應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [如何設定應用程式閘道以使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [瞭解與 Azure Web 應用程式閘道的第 7 層負載平衡](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.8 | 1.5 | 客戶 |

使用虛擬網路服務標記在網路安全組或 Azure 防火牆上定義網路存取控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱(例如 ApiManagement),可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

您還可以使用應用程式安全組來幫助簡化複雜的安全配置。 應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。

- [瞭解並使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [瞭解及使用應用程式安全群組](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.9 | 11.1 | 客戶 |

使用 Azure 策略定義和實施網路資源的標準安全配置。

您還可以使用 Azure 藍圖透過在單個藍圖定義中打包關鍵環境工件(如 Azure 資源管理器樣本、RBAC 控制件和策略)來簡化大規模 Azure 部署。 您可以將藍圖應用於新訂閱,並通過版本控制微調控制和管理。

- [如何設定與管理 Azure 政策](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [網路的 Azure 政策範例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.10 | 11.2 | 客戶 |

對 NSG 和其他與網路安全和流量流相關的資源使用標記。 對於單個 NSG 規則,使用「描述」欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間(等)。

使用與標記相關的任何內置 Azure 策略定義(如"要求標記及其值")確保所有資源都使用標記創建,並通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI 查找資源或根據資源標記執行操作。

- [如何建立與使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何建立具有安全設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.11 | 11.3 | 客戶 |

使用 Azure 活動日誌監視資源配置並檢測對 Azure 資源的更改。 在 Azure 監視器中創建警報,這些警報將在對關鍵資源的更改時觸發。

- [如何檢視與檢索 Azure 活動紀錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全控制:[紀錄記錄和監視](security-control-logging-monitoring.md)
