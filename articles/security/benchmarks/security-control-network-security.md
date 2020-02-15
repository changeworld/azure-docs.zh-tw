---
title: Azure 安全性控制-網路安全性
description: 安全性控制網路安全性
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251867"
---
# <a name="security-control-network-security"></a>安全性控制：網路安全性

網路安全性建議著重于指定允許或拒絕存取 Azure 服務的網路通訊協定、TCP/UDP 埠和網路聯機服務。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1-14。3 | 客戶 |

請確定所有虛擬網路子網部署都有一個網路安全性群組，其適用于應用程式受信任埠和來源的特定網路存取控制。 使用已啟用私人連結的 Azure 服務、在 Vnet 內部署服務，或使用私人端點私下連接。 如需服務特有的需求，請參閱該特定服務的安全性建議。

或者，如果您有特定的使用案例，就可以藉由執行 Azure 防火牆來滿足需求。

私人連結的一般資訊：

https://docs.microsoft.com/azure/private-link/private-link-overview

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署和設定 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視並記錄 Vnet、子網和 Nic 的設定和流量

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.2 | 9.3、12。2 | 客戶 |

使用 Azure 資訊安全中心並遵循網路保護建議，協助保護您在 Azure 中的網路資源。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶以進行流量審核。

如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 web 應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.3 | 9.5 | 客戶 |

在重要 Web 應用程式前方部署 Azure Web 應用程式防火牆（WAF），以進行連入流量的額外檢查。 啟用診斷設定以進行 WAF，並將記錄內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.4 | 12.3 | 客戶 |

在您的 Azure 虛擬網路上啟用 DDoS 標準保護，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意 IP 位址的通訊。

在組織的每個網路界限部署 Azure 防火牆，並啟用威脅情報並設定為 &quot;警示和拒絕&quot; 惡意網路流量。

使用 Azure 資訊安全中心的即時網路存取來設定 Nsg，以限制在有限期間內將端點暴露在核准的 IP 位址。

使用 Azure 資訊安全中心的彈性網路強化，以根據實際的流量和威脅情報來建議限制埠和來源 Ip 的 NSG 設定。

如何設定 DDoS 保護：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

瞭解 Azure 資訊安全中心整合式威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

瞭解 Azure 資訊安全中心的彈性網路強化：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

瞭解 Azure 資訊安全中心即時網路存取控制：

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.5 | 12.5、15。8 | 客戶 |

記錄 NSG 流程記錄到儲存體帳戶以產生流量記錄。 如果需要調查異常活動，請啟用網路監看員封包捕捉。

如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路監看員：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統（IDS/IPS）

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.6 | 12.6、12。7 | 客戶 |

在組織的每個網路界限部署 Azure 防火牆，並啟用威脅情報並設定為 &quot;警示和拒絕&quot; 惡意網路流量。

如何部署 Azure 防火牆： https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆設定警示： https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 應用程式的流量

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.7 | 12.9、12.10 | 客戶 |

針對受信任憑證啟用 HTTPS/SSL 的 web 應用程式，部署 Azure 應用程式閘道。

如何部署應用程式閘道：

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

如何設定應用程式閘道使用 HTTPS：

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

瞭解 Azure web 應用程式閘道的第7層負載平衡：

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.8 | 1.5 | 客戶 |

使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，ApiManagement），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。

瞭解及使用服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.9 | 11.1 | 客戶 |

使用 Azure 原則定義和執行網路資源的標準安全性設定。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、RBAC 控制項和原則），以簡化大規模的 Azure 部署。 您可以將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

網路的 Azure 原則範例：

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

如何建立 Azure 藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.1 | 11.2 | 客戶 |

使用標籤來 Nsg 和與網路安全性和流量相關的其他資源。 針對個別的 NSG 規則，使用 [&quot;描述&quot;] 欄位，針對允許進出網路流量的任何規則，指定商務需求和（或）持續時間（等）。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 1.11 | 11.3 | 客戶 |

使用 Azure 原則來驗證（及/或補救）網路資源的設定。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

網路的 Azure 原則範例：

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[記錄和監視](security-control-logging-monitoring.md)
