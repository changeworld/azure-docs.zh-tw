---
title: 使用容器的 Azure 監視器設定 PV 監視 |Microsoft Docs
description: 本文說明如何使用容器的 Azure 監視器來設定具有持續性磁片區的監視 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704468"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>使用容器的 Azure 監視器設定 PV 監視

從代理程式版本 *ciprod10052020* 開始，容器整合式代理程式的 Azure 監視器現在支援監視 PV (永久性磁片區) 使用量。

## <a name="pv-metrics"></a>PV 計量

容器的 Azure 監視器會在60sec 間隔收集下列計量，並將其儲存在 **InsightMetrics** 資料表中，以自動開始監視 PV。

|度量名稱 |) 的計量維度 (標記 |描述 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|使用特定 pod 所使用之宣告的特定永久性磁片區已使用的空間（以位元組為單位）。 `pvCapacityBytes` 會在 [標記] 欄位中折迭為維度，以減少資料內嵌成本並簡化查詢。|

## <a name="monitor-persistent-volumes"></a>監視持續性磁片區

針對容器 Azure 監視器，會在每個叢集的活頁簿中包含此度量的預先設定圖表。 您可以從左側窗格中選取活頁簿，以及從深入解析的 [**查看活頁簿**] 下拉式清單中，從 AKS 叢集，在 [**工作負載詳細資料**] 活頁簿的 [永久磁片區] 索引標籤中找到圖表。 您也可以針對 PV 使用方式啟用建議的警示，並在 Log Analytics 中查詢這些計量。  

![Azure 監視器 PV 工作負載活頁簿範例](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>後續步驟

- [在此](./container-insights-agent-config.md)深入瞭解收集的 PV 計量。
