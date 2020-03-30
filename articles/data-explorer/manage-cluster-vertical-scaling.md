---
title: 管理群集垂直擴展（向上擴展），以匹配 Azure 資料資源管理器中的需求
description: 本文介紹根據不斷變化的需求向上擴展和縮小 Azure 資料資源管理器群集的步驟。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560434"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>在 Azure 資料資源管理器中管理群集垂直擴展（向上擴展），以適應不斷變化的需求

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 靜態叢集大小可能導致使用量過低或使用量過高，兩者皆不理想。

由於無法絕對準確地預測群集的需求，因此更好的方法是*擴展*群集，根據不斷變化的需求添加和刪除容量和 CPU 資源。 

有兩個工作流用於縮放 Azure 資料資源管理器群集：

* [水準縮放](manage-cluster-horizontal-scaling.md)，也稱為向上和向外縮放。
* 垂直縮放，也稱為向上和向下縮放。

本文介紹垂直縮放工作流：

## <a name="configure-vertical-scaling"></a>配置垂直縮放

1. 在 Azure 門戶中，轉到 Azure 資料資源管理器群集資源。 在 **"設置"** 下，選擇 **"向上縮放**"。

1. 在 **"向上縮放**"視窗中，您將看到群集的可用 SKU 清單。 例如，在下圖中，只有四個 SKU 可用。

    ![相應增加](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU 被禁用，因為它們是當前的 SKU，或者它們在群集所在的區域不可用。

1. 要更改 SKU，請選擇新的 SKU，然後按一下"**選擇**"。

> [!NOTE]
> * 垂直縮放過程可能需要幾分鐘時間，在此期間，群集將掛起。 
> * 向下擴展可能會損害群集性能。
> * 價格是群集虛擬機器和 Azure 資料資源管理器服務成本的估計。 不包括其他費用。 有關估計值，請參閱 Azure 資料資源管理器[成本估算器](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)頁，請參閱 Azure 資料資源管理器[定價頁](https://azure.microsoft.com/pricing/details/data-explorer/)，瞭解完整的定價資訊。

現在，您已為 Azure 資料資源管理器群集配置了垂直縮放。 為水準縮放添加另一個規則。 如果需要有關群集縮放問題的説明，請在 Azure 門戶中[打開支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>後續步驟

* [管理群集水準縮放](manage-cluster-horizontal-scaling.md)，以便根據指定的指標動態擴展實例計數。

* 通過按照本文監視資源使用方式：監視[Azure 資料資源管理器的性能、運行狀況以及指標的使用](using-metrics.md)。

