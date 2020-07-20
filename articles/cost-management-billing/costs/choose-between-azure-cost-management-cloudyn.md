---
title: 選擇 Azure 成本管理和 Cloudyn
description: 本文可協助您判斷 Azure 成本管理還是 Cloudyn 較符合您的成本管理需求。
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 9b17cdfbae7fccae146f01654fb755f23f00563c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80083205"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>選擇 Azure 成本管理和 Cloudyn

Cloudyn 即將在 2020 年底淘汰。 現有的 Cloudyn 功能會盡可能直接整合到 Azure 入口網站中。 除了 CSP 客戶以外，目前沒有任何新的客戶可以上線。 現有產品在完全淘汰之前將保有其支援。

Microsoft 已收購 Cloudyn，並且以原生方式將其成本管理功能從 Cloudyn 入口網站遷移至 Azure。 若要使用新功能，請登入 Azure 入口網站並瀏覽至 Azure 服務清單中的[成本管理與計費](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)。 相較於 Cloudyn，原生體驗會提供更佳的效能和較低的資料延遲 (大約八小時)。

我們已將 Enterprise 合約、隨用隨付及 MSDN 供應項目類別的重要功能遷移到 Azure 成本管理。 將 CSP 訂用帳戶遷移至 Azure 成本管理的作業正在進行中。

如果您有尚未遷移的供應項目類別，您應該繼續使用 Cloudyn 入口網站。 其他人則可以使用 Azure 成本管理。

## <a name="recommended-services-by-offer"></a>依供應項目建議的服務

| Microsoft Azure 供應項目 | 建議的成本管理服務 |
| --- | --- |
| Azure Enterprise 合約 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft 客戶合約 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| 合作夥伴支援的 Microsoft 客戶合約 | [Azure 成本管理](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>可用的成本管理功能

下列有些功能可在 Cloudyn 中使用，但現在這些功能全部都可在 Azure 成本管理中使用。

- API
- Azure 成本最佳化建議，包括 (但不限於)：
    - Azure 執行個體調整為最適大小和關閉建議
    - Azure 保留建議
- 預算
- 成本分析
- 將資料匯出至 Azure 儲存體帳戶
- 更低的延遲性
- Power BI 範本應用程式
- 資源標籤支援
- AWS 的跨雲端成本分析支援

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure 成本管理](../cost-management-billing-overview.md)。