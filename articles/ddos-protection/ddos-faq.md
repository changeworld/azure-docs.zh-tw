---
title: Azure DDoS 保護標準常見問題
description: Azure DDoS 保護標準的常見問題，可協助防禦 DDoS 攻擊。
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: b53501bf5aa4bafb0229158edfe0cb244116b909
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600845"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS 保護標準常見問題

本文將回答有關 Azure DDoS 保護 Standard 的常見問題。 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>什麼是分散式阻絕服務 (DDoS) 攻擊？
分散式阻斷服務（或 DDoS）是一種攻擊，攻擊者會將更多要求傳送給應用程式，而不是應用程式能夠處理的要求。 產生的效果是資源即將耗盡，因而影響應用程式的可用性，以及服務其客戶的能力。 在過去幾年來，業界發現攻擊明顯增加，攻擊變得更精密且更大。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。

## <a name="what-is-azure-ddos-protection-standard-service"></a>什麼是標準服務 Azure DDoS 保護？
Azure DDoS 保護 Standard （結合應用程式設計最佳作法）提供增強的 DDoS 風險降低功能，以防禦 DDoS 攻擊。 它會自動調整以協助保護您在虛擬網路中的特定 Azure 資源。 只需在任何新的或現有的虛擬網路上啟用保護，而不需進行任何應用程式或資源變更。 它具有數個優於基本服務的優點，包括記錄、警示與遙測。 如需詳細資訊，請參閱 [Azure DDoS 保護 Standard 總覽](ddos-protection-overview.md) 。 

## <a name="how-does-pricing-work"></a>價格如何運作？
DDoS 保護計劃每月固定收取每月 $2944 的費用，最多可涵蓋100個公用 IP 位址。 針對額外資源的保護，每個月每個資源會額外增加 $30 的費用。 

在租使用者下，單一的 DDoS 保護計劃可以跨多個訂用帳戶使用，因此不需要建立一個以上的 DDoS 保護計劃。

如需詳細資訊，請參閱 [Azure DDoS 保護標準定價](https://azure.microsoft.com/pricing/details/ddos-protection/) 。

## <a name="is-the-service-zone-resilient"></a>服務區域是否可復原？
可以。 Azure DDoS 保護預設為區域復原。

## <a name="how-do-i-configure-the-service-to-be-zone-resilient"></a>如何? 將服務設定為具備區域復原能力？
不需要客戶設定即可啟用區域復原。 預設會提供 Azure DDoS 保護資源的區域復原功能，並由服務本身管理。

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>服務層級 (第7層) 的保護為何？
客戶可以搭配 Web 應用程式防火牆使用 Azure DDoS 保護服務 (WAF) ，以在網路層 (第3層和第4層（由 Azure DDoS 保護 Standard) 和應用層 (第7層提供）（由 WAF) 提供）提供保護。 WAF 供應專案包括 Azure [應用程式閘道 WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，以及 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中提供的協力廠商 web 應用程式防火牆供應專案。

## <a name="are-services-unsafe-in-azure-without-the-service"></a>沒有服務的 Azure 中的服務不安全嗎？
在 Azure 上執行的服務原本就受到 Azure DDoS 保護基本保護，以保護 Azure 的基礎結構。 不過，保護基礎結構的保護有更高的閾值，但大部分的應用程式都有可處理的容量，而且不提供遙測或警示，因此，雖然流量可能會被認為是平臺無害的流量，但應用程式接收它可能會造成損害。 

藉由上架至 Azure DDoS 保護 Standard 服務，應用程式會取得專用監視來偵測攻擊和應用程式特定閾值。 服務將會受到調整為預期流量量的設定檔所保護，以提供更緊密的防禦來防範 DDoS 攻擊。

## <a name="what-are-the-supported-protected-resource-types"></a>受支援的受保護資源類型有哪些？
以 ARM 為基礎的 Vnet 中的公用 Ip 目前是唯一的受保護資源類型。 未提供 PaaS 服務 (多租使用者) 。 如需詳細資訊，請參閱 [Azure DDoS 保護標準參考架構](ddos-protection-reference-architectures.md) 。

## <a name="are-classicrdfe-protected-resources-supported"></a>是否支援傳統/RDFE 受保護的資源？
預覽版僅支援以 ARM 為基礎的受保護資源。 不支援傳統/RDFE 部署中的 Vm。 目前尚未針對傳統/RDFE 資源規劃支援。 如需詳細資訊，請參閱 [Azure DDoS 保護標準參考架構](ddos-protection-reference-architectures.md) 。

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>我可以使用 DDoS 保護來保護 PaaS 資源嗎？
目前不支援連接到多租使用者的公用 Ip、單一 VIP PaaS 服務。 不支援的資源範例包括儲存體 Vip、事件中樞 Vip 和應用程式/雲端服務應用程式。 如需詳細資訊，請參閱 [Azure DDoS 保護標準參考架構](ddos-protection-reference-architectures.md) 。

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>我可以使用 DDoS 保護來保護內部部署資源嗎？
您必須將服務的公用端點與 Azure 中的 VNet 相關聯，才能啟用 DDoS 保護。 範例設計包括：
- Web sites (Azure 中的 IaaS) 和內部內部部署資料中心內的後端資料庫。 
- Azure 中的應用程式閘道 (在內部部署資料中心的應用程式閘道/WAF) 和網站上啟用 DDoS 保護。

如需詳細資訊，請參閱 [Azure DDoS 保護標準參考架構](ddos-protection-reference-architectures.md) 。

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>我可以在 Azure 外部註冊網域，並將其與 VM 或 ELB 等受保護的資源產生關聯嗎？
在公用 IP 案例中，只要相關聯的公用 IP 裝載于 Azure 上，DDoS 保護服務就會支援任何應用程式，不論相關聯的網域是在何處註冊或託管。 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>我可以手動設定套用至 Vnet/Public Ip 的 DDoS 原則嗎？
否，目前不提供原則自訂。

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>我可以允許清單/封鎖清單特定 IP 位址嗎？
否，目前無法使用手動設定。

## <a name="how-can-i-test-ddos-protection"></a>如何測試 DDoS 保護？
請參閱 [透過模擬進行測試](test-through-simulations.md)。

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>在入口網站上載入計量需要多久的時間？
在5分鐘內，應會在入口網站上顯示度量。 如果您的資源受到攻擊，則在5-7 分鐘內，會在入口網站上開始顯示其他計量。 
    
