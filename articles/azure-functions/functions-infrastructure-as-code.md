---
title: 自動將函數應用程式資源部署至 Azure
description: 了解如何建置能部署函數應用程式的 Azure Resource Manager 範本。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 4b649942a52c51aef0d6edd17b913f75e1fb247b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674162"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions 中函數應用程式的自動化資源部署

您可以使用 Azure Resource Manager 範本來部署函數應用程式。 本文概述執行這項作業所需的資源和參數。 您可能需要部署額外的資源，視函數應用程式中的[觸發程序和繫結](functions-triggers-bindings.md)而定。

如需建立範本的詳細資訊，請參閱 [撰寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)。

如需範例範本，請參閱：
- [採用取用方案的函數應用程式]
- [採用 Azure App Service 方案的函數應用程式]

## <a name="required-resources"></a>所需資源

Azure Functions 部署通常包含下列資源：

| 資源                                                                           | 需求 | 語法和屬性參考                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| 函數應用程式                                                                     | 必要    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Azure 儲存體](../storage/index.yml)帳戶                                   | 必要    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md)元件 | 選擇性    | [Microsoft Insights/元件](/azure/templates/microsoft.insights/components)         |
| [主控方案](./functions-scale.md)                                             | 選用<sup>1</sup>    | [Microsoft. Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup>只有當您選擇在 [Premium 方案](./functions-premium-plan.md) 或 [App Service 方案](../app-service/overview-hosting-plans.md)上執行函數應用程式時，才需要主控方案。

> [!TIP]
> 雖然並非必要，但強烈建議您為應用程式設定 Application Insights。

<a name="storage"></a>
### <a name="storage-account"></a>儲存體帳戶

函數應用程式需要 Azure 儲存體帳戶。 您需要支援 Blob、資料表、佇列和檔案的一般用途帳戶。 如需詳細資訊，請參閱 [Azure Functions 儲存體帳戶需求](storage-considerations.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

此外，您必須在網站設定中將 `AzureWebJobsStorage` 屬性指定為應用程式設定。 若函式應用程式不使用 Application Insights 以進行監視，也應該將 `AzureWebJobsDashboard` 指定為應用程式設定。

Azure Functions 執行階段會使用 `AzureWebJobsStorage` 連接字串來建立內部佇列。  在未啟用 Application Insights 的情況下，執行階段會使用 `AzureWebJobsDashboard` 連接字串來記錄至 Azure 資料表儲存體，並啟動入口網站中的 [監視] 索引標籤。

這些屬性會在 `siteConfig` 物件的 `appSettings`集合中指定：

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

建議 Application Insights，以監視您的函數應用程式。 Application Insights 的資源會以類型 [ **Microsoft. Insights/元件** ] 和 [ **網路** 類型] 來定義：

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

此外，您必須使用應用程式設定，將檢測金鑰提供給函數應用程式 `APPINSIGHTS_INSTRUMENTATIONKEY` 。 這個屬性是在物件的集合中指定的 `appSettings` `siteConfig` ：

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>主控方案

主控方案的定義會有所不同，而且可以是下列其中一項：
* [使用量方案](#consumption) (預設) 
* [進階方案](#premium)
* [App Service 計劃](#app-service-plan)

### <a name="function-app"></a>函數應用程式

函數應用程式資源是 **使用類型為** **functionapp** 的資源所定義：

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
> 如果您要明確定義主控方案，dependsOn 陣列中會需要額外的專案： `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函數應用程式必須包含下列應用程式設定：

| 設定名稱                 | 描述                                                                               | 範例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 函數執行時間用於內部佇列的儲存體帳戶連接字串 | 查看 [儲存體帳戶](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 執行時間的版本                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 要用於此應用程式中函式的語言堆疊                                   | `dotnet`、`node`、`java`、`python` 或 `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | 只有在使用 `node` 語言堆疊時，才需要指定要使用的版本              | `10.14.1`                             |

這些屬性會在屬性的 `appSettings` 集合中指定 `siteConfig` ：

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>在取用方案上部署

取用方案會在您的程式碼執行時自動設定計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應放大。 您不需要支付閒置 Vm 的費用，也不需要預先保留容量。 若要深入了解，請參閱 [Azure Functions 規模調整和主控](consumption-plan.md)。

如需範例 Azure Resource Manager 範本，請參閱[採用取用方案的函數應用程式]。

### <a name="create-a-consumption-plan"></a>建立取用方案

不需要定義耗用量方案。 當您建立函式應用程式資源本身時，系統會根據每個區域自動建立或選取一個。

取用方案是一種特殊類型的「serverfarm」資源。 針對 Windows，您可以使用 `Dynamic` 和屬性的值來指定它 `computeMode` `sku` ：

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
> 無法針對 Linux 明確定義耗用量方案。 系統會自動建立此檔案。

如果您明確地定義取用方案，則必須 `serverFarmId` 在應用程式上設定屬性，使其指向方案的資源識別碼。 您應確定函式應用程式也有 `dependsOn` 此計畫的設定。

### <a name="create-a-function-app"></a>建立函數應用程式

#### <a name="windows"></a>Windows

在 Windows 上，取用方案需要網站設定中的兩個額外設定： `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 和 `WEBSITE_CONTENTSHARE` 。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

在 Linux 上，函數應用程式必須 `kind` 將其設定為 `functionapp,linux` ，而且必須將 `reserved` 屬性設定為 `true` ：

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>在 Premium 方案上部署

Premium 方案提供與取用方案相同的調整，但包含專屬資源和額外的功能。 若要深入瞭解，請參閱 [Azure Functions Premium 方案](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>建立進階方案

Premium 方案是一種特殊類型的「serverfarm」資源。 您可以 `EP1` `EP2` `EP3` 針對 `Name` `sku` [description 物件](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)中的屬性值，使用、或來指定它。

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

Premium 方案上的函式應用程式必須將 `serverFarmId` 屬性設定為稍早建立之方案的資源識別碼。 此外，Premium 方案還需要網站設定中的兩個額外設定： `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 和 `WEBSITE_CONTENTSHARE` 。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>部署 App Service 方案

在 App Service 方案中，您的函數應用程式是依據基本、標準或進階 SKU 在專用的 VM 上執行，就像 Web 應用程式一樣。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

如需範例 Azure Resource Manager 範本，請參閱[採用 Azure App Service 方案的函數應用程式]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

App Service 方案是由「serverfarm」資源所定義。

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

若要在 Linux 上執行您的應用程式，您也必須將設定 `kind` 為 `Linux` ：

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

App Service 方案上的函數應用程式必須將 `serverFarmId` 屬性設定為稍早建立之方案的資源識別碼。

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux 應用程式也應該 `linuxFxVersion` 在下包含屬性 `siteConfig` 。 如果您只是部署程式碼，則這個值是由您所需的執行時間堆疊所決定，其格式 ```runtime|runtimeVersion``` 如下：

| Stack            | 範例值                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.0` |

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

如果您要 [部署自訂的容器映射](./functions-create-function-linux-custom-image.md)，您必須使用來加以指定， `linuxFxVersion` 並且包含可讓您的映射提取的設定，如 [用於容器的 Web App](../app-service/index.yml)中所示。 此外，請將設定 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 為 `false` ，因為容器本身會提供您的應用程式內容：

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
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
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
                    "value": "~3"
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

函數應用程式有許多子資源可供您用於部署，包括應用程式設定和原始檔控制選項。 您也可以選擇移除 **>sourcecontrols** 子資源，並改為使用不同的 [部署選項](functions-continuous-deployment.md) 。

> [!IMPORTANT]
> 若要使用 Azure Resource Manager 成功部署應用程式，請務必了解資源在 Azure 中部署的方式。 在下列範例中，將使用 **siteConfig** 套用高層級組態。 請務必將這些組態設定為高層級，因為它們會將資訊傳遞給 Functions 執行階段和部署引擎。 在套用子 **sourcecontrols/web** 資源之前，需要高層級資訊。 雖然可以在子層級設定 **/appSettings** 資源中設定這些設定，但在某些情況下，您必須先部署函數應用程式， *再* 套用 **config/appSettings** 。 例如，在搭配使用函數應用程式與 [Logic Apps](../logic-apps/index.yml) 時，您的函數為另一個資源的相依性。

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
                "value": "~3"
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
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
> 此範本會使用 [專案](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 應用程式設定值，此值會設定函式部署引擎 (Kudu) 在其中尋找可部署程式碼的基礎目錄。 在我們的存放庫中，我們的函數是位於 **src** 資料夾的子資料夾中。 因此，在上述範例中，我們將應用程式設定值設定為 `src`。 如果您的函數位於您存放庫的根，或您並非從來源控制項進行部署，您可以移除此應用程式設定值。

## <a name="deploy-your-template"></a>部署範本

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

下列 PowerShell 命令會建立資源群組，並部署範本，以使用其必要的資源來建立函數應用程式。 若要在本機執行，您必須安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 。 執行 [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) 以登入。

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

若要測試此部署，您可以使用 [像這樣的範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) ，在取用方案中于 Windows 上建立函數應用程式。 取代為 `<function-app-name>` 函數應用程式的唯一名稱。

## <a name="next-steps"></a>下一步

深入了解如何開發並設定 Azure Functions。

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure 函數應用程式設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](./functions-get-started.md)

<!-- LINKS -->

[採用取用方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[採用 Azure App Service 方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json