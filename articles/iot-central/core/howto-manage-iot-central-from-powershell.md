---
title: 從 Azure PowerShell 管理 IoT Central | Microsoft Docs
description: 本文說明如何從 Azure PowerShell 建立和管理您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: df663861010177ba54c56ee4495458ea1b928359
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985708"
---
# <a name="manage-iot-central-from-azure-powershell"></a>從 Azure PowerShell 管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)來管理應用程式，而不是在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您偏好在本機電腦上執行 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps) \(英文\)。 當您在本機上執行 Azure PowerShell 時，在嘗試本文中所述的 Cmdlet 之前，請先使用 **Connect-AzAccount** Cmdlet 來登入 Azure。

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
  -Sku "ST1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

腳本會先在「美國東部」位置為應用程式建立資源群組。 下表說明搭配 **New-AzIotCentralApp** 命令使用的參數：

|參數         |說明 |
|------------------|------------|
|resourceGroupName |包含應用程式的資源群組。 此資源群組必須已經存在於您的訂用帳戶中。 |
|位置 |根據預設，此 Cmdlet 會使用來自資源群組的位置。 目前，您可以在**美國**、**澳大利亞**、**亞太地區**或**歐洲**地區建立 IoT Central 應用程式。  |
|名稱              |應用程式在 Azure 入口網站中的名稱。 |
|子網域         |應用程式 URL 中的子網域。 在範例中，應用程式 URL 是 https://mysubdomain.azureiotcentral.com 。 |
|SKU               |目前，您可以使用**ST1**或**ST2**。 請參閱 [Azure IoT Central 價格](https://azure.microsoft.com/pricing/details/iot-central/)。 |
|範本          | 要使用的應用程式範本。 如需詳細資訊，請參閱下列表格： |
|DisplayName       |在 UI 中顯示的應用程式名稱。 |

**具有正式推出功能的應用程式範本**

| 範本名稱            | 說明 |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 為您建立空的應用程式，以填入您自己的裝置範本和裝置。


**具有公開預覽功能的應用程式範本**

| 範本名稱            | 說明 |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | 建立空白的隨插即用預覽應用程式，讓您填入自己的裝置範本和裝置。 |
| iotc-condition@1.0.0     | 建立具有存放區內分析-條件監視範本的應用程式。 使用此範本來連接和監視存放區環境。 |
| iotc-consumption@1.0.0   | 建立具有水耗用量監視範本的應用程式。 使用此範本來監視和控制水流程。 |
| iotc-distribution@1.0.0  | 建立具有數位散發範本的應用程式。 使用此範本來 digitalizing 主要資產和動作，以改善倉儲輸出效率。 |
| iotc-inventory@1.0.0     | 使用智慧清查管理範本建立應用程式。 使用此範本可將接收、產品移動、迴圈計數和感應器追蹤自動化。 |
| iotc-logistics@1.0.0     | 使用已連線的物流範本建立應用程式。 使用此範本，透過位置和條件監視，在空中、水和土地之間即時追蹤您的出貨。 |
| iotc-meter@1.0.0         | 使用智慧計量監視範本建立應用程式。 使用此範本來監視能源消耗、網路狀態，以及識別趨勢，以改善客戶支援和智慧計量管理。  |
| iotc-patient@1.0.0       | 建立具有持續患者監視範本的應用程式。 使用此範本來擴充病人護理、重新許可及管理疾病。 |
| iotc-power@1.0.0         | 使用「日光面板監視」範本建立應用程式。 使用此範本來監視日光面板狀態、能源產生趨勢。 |
| iotc-quality@1.0.0       | 建立具有水品質監控範本的應用程式。 使用此範本以數位方式監視水品質。|
| iotc-store@1.0.0         | 建立具有存放區內分析-結帳範本的應用程式。 使用此範本來監視和管理您的存放區內的簽出流程。 |
| iotc-waste@1.0.0         | 建立具有連線垃圾管理範本的應用程式。 使用此範本來監視浪費區間和分派欄位運算子。 |

> [!NOTE]
> 預覽應用程式範本目前僅適用于**歐洲**和**美國**地區。

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
