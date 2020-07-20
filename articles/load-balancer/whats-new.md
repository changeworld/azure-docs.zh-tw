---
title: Azure Load Balancer 的新功能
description: 了解 Azure Load Balancer 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147469"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer 的新功能

Azure Load Balancer 會定期更新。 請隨時掌握最新公告。 本文提供以下相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已取代的功能 (如果適用)

您也可以在[這裡](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)找到最新的 Azure Load Balancer 更新並訂閱 RSS 摘要。

## <a name="new-features"></a>新功能

|功能  |描述  |新增日期  |
|---------|---------|---------|
| 支援 IP 型後端集區管理 (預覽) | Azure Load Balancer 支援透過 IPv4 或 IPv6 位址在後端集區中新增和移除資源。 這可讓您輕鬆管理與 Load Balancer 相關聯的容器、虛擬機器和虛擬機器擴展集。 其也允許在建立相關聯的資源之前，將 IP 位址保留為後端集區的一部分。 | 2020 年 7 月 |
| 使用 Azure 監視器的 Azure Load Balancer 深入解析 | 此功能已建置為 Azure 網路監視器的一部分，客戶現在在 Azure 入口網站中，會擁有其所有 Load Balancer 設定的拓撲對應，以及其 Standard Load Balancer (已預先設定了計量) 的健康情況儀表板。 [開始使用並深入了解](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| Azure Load Balancer 的 IPv6 支援 (正式推出) | 您可以使用 IPv6 位址作為您 Azure Load Balancer 的前端。 [在這裡了解如何建立雙重堆疊應用程式](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020 年 4 月|
| 閒置逾時時的 TCP 重設 (正式推出)| 使用 TCP 重設來建立更具有預測性的應用程式行為。 [深入了解](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="next-steps"></a>後續步驟

如需 Azure Load Balancer 的詳細資訊，請參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和[常見問題集](load-balancer-faqs.md)。