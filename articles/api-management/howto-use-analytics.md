---
title: 在 Azure API 管理中使用 API 分析 |Microsoft Docs
description: 使用 Azure API 管理中的分析，協助您瞭解及分類 Api 和 API 效能的使用方式。
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841402"
---
# <a name="get-api-analytics-in-azure-api-management"></a>取得 Azure API 管理中的 API 分析

Azure API 管理可為您的 Api 提供內建的分析。 分析跨多個維度的 API 管理實例中 Api 的使用方式和效能，包括：

* Time
* [地理位置]
* API
* API 作業
* 產品
* 訂用帳戶
* 使用者
* Requests

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="入口網站中的時間軸分析":::

流量分析進行 Api 的高階監視和疑難排解。 如需其他監視功能，包括近乎即時的計量和資源記錄以進行診斷和審核，請參閱 [教學課程：監視已發佈的 api](api-management-howto-use-azure-monitor.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>分析-入口網站

使用 Azure 入口網站來查看您的 API 管理實例的分析資料概覽。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 API 管理執行個體。 
1. 在左側功能表中，選取 [ **監視**] 底下的 [ **分析**]。

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="在入口網站中選取 API 管理實例的分析":::  
1. 選取資料的時間範圍，或輸入自訂的時間範圍。
1. 選取分析資料的報表類別，例如 **時間軸**、 **地理位置** 等等。
1. （選擇性）依一或多個其他類別篩選報表。

## <a name="analytics---rest-api"></a>分析-REST API

使用 API 管理 REST API 中的 [報表](/rest/api/apimanagement/2019-12-01/reports) 作業，來取得和篩選您的 api 管理實例的分析資料。

可用的作業會依 API、地理位置、API 作業、產品、要求、訂用帳戶、時間或使用者來傳回報告記錄。

## <a name="next-steps"></a>後續步驟

* 如需 API 管理中 Azure 監視器功能的簡介，請參閱 [教學課程：監視已發佈的 api](api-management-howto-use-azure-monitor.md)
* 如需詳細的 HTTP 記錄和監視，請參閱 [使用 AZURE Api 管理、事件中樞和 Moesif 監視您的 api](api-management-log-to-eventhub-sample.md)。
* 瞭解如何整合 [AZURE API 管理與 Azure 應用程式 Insights](api-management-howto-app-insights.md)。