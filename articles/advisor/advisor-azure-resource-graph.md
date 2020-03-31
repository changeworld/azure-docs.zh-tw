---
title: Azure 資源圖中的顧問資料
description: 在 Azure 資源圖中查詢顧問資料
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502447"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>查詢資源圖資源管理器中的顧問資料（Azure 資源圖）

顧問資源現在已連接到 Azure[資源圖](https://azure.microsoft.com/features/resource-graph/)。 這為顧問建議的許多大規模客戶方案奠定了基礎。 以前無法大規模執行且現在可以使用資源圖實現的方案很少是：
* 提供對 Azure 門戶中的所有訂閱執行複雜查詢的功能
* 按類別類型（如高可用性、性能）和影響類型（高、中、低）匯總的建議
* 特定建議類型的所有建議
* 按建議類別影響的資源計數

![Azure 資源圖資源管理器中的顧問](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure 圖形中的顧問資源類型

[資源圖](https://docs.microsoft.com/azure/governance/resource-graph/)中的可用顧問資源類型：有 3 種資源類型可用於在顧問資源下查詢。 下面是資源圖中現在可用於查詢的資源的清單。
* 微軟.顧問/配置
* 微軟.顧問/建議
* 微軟.顧問/壓制

這些資源類型列在名為 AdvisorResources 的新表下，您還可以在 Azure 門戶中的資源圖資源管理器中查詢該表。


## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [顧問卓越運營建議](advisor-operational-excellence-recommendations.md)
* [顧問 REST API](https://docs.microsoft.com/rest/api/advisor/)
