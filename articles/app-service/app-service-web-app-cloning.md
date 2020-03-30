---
title: 使用 PowerShell 複製應用程式
description: 了解如何使用 PowerShell，將您的 App Service 應用程式複製到新的應用程式。 介紹了各種克隆方案，包括流量管理器集成。
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255193"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>使用 PowerShell 複製 Azure App Service App

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

隨著 Microsoft Azure PowerShell 1.1.0 版的發行，`New-AzWebApp` 中新增了一個新選項，可讓您將現有的 App Service 應用程式複製到不同區域或相同區域中新建立的應用程式。 此選項可讓客戶輕鬆且快速地跨不同區域部署許多應用程式。

標準、高級、高級 V2 和隔離應用服務方案支援應用克隆。 新功能會使用與 App Service 備份功能相同的限制，請參閱[在 Azure App Service 中備份應用程式](manage-backup.md)。

## <a name="cloning-an-existing-app"></a>複製現有的應用程式
方案：美國中南部區域的現有應用，您希望將內容克隆為美國中北部區域的新應用。 使用 Azure Resource Manager 版本的 PowerShell Cmdlet 並搭配 `-SourceWebApp` 選項來建立新的應用程式，即可實現此目的。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令來取得來源應用程式的資訊 (在此例中名為 `source-webapp`)：

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

若要建立新的 App Service 方案，您可以使用 `New-AzAppServicePlan` 命令，如下列範例所示。

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

使用`New-AzWebApp`該命令，可以在美國中北部區域創建新應用，並將其綁定到現有的應用服務方案。 此外，您可以使用與來源應用程式相同的資源群組，也可以定義新的資源群組，如下列命令所示：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

若要複製現有的應用程式 (包括所有相關聯的部署位置)，您必須使用 `IncludeSourceWebAppSlots` 參數。  請注意，`IncludeSourceWebAppSlots`該參數僅支援克隆整個應用（包括其所有插槽）。 下列 PowerShell 命令示範如何搭配 `New-AzWebApp` 命令使用該參數：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

若要複製相同區域內的現有應用程式，您必須在相同區域中建立新的資源群組和新的 App Service 方案，然後使用下列 PowerShell 命令來複製應用程式：

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>複製現有應用程式至 App Service 環境
方案：美國中南部區域的現有應用，您希望將內容克隆到新應用到現有應用服務環境 （ASE）。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令來取得來源應用程式的資訊 (在此例中名為 `source-webapp`)：

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

若知道 ASE 的名稱和 ASE 所屬的資源群組名稱，您就能在現有 ASE 中建立新的應用程式，如下列命令所示：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

由於舊版因素，因此需有 `Location` 參數，但在 ASE 中建立應用程式時，則會忽略此參數。 

## <a name="cloning-an-existing-app-slot"></a>複製現有的應用程式位置
方案：您希望將應用的現有部署槽克隆為新應用或新槽。 新的應用程式可以位於與原始應用程式位置相同的區域或不同區域中。

若知道包含來源應用程式的資源群組名稱，您就能使用下列 PowerShell 命令，來取得繫結至 `source-app` 之來源應用程式位置的資訊 (在此例中名為 `source-appslot`)：

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

下列命令示範如何建立從來源應用程式至新應用程式的複製作業：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>在複製應用程式時設定流量管理員
建立多區域的應用程式並將 Azure 流量管理員設定為將流量路由傳送到這些所有應用程式，是確保客戶的應用程式具有高可用性的重要案例。 複製現有應用程式時，您可以選擇將這兩個應用程式連線到新的流量管理員設定檔或現有設定檔。 只支援 Azure Resource Manager 版本的流量管理員。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>在複製應用程式時建立新流量管理員設定檔
方案：您希望將應用克隆到另一個區域，同時配置包含這兩個應用的 Azure 資源管理器流量管理器設定檔。 下列命令示範如何建立從來源應用程式至新應用程式的複製作業，同時設定新的流量管理員設定檔：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>將新複製的應用程式新增至現有的流量管理員設定檔
方案：您已經擁有 Azure 資源管理器流量管理器設定檔，並希望將兩個應用添加為終結點。 若要這麼做，您必須先組合現有的流量管理員設定檔識別碼。 您需要訂用帳戶識別碼、資源群組名稱和現有流量管理員設定檔名稱。

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

擁有流量管理員識別碼之後，下列命令示範如何建立從來源應用程式至新應用程式的複製作業，同時將它們新增至現有的流量管理員設定檔：

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>目前的限制
以下是已知的應用程式複製限制：

* 不會複製自動調整設定
* 不會複製備份排程設定
* 不會複製 VNET 設定
* 不會自動在目的地應用程式上設定 App Insights
* 不會複製簡單驗證設定
* 不會複製 Kudu 延伸模組
* 不會複製 TiP 規則
* 不會複製資料庫內容
* 如果複製到不同縮放單位，輸出 IP 位址將會變更
* 不適用於 Linux 應用程式

### <a name="references"></a>參考
* [App Service 複製](app-service-web-app-cloning.md)
* [在 Azure App Service 中備份應用程式](manage-backup.md)
* [Azure Resource Manager 的 Azure 流量管理員支援預覽](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service 環境簡介](environment/intro.md)
* [搭配使用 Azure PowerShell 與 Azure 資源管理員](../azure-resource-manager/management/manage-resources-powershell.md)

