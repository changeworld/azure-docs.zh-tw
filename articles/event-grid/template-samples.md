---
title: Azure Resource Manager 範本範例 - Event Grid | Microsoft Docs
description: 本文提供 GitHub 上適用於 Azure 事件方格的 Azure Resource Manager 範本範例清單。
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119050"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 的 Azure Resource Manager 範本

如需要在範本中使用的 JSON 語法和屬性，請參閱 [Microsoft.EventGrid 資源類型](/azure/templates/microsoft.eventgrid/allversions)。 下表包含 Event Grid 的 Azure Resource Manager 範本連結。

## <a name="event-grid-subscriptions"></a>事件方格訂用帳戶
- [搭配 WebHook 端點的自訂主題和訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) - 部署事件方格自訂主題。 為使用 WebHook 端點的自訂主題建立訂用帳戶。 
- [搭配 EventHub 端點的自訂主題訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler) - 建立自訂主題的事件方格訂用帳戶。 訂用帳戶會使用端點的事件中樞。 
- [Azure 訂用帳戶或資源群組訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) - 訂閱資源群組或 Azure 訂用帳戶的事件。 在部署期間指定為目標的資源群組為事件來源。 訂用帳戶會使用端點的 WebHook。 
- [Blob 儲存體帳戶及訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) - 部署 Azure Blob 儲存體帳戶及訂閱該儲存體帳戶的事件。 

## <a name="next-steps"></a>後續步驟
請參閱下列範例：

- [PowerShell 範例](powershell-samples.md)
- [CLI 範例](cli-samples.md)
