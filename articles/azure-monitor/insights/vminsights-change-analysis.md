---
title: 適用於 VM 的 Azure 監視器中的變更分析
description: 適用於 VM 的 Azure 監視器與應用程式變更分析整合的整合可讓您查看對虛擬機器所做的任何變更，而這些變更可能會影響 it 效能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711223"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器中的變更分析
適用於 VM 的 Azure 監視器與 [應用程式變更分析](../app/change-analysis.md) 整合的整合可讓您查看對虛擬機器所做的任何變更，而這些變更可能會影響 it 效能。

## <a name="overview"></a>概觀
假設您的 VM 執行速度很慢，而且想要調查其設定的最新變更是否可能會影響其效能。 您可以使用適用於 VM 的 Azure 監視器來查看 VM 的效能，並找出過去一小時內的記憶體使用量增加。 變更分析可協助您判斷在這段時間內進行的任何設定變更是否為此增加的原因。

應用程式變更分析服務會匯總來自 [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) 的變更，以及來自 Azure Resource Manager 的嵌套屬性變更，例如網路安全性規則。 

## <a name="enabling-change-analysis"></a>啟用變更分析
若要在適用於 VM 的 Azure 監視器中上架變更分析，您必須註冊 *ChangeAnalysis* 資源提供者。 當您第一次啟動 Azure 入口網站中的適用於 VM 的 Azure 監視器或應用程式變更分析時，系統會自動為您註冊此資源提供者。 應用程式變更分析是一項免費服務，對資源沒有任何效能負擔。

## <a name="view-change-analysis"></a>查看變更分析
您可以選取 [**變更**] 選項，從適用於 VM 的 Azure 監視器的 [**效能**] 或 [**對應**] 索引標籤中取得變更分析。 

[![調查變更](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


按一下 [ **調查變更** ] 按鈕，啟動針對 VM 篩選的應用程式變更分析頁面。 您可以查看列出的變更，查看是否有任何可能造成此問題的變更。 如果您不確定特定的變更，您可以參考 [依資料行 **變更** ]，以判斷進行變更的人員。

[![變更詳細資料](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [應用程式變更分析](../app/change-analysis.md)。
- 深入瞭解 [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md)。 

