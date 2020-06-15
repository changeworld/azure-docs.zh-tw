---
title: 範本函式 - 資源
description: 描述 Azure Resource Manager 範本中用來擷取資源相關值的函式。
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: aea3f654551f66390afa207ac5ce682d23e5bfe9
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780569"
---
# <a name="resource-functions-for-arm-templates"></a>ARM 範本的資源函式

Resource Manager 提供下列函式，以在您的 Azure Resource Manager (ARM) 範本中取得資源值：

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [提供者](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [訂用帳戶](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

若要從參數、變數或目前的部署中取得值，請參閱 [部署值函式](template-functions-deployment.md)。

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

傳回[延伸模組資源](../management/extension-resource-types.md)的資源識別碼，這是可套用至其他資源以新增至其功能的資源類型。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| resourceId |是 |字串 |套用延伸模組資源之資源的資源識別碼。 |
| resourceType |是 |字串 |資源的類型 (包括資源提供者命名空間)。 |
| resourceName1 |是 |字串 |資源的名稱。 |
| resourceName2 |否 |字串 |下一個資源名稱區段 (如有必要)。 |

當資源類型包含更多區段時，請繼續新增資源名稱作為參數。

### <a name="return-value"></a>傳回值

此函式所傳回之資源識別碼的基本格式為：

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

範圍區段會隨著要擴充的資源而不同。

當延伸模組資源套用至**資源**時，資源識別碼會以下列格式傳回：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

當延伸模組資源套用至**資源群組**時，格式為：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

當延伸模組資源套用至**訂用帳戶**時，格式為：

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

當延伸模組資源套用至**管理群組**時，格式為：

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId 範例

下列範例會傳回資源群組鎖定的資源識別碼。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

此函式的語法因清單作業的名稱而異。 每項實作會對支援 list 作業的資源類型傳回值。 此作業必須以 `list` 開頭。 常見的使用方式包括 `listKeys`、`listKeyValue` 和 `listSecrets`。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |字串 |資源的唯一識別碼。 |
| apiVersion |是 |字串 |資源執行階段狀態的 API 版本。 一般而言，格式為 **yyyy-mm-dd**。 |
| functionValues |否 |物件 (object) | 具有函式值的物件。 只針對以下函式提供此物件：可支援在儲存體帳戶上接收具有參數值的物件，例如 **listAccountSas**。 本文會顯示傳遞函式值的範例。 |

### <a name="valid-uses"></a>有效用法

list 函式只能用在資源定義的屬性中，以及範本或部署的輸出區段中。 與[屬性反覆運算](copy-properties.md)搭配使用時，您可以將 list 函式用於 `input`，因為運算式會指派給資源屬性。 您無法將這些函式用於 `count`，因為在解析 list 函式之前就必須確定計數。

### <a name="implementations"></a>實作

下表顯示可能的 list* 用法。

| 資源類型 | 函式名稱 |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration | [ListKeyValue](/rest/api/appconfiguration/configurationstores/listkeyvalue) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

為判斷哪一個資源類型具有 list 作業，您可以使用下列選項：

* 檢視資源提供者的 [REST API 作業](/rest/api/)，並尋找 list 作業。 例如，儲存體帳戶具有 [listKeys 作業](/rest/api/storagerp/storageaccounts)。
* 使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell Cmdlet。 下列範例會取得儲存體帳戶的所有 list 作業︰

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 使用下列 Azure CLI 命令可只篩選出 list 作業：

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>傳回值

傳回的物件會因您使用的清單函式而異。 例如，儲存體帳戶的 listKeys 會傳回下列格式：

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

其他 list 函式具有不同的傳回格式。 若要查看函式的格式，請將函式放入 [輸出] 區段，如範本範例所示。

### <a name="remarks"></a>備註

使用資源名稱或 [resourceId 函式](#resourceid)來指定資源。 在部署所參考資源的相同範本中使用這個函式時，請使用資源名稱。

如果您在有條件部署的資源中使用 **list** 函式，即使未部署資源，也會評估該函式。 如果 **list** 函式參考不存在的資源，就會發生錯誤。 請使用 **if** 函式，以確保只有在部署資源時才會評估函式。 如需對有條件部署的資源搭配使用 if 和 list 的範例範本，請參閱 [if 函式](template-functions-logical.md#if)。

### <a name="list-example"></a>清單範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json)顯示如何在 outputs 區段中從儲存體帳戶傳回主要和次要金鑰。 它也會傳回儲存體帳戶的 SAS 權杖。

若要取得 SAS 權杖，請傳遞到期時間的物件。 到期時間必須是未來的時間。 此範例的用意是要示範如何使用清單函式。 一般而言，您會在資源值中使用 SAS 權杖，而非將它傳回作為輸出值。 輸出值會儲存於部署歷程記錄，並不安全。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

如需 listKeyValue 範例，請參閱[快速入門：使用應用程式組態和 Resource Manager 範本進行自動化 VM 部署](../../azure-app-configuration/quickstart-resource-manager.md#deploy-vm-using-stored-key-values)。

## <a name="providers"></a>提供者

`providers(providerNamespace, [resourceType])`

傳回資源提供者和其所支援資源類型的相關資訊。 如果未提供資源類型，則函式會傳回資源提供者所有的支援類型。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| providerNamespace |是 |字串 |提供者的命名空間 |
| resourceType |否 |字串 |所指定命名空間內的資源類型。 |

### <a name="return-value"></a>傳回值

每個支援類型都會以下列格式傳回：

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

不保證所傳回的值會有陣列順序。

### <a name="providers-example"></a>提供者範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)顯示如何使用 provider 函式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

針對 **Microsoft.Web** 資源提供者和**網站**資源類型，上述範例會以下列格式傳回物件：

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

傳回代表資源執行階段狀態的物件。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |字串 |資源的名稱或唯一識別碼。 當參考目前範本中的資源時，只會提供資源名稱做為參數。 參考先前部署的資源時，或資源的名稱不明確時，請提供資源識別碼。 |
| apiVersion |否 |字串 |指定的資源的 API 版本。 **如果在相同的範本內未供應資源，則需要此參數。** 一般而言，格式為 **yyyy-mm-dd**。 如需資源的有效 API 版本，請參閱[範本參考](/azure/templates/)。 |
| 'Full' |否 |字串 |值，指定是否要傳回完整資源物件。 如果您未指定 `'Full'`，則只會傳回資源的屬性物件。 完整物件包括例如資源識別碼和位置的值。 |

### <a name="return-value"></a>傳回值

每種資源類型都會針對 reference 函式傳回不同屬性。 此函式不會傳回單一的預先定義格式。 此外，傳回的值會根據 `'Full'` 引數的值而有所不同。 若要查看資源類型的屬性，請在輸出區段中傳回物件，如範例所示。

### <a name="remarks"></a>備註

參照函數會擷取過去部署資源或是目前範本部署資源的狀態。 本文會介紹這兩個案例的範例。

一般而言，您可以使用 **reference** 函式從物件傳回特定值，例如 Blob 端點 URI 或完整網域名稱。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

當您需要並非屬性結構描述一部分的資源值時，使用 `'Full'`。 例如，若要設定金鑰保存庫存取原則，請取得虛擬機器的身分識別屬性。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>有效用法

參考函式只能用在資源定義的屬性中，以及範本或部署的輸出區段中。 與[屬性反覆運算](copy-properties.md)搭配使用時，您可以將 reference 函式用於 `input`，因為運算式會指派給資源屬性。

您無法使用 reference 函式在複製迴圈中設定 `count` 屬性的值。 您可以使用該函式來設定迴圈中的其他屬性。 計數屬性的參考遭到封鎖，因為在解析 reference 函式之前就必須決定該屬性。

您無法在[巢狀範本](linked-templates.md#nested-template)的輸出中使用 reference 函式，以傳回已在巢狀範本中部署的資源。 此時應使用[連結的範本](linked-templates.md#linked-template)。

如果您在有條件部署的資源中使用 **reference** 函式，即使未部署資源，也會評估該函式。  如果 **reference** 函式參考不存在的資源，就會發生錯誤。 請使用 **if** 函式，以確保只有在部署資源時才會評估函式。 如需對有條件部署的資源搭配使用 if 和 reference 的範例範本，請參閱 [if 函式](template-functions-logical.md#if)。

### <a name="implicit-dependency"></a>隱含相依性

如果在相同的範本內佈建所參考的資源且您會依其名稱 (而非資源識別碼) 來參考該資源，則可使用 reference 函式，隱含地宣告某一個資源相依於另一個資源。 您不需要同時使用 dependsOn 屬性。 所參考的資源完成部署之前不會評估函式。

### <a name="resource-name-or-identifier"></a>資源名稱或識別碼

參考部署在相同範本中的資源時，請提供資源的名稱。

```json
"value": "[reference(parameters('storageAccountName'))]"
```

參考未部署在相同範本中的資源時，請提供資源識別碼和 `apiVersion`。

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

若要明確指出您要參考的資源，您可以提供完整的資源識別碼。

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

當建構資源的完整參考時，要從類型和名稱合併區段的順序並非只是將兩個串連。 相反地，在命名空間之後，使用從最特定到最不特定的一連串*類型/名稱*組：

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 為正確 `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 為不正確

若要簡化建立任何資源識別碼的程序，請使用本文件中說明的 `resourceId()` 函式，而不是 `concat()` 函式。

### <a name="get-managed-identity"></a>取得受控識別

[適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)是針對某些資源隱含建立的[延伸模組資源類型](../management/extension-resource-types.md)。 由於受控識別未明確定義於範本中，因此您必須參考套用身分識別的資源。 請使用 `Full` 取得所有屬性，包括隱含建立的身分識別。

例如，若要針對套用至虛擬機器擴展集的受控識別取得其租用戶識別碼，請使用：

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>參考範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)部署資源，並會參考該資源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

上述範例會傳回兩個物件。 屬性物件會使用下列格式：

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

完整物件會使用下列格式：

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json)參照了本範本中未部署之儲存體帳戶。 此儲存體帳戶已經存在在同樣的訂用帳戶中。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

傳回代表目前資源群組的物件。

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

所含的資源由其他服務管理的資源群組，才會傳回 **managedBy** 屬性。 就受控應用程式、Databricks 和 AKS 而言，該屬性的值將是管理端資源的資源識別碼。

### <a name="remarks"></a>備註

`resourceGroup()` 函式不能用於[部署在訂用帳戶層級](deploy-to-subscription.md)中的範本， 只能用於部署到資源群組中的範本。 您可以在以資源群組為目標的[連結或巢狀範本 (具有內部範圍)](linked-templates.md) 中使用 `resourceGroup()` 函式，即使已將父範本部署至訂用帳戶亦然。 在此情況下，連結或巢狀範本會在資源群組層級部署。 如需關於在訂用帳戶層級部署中將資源群組設為目標的詳細資訊，請參閱[將 Azure 資源部署至多個訂用帳戶和資源群組](cross-resource-group-deployment.md)。

resourceGroup 函式的常見用法是在和資源群組相同的位置中建立資源。 下列範例會使用資源群組位置作為預設參數值。

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

您也可以使用 resourceGroup 函式，將資源群組中的標籤套用至資源。 如需詳細資訊，請參閱[從資源群組套用標籤](../management/tag-resources.md#apply-tags-from-resource-group)。

使用巢狀範本部署至多個資源群組時，您可以指定評估 resourceGroup 函式的範圍。 如需詳細資訊，請參閱[將 Azure 資源部署至多個訂用帳戶或資源群組](cross-resource-group-deployment.md)。

### <a name="resource-group-example"></a>資源群組範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)會傳回資源群組的屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

上述範例會以下列格式傳回物件︰

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

傳回資源的唯一識別碼。 如果資源名稱不確定或未佈建在相同的範本內，請使用此函數。 傳回的識別碼格式會根據進行部署的範圍是資源群組、訂用帳戶、管理群組還是租用戶，而有所不同。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字串 (GUID 格式) |預設值為目前的訂用帳戶。 需要擷取另一個訂用帳戶中的資源群組時，請指定此值。 只有在資源群組或訂用帳戶範圍進行部署時，才需要提供此值。 |
| resourceGroupName |否 |字串 |預設值為目前資源群組。 需要擷取另一個訂用帳戶中的資源群組時，請指定此值。 只有在部署於資源群組範圍時，才需要提供此值。 |
| resourceType |是 |字串 |資源的類型 (包括資源提供者命名空間)。 |
| resourceName1 |是 |字串 |資源的名稱。 |
| resourceName2 |否 |字串 |下一個資源名稱區段 (如有必要)。 |

當資源類型包含更多區段時，請繼續新增資源名稱作為參數。

### <a name="return-value"></a>傳回值

當範本部署於資源群組範圍時，資源識別碼會以下列格式傳回：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

在[訂用帳戶層級部署](deploy-to-subscription.md)中使用時，資源識別碼會以下列格式傳回：

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

使用於[管理群組層級部署](deploy-to-management-group.md)或租用戶層級部署時，資源識別碼會以下列格式傳回：

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

若要取得其他格式的識別碼，請參閱：

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>備註

您應提供的參數數目會根據資源是父資源還是子資源，以及資源是否位於相同的訂用帳戶或資源群組中，而有所不同。

若要對相同訂用帳戶和資源群組中的父資源取得資源識別碼，請提供資源的類型和名稱。

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

若要取得子資源的資源識別碼，請注意資源類型中的區段數目。 請提供資源類型中每個區段的資源名稱。 區段的名稱會對應至存在於該階層部分的資源。

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

若要對位於相同訂用帳戶、但不同資源群組中的資源取得資源識別碼，請提供資源群組名稱。

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

若要對不同訂用帳戶和資源群組中的資源取得資源識別碼，請提供訂用帳戶識別碼和資源群組名稱。

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

通常，在替代資源群組中使用儲存體帳戶或虛擬網路時，需要使用此函數。 下列範例顯示如何輕鬆地使用外部資源群組中的資源：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>資源識別碼範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)會傳回資源群組中儲存體帳戶的資源識別碼：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>訂用帳戶

`subscription()`

傳回目前部署的訂用帳戶詳細資料。

### <a name="return-value"></a>傳回值

此函式會傳回下列格式︰

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>備註

使用巢狀範本部署至多個訂用帳戶時，您可以指定評估訂用帳戶函式的範圍。 如需詳細資訊，請參閱[將 Azure 資源部署至多個訂用帳戶或資源群組](cross-resource-group-deployment.md)。

### <a name="subscription-example"></a>訂用帳戶範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)顯示在 outputs 區段中所呼叫的 subscription 函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

傳回部署於訂用帳戶層級之資源的唯一識別碼。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字串 (GUID 格式) |預設值為目前的訂用帳戶。 需要擷取另一個訂用帳戶中的資源群組時，請指定此值。 |
| resourceType |是 |字串 |資源的類型 (包括資源提供者命名空間)。 |
| resourceName1 |是 |字串 |資源的名稱。 |
| resourceName2 |否 |字串 |下一個資源名稱區段 (如有必要)。 |

當資源類型包含更多區段時，請繼續新增資源名稱作為參數。

### <a name="return-value"></a>傳回值

識別碼會以下列格式傳回：

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>備註

您可以使用此函式，對[部署至訂用帳戶](deploy-to-subscription.md) (而不是資源群組) 的資源取得資源識別碼。 傳回的識別碼與 [resourceId](#resourceid) 函式所傳回的值不同；其中不含資源群組值。

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID 範例

下列範本會指派內建角色。 您可以將其部署至資源群組或訂用帳戶。 範本會使用 subscriptionResourceId 函式取得內建角色的資源識別碼。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

傳回部署於租用戶層級之資源的唯一識別碼。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| resourceType |是 |字串 |資源的類型 (包括資源提供者命名空間)。 |
| resourceName1 |是 |字串 |資源的名稱。 |
| resourceName2 |否 |字串 |下一個資源名稱區段 (如有必要)。 |

當資源類型包含更多區段時，請繼續新增資源名稱作為參數。

### <a name="return-value"></a>傳回值

識別碼會以下列格式傳回：

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>備註

您可以使用此函式，對部署至租用戶的資源取得資源識別碼。 傳回的識別碼與其他資源識別碼函式所傳回的值不同；其中不含資源群組或訂用帳戶值。

## <a name="next-steps"></a>後續步驟

* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](copy-resources.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](deploy-powershell.md)。

