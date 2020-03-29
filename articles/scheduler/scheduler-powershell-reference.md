---
title: 電源殼 Cmdlet 參考
description: 了解 Azure 排程器的 PowerShell Cmdlet
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898490"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 排程器 PowerShell Cmdlet 參考

> [!IMPORTANT]
> [Azure 邏輯應用](../logic-apps/logic-apps-overview.md)正在替換[正在停用](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 計畫程式。 要繼續處理在計畫程式中設置的作業，請儘快[遷移到 Azure 邏輯應用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 
>
> 計畫程式在 Azure 門戶中不再可用，但[REST API](/rest/api/scheduler)和[Azure 計畫程式 PowerShell Cmdlet](scheduler-powershell-reference.md)此時仍然可用，以便您可以管理作業和作業集合。

若要撰寫用於建立及管理排程器作業與作業集合的指令碼，您可以使用 PowerShell Cmdlet。 此文章列出主要 Azure 排程器的 PowerShell Cmdlet，並提供其參考文章的連結。 若要為 Azure 訂用帳戶安裝 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 如需 [Azure Resource Manager Cmdlet](/powershell/azure/overview) 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | 描述 |
|--------|-------------|
| [禁用-Az計畫員作業收集](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |停用工作集合。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |啟用工作集合。 |
| [獲取-時程表員作業](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |取得排程器作業。 |
| [獲取-Az-時程表作業收集](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |取得工作集合。 |
| [獲取-Az時程表作業歷史](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |取得工作歷程記錄。 |
| [新-亞比克斯HttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |建立 HTTP 工作。 |
| [新-亞比克斯計畫作業收集](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |建立工作集合。 |
| [新-亞表服務匯流排佇列作業](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | 建立服務匯流排佇列作業。 |
| [新-亞表服務匯流排主題作業](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |建立服務匯流排主題作業。 |
| [新-亞表存儲佇列作業](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |建立儲存體佇列作業。 |
| [刪除-亞比克斯計畫作業](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |移除排程器工作。 |
| [刪除-亞比克斯計畫作業集](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |移除工作集合。 |
| [設置-Az-計畫HttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改排程器 HTTP 工作。 |
| [設置-時程表作業收集](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改工作集合。 |
| [設置-Az計畫服務匯流排佇列作業](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服務匯流排佇列作業。 |
| [設置-Az計畫服務匯流排主題作業](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服務匯流排主題作業。 |
| [設置-Az計畫存儲佇列作業](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改儲存體佇列作業。 |
||| 

如需詳細資訊，您可以執行下列任何 Cmdlet： 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>後續步驟

* [Azure 排程器概念、術語及實體階層](scheduler-concepts-terms.md)
* [Azure 排程器限制、預設值和錯誤碼](scheduler-limits-defaults-errors.md)
* [Azure 排程器 REST API 參考](/rest/api/scheduler)