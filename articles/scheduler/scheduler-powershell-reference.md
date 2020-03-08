---
title: PowerShell Cmdlet 參考
description: 了解 Azure 排程器的 PowerShell Cmdlet
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898490"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 排程器 PowerShell Cmdlet 參考

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md)會取代即將[淘汰](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 排程器。 若要繼續使用您在排程器中設定的作業，請儘快[遷移至 Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) 。 
>
> 排程器已無法在 Azure 入口網站中使用，但[REST API](/rest/api/scheduler)和 Azure 排程器[PowerShell Cmdlet](scheduler-powershell-reference.md)目前仍可供使用，讓您可以管理您的作業和工作集合。

若要撰寫用於建立及管理排程器作業與作業集合的指令碼，您可以使用 PowerShell Cmdlet。 本文列出 Azure 排程器的主要 PowerShell Cmdlet，並提供其參考文章的連結。 若要為 Azure 訂用帳戶安裝 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 如需 [Azure Resource Manager Cmdlet](/powershell/azure/overview) 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | 描述 |
|--------|-------------|
| [停用-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |停用工作集合。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |啟用工作集合。 |
| [AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |取得排程器作業。 |
| [AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |取得工作集合。 |
| [AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |取得工作歷程記錄。 |
| [新增-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |建立 HTTP 工作。 |
| [新增-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |建立工作集合。 |
| [新增-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | 建立服務匯流排佇列作業。 |
| [新增-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |建立服務匯流排主題作業。 |
| [新增-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |建立儲存體佇列作業。 |
| [移除-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |移除排程器工作。 |
| [移除-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |移除工作集合。 |
| [設定-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改排程器 HTTP 工作。 |
| [設定-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改工作集合。 |
| [設定-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服務匯流排佇列作業。 |
| [設定-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服務匯流排主題作業。 |
| [設定-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改儲存體佇列作業。 |
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