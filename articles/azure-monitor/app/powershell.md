---
title: 使用 PowerShell 將 Azure Application Insights 自動化 | Microsoft Docs
description: 使用 Azure 資源管理器範本在 PowerShell 中自動創建和管理資源、警報和可用性測試。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275876"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>使用 PowerShell 管理應用程式見解資源

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文說明如何使用 Azure 資源管理，自動將 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 資源的建立和更新自動化。 例如，您可能建置程序中這麼做。 除了基本的 Application Insights 資源外，您可以建立[可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)、設定[警示](../../azure-monitor/app/alerts.md)、設定[價格配置](pricing.md)和建立其他 Azure 資源。

建立這些資源的關鍵是 [Azure 資源管理員](../../azure-resource-manager/management/manage-resources-powershell.md)適用的 JSON 範本。 基本過程是：下載JSON現有資源的定義;參數化某些值，如名稱;然後，每當您想要創建新資源時，請運行該範本。 您可以一起封裝幾項資源一次全部建立，例如一個包含可用性測試、警示和連續匯出儲存體的應用程式監視器。 部分參數化有一些微妙之處，我們會在這裡說明。

## <a name="one-time-setup"></a>單次設定
若您未曾將 PowerShell 與 Azure 訂用帳戶搭配使用：

在您要執行指令碼的電腦上安裝 Azure Powershell 模組：

1. 安裝 [Microsoft Web Platform Installer (v5 或更高版本)](https://www.microsoft.com/web/downloads/platform.aspx)。
2. 請使用它來安裝 Microsoft Azure Powershell。

除了使用資源管理器範本外，還有一組豐富的[應用程式見解 PowerShell Cmdlet，](https://docs.microsoft.com/powershell/module/az.applicationinsights)這使得在程式設計中輕鬆配置應用程式見解資源。 Cmdlet 啟用的功能包括：

* 創建和刪除應用程式見解資源
* 獲取應用程式見解資源及其屬性的清單
* 創建和管理連續匯出
* 創建和管理應用程式金鑰
* 設置每日上限
* 設置定價計畫

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>使用 PowerShell Cmdlet 創建應用程式見解資源

下面瞭解如何使用[New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) Cmdlet 在 Azure 東部美國資料中心創建新的應用程式見解資源：

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>使用資源管理器範本創建應用程式見解資源

以下是如何使用資源管理器範本創建新的應用程式見解資源。

### <a name="create-the-azure-resource-manager-template"></a>創建 Azure 資源管理器範本

建立新的 .json 檔案 - 在此範例中稱為 `template1.json` 。 將此內容複製到其中：

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>使用資源管理器範本創建新的應用程式見解資源

1. 在 PowerShell 中，使用`$Connect-AzAccount`
2. 將上下文設置為訂閱，`Set-AzContext "<subscription ID>"`
2. 運行新部署以創建新的應用程式見解資源：
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` 是您要在其中建立新資源的群組。
   * `-TemplateFile` 必須出現在自訂參數之前。
   * `-appName` 是要建立的資源的名稱。

您可以新增其他參數 - 可在範本的參數區段中找到其描述。

## <a name="get-the-instrumentation-key"></a>獲取檢測金鑰

建立應用程式資源之後，您會想要檢測金鑰： 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

要查看應用程式見解資源的許多其他屬性的清單，請使用：

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

可通過 Cmdlet 提供其他屬性：
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

有關這些 Cmdlet 的參數，請參閱[詳細文檔](https://docs.microsoft.com/powershell/module/az.applicationinsights)。  

## <a name="set-the-data-retention"></a>設置資料保留 

要獲取應用程式見解資源的當前資料保留，可以使用 OSS 工具[ARMClient](https://github.com/projectkudu/ARMClient)。  （從[大衛·埃博](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[丹尼爾·鮑比斯](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章中瞭解更多關於ARMClient。 下面是使用 的示例，用於`ARMClient`獲取當前保留：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

要設置保留，該命令是類似的 PUT：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

要使用上述範本將資料保留設置為 365 天，請使用以下運行：

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

以下腳本還可用於更改保留。 複製此腳本以保存為`Set-ApplicationInsightsRetention.ps1`。

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

然後，此腳本可用作：

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>設置每日上限

要獲取每日上限屬性，請使用[Set-Az應用程式見解定價計畫](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)Cmdlet： 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

要設置每日上限屬性，請使用相同的 Cmdlet。 例如，要將上限設置為 300 GB/天，

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

您還可以使用[ARMClient](https://github.com/projectkudu/ARMClient)獲取和設置每日上限參數。  要獲取當前值，請使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>設置每日上限重置時間

要設置每日上限重置時間，可以使用[ARMClient](https://github.com/projectkudu/ARMClient)。 下面是使用 的示例，用於`ARMClient`將重置時間設置為新小時（在此示例中為 12：00 UTC）：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>設置定價計畫 

要獲取當前定價計畫，請使用[Set-Az應用程式見解定價計畫](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)Cmdlet：

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

要設置定價計畫，請使用與`-PricingPlan`指定的相同的 Cmdlet：  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

您還可以使用上面的資源管理器範本在現有應用程式見解資源上設置定價計畫，從計費資源中省略"Microsoft.insights/元件"資源和`dependsOn`節點。 例如，要將其設置為"每 GB 計畫"（以前稱為基本計畫），請運行：

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

定義為`priceCode`：

|priceCode|計劃|
|---|---|
|1|每 GB（以前稱為基本計畫）|
|2|每個節點（以前名稱為企業計畫）|

最後，您可以使用[ARMClient](https://github.com/projectkudu/ARMClient)獲取和設置定價計畫和每日上限參數。  要獲取當前值，請使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

您可以使用以下參數設置所有這些參數：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

這將將每日上限設置為 200 GB/天，將每日上限重置時間配置為 12：00 UTC，在達到上限和達到警告級別時發送電子郵件，並將警告閾值設置為上限的 90%。  

## <a name="add-a-metric-alert"></a>新增度量警示

要自動創建指標警報，請參閱[指標警報範本一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>新增可用性測試

要自動執行可用性測試，請參閱[指標警報範本一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)。

## <a name="add-more-resources"></a>新增其他資源

若要自動建立任何類型的任何其他資源，手動建立範例，然後從 [Azure Resource Manager](https://resources.azure.com/) 複製並參數化其程式碼。 

1. 開啟 [Azure 資源管理員](https://resources.azure.com/)。 向下導覽 `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` 直到應用程式資源。 
   
    ![Azure 資源總管中的瀏覽](./media/powershell/01.png)
   
    *元件* 是用來顯示應用程式的基本 Application Insights 資源。 相關聯的警示規則和可用性 Web 測試有個別的資源。
2. 將元件的 JSON 複製到 `template1.json`的適當位置。
3. 刪除這些屬性：
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. 打開`webtests`和`alertrules`節，並將單個專案的 JSON 複製到範本中。 （不要從`webtests`或`alertrules`節點複製：進入它們下的項。
   
    每個 Web 測試都有一個關聯的警示規則，您必須同時複製這兩者。
   
    您也可以包含計量的警示。 [計量名稱](powershell-alerts.md#metric-names)。
5. 在每個資源中插入下面這行：
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>參數化範本
現在您必須以參數取代特定的名稱。 若要[參數化範本](../../azure-resource-manager/templates/template-syntax.md)，您要使用[一組協助程式函式](../../azure-resource-manager/templates/template-functions.md)撰寫表示式。 

您無法將參數化字串的一部分，因此請使用 `concat()` 建置字串。

以下是您會想要進行的替換的範例。 每個替換各出現數次。 您的範本中可能需要其他替換。 這些範例使用我們在範本頂端定義的參數和變數。

| 尋找 | 取代為 |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (小寫) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>設定資源間的相依性
Azure 應以嚴格的順序設定資源。 為確保一項設定完成後再開始下一項設定，請加入相依性命令行：

* 在可用性測試資源中︰
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 可用性測試的警示資源中︰
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>後續步驟
其他自動化文件：

* [建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - 快速方法 (不使用範本)
* [設置警報](powershell-alerts.md)
* [建立 Web 測試](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [將 Azure 診斷發送到應用程式見解](powershell-azure-diagnostics.md)
* [從 GitHub 部署至 Azure (英文)](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [建立版本附註](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)