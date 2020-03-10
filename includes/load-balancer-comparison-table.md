---
title: 包含檔案
description: 包含檔案
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202484"
---
| | Standard Load Balancer | 基本負載平衡器 |
| --- | --- | --- |
| [後端集區大小](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | 支援最多 1000 個執行個體。 | 支援最多 300 個執行個體。 |
| 後端集區端點 | 單一虛擬網路中的任何虛擬機器或虛擬機器擴展集。 | 在單一可用性設定組或虛擬機器擴展集中的虛擬機器。 |
| [健康情況探查](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [健康狀態探查關閉行為](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | 執行個體探查關閉__和__所有探查關閉時，TCP 連線保持作用中。 | 執行個體探查關閉時，TCP 連線保持作用中。 所有探查關閉時，所有 TCP 連線都會終止。 |
| 可用性區域 | 輸入和輸出流量的區域備援和區域性前端。 | 無法使用 |
| 診斷 | [Azure 監視器多維度計量](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Azure 監視器記錄](../articles/load-balancer/load-balancer-monitor-log.md) |
| HA 連接埠 | [適用於內部負載平衡器](../articles/load-balancer/load-balancer-ha-ports-overview.md) | 無法使用 |
| 預設保護 | 除非網路安全性群組允許，否則會對輸入流量關閉。 請注意，從 VNet 到內部負載平衡器的內部流量仍受允許。 | 預設為開放狀態。 網路安全性群組 (選擇性)。 |
| 輸出規則 | [宣告式輸出 NAT 設定](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 無法使用 |
| 閒置時的 TCP 重設 | [適用於任何規則](../articles/load-balancer/load-balancer-tcp-reset.md) | 無法使用 |
| [多個前端](../articles/load-balancer/load-balancer-multivip-overview.md) | 輸入和[輸出](../articles/load-balancer/load-balancer-outbound-connections.md) | 僅輸入 |
| 管理作業 | 大部分的作業 < 30 秒 | 通常是 60-90+ 秒 |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 無法使用 | 
