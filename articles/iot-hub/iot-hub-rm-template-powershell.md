---
title: 使用範本建立 Azure IoT 中樞 (PowerShell) | Microsoft Docs
description: 如何使用 Azure 資源管理器範本使用 Azure PowerShell 創建 IoT 中心。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976613"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>使用 Azure Resource Manager 範本建立 IoT 中樞 (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

瞭解如何使用 Azure 資源管理器範本創建 IoT 中心和消費者組。 Resource Manager 範本是 JSON 檔案，該檔案定義您需要為您的解決方案部署的資源。 有關開發資源管理器範本的詳細資訊，請參閱[Azure 資源管理器文檔](https://docs.microsoft.com/azure/azure-resource-manager/)。

如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

此快速入門中使用的資源管理器範本來自[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)。 以下是範本的複本：

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

該範本創建一個 Azure Iot 中心，具有三個終結點（事件集線、雲到設備和消息傳遞）和一個消費者組。 有關更多範本示例，請參閱[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)。 Iot 中心範本架構可以[在此處](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)找到。

部署範本的方法有多種。  在本教程中使用 Azure PowerShell。

要運行 PowerShell 腳本，請選擇 **"嘗試"** 以打開 Azure 雲外殼。 要粘貼腳本，請按右鍵 shell，然後選擇"粘貼"：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

從 PowerShell 腳本中可以看到，使用的範本來自 Azure 快速入門範本。 要使用您自己的範本檔，您需要首先將範本檔上載到 Cloud 外殼，然後使用`-TemplateFile`開關指定檔案名。  例如，請參閱[部署範本](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)。

## <a name="next-steps"></a>後續步驟

現在，您已經使用 Azure 資源管理器範本部署了 IoT 中心，您可能需要進一步探索：

* 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。
* 如需 Azure Resource Manager 功能的詳細資訊，請參閱 [Azure Resource Manager 概觀][lnk-azure-rm-overview]。
* 若需要在範本中使用的 JSON 語法和屬性，請參閱 [Microsoft.Devices 資源類型](/azure/templates/microsoft.devices/iothub-allversions)。

若要深入了解如何開發 IoT 中樞，請參閱以下文章︰

* [C SDK 簡介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
