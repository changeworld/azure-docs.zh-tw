---
title: Azure Resource Graph 中的 Advisor 資料
description: 在 Azure Resource Graph 中進行 Advisor 資料的查詢
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057782"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>在 Resource Graph Explorer 中查詢 Advisor 資料（Azure Resource Graph）

Advisor 資源現在已上架到[Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/)。 這會為許多適用于 Advisor 建議的大規模客戶案例奠定基礎。 幾種情況下不可能進行規模調整，而且現在可以使用 Resource Graph 來達成：
* 提供在 Azure 入口網站中，為您的所有訂閱執行複雜查詢的功能
* 依類別目錄類型（例如可靠性、效能）和影響類型（高、中、低）摘要的建議
* 針對特定建議類型的所有建議
* 依建議分類的受影響資源計數

![Azure 資源圖表 explorer 中的 Advisor](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph 中的 Advisor 資源類型

[Resource Graph](../governance/resource-graph/index.yml)中可用的 Advisor 資源類型：有3個資源類型可供在 Advisor 資源下查詢。 以下是現在可在 Resource Graph 中查詢的資源清單。
* Microsoft Advisor/設定
* Microsoft Advisor/建議
* Microsoft Advisor/隱藏

這些資源類型會列在名為 AdvisorResources 的新資料表底下，您也可以在 Azure 入口網站的 [Resource Graph Explorer] 中查詢。


## <a name="next-steps"></a>接下來的步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
