---
title: 用於高密度託管的按應用縮放
description: 獨立于應用服務方案擴展應用，並優化計畫中的橫向擴展實例。
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672341"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>使用每個應用縮放在 Azure 應用服務上進行高密度託管

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用應用服務時，可以通過縮放應用[運行的應用服務方案](overview-hosting-plans.md)來擴展應用。 當有多個應用程式執行於相同 App Service 方案中時，每個向外延展的執行個體都會執行方案中的所有應用程式。

可以在應用服務方案級別啟用 *"按應用縮放"，* 以允許獨立于承載應用的應用服務方案縮放應用。 如此一來，App Service 方案可以調整為 10 個執行個體，但是應用程式可以設定為僅使用五個。

> [!NOTE]
> 個別應用程式調整僅適用於**標準**、**進階**、**進階 V2** 及**獨立** 定價層。
>

應用分配給可用的應用服務方案，使用盡最大努力跨實例分發的方法。 雖然不能保證均勻分發，但平臺將確保同一應用的兩個實例不會託管在同一應用服務方案實例上。

平臺不依賴于指標來決定工作人員分配。 僅當從應用服務方案添加或刪除實例時，才會重新平衡應用程式。

## <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 進行個別應用程式調整

透過將 ```-PerSiteScaling $true``` 參數傳遞給 ```New-AzAppServicePlan``` Cmdlet，建立一個有「個別應用程式調整」的方案。

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

透過將 `-PerSiteScaling $true` 參數傳遞給 ```Set-AzAppServicePlan``` Cmdlet，對現有 App Service 方案啟用「個別應用程式調整」。

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

在應用程式層級，設定應用程式可以在 App Service 方案中使用的執行個體數目。

在以下範例中，應用程式限制為 2 個執行個體，不論其基礎 App Service 方案的規模相應放大到多少個執行個體。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` 不同於 `$newapp.MaxNumberOfWorkers`。 個別應用程式調整會使用 `$newapp.SiteConfig.NumberOfWorkers` 來決定應用程式的調整特性。

## <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure Resource Manager 進行個別應用程式調整

下列「Azure Resource Manager 範本」會建立：

- 規模相應放大到 10 個執行個體的 App Service 方案
- 已設定為將上限調整成 5 個執行個體的應用程式。

App Service 方案會將 **PerSiteScaling** 屬性設為 true (`"perSiteScaling": true`)。 該應用程式正在將要使用的**工作人員數**設置為 5 `"properties": { "numberOfWorkers": "5" }`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度裝載的建議組態

個別應用程式調整是全域 Azure 區域和 [App Service 環境](environment/app-service-app-service-environment-intro.md)中啟用的功能。 但是，建議的策略是使用應用服務環境來利用其高級功能和更大的應用服務方案容量。  

請遵循下列步驟來設定應用程式的高密度裝載︰

1. 將應用服務方案指定為高密度計畫，並將其擴展到所需的容量。
1. 在 App Service 方案上將 `PerSiteScaling` 旗標設定為 true。
1. 新應用程式會建立並指派給該 App Service 方案，其中 **numberOfWorkers** 屬性會設定為 **1**。
   - 使用此配置可以產生最高的密度。
1. 背景工作角色數目可依每個應用程式單獨設定，以視需要授與額外資源。 例如：
   - 高用量應用程式可以將 **numberOfWorkers** 設定為 **3**，讓該應用程式具有更多的處理容量。
   - 低用量應用程式會將 **numberOfWorkers** 設定為 **1**。

## <a name="next-steps"></a>後續步驟

- [Azure App Service 方案深入概觀](overview-hosting-plans.md)
- [App Service 環境簡介](environment/app-service-app-service-environment-intro.md)
