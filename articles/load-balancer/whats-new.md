---
title: Azure Load Balancer 的新功能
description: 了解 Azure Load Balancer 的新功能，例如最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 3a20b69189226486de8cfceb95e2fa79a976cb12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841028"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer 的新功能

Azure Load Balancer 會定期更新。 請隨時掌握最新公告。 本文提供以下相關資訊：

- 最新版本
- 已知問題
- 錯誤修正
- 已取代的功能 (如果適用)

您也可以在[這裡](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)找到最新的 Azure Load Balancer 更新並訂閱 RSS 摘要。

## <a name="recent-releases"></a>最新版本

| 類型 |名稱 |描述  |新增日期  |
| ------ |---------|---------|---------|
| 功能 | 支援跨資源群組的移動 | [資源群組移動](https://azure.microsoft.com/updates/standard-resource-group-move/)的 Standard Load Balancer 和標準公用 IP 支援。 | 2020 年 10 月 |
| 功能 | 支援 IP 型後端集區管理 (預覽) | Azure Load Balancer 支援透過 IPv4 或 IPv6 位址在後端集區中新增和移除資源。 這可讓您輕鬆管理與 Load Balancer 相關聯的容器、虛擬機器和虛擬機器擴展集。 其也允許在建立相關聯的資源之前，將 IP 位址保留為後端集區的一部分。 在[這裡](backend-pool-management.md)深入了解|2020 年 7 月 |
| 功能| 使用 Azure 監視器的 Azure Load Balancer 深入解析 | 此功能已建置為 Azure 網路監視器的一部分，客戶現在在 Azure 入口網站中，會擁有其所有 Load Balancer 設定的拓撲對應，以及其 Standard Load Balancer (已預先設定了計量) 的健康情況儀表板。 [開始使用並深入了解](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| 驗證 | 增加 HA 連接埠的驗證 | 已新增驗證，確保只有在啟用浮動 IP 時，才可設定 HA 連接埠規則和非 HA 連接埠規則。 先前已進行這項設定，但未如預期般執行。 未進行任何功能變更。 您可以在[這裡](load-balancer-ha-ports-overview.md#limitations)深入了解| 2020 年 6 月 |
| 功能| Azure Load Balancer 的 IPv6 支援 (正式推出) | 您可以使用 IPv6 位址作為您 Azure Load Balancer 的前端。 [在這裡了解如何建立雙重堆疊應用程式](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020 年 4 月|
| 功能| 閒置逾時時的 TCP 重設 (正式推出)| 使用 TCP 重設來建立更具有預測性的應用程式行為。 [深入了解](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="known-issues"></a>已知問題

產品小組正積極處理下列已知問題的解決方法：

|問題 |描述  |降低  |
| ---------- |---------|---------|
| Log Analytics 匯出 | Log Analytics 無法匯出 Standard Load Balancer 的計量，也無法匯出基本 Load Balancer 的健全狀態探查狀態記錄  | [針對 Standard Load Balancer，使用 Azure 監視器取得多維度計量](load-balancer-standard-diagnostics.md)。 雖然無法使用 Log Analytics 進行監視，但 Azure 監視器會提供一組豐富的多維度計量視覺效果。 您可以透過 Load Balancer 的 [深入解析] 子刀鋒視窗，利用預先設定的計量儀表板。 如果使用基本 Load Balancer，請[升級至標準](upgrade-basic-standard.md)以進行生產層級計量監視。

  

## <a name="next-steps"></a>後續步驟

如需 Azure Load Balancer 的詳細資訊，請參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和[常見問題集](load-balancer-faqs.md)。
