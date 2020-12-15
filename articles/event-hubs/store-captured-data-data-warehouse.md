---
title: 教學課程：將事件資料遷移至 Azure Synapse Analytics - Azure 事件中樞
description: 說明如何使用 Azure 事件方格和 Functions 將事件中樞擷取的資料遷移至 Azure Synapse Analytics。
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854089"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>教學課程：使用事件方格和 Azure Functions 將擷取的事件中樞資料遷移至 Azure Synapse Analytics
Azure 事件中樞[擷取](./event-hubs-capture-overview.md)可讓您在 Azure Blob 儲存體或 Azure Data Lake Store 的事件中樞內擷取串流資料。 本教學課程說明如何使用由[事件方格](../event-grid/overview.md)所觸發的 Azure 函式，將已擷取的事件中樞資料從儲存體遷移至 Azure Synapse Analytics。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>後續步驟 
您可以對資料倉儲使用功能強大的資料視覺效果工具，以產生付諸行動的深入解析。

本文說明如何使用 [Power BI 搭配 Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)