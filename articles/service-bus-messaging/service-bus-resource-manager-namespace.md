---
title: 使用範本創建 Azure 服務匯流排命名空間
description: 使用 Azure Resource Manager 範本來建立服務匯流排傳訊命名空間
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264470"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本創建服務匯流排命名空間

瞭解如何部署 Azure 資源管理器範本以創建服務匯流排命名空間。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 有關創建範本的詳細資訊，請參閱 Azure[資源管理器文檔](/azure/azure-resource-manager/)。

以下範本也可用於創建服務匯流排命名空間：

* [建立服務匯流排命名空間與佇列](./service-bus-resource-manager-namespace-queue.md)
* [建立服務匯流排命名空間與主題和訂用帳戶](./service-bus-resource-manager-namespace-topic.md)
* [建立服務匯流排命名空間與佇列和授權規則](./service-bus-resource-manager-namespace-auth-rule.md)
* [建立服務匯流排命名空間與主題、訂用帳戶和規則](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間

在此快速入門中，使用[Azure 快速入門範本中的](https://azure.microsoft.com/resources/templates/)[現有資源管理器](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json)範本：

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

若要尋找更多範本範例，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)。

要通過部署範本創建服務匯流排命名空間，請：

1. 選取下列程式碼區塊中的 [試用]****，然後依照指示登入 Azure Cloud Shell。

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    資源組名稱是附加**rg**的服務匯流排命名空間名稱。

2. 選取 [複製]**** 來複製 PowerShell 指令碼。
3. 以滑鼠右鍵按一下殼層主控台，然後選取 [貼上]****。

建立事件中樞需要幾分鐘的時間。

## <a name="verify-the-deployment"></a>驗證部署

要查看已部署的服務匯流排命名空間，可以從 Azure 門戶打開資源組，或使用以下 Azure PowerShell 腳本。 如果 Cloud 外殼仍然打開，則無需複製/運行以下腳本的第一行和第二行。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell 用於在本教程中部署範本。 有關其他範本部署方法，請參閱：

* [通過使用 Azure 門戶](../azure-resource-manager/templates/deploy-portal.md)。
* [通過使用 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。
* [通過使用 REST API](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 如果 Cloud 外殼仍然打開，則無需複製/運行以下腳本的第一行和第二行。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立「服務匯流排」命名空間。 請參閱其他快速入門以了解如何建立佇列、主題/訂用帳戶，以及如何使用它們：

* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [開始使用服務匯流排主題](service-bus-dotnet-how-to-use-topics-subscriptions.md)
