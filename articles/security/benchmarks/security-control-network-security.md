---
title: Azure 安全控制 - 網路安全
description: 安全控制網路安全
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251867"
---
# <a name="security-control-network-security"></a>安全控制：網路安全

網路安全建議側重于指定允許或拒絕訪問 Azure 服務的網路通訊協定、TCP/UDP 埠和網路連接的服務。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：使用虛擬網路上的網路安全性群組或 Azure 防火牆保護資源

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | 客戶 |

確保所有虛擬網路子網部署都應用了網路安全性群組，並應用了特定于應用程式的可信埠和源的網路存取控制。 在啟用專用連結時使用 Azure 服務，在 Vnet 中部署服務，或使用專用終結點進行私下連接。 有關服務的特定要求，請參閱該特定服務的安全建議。

或者，如果您有特定的用例，則可以通過實現 Azure 防火牆來滿足要求。

私人連結的一般資訊：

https://docs.microsoft.com/azure/private-link/private-link-overview

如何創建虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何創建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署和配置 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2： 監視和記錄 Vnet、子網和 NIC 的配置和流量

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.2 | 9.3, 12.2 | 客戶 |

使用 Azure 安全中心並遵循網路保護建議來説明保護 Azure 中的網路資源。 啟用 NSG 流日誌並將日誌發送到存儲帳戶以進行流量審核。

如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 安全中心提供的網路安全：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3： 保護關鍵 Web 應用程式

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.3 | 9.5 | 客戶 |

在關鍵 Web 應用程式前面部署 Azure Web 應用程式防火牆 （WAF），以便對傳入流量進行額外檢查。 啟用 WAF 的診斷設置，並將日誌引入存儲帳戶、事件中心或日誌分析工作區。

如何部署 Azure WAF：

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4： 拒絕與已知惡意 IP 位址的通信

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.4 | 12.3 | 客戶 |

在 Azure 虛擬網路上啟用 DDoS 標準保護，以防止 DDoS 攻擊。 使用 Azure 安全中心集成威脅智慧拒絕與已知惡意 IP 位址的通信。

在組織的每個網路邊界部署 Azure 防火牆，啟用威脅智慧並將其配置為&quot;針對惡意網路流量發出警報和拒絕&quot;。

使用 Azure 安全中心"及時網路"訪問來配置 NSG，以在有限時間內將終結點暴露到已批准的 IP 位址。

使用 Azure 安全中心自我調整網路強化來建議基於實際流量和威脅情報限制埠和源 IP 的 NSG 配置。

如何配置 DDoS 保護：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

瞭解 Azure 安全中心集成威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

瞭解 Azure 安全中心自我調整網路強化：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

及時瞭解 Azure 安全中心 網路存取控制：

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5： 記錄網路資料包和流日誌

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.5 | 12.5, 15.8 | 客戶 |

將 NSG 流日誌記錄到存儲帳戶中以生成流記錄。 如果需要調查異常活動，則啟用網路觀察程式資料包捕獲。

如何啟用 NSG 流日誌：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路觀察程式：

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6： 部署基於網路的入侵偵測/入侵防禦系統（IDS/IPS）

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.6 | 12.6, 12.7 | 客戶 |

在組織的每個網路邊界部署 Azure 防火牆，啟用威脅智慧並將其配置為&quot;針對惡意網路流量發出警報和拒絕&quot;。

如何部署 Azure 防火牆：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆配置警報：https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7： 管理 Web 應用程式的流量

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.7 | 12.9, 12.10 | 客戶 |

為啟用了 HTTPS/SSL 的 Web 應用程式為 Web 應用程式部署 Azure 應用程式閘道，這些證書適用于受信任的證書。

如何部署應用程式閘道：

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

如何配置應用程式閘道以使用 HTTPS：

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

瞭解第 7 層負載平衡與 Azure Web 應用程式閘道：

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8： 最大限度降低網路安全規則的複雜性和管理開銷

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.8 | 1.5 | 客戶 |

使用虛擬網路服務標記在網路安全性群組或 Azure 防火牆上定義網路訪問控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱（例如 ApiManagement），可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記。

瞭解並使用服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9： 維護網路設備的標準安全配置

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.9 | 11.1 | 客戶 |

使用 Azure 策略定義和實施網路資源的標準安全配置。

您還可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件（如 Azure 資源管理器範本、RBAC 控制項和策略）來簡化大規模 Azure 部署。 您可以將藍圖應用於新訂閱，並通過版本控制微調控制和管理。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

用於網路的 Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

如何創建 Azure 藍圖：

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10： 文檔流量配置規則

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.1 | 11.2 | 客戶 |

對 NSG 和其他與網路安全和流量流相關的資源使用標記。 對於單個 NSG 規則，&quot;使用&quot;"描述"欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間（等）。

如何創建和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何創建虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何創建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11： 使用自動工具監控網路資源配置並檢測更改

| Azure ID | 獨聯體的代用品 | 責任 |
|--|--|--|
| 1.11 | 11.3 | 客戶 |

使用 Azure 策略驗證（和/或修復）網路資源的配置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

用於網路的 Azure 策略示例：

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全控制：[日誌記錄和監視](security-control-logging-monitoring.md)
