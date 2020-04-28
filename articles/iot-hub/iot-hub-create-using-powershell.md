---
title: 使用 PowerShell Cmdlet 建立 Azure IoT 中樞 | Microsoft Docs
description: 瞭解如何使用 PowerShell Cmdlet 來建立資源群組，然後在資源群組中建立 IoT 中樞。 另請瞭解如何移除中樞。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890594"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>使用 AzIotHub Cmdlet 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure PowerShell Cmdlet 建立及管理 Azure IoT 中樞。 此教學課程會示範如何使用 PowerShell 建立 IoT 中樞。

若要完成此操作說明，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶

如果您使用 Cloud Shell，表示您已登入訂用帳戶。 但如果您是在本機執行 PowerShell，請輸入下列命令來登入您的 Azure 訂用帳戶：

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

您需要一個資源群組來部署 IoT 中樞。 您可以使用現有的資源群組，或建立一個新的群組。

若要為您的 IoT 中樞建立資源群組，請使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup)命令。 此範例會在**美國東部**區域中建立一個稱為 **MyIoTRG1** 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

若要在您于上一個步驟中建立的資源群組中建立 IoT 中樞，請使用[AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub)命令。 此範例會在**美國東部**區域中建立一個稱為 **MyTestIoTHub** 的 **S1** 中樞：

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 中樞名稱必須是全域唯一的。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

您可以使用[AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)命令來列出您訂用帳戶中的所有 IoT 中樞：

```azurepowershell-interactive
Get-AzIotHub
```

此範例會顯示您在上一個步驟中建立的 S1 標準 IoT 中樞。

您可以使用[AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)命令來刪除 IoT 中樞：

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

或者，您可以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)命令來移除資源群組及其包含的所有資源：

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>後續步驟

現在您已經使用 PowerShell Cmdlet 部署 IoT 中樞，若要進一步探索，請參閱下列文章︰

* [搭配 IoT 中樞使用的 PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.iothub/)。

* [IoT 中樞資源提供者 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)。

若要深入了解如何開發 IoT 中樞，請參閱以下文章︰

* [C SDK 簡介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
