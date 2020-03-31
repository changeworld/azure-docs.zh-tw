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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898531"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure 排程器中的限制、配額以及節流閾值

> [!IMPORTANT]
> [Azure 邏輯應用](../logic-apps/logic-apps-overview.md)正在替換[正在停用](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 計畫程式。 要繼續處理在計畫程式中設置的作業，請儘快[遷移到 Azure 邏輯應用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 計畫程式在 Azure 門戶中不再可用，但[REST API](/rest/api/scheduler)和[Azure 計畫程式 PowerShell Cmdlet](scheduler-powershell-reference.md)此時仍然可用，以便您可以管理作業和作業集合。

## <a name="limits-quotas-and-thresholds"></a>限制、配額與閾值

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id 標頭

針對計畫程式服務發出的每個請求都會返回一個名為**x-ms-request-id 的**回應標頭。此標頭包含唯一標識請求的不透明值。 因此，如果要求一直失敗，並且您確認要求的格式正確，則可以透過提供 **x-ms-request-id** 回應標頭值並包含下列詳細資料，向 Microsoft 回報錯誤： 

* **x-ms-request-id** 值
* 提出要求的大約時間 
* Azure 訂用帳戶、作業集合和作業識別碼 
* 要求所嘗試的作業類型

## <a name="next-steps"></a>後續步驟

* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
* [Azure 排程器的方案與計費](scheduler-plans-billing.md)
* [Azure 排程器 REST API 參考](/rest/api/scheduler)
* [Azure 排程器 PowerShell Cmdlet 參考](scheduler-powershell-reference.md)