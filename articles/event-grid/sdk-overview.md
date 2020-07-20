---
title: Azure Event Grid SDK
description: 說明 Azure Event Grid 的 SDK。 這些 SDK 提供管理、發佈和使用。
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 2b21d8bf2c393b096e9046765e6cfadc76baffbf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119101"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>用來管理及發佈的 Event Grid SDK

Event Grid 提供可讓您以程式設計方式管理資源和張貼事件的 SDK。

## <a name="management-sdks"></a>管理 SDK

管理 SDK 可讓您建立、更新和刪除 Event Grid 主題和訂用帳戶。 目前有下列可用的 SDK：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [節點](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>資料平面 SDK

資料平面 SDK 可讓您將事件張貼到主題，方法是利用驗證、形成事件，並以非同步方式張貼到指定的端點。 此外也可讓您使用第一方事件。 目前有下列可用的 SDK：

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [節點](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>後續步驟

* 如需應用程式範例，請參閱 [Event Grid 程式碼範例](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid)。
* 若要初步了解事件格線，請參閱[什麼是事件格線？](overview.md)
* 對於 Azure CLI 中的 Event Grid 命令，請參閱 [Azure CLI](/cli/azure/eventgrid)。
* 對於 PowerShell 中的 Event Grid 命令，請參閱 [PowerShell](/powershell/module/az.eventgrid)。
