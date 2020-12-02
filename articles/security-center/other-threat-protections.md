---
title: Azure 資訊安全中心提供的其他威脅防護
description: 了解 Azure 資訊安全中心除了 Azure Defender 還提供哪些威脅防護
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 7d59c0e751054522f4c54fe059f19aa0a941464e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489991"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure 資訊安全中心提供的其他威脅防護
除了內建的 [Azure Defender 保護](azure-defender.md)以外，Azure 資訊安全中心也提供下列威脅防護功能。

> [!TIP]
> 若要啟用資訊安全中心的威脅防護功能，您必須在包含適用工作負載的訂用帳戶上啟用 Azure Defender。
>
> 您只能在資源層級為 **適用於 MariaDB/MySQL/PostgreSQL 的 Azure 資料庫** 啟用威脅防護。


## <a name="threat-protection-for-azure-network-layer"></a>Azure 網路層的威脅防護 <a name="network-layer"></a>
資訊安全中心的網路層分析立基於範例 [IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，這是由 Azure 核心路由器收集到的封包標頭。 根據此資料摘要，資訊安全中心會使用機器學習模型來識別並標示惡意的流量活動。 資訊安全中心也會使用 Microsoft 威脅情報資料庫來擴充 IP 位址。

某些網路設定會使資訊安全中心無法針對可疑的網路活動產生警示。 若要讓資訊安全中心產生網路警示，請確定：
- 您的虛擬機器具有公用 IP 位址 (或位於具有公用 IP 位址的負載平衡器上)。
- 虛擬機器的網路輸出流量不會遭到外部 IDS 解決方案封鎖。

如需 Azure 網路層警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurenetlayer)。


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Azure Resource Manager 的威脅防護 (預覽)<a name ="management-layer"></a>
以 Azure Resource Manager 為基礎的資訊安全中心保護層目前處於預覽階段。

資訊安全中心會利用 Azure Resource Manager 事件來提供額外的保護層，我們將其視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄，資訊安全中心會在 Azure 訂用帳戶環境中偵測不尋常或可能有害的作業。

如需 Azure Resource Manager (預覽) 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureresourceman)。


>[!NOTE]
> 前述的幾項分析由 Microsoft Cloud App Security 提供技術支援。 若要受益於這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則依預設會啟用這些警示。 若要停用警示：
>
> 1. 在資訊安全中心功能表中，選取 [定價和設定]。
> 1. 選取您想變更的訂用帳戶。
> 1. 選取 [威脅偵測]。
> 1. 清除 [允許 Microsoft Cloud App Security 存取我的資料]，然後選取 [儲存]。


>[!NOTE]
>資訊安全中心會將關乎安全性的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。

1. 設定您要安裝代理程式的工作區。 請確定工作區位於您在資訊安全中心使用的相同訂用帳戶中，而且您具備工作區的讀取/寫入權限。

1. 啟用 **Azure Defender**，然後選取 [儲存]。


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB 的威脅防護 (預覽)<a name="cosmos-db"></a>

Azure Cosmos DB 警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索 Azure Cosmos DB 帳戶而產生的。

如需詳細資訊，請參閱

* [Azure Cosmos DB 的進階威脅防護 (預覽)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威脅防護警示清單 (預覽)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>其他 Microsoft 服務的威脅防護 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF 的威脅防護 <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 應用程式閘道 WAF 會以 Open Web Application Security Project 中的核心規則集 3.0 或 2.2.9 為基礎。 系統會自動更新 WAF 以防護新的弱點。 

如果您有 Azure WAF 的授權，您的 WAF 警示將會串流到資訊安全中心，而不需要額外的設定。 若要進一步了解 WAF 所產生的警示，請參閱 [Web 應用程式防火牆 CRS 規則群組與規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 保護的威脅防護 <a name="azure-ddos"></a>

目前已知分散式阻斷服務 (DDoS) 攻擊是很容易執行的。 這類攻擊已成為很大的安全顧慮，特別是當您將應用程式移轉至雲端時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定任何可透過網際網路連線的任何端點。

若要防範 DDoS 攻擊，請購買 Azure DDoS 保護的授權，並確實遵循應用程式設計的最佳做法。 DDoS 保護提供不同的服務層級。 如需詳細資訊，請參閱 [Azure DDoS 保護概觀](../ddos-protection/ddos-protection-overview.md)。

如需 Azure DDoS 保護警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>後續步驟
若要深入了解這些威脅防護功能的安全性警示，請參閱下列文章：

* [所有 Azure 資訊安全中心警示的參考資料表](alerts-reference.md)
* [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
* [在 Azure 資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [持續匯出資訊安全中心資料](continuous-export.md)