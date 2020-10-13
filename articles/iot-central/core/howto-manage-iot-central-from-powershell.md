---
title: 從 Azure PowerShell 管理 IoT Central | Microsoft Docs
description: 本文說明如何從 Azure PowerShell 建立及管理您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: 77327689911a0fc36df14f69ef4d48e5cb120375
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89067751"
---
# <a name="manage-iot-central-from-azure-powershell"></a>從 Azure PowerShell 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/azure/)來管理您的應用程式，而不需在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您偏好在本機電腦上執行 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps) \(英文\)。 當您在本機上執行 Azure PowerShell 時，在嘗試本文中所述的 Cmdlet 之前，請先使用 **Connect-AzAccount** Cmdlet 來登入 Azure。

> [!TIP]
> 如果您需要在不同的 Azure 訂用帳戶中執行 PowerShell 命令，請參閱 [變更有效訂](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription)用帳戶。

## <a name="install-the-iot-central-module"></a>安裝 IoT Central 模組

執行下列命令來檢查已安裝在您 PowerShell 環境中的 [IoT Central 模組](https://docs.microsoft.com/powershell/module/az.iotcentral/) \(英文\)：

```powershell
Get-InstalledModule -name Az.I*
```

如果已安裝模組的清單沒有包含 **Az.IotCentral**，請執行下列命令：

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>建立應用程式

使用 [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) \(英文\) Cmdlet 來在您的 Azure 訂用帳戶中建立 IoT Central 應用程式。 例如：

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

該指令碼會先為應用程式在美國東部區域中建立資源群組。 下表說明搭配 **New-AzIotCentralApp** 命令使用的參數：

|參數         |描述 |
|------------------|------------|
|resourceGroupName |包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
|Location |根據預設，此 Cmdlet 會使用來自資源群組的位置。 目前，您可以在**澳大利亞**、**亞太地區**、**歐洲**、**美國** **、英國和****日本**地區建立 IoT Central 應用程式。 |
|名稱              |應用程式在 Azure 入口網站中的名稱。 |
|子網域         |應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 `https://mysubdomain.azureiotcentral.com`。 |
|SKU               |目前，您可以使用 **ST1** 或 **ST2**。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
|範本          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格。 |
|DisplayName       |在 UI 中顯示的應用程式名稱。 |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>檢視 IoT Central 應用程式

使用 [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) \(英文\) Cmdlet 來列出您的 IoT Central 應用程式並檢視中繼資料。

## <a name="modify-an-application"></a>修改應用程式

使用 [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) \(英文\) Cmdlet 來更新 IoT Central 應用程式的中繼資料。 例如，若要變更應用程式的顯示名稱：

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>移除應用程式

使用 [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) \(英文\) Cmdlet 來刪除 IoT Central 應用程式。 例如：

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure PowerShell 管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
