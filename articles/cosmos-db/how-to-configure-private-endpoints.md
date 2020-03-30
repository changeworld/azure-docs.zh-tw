---
title: 為 Azure 宇宙帳戶配置 Azure 專用連結
description: 瞭解如何設置 Azure 專用連結，以便使用虛擬網路中的私人 IP 位址訪問 Azure Cosmos 帳戶。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 944341b1ef88c7e3d64a74536720eb9fb1d17321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152735"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>為 Azure 宇宙帳戶配置 Azure 專用連結

通過使用 Azure 專用連結，可以通過專用終結點連接到 Azure Cosmos 帳戶。 專用終結點是虛擬網路中子網中的一組私人 IP 位址。 然後，您可以通過私人 IP 位址限制對 Azure Cosmos 帳戶的訪問。 當私有鏈路與受限的 NSG 策略結合使用時，它有助於減少資料外泄的風險。 要瞭解有關私有終結點的更多詳細資訊，請參閱[Azure 專用連結](../private-link/private-link-overview.md)一文。

專用連結允許使用者從虛擬網路內或任何對等虛擬網路訪問 Azure Cosmos 帳戶。 通過 VPN 或 Azure ExpressRoute 通過專用對等互連，映射到專用連結的資源也可在本地訪問。 

您可以使用自動或手動審批方法連接到使用專用連結配置的 Azure Cosmos 帳戶。 要瞭解更多資訊，請參閱專用連結文檔的[審批工作流](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)部分。 

本文介紹了創建專用終結點的步驟。 它假定您使用的是自動核准方法。

> [!NOTE]
> 專用終結點支援目前通常僅在支援區域中用於閘道連接模式。 對於直接模式，它可作為預覽功能提供。

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>使用 Azure 門戶創建專用終結點

使用以下步驟使用 Azure 門戶為現有 Azure Cosmos 帳戶創建專用終結點：

1. 在"**所有資源**"窗格中，選擇 Azure Cosmos 帳戶。

1. 從設置清單中選擇**專用終結點連接**，然後選擇**專用終結點**：

   ![在 Azure 門戶中創建專用終結點的選擇](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. 在 **"創建專用終結點 - 基礎知識"** 窗格中，輸入或選擇以下詳細資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳情** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取資源群組。|
    | **實例詳細資訊** |  |
    | 名稱 | 輸入專用終結點的任何名稱。 如果獲取此名稱，請創建唯一的名稱。 |
    |區域| 選擇要部署專用連結的區域。 在虛擬網路所在的同一位置創建專用終結點。|
    |||
1. 選擇 **"下一步"：資源**。
1. 在 [建立私人端點 - 資源]**** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選擇 **"連接到目錄中的 Azure 資源**"。 <br/><br/> 然後，您可以選擇一個資源來設置專用連結。 或者，您可以使用與其他人共用的資源識別碼 或別名連接到其他人的資源。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選擇**微軟.AzureCosmosDB/資料庫帳戶**。 |
    | 資源 |選擇 Azure 宇宙帳戶。 |
    |目標子資源 |選擇要映射的 Azure 宇宙 DB API 類型。 這預設為 SQL、MongoDB 和 Cassandra API 的一個選項。 對於 Gremlin 和表 API，您還可以選擇**Sql，** 因為這些 API 與 SQL API 可交互操作。 |
    |||

1. 選擇 **"下一步"：配置**。
1. 在 **"創建專用終結點 - 配置**"中，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路功能**| |
    | 虛擬網路| 選擇虛擬網路。 |
    | 子網路 | 選擇子網。 |
    |**專用 DNS 集成**||
    |與私人 DNS 區域整合 |選取 [是]****。 <br><br/> 要與專用終結點私下連接，您需要 DNS 記錄。 我們建議您將專用終結點與專用 DNS 區域集成。 您還可以使用自己的 DNS 伺服器或使用虛擬機器上的主機檔創建 DNS 記錄。 |
    |私人 DNS 區域 |選擇**privatelink.documents.azure.com**。 <br><br/> 將自動確定專用 DNS 區域。 不能使用 Azure 門戶來更改它。|
    |||

1. 選擇 **"審閱" = 創建**。 在 **"審核 + 創建"** 頁上，Azure 驗證配置。
1. 當您看到**驗證傳遞**的消息時，選擇 **"創建**"。

在 Azure 門戶中批准 Azure Cosmos 帳戶的專用連結後，"**防火牆"和"虛擬網路**"窗格中的 **"所有網路"** 選項不可用。

下表顯示了不同 Azure Cosmos 帳戶 API 類型、支援的子資源與相應的私人區域名稱之間的映射。 您還可以通過 SQL API 訪問 Gremlin 和表 API 帳戶，因此這些 API 有兩個條目。

|Azure 宇宙帳戶 API 類型  |支援的子資源（或組指示項） |私人區域名稱  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>獲取私人 IP 位址

預配專用終結點後，可以查詢 IP 位址。 要查看 Azure 門戶中的 IP 位址，

1. 選取 [所有資源]****。
1. 搜索您之前創建的私有終結點。 在這種情況下，它是**bbPrivateEndpoint3**。
1. 選擇 **"概述**"選項卡以查看 DNS 設置和 IP 位址。

![Azure 門戶中的私人 IP 位址](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

每個專用終結點創建多個 IP 位址：

* 一個用於 Azure Cosmos 帳戶的全域（與區域無關）終結點
* 部署 Azure Cosmos 帳戶的每個區域一個

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>使用 Azure PowerShell 創建專用終結點

運行以下 PowerShell 腳本，為現有 Azure Cosmos 帳戶創建名為"MyPrivateEndpoint"的專用終結點。 將變數值替換為環境的詳細資訊。

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將專用終結點與專用 DNS 區域集成

創建專用終結點後，可以使用以下 PowerShell 腳本將其與專用 DNS 區域集成：

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>獲取私人 IP 位址

預配專用終結點後，可以使用以下 PowerShell 腳本查詢 IP 位址和 FQDN 映射：

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>使用 Azure CLI 創建專用終結點

運行以下 Azure CLI 腳本，為現有 Azure Cosmos 帳戶創建名為"myPrivateEndpoint"的專用終結點。 將變數值替換為環境的詳細資訊。

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將專用終結點與專用 DNS 區域集成

創建專用終結點後，可以使用以下 Azure CLI 腳本將其與專用 DNS 區域集成：

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>使用資源管理器範本創建專用終結點

您可以通過在虛擬網路子網中創建專用終結點來設置專用連結。 通過使用 Azure 資源管理器範本來實現此目的。

使用以下代碼創建名為"PrivateEndpoint_template.json"的資源管理器範本。 此範本為現有虛擬網路中的現有 Azure Cosmos SQL API 帳戶創建專用終結點。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**定義範本的參數檔**

為範本創建參數檔，並將其命名為"PrivateEndpoint_parameters.json"。 將以下代碼添加到參數檔：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**使用 PowerShell 腳本部署範本**

使用以下代碼創建 PowerShell 腳本。 在運行腳本之前，請將訂閱 ID、資源組名稱和其他變數值替換為環境的詳細資訊。

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

在 PowerShell 腳本中`GroupId`，變數只能包含一個值。 該值是帳戶的 API 類型。 允許的值是： `Sql` `MongoDB`、 `Cassandra` `Gremlin`、 `Table`、 和 。 某些 Azure Cosmos 帳戶類型可通過多個 API 訪問。 例如：

* 可以從 Gremlin 和 SQL API 帳戶訪問 Gremlin API 帳戶。
* 可以從表和 SQL API 帳戶訪問表 API 帳戶。

對於這些帳戶，必須為每個 API 類型創建一個專用終結點。 相應的 API 類型在陣列中`GroupId`指定。

成功部署範本後，可以看到類似于下圖所示的輸出。 `provisioningState`值是`Succeeded`私有終結點設置正確。

![資源管理器範本的部署輸出](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

部署範本後，私人 IP 位址將保留在子網中。 Azure Cosmos 帳戶的防火牆規則配置為僅接受來自專用終結點的連接。

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將專用終結點與專用 DNS 區域集成

使用以下代碼創建名為"PrivateZone_template.json"的資源管理器範本。 此範本為現有虛擬網路中的現有 Azure Cosmos SQL API 帳戶創建專用 DNS 區域。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

使用以下代碼創建名為"PrivateZoneRecords_template.json"的資源管理器範本。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**定義範本的參數檔**

為範本創建以下兩個參數檔。 創建"PrivateZone_parameters.json"。 取代為下列程式碼：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

創建"PrivateZoneRecords_parameters.json"。 取代為下列程式碼：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**使用 PowerShell 腳本部署範本**

使用以下代碼創建 PowerShell 腳本。 在運行腳本之前，請將訂閱 ID、資源組名稱和其他變數值替換為環境的詳細資訊。

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>設定自訂 DNS

應在創建專用終結點的子網中使用專用 DNS 區域。 配置終結點，以便將每個私人 IP 位址映射到 DNS 條目。 （請參閱前面`fqdns`顯示的回應中的屬性。

創建專用終結點時，可以將其與 Azure 中的專用 DNS 區域集成。 如果選擇改用自訂 DNS 區域，則必須將其配置為為專用終結點保留的所有私人 IP 位址添加 DNS 記錄。

## <a name="private-link-combined-with-firewall-rules"></a>專用連結與防火牆規則相結合

當您將專用連結與防火牆規則結合使用時，可能會出現以下情況和結果：

* 如果不配置任何防火牆規則，則預設情況下，所有流量都可以訪問 Azure Cosmos 帳戶。

* 如果配置公共流量或服務終結點並創建專用終結點，則不同類型的傳入流量將由相應類型的防火牆規則授權。

* 如果不配置任何公共流量或服務終結點，並且創建專用終結點，則 Azure Cosmos 帳戶只能通過專用終結點訪問。 如果不配置公共流量或服務終結點，則在所有已批准的專用終結點被拒絕或刪除後，帳戶將對整個網路開放。

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>添加或刪除區域時更新專用終結點

向 Azure Cosmos 帳戶添加或刪除區域需要添加或刪除該帳戶的 DNS 條目。 添加或刪除區域後，可以更新子網的專用 DNS 區域，以反映添加或刪除的 DNS 條目及其相應的私人 IP 位址。

例如，假設您在三個區域部署 Azure Cosmos 帳戶："美國西部"、"美國中部"和"西歐"。 為帳戶創建專用終結點時，子網中會保留四個專用 IP。 三個區域各有一個 IP，全域/區域無關端點有一個 IP。

稍後，您可以將新區域（例如"美國東部"）添加到 Azure Cosmos 帳戶。 添加新區域後，您需要向專用 DNS 區域或自訂 DNS 添加相應的 DNS 記錄。

刪除區域時，可以使用相同的步驟。 刪除該區域後，您需要從專用 DNS 區域或自訂 DNS 中刪除相應的 DNS 記錄。

## <a name="current-limitations"></a>目前的限制

當您使用具有 Azure Cosmos 帳戶的專用連結時，以下限制適用：

* Azure Cosmos 帳戶和虛擬網路的專用連結支援僅在特定區域提供。 有關受支援區域的清單，請參閱"專用連結"一文的["可用區域](../private-link/private-link-overview.md#availability)"部分。 

  > [!NOTE]
  > 要創建專用終結點，請確保虛擬網路和 Azure Cosmos 帳戶都位於受支援的區域。

* 使用直接模式連接使用與 Azure Cosmos 帳戶的專用連結時，只能使用 TCP 協定。 HTTP 協定尚不受支援。

* 專用終結點支援目前通常僅在支援區域中用於閘道連接模式。 對於直接模式，它可作為預覽功能提供。

* 當您使用 Azure Cosmos DB 的 MongoDB 帳戶 API 時，僅支援伺服器版本 3.6 上的帳戶（即，使用格式`*.mongo.cosmos.azure.com`中的終結點的帳戶）的專用終結點。 伺服器版本 3.2 上的帳戶不支援專用連結（即，使用終結點的帳戶格式`*.documents.azure.com`）。 要使用專用連結，應將舊帳戶遷移到新版本。

* 當您使用 Azure Cosmos DB 的 API 時，對於具有專用連結的 MongoDB 帳戶，不能使用 Robo 3T、Studio 3T 和 Mongoose 等工具。 僅當指定了參數時，`appName=<account name>`終結點才能具有專用連結支援。 例如 `replicaSet=globaldb&appName=mydbaccountname`。 由於這些工具不會將連接字串中的應用名稱傳遞給服務，因此無法使用專用連結。 但是，您仍可以通過使用帶有 3.6 版本的 SDK 驅動程式來訪問這些帳戶。

* 如果虛擬網路包含專用連結，則無法移動或刪除虛擬網路。

* 如果 Azure Cosmos 帳戶附加到專用終結點，則無法刪除該帳戶。

* 不能將 Azure Cosmos 帳戶容錯移轉到未映射到附加到該帳戶的所有專用終結點的區域。

* 應至少授予 Azure Cosmos 帳戶作用域中的"*/私有終結點連接審批"許可權，以創建自動批准的專用終結點。

### <a name="limitations-to-private-dns-zone-integration"></a>對專用 DNS 區域集成的限制

當您刪除專用終結點或從 Azure Cosmos 帳戶中刪除區域時，不會自動刪除專用 DNS 區域中的 DNS 記錄。 您必須在以下之前手動刪除 DNS 記錄：

* 添加連結到此專用 DNS 區域的新專用終結點。
* 向連結到此專用 DNS 區域的專用終結點的任何資料庫帳戶添加新區域。

如果不清理 DNS 記錄，則可能會出現意外的資料平面問題。 這些問題包括資料中斷到私有終結點刪除或區域刪除後添加的區域。

## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure Cosmos DB 安全功能的更多，請參閱以下文章：

* 要為 Azure Cosmos DB 配置防火牆，請參閱[防火牆支援](firewall-support.md)。

* 要瞭解如何為 Azure Cosmos 帳戶配置虛擬網路服務終結點，請參閱[配置來自虛擬網路的訪問](how-to-configure-vnet-service-endpoint.md)。

* 要瞭解有關私有連結的更多詳細資訊，請參閱[Azure 專用連結](../private-link/private-link-overview.md)文檔。
