---
title: 自動將功能應用資源部署到 Azure
description: 了解如何建置能部署函數應用程式的 Azure Resource Manager 範本。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 7155a3fa9481ef5f2da62d85d4a932ad5e8e8ab1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382532"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions 中函數應用程式的自動化資源部署

您可以使用 Azure Resource Manager 範本來部署函數應用程式。 本文概述執行這項作業所需的資源和參數。 您可能需要部署額外的資源，視函數應用程式中的[觸發程序和繫結](functions-triggers-bindings.md)而定。

有關建立樣本的詳細資訊,請參閱[創作 Azure 資源管理員樣本](../azure-resource-manager/templates/template-syntax.md)。

如需範例範本，請參閱：
- [採用取用方案的函數應用程式]
- [採用 Azure App Service 方案的函數應用程式]

## <a name="required-resources"></a>所需資源

Azure 函數部署通常包含以下資源:

| 資源                                                                           | 需求 | 語法與屬性參照                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函數應用程式                                                                     | 必要    | [微軟.網站/網站](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 儲存](../storage/index.yml)帳戶                                   | 必要    | [微軟儲存記憶體](/azure/templates/microsoft.storage/storageaccounts) |   |
| [應用程式見解](../azure-monitor/app/app-insights-overview.md)元件 | 選用    | [微軟.Insights/元件](/azure/templates/microsoft.insights/components)         |   |
| [託管計劃](./functions-scale.md)                                             | 選擇<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>僅當選擇在[先進計畫](./functions-premium-plan.md)或[應用服務計畫](../app-service/overview-hosting-plans.md)上運行函數應用時,才需要託管計劃。

> [!TIP]
> 雖然不是必需的,但強烈建議您為應用配置應用程式見解。

<a name="storage"></a>
### <a name="storage-account"></a>儲存體帳戶

函數應用程式需要 Azure 儲存體帳戶。 您需要支援 Blob、資料表、佇列和檔案的一般用途帳戶。 如需詳細資訊，請參閱 [Azure Functions 儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

此外，您必須在網站設定中將 `AzureWebJobsStorage` 屬性指定為應用程式設定。 若函式應用程式不使用 Application Insights 以進行監視，也應該將 `AzureWebJobsDashboard` 指定為應用程式設定。

Azure Functions 執行階段會使用 `AzureWebJobsStorage` 連接字串來建立內部佇列。  在未啟用 Application Insights 的情況下，執行階段會使用 `AzureWebJobsDashboard` 連接字串來記錄至 Azure 資料表儲存體，並啟動入口網站中的 [監視]**** 索引標籤。

這些屬性會在 `siteConfig` 物件的 `appSettings`集合中指定：

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

建議使用應用程式見解來監視功能應用。 應用程式見解資源使用**Microsoft.Insights/元件**和類型**Web**類型定義:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

此外,需要使用`APPINSIGHTS_INSTRUMENTATIONKEY`應用程式設置向函數應用提供檢測密鑰。 此屬性在`appSettings``siteConfig`物件的集合中指定:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>主控方案

託管計劃的定義各不相同,可以是以下定義之一:
* [消耗計劃](#consumption)(預設)
* [進階方案](#premium)
* [套用服務計劃](#app-service-plan)

### <a name="function-app"></a>函式應用程式

函式應用程式是透過使用**Microsoft.Web/網站**和類型**函式應用**類型的資源定義的:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> 如果要顯式定義託管計劃,則 dependsOn 陣列中將需要一個附加項:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函數應用必須包含以下應用程式設定:

| 設定名稱                 | 描述                                                                               | 範例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 函式執行時用於內部佇列的儲存帳戶的連接字串 | 請參考[儲存帳戶](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure 函數執行時的版本                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 用此應用程式中函數的語言堆疊                                   | `dotnet`、`node`、`java`、`python` 或 `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | 只使用語言`node`堆疊時,指定要使用的版本              | `10.14.1`                             |

這些屬性在`appSettings``siteConfig`屬性中的集合中指定:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>在消耗計畫上部署

消耗計劃會在代碼運行時自動分配計算能力,根據需要擴展以處理負載,然後在代碼未運行時擴展。 您不必為空閒 VM 付費,也不必提前預留容量。 若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md#consumption-plan)。

如需範例 Azure Resource Manager 範本，請參閱[採用取用方案的函數應用程式]。

### <a name="create-a-consumption-plan"></a>建立取用方案

不需要定義消耗計劃。 當您創建函數應用資源本身時,將自動創建或根據每個區域選擇一個。

消耗計劃是一種特殊類型的「伺服器場」資源。 對於 Windows,可以`Dynamic``computeMode`使用`sku`與 屬性的值來指定它:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> 無法為 Linux 顯式定義消耗計畫。 它將自動創建。

如果顯式定義消耗計劃,則需要在應用上設置`serverFarmId`屬性,以便它指向計劃的資源 ID。 應確保函數應用也具有`dependsOn`計劃的設置。

### <a name="create-a-function-app"></a>建立函數應用程式

#### <a name="windows"></a>Windows

在 Windows 上,消耗計劃需要在站台配置中增加`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`兩`WEBSITE_CONTENTSHARE`個 設定 :和 。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

在 Linux 上,函數`kind`應用必須`functionapp,linux`將其設定為`reserved`,並且必須`true`將 屬性設定為 :

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>在進階計畫上部署

高級計劃提供與消費計劃相同的擴展,但包括專用資源和附加功能。 要瞭解更多資訊,請參閱[Azure 函數進階計畫](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>建立進階方案

高級計劃是一種特殊類型的「伺服器場」資源。 `EP1`可以使用`EP2`、`EP3``Name``sku`或[用於描述物件](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)中的屬性值來指定它。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>建立函數應用程式

進階計畫中的函數應用必須`serverFarmId`將 屬性設定為之前創建的計畫的資源 ID。 此外,進階計畫需要在網站配置中增加兩個設定:`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING``WEBSITE_CONTENTSHARE`和 。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>在應用服務計畫上部署

在 App Service 方案中，您的函數應用程式是依據基本、標準或進階 SKU 在專用的 VM 上執行，就像 Web 應用程式一樣。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

如需範例 Azure Resource Manager 範本，請參閱[採用 Azure App Service 方案的函數應用程式]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

應用服務計畫由「伺服器場」資源定義。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

在 Linux 執行應用,您`kind`必須設定為`Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>建立函數應用程式

應用服務計畫中的函數應用必須將`serverFarmId`屬性設置為之前創建的計畫的資源 ID。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux 應用程式還應`linuxFxVersion`包括`siteConfig`下的屬性。 如果只是部署代碼,則此值由所需的運行時堆疊確定:

| Stack            | 範例值                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

如果要[部署自定義容器映射](./functions-create-function-linux-custom-image.md),則必須`linuxFxVersion`使用並包括允許提取映射的配置,如容器[的 Web 應用](/azure/app-service/containers)中所示。 此外,設置為`WEBSITES_ENABLE_APP_SERVICE_STORAGE``false`,因為應用內容在容器本身中提供:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>自訂部署

函數應用程式有許多子資源可供您用於部署，包括應用程式設定和原始檔控制選項。 您還可以選擇移除**來源控制器**的子資源,而是使用其他[部署選項](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要使用 Azure Resource Manager 成功部署應用程式，請務必了解資源在 Azure 中部署的方式。 在下列範例中，將使用 **siteConfig** 套用高層級組態。 請務必將這些組態設定為高層級，因為它們會將資訊傳遞給 Functions 執行階段和部署引擎。 在套用子 **sourcecontrols/web** 資源之前，需要高層級資訊。 儘管可以在子級**配置/appSettings**資源中配置這些設置,但在某些情況下,必須在應用**配置/應用設置***之前*部署函數應用。 例如，在搭配使用函數應用程式與 [Logic Apps](../logic-apps/index.yml) 時，您的函數為另一個資源的相依性。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 此範本使用[Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file)應用設定值,該值設置函數部署引擎 (Kudu) 查找可部署代碼的基本目錄。 在我們的存放庫中，我們的函數是位於 **src** 資料夾的子資料夾中。 因此，在上述範例中，我們將應用程式設定值設定為 `src`。 如果您的函數位於您存放庫的根，或您並非從來源控制項進行部署，您可以移除此應用程式設定值。

## <a name="deploy-your-template"></a>部署您的範本

您可以使用以下任何方式來部署範本：

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>部署至 Azure 按鈕

以 GitHub 中 `azuredeploy.json` 檔案的原始路徑 [URL 編碼](https://www.bing.com/search?q=url+encode)版本取代 ```<url-encoded-path-to-azuredeploy-json>```。

以下是使用 Markdown 的範例：

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

以下是使用 HTML 的範例：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>使用 PowerShell 進行部署

以下 PowerShell 命令建立資源組並部署範本,該範本使用其所需資源創建函數應用。 要在本地運行,必須安裝[Azure PowerShell。](/powershell/azure/install-az-ps) 運行[`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount)以登錄。

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

要測試此部署,可以使用[類似此範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json)在"消耗"計劃中在 Windows 上創建函數應用。 替換為`<function-app-name>`函數應用的唯一名稱。

## <a name="next-steps"></a>後續步驟

深入了解如何開發並設定 Azure Functions。

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure 函數應用設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)

<!-- LINKS -->

[採用取用方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[採用 Azure App Service 方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
