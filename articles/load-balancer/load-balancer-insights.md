---
title: Azure Load Balancer 的見解
description: 使用負載平衡器深入解析來達成快速的錯誤當地語系化和明智的設計決策
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 2168ee05ab93655cc0ad87221bff29c1b6b1035d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897584"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>使用深入解析來監視和設定您的 Azure Load Balancer

透過 [網路的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/insights-overview#azure-monitor-for-networks-preview)，您就可以為負載平衡器提供功能相依性視覺效果和預先設定的計量儀表板。 這些視覺效果可協助您做出明智的設計決策，以及快速地當地語系化、診斷及解決任何錯誤。

>[!NOTE] 
>請注意這項功能目前為預覽狀態，而且功能相依性視圖和預先設定的儀表板可能會變更以改善這項體驗

>[!IMPORTANT]
>需要 Standard Load Balancer，才能在預先設定的計量儀表板中查看 Load Balancer 命名空間的度量。 不過，您仍然可以看到來自 VM、虛擬機器擴展集和連線監視器命名空間的計量，但我們建議您 [升級為](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard) 任何生產工作負載的標準，以充分利用一組健全的 Load Balancer 計量。

## <a name="functional-dependency-view"></a>功能相依性視圖

功能相依性視圖可讓您即使是最複雜的負載平衡器，也能讓您的圖片。 您可以對最新的 Load Balancer 設定提供視覺上的意見反應，同時進行更新，同時記住您的設定。

您可以造訪 Azure 中 Load Balancer 資源的深入解析分頁，以存取此視圖。

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="功能相依性視圖的 Depecition。負載平衡器的前端可透過設定的規則連線到後端集區成員。針對 Standard Load Balancer，從負載平衡規則到後端集區實例的程式碼會根據健康情況探查狀態以色彩標示。" border="true":::

針對標準負載平衡器，您的後端集區資源會以色彩標示健康情況探查狀態，指出後端集區目前的可用性以提供流量。 除了上述拓撲，您也會看到健全狀況狀態的圖表，並提供應用程式健康情況的快照集。

## <a name="metrics-dashboard"></a>計量儀表板

您可以從 Load Balancer 的深入解析分頁，選取更詳細的度量來查看預先設定的 [Azure 監視器活頁簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) ，其中包含與 Load Balancer 特定層面相關的度量視覺效果。 此儀表板會顯示 Load Balancer 狀態，以及頁面頂端相關檔的連結。

一開始，您會看到 [總覽] 索引標籤。您可以流覽可用的索引標籤，其中每個索引標籤都包含與 Load Balancer 特定層面相關的視覺效果。 每個索引標籤底部的儀表板中都有提供各自的明確指引。

目前可用的儀表板索引標籤為：
* 概觀
* 前端和後端可用性
* 資料輸送量
* 流量散發
* 連線監視
* 計量定義 

### <a name="overview-tab"></a>Overview (概觀) 索引標籤
[總覽] 索引標籤包含可搜尋的方格，其中包含每個連接到 Load Balancer 的前端 Ip 的整體資料路徑可用性和健康情況探查狀態。 這些計量指出前端 IP 是否有回應，而且您後端集區中的計算實例會個別回應輸入連線。

您也可以在此頁面上查看每個前端 IP 的整體資料輸送量，以瞭解您是否要產生及接收預期的流量層級。 頁面底部的指導方針會將您導向至適當的索引標籤，您應該會看到任何不規則的值。

### <a name="frontend-and-backend-availability-tab"></a>前端和後端可用性索引標籤
前端和後端可用性索引標籤會顯示資料路徑輸送量和健康情況探查狀態計量，顯示在幾個實用的觀點中。 第一個圖表會顯示匯總值，因此您可以判斷是否有問題。 圖形的其餘部分會顯示依各種維度分割的這些計量，讓您可以針對任何輸入可用性問題進行疑難排解並識別其來源。

頁面底部會提供用來觀看這些圖表的工作流程，並有各種徵兆的常見原因。 

### <a name="data-throughput-tab"></a>[資料輸送量] 索引標籤
[資料輸送量] 索引標籤可讓您查看輸入和輸出輸送量，以找出流量模式是否如預期般運作。 它會顯示依前端 IP 和前端埠分割的輸入和輸出資料輸送量，讓您可以識別您執行的服務是否個別執行。

### <a name="flow-distribution"></a>流量散發
[流程散發] 索引標籤可協助您將後端實例接收和產生的流程數目視覺化及管理。 它會顯示輸入和輸出流量的流程建立率和流程計數，以及每個 VM 和虛擬機器擴展集實例所接收的網路流量。 

這些視圖可讓您回饋 Load Balancer 設定或流量模式是否會導致不平衡流量。 例如，如果您已設定會話親和性，而單一用戶端進行了不相稱的要求數目。 它也會讓您知道您是否已接近機器大小的 [每個 VM 流量限制](https://docs.microsoft.com/azure/virtual-network/virtual-machine-network-throughput#flow-limits-and-recommendations) 。

### <a name="connection-monitors"></a>連線監視
[連線監視器] 索引標籤會顯示您已設定之所有連線 [監視器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)  的全域地圖上的來回延遲。 這些視覺效果為具有嚴格延遲需求的服務提供有用的資訊。 若要符合您的需求，您可能需要新增額外的區域部署，或移至 [跨區域負載平衡](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) 模型

### <a name="metric-definitions"></a>計量定義
[度量定義] 索引標籤包含 [多維度計量文章](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)中顯示的所有資訊。

## <a name="next-steps"></a>後續步驟
* 如果有任何可改善的資訊，請參閱儀表板並使用以下連結提供意見反應
* [請參閱計量檔，以確保您瞭解如何計算每個度量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)
* [為您的 Load Balancer 建立連線監視器](https://docs.microsoft.com/azure/network-watcher/connection-monitor)
* [建立您自己](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)的活頁簿，您可以按一下詳細計量儀表板中的 [編輯] 按鈕來取得靈感
