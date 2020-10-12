---
title: Azure Resource Graph 中的 Advisor 資料
description: 在 Azure Resource Graph 中建立 Advisor 資料的查詢
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87057782"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>在 Resource Graph Explorer 中查詢 Advisor 資料 (Azure Resource Graph) 

Advisor 資源現在上線至 [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/)。 這會為許多適用于 Advisor 建議的大規模客戶案例奠定基礎。 幾種情況下，您不可能先進行調整，而且現在可以使用 Resource Graph 來達成：
* 提供在 Azure 入口網站中為您的所有訂用帳戶執行複雜查詢的功能
* 依類別類型摘要的建議 ( 例如可靠性、效能) 和影響類型 (高、中、低) 
* 特定建議類型的所有建議
* 依建議分類的受影響資源計數

![Azure resource graph explorer 中的 Advisor](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph 中的 Advisor 資源類型

[Resource Graph](../governance/resource-graph/index.yml)中可用的建議程式資源類型：有3種可用來查詢 advisor 資源的資源類型。 以下是現在可在 Resource Graph 中查詢的資源清單。
* Microsoft Advisor/設定
* Microsoft Advisor/建議
* Microsoft. Advisor/隱藏

這些資源類型會列在名為 AdvisorResources 的新資料表下，您也可以在 Azure 入口網站的 Resource Graph Explorer 中進行查詢。


## <a name="next-steps"></a>接下來的步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure Advisor 簡介](advisor-overview.md) (機器翻譯)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
