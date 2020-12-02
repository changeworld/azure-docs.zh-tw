---
title: Azure 安全性控制-網路安全性
description: Azure 安全性控制網路安全性
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a232f8e8c35e265a8243ac79e465c03f6b9650e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487859"
---
# <a name="security-control-network-security"></a>安全性控制：網路安全性

網路安全性建議著重于指定允許或拒絕存取 Azure 服務的網路通訊協定、TCP/UDP 埠和網路聯機服務。

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1、14.2、14。3 | 客戶 |

確定所有虛擬網路子網部署都已套用網路安全性群組，且該網路存取控制適用于您應用程式的受信任埠和來源。 如果有的話，請使用私人端點搭配 Private Link，藉由將 VNet 身分識別延伸至服務，來保護您的虛擬網路的 Azure 服務資源。 當私人端點和 Private Link 無法使用時，請使用服務端點。 如需瞭解服務的特定需求，請參閱該特定服務的安全性建議。 

或者，如果您有特定的使用案例，您可以藉由執行 Azure 防火牆來滿足需求。

- [瞭解虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [瞭解 Azure Private Link](../../private-link/private-link-overview.md)

- [如何建立虛擬網路](../../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和設定 Azure 防火牆](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.2 | 9.3、12.2、12。8 | 客戶 |

使用 Azure 資訊安全中心，並遵循網路保護建議來協助保護您在 Azure 中的網路資源。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶進行流量稽核。 您也可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [如何啟用 NSG 流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../../network-watcher/traffic-analytics.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.3 | 9.5 | 客戶 |

將 Azure Web 應用程式防火牆 (WAF) 部署在重要的 Web 應用程式前面，以額外檢查連入流量。 啟用診斷設定以 WAF 記錄，並將其內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。

- [如何部署 Azure WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.4 | 12.3 | 客戶 |

在您的 Azure 虛擬網路上啟用 DDoS 標準保護，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意 IP 位址的通訊。

在每個組織的網路界限上部署 Azure 防火牆，並啟用威脅情報並設定為惡意網路流量的「警示和拒絕」。

使用 Azure 資訊安全中心的即時網路存取來設定 Nsg，將端點的公開限制在有限的期間內。

使用 Azure 資訊安全中心調適型網路強化，根據實際的流量和威脅情報，建議可限制埠和來源 Ip 的 NSG 設定。

- [如何設定 DDoS 保護](../../ddos-protection/manage-ddos-protection.md)

- [如何部署 Azure 防火牆](../../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 資訊安全中心的整合式威脅情報](../../security-center/azure-defender.md)

- [瞭解 Azure 資訊安全中心適應性網路強化](../../security-center/security-center-adaptive-network-hardening.md)

- [瞭解 Azure 資訊安全中心及時的網路存取控制](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1.5：記錄網路封包

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.5 | 12.5 | 客戶 |

啟用網路監看員封包捕捉以調查異常活動。

- [如何啟用網路監看員](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.6 | 12.6、12。7 | 客戶 |

從支援具有承載檢查功能的 IDS/IPS 功能的 Azure Marketplace 中選取供應專案。  如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 Azure 防火牆威脅情報型篩選可以警示並拒絕傳向和來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火牆](../../firewall/tutorial-firewall-deploy-portal.md)

- [如何使用 Azure 防火牆設定警示](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.7 | 12.9、12.10 | 客戶 |

針對已啟用受信任憑證的 HTTPS/TLS 的 web 應用程式部署 Azure 應用程式閘道。

- [如何部署應用程式閘道](../../application-gateway/quick-create-portal.md)

- [如何將應用程式閘道設定為使用 HTTPS](../../application-gateway/create-ssl-portal.md)

- [瞭解 Azure web 應用程式閘道的第7層負載平衡](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.8 | 1.5 | 客戶 |

使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

您也可以使用應用程式安全性群組來協助簡化複雜的安全性設定。 應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。

- [瞭解和使用服務標記](../../virtual-network/service-tags-overview.md)

- [瞭解及使用應用程式安全性群組](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.9 | 11.1 | 客戶 |

使用 Azure 原則定義和執行網路資源的標準安全性設定。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品，例如 Azure 資源管理員範本、Azure RBAC 控制和原則，來簡化大規模的 Azure 部署。 您可以將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../../governance/policy/samples/built-in-policies.md#network)

- [如何建立 Azure 藍圖](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.10 | 11.2 | 客戶 |

使用 Nsg 的標記，以及與網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

- [如何建立虛擬網路](../../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.11 | 11.3 | 客戶 |

使用 Azure 活動記錄來監視資源設定，並偵測對您 Azure 資源的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

- [如何檢視及擷取 Azure 活動記錄事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制： [記錄和監視](security-control-logging-monitoring.md)