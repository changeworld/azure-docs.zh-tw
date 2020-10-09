---
title: 使用範本建立 Azure IoT 中樞 (PowerShell) | Microsoft Docs
description: 如何使用 Azure Resource Manager 範本來建立具有 Azure PowerShell 的 IoT 中樞。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75976613"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>使用 Azure Resource Manager 範本建立 IoT 中樞 (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

瞭解如何使用 Azure Resource Manager 範本來建立 IoT 中樞和取用者群組。 Resource Manager 範本是 JSON 檔案，該檔案定義您需要為您的解決方案部署的資源。 如需開發 Resource Manager 範本的詳細資訊，請參閱 [Azure Resource Manager 檔](https://docs.microsoft.com/azure/azure-resource-manager/)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

本快速入門中使用的 Resource Manager 範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)。 以下是範本的複本：

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

此範本會建立具有三個端點的 Azure Iot 中樞 (eventhub、雲端到裝置、訊息) 和取用者群組。 如需更多範本範例，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)。 您可以在  [這裡](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)找到 Iot 中樞範本架構。

有幾種方法可以部署範本。  您會在本教學課程中使用 Azure PowerShell。

若要執行 PowerShell 腳本，請選取 [ **試試看** ] 以開啟 Azure Cloud shell。 若要貼上腳本，請以滑鼠右鍵按一下 shell，然後選取 [貼上]：

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

您可以從 PowerShell 腳本中看到，使用的範本是來自 Azure 快速入門範本。 若要使用您自己的，您必須先將範本檔案上傳至 Cloud shell，然後使用 `-TemplateFile` 參數來指定檔案名。  如需範例，請參閱 [部署範本](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)。

## <a name="next-steps"></a>後續步驟

現在您已使用 Azure Resource Manager 範本部署 IoT 中樞，您可能會想要進一步探索：

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
