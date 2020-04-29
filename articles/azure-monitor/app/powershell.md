---
title: 使用 PowerShell 將 Azure Application Insights 自動化 | Microsoft Docs
description: 使用 Azure Resource Manager 範本，自動建立和管理 PowerShell 中的資源、警示和可用性測試。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275876"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>使用 PowerShell 管理 Application Insights 資源

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文說明如何使用 Azure 資源管理，自動將 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 資源的建立和更新自動化。 例如，您可能建置程序中這麼做。 除了基本的 Application Insights 資源外，您可以建立[可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)、設定[警示](../../azure-monitor/app/alerts.md)、設定[價格配置](pricing.md)和建立其他 Azure 資源。

建立這些資源的關鍵是 [Azure 資源管理員](../../azure-resource-manager/management/manage-resources-powershell.md)適用的 JSON 範本。 基本程式是：下載現有資源的 JSON 定義;參數化特定的值，例如名稱;然後，每當您想要建立新的資源時，再執行範本。 您可以一起封裝幾項資源一次全部建立，例如一個包含可用性測試、警示和連續匯出儲存體的應用程式監視器。 部分參數化有一些微妙之處，我們會在這裡說明。

## <a name="one-time-setup"></a>單次設定
若您未曾將 PowerShell 與 Azure 訂用帳戶搭配使用：

在您要執行指令碼的電腦上安裝 Azure Powershell 模組：

1. 安裝 [Microsoft Web Platform Installer (v5 或更高版本)](https://www.microsoft.com/web/downloads/platform.aspx)。
2. 請使用它來安裝 Microsoft Azure Powershell。

除了使用 Resource Manager 範本之外，還有一組豐富的[Application Insights PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)，可讓您輕鬆地以程式設計方式設定 Application Insights 資源。 Cmdlet 所啟用的功能包括：

* 建立和刪除 Application Insights 資源
* 取得 Application Insights 資源及其屬性的清單
* 建立和管理連續匯出
* 建立和管理應用程式金鑰
* 設定每日上限
* 設定定價方案

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>使用 PowerShell Cmdlet 建立 Application Insights 資源

以下說明如何使用[AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) Cmdlet，在 Azure 美國東部資料中心建立新的 Application Insights 資源：

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>使用 Resource Manager 範本建立 Application Insights 資源

以下說明如何使用 Resource Manager 範本來建立新的 Application Insights 資源。

### <a name="create-the-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>使用 Resource Manager 範本來建立新的 Application Insights 資源

1. 在 PowerShell 中，使用登入 Azure`$Connect-AzAccount`
2. 使用將您的內容設定為訂用帳戶`Set-AzContext "<subscription ID>"`
2. 執行新的部署，以建立新的 Application Insights 資源：
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` 是您要在其中建立新資源的群組。
   * `-TemplateFile` 必須出現在自訂參數之前。
   * `-appName` 是要建立的資源的名稱。

您可以新增其他參數 - 可在範本的參數區段中找到其描述。

## <a name="get-the-instrumentation-key"></a>取得檢測金鑰

建立應用程式資源之後，您會想要檢測金鑰： 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

若要查看 Application Insights 資源的許多其他屬性清單，請使用：

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

其他屬性可透過 Cmdlet 取得：
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

如需這些 Cmdlet 的參數，請參閱[詳細檔](https://docs.microsoft.com/powershell/module/az.applicationinsights)。  

## <a name="set-the-data-retention"></a>設定資料保留期 

若要取得 Application Insights 資源的目前資料保留，您可以使用 OSS 工具[ARMClient](https://github.com/projectkudu/ARMClient)。  （深入瞭解從[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章 ARMClient）。 以下是使用`ARMClient`的範例，以取得目前的保留期：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

若要設定保留期，此命令是類似的 PUT：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

若要使用上述範本將資料保留設定為365天，請執行：

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

下列腳本也可以用來變更保留期。 複製此腳本以另存`Set-ApplicationInsightsRetention.ps1`新檔。

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

接著，您可以使用此腳本來執行下列動作：

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>設定每日上限

若要取得每日上限屬性，請使用[AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) Cmdlet： 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

若要設定每日上限屬性，請使用相同的 Cmdlet。 例如，若要將上限設定為每日 300 GB，

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

您也可以使用[ARMClient](https://github.com/projectkudu/ARMClient)來取得及設定每日上限參數。  若要取得目前的值，請使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>設定每日上限重設時間

若要設定每日上限重設時間，您可以使用[ARMClient](https://github.com/projectkudu/ARMClient)。 以下是使用`ARMClient`的範例，將重設時間設定為新的小時（在此範例中為 12:00 UTC）：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>設定定價方案 

若要取得目前的定價方案，請使用[AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) Cmdlet：

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

若要設定定價方案，請搭配使用相同的`-PricingPlan` Cmdlet 與指定的：  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

您也可以使用上述的 Resource Manager 範本，在現有的 Application Insights 資源上設定定價方案，省略「microsoft Insights/元件」資源和計費資源`dependsOn`中的節點。 例如，若要將它設定為每 GB 方案（先前稱為「基本」方案），請執行：

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode`定義為：

|priceCode|計劃|
|---|---|
|1|每 GB （先前名為基本方案）|
|2|針對每個節點（先前命名為企業方案）|

最後，您可以使用[ARMClient](https://github.com/projectkudu/ARMClient)來取得及設定定價方案和每日上限參數。  若要取得目前的值，請使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

而且您可以使用來設定所有參數：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

這會將每日上限設為 200 GB/天、將每日上限重設時間設定為 12:00 UTC、在達到上限時傳送電子郵件，以及符合警告層級，並將警告閾值設定為上限的90%。  

## <a name="add-a-metric-alert"></a>新增度量警示

若要自動建立計量警示，請參閱計量[警示範本一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>新增可用性測試

若要將可用性測試自動化，請參閱計量[警示範本一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)。

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
4. 開啟`webtests`和`alertrules`區段，然後將個別專案的 JSON 複製到您的範本中。 （請勿從`webtests`或`alertrules`節點複製：移至其底下的專案。）
   
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
* [設定警示](powershell-alerts.md)
* [建立 Web 測試](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [將 Azure 診斷傳送至 Application Insights](powershell-azure-diagnostics.md)
* [從 GitHub 部署至 Azure (英文)](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [建立版本附註](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)