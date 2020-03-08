---
title: Azure 排程器中的限制、配額與閾值
description: 了解 Azure 排程器的限制、配額、預設值，以及節流閾值
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898531"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure 排程器中的限制、配額以及節流閾值

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md)會取代即將[淘汰](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 排程器。 若要繼續使用您在排程器中設定的作業，請儘快[遷移至 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) 。 
>
> 排程器已無法在 Azure 入口網站中使用，但[REST API](/rest/api/scheduler)和 Azure 排程器[PowerShell Cmdlet](scheduler-powershell-reference.md)目前仍可供使用，讓您可以管理您的作業和工作集合。

## <a name="limits-quotas-and-thresholds"></a>限制、配額與閾值

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 標頭

對排程器服務提出的每個要求都會傳回名為**x-ms-request-id**的回應標頭。此標頭包含可唯一識別要求的不透明值。 因此，如果要求一直失敗，並且您確認要求的格式正確，則可以透過提供 **x-ms-request-id** 回應標頭值並包含下列詳細資料，向 Microsoft 回報錯誤： 

* **x-ms-request-id** 值
* 提出要求的大約時間 
* Azure 訂用帳戶、作業集合和作業識別碼 
* 要求所嘗試的作業類型

## <a name="next-steps"></a>後續步驟

* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
* [Azure 排程器的計畫和計費](scheduler-plans-billing.md)
* [Azure 排程器 REST API 參考](/rest/api/scheduler)
* [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)