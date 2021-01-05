---
title: Azure 監視器計量圖表範例
description: 了解如何將您的 Azure 監視器資料視覺化。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 05dadfe88ed64aea8066b02298ba158a44a03c6f
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760164"
---
# <a name="metric-chart-examples"></a>計量圖表範例 

Azure 平台提供[超過一千個計量](./metrics-supported.md)，且許多都有維度。 藉由使用[維度篩選](./metrics-charts.md)、套用[分割](./metrics-charts.md)、控制圖表類型，以及調整圖表設定，您將可建立功能強大的診斷檢視和儀表板，以提供基礎結構和應用程式健康情況的深入解析。 本文提供可使用[計量瀏覽器](./metrics-charts.md)來建置的一些圖表範例，並說明設定各個圖表所需的步驟。

要與全世界分享您絕佳的圖表範例嗎？ 請在 GitHub 上共同撰寫此頁面，並在此處分享您自己的圖表範例！

## <a name="website-cpu-utilization-by-server-instances"></a>伺服器執行個體的網站 CPU 使用率

此圖表顯示 App Service 的 CPU 是否在可接受的範圍內，並依執行個體細分，以判斷負載是否適當分散。 在圖表中您可以看到，應用程式在上午 6 點之前執行於單一伺服器執行個體上，然後藉由新增另一個執行個體進行相應增加。

![依伺服器執行個體顯示的平均 CPU 百分比折線圖](./media/metrics-charts/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>如何設定此圖表？

選取您的 App Service 資源，並找出 [CPU 百分比] 計量。 然後，按一下 [套用分割]，並選取 [執行個體] 維度。

## <a name="application-availability-by-region"></a>依區域顯示的應用程式可用性

依區域檢視應用程式的可用性，可識別哪個地理位置發生問題。 此圖表顯示 Application Insights 可用性計量。 您可以看到，受監視的應用程式沒有來自美國東部資料中心的可用性問題，但是遇到來自美國西部和東亞的局部可用性問題。

![依位置顯示的平均可用性圖表](./media/metrics-charts/availability-by-location.png)

### <a name="how-to-configure-this-chart"></a>如何設定此圖表？

您必須先為網站開啟 [Application Insights 可用性](../app/monitor-web-app-availability.md)監視。 接著，選擇您的 Application Insights 資源，並選取 [可用性] 計量。 在 [執行位置] 維度上套用分割。

## <a name="volume-of-failed-storage-account-transactions-by-api-name"></a>依 API 名稱的失敗儲存體帳戶交易數量

您的儲存體帳戶資源發生超過大量的失敗交易。 您可以使用交易計量來識別哪些 API 負責過度的失敗。 請注意，下列圖表是使用相同的維度 (API 名稱來設定) 在分割和篩選失敗的回應類型中：

![API 交易的長條圖](./media/metrics-charts/split-and-filter-example.png)

### <a name="how-to-configure-this-chart"></a>如何設定此圖表？

在計量選擇器中，選取您的儲存體帳戶和 [交易] 計量。 將圖表類型切換至 [長條圖]。 按一下 [套用分割]，並選取 [API 名稱] 維度。 然後，按一下 [新增篩選]，並再次選擇 [API 名稱] 維度。 在 [篩選] 對話方塊中，選取您要在圖表上繪製的 API。

## <a name="next-steps"></a>後續步驟

* 了解 Azure 監視器 [Workbooks](./workbooks-overview.md)
* 深入了解[計量瀏覽器](metrics-charts.md)

