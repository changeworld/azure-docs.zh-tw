---
title: Azure Load Balancer SKU
description: Azure Load Balancer SKU 概觀
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: d08d7a81fddfe70593c31ac3ebd2191679ea1220
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206352"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer SKU

Azure Load Balancer 有兩個變體或 SKU。

## <a name="sku-comparison"></a><a name="skus"></a> SKU 比較

負載平衡器支援基本和標準 SKU。 這些 SKU 的案例規模、功能、價格各不相同。 任何可在基本負載平衡器建立的案例，都可以使用標準負載平衡器來建立。

若要比較並了解其中的差異，請參閱下表。 如需詳細資訊，請參閱 [Azure Standard Load Balancer 概觀](load-balancer-standard-overview.md)。

>[!NOTE]
> Microsoft 建議使用標準負載平衡器。
獨立 VM、可用性設定組和虛擬機器擴展集都只能和一個 SKU 連線，永遠不能和兩者同時連線。 與公用 IP 位址搭配使用時，負載平衡器和公用 IP 位址的 SKU 必須相符。 負載平衡器和公用 IP 的 SKU 不可變動。

| | Standard Load Balancer | 基本負載平衡器 |
| --- | --- | --- |
| **[後端集區大小](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)** | 支援最多 1000 個執行個體。 | 支援最多 300 個執行個體。 |
| **後端集區端點** | 單一虛擬網路中的任何虛擬機器或虛擬機器擴展集。 | 在單一可用性設定組或虛擬機器擴展集中的虛擬機器。 |
| **[健康情況探查](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[健康情況探查關閉行為](./load-balancer-custom-probe-overview.md#probedown)** | 執行個體探查關閉__和__所有探查關閉時，TCP 連線保持作用中。 | 執行個體探查關閉時，TCP 連線保持作用中。 所有探查關閉時，所有 TCP 連線都會終止。 |
| **可用性區域** | 輸入和輸出流量的區域備援和區域性前端。 | 無法使用 |
| **診斷** | [Azure 監視器多維度計量](./load-balancer-standard-diagnostics.md) | [Azure 監視器記錄](./load-balancer-monitor-log.md) |
| **HA 連接埠** | [適用於內部負載平衡器](./load-balancer-ha-ports-overview.md) | 無法使用 |
| **預設保護** | 除非網路安全性群組允許，否則會對輸入流量關閉。 請注意，從 VNet 到內部負載平衡器的內部流量仍受允許。 | 預設為開放狀態。 網路安全性群組 (選擇性)。 |
| **輸出規則** | [宣告式輸出 NAT 設定](./load-balancer-outbound-rules-overview.md) | 無法使用 |
| **TCP 重設閒置** | [適用於任何規則](./load-balancer-tcp-reset.md) | 無法使用 |
| **[多個前端](./load-balancer-multivip-overview.md)** | 輸入和[輸出](./load-balancer-outbound-connections.md) | 僅輸入 |
| **管理作業** | 大部分的作業 < 30 秒 | 通常是 60-90+ 秒 |
| **SLA** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 無法使用 | 

如需詳細資訊，請參閱[負載平衡器限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)。 如需標準 Load Balancer 詳細資料，請參閱[概觀](load-balancer-standard-overview.md)、[定價](https://aka.ms/lbpricing)及 [SLA](https://aka.ms/lbsla)。

## <a name="limitations"></a>限制

- SKU 不可變動。 您無法變更現有資源的 SKU。
- 獨立虛擬機器資源、可用性設定組資源或虛擬機器擴展集資源都只能參考一個 SKU，絕不會同時參考兩者。
- Standard Load Balancer 與標準公用 IP 資源不支援[移動訂用帳戶作業](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

## <a name="next-steps"></a>後續步驟

- 請參閱 [建立公用 Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) 以開始使用 Load Balancer。
- 了解如何使用[標準 Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。
- 深入了解[健康情況探查](load-balancer-custom-probe-overview.md)。
- 了解如何使用 [Load Balancer 來進行輸出連線](load-balancer-outbound-connections.md)。
- 了解[具有 HA 連接埠負載平衡規則的標準 Load Balancer](load-balancer-ha-ports-overview.md)。
- 深入了解[網路安全性群組](../virtual-network/security-overview.md)。
