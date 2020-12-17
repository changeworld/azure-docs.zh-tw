---
title: 設定 Azure Cosmos 帳戶的 Azure Private Link
description: 了解如何使用虛擬網路中的私人 IP 位址來設定 Azure Private Link，以存取 Azure Cosmos 帳戶。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/16/2020
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9a6db0d25165059581d7ffafa5b8e7fd19330c87
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629641"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>設定 Azure Cosmos 帳戶的 Azure Private Link
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

藉由使用 Azure Private Link，即可透過私人端點連線到 Azure Cosmos 帳戶。 私人端點是虛擬網路內子網路中的一組私人 IP 位址。 您可接著限制透過私人 IP 位址對 Azure Cosmos 帳戶進行存取。 當 Private Link 與受限制的 NSG 原則結合時，即有助於降低資料外洩的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](../private-link/private-link-overview.md) 一文。

> [!NOTE]
> Private Link 不會讓公用 DNS 無法解析您的 Azure Cosmos 端點。 傳入要求的篩選會在應用層級執行，而不是在傳輸或網路層級進行。

Private Link 可讓使用者從虛擬網路內或從任何對等互連的虛擬網路存取 Azure Cosmos 帳戶。 對應至 Private Link 的資源也可透過 VPN 或 Azure ExpressRoute，以私人對等互連方式在內部部署環境存取。 

您可使用自動或手動核准方法來連線到設定 Private Link 的 Azure Cosmos 帳戶。 若要深入了解，請參閱 Private Link 文件的[核准工作流程](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)一節。 

本文將說明建立私人端點的步驟。 其假設您使用自動核准方法。

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>使用 Azure 入口網站建立私人端點

使用下列步驟，透過 Azure 入口網站為現有的 Azure Cosmos 帳戶建立私人端點：

1. 從 [所有資源] 窗格，選擇 Azure Cosmos 帳戶。

1. 從設定清單選取 [私人端點連線]，然後選取 [私人端點]：

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="在 Azure 入口網站中建立私人端點的選項":::

1. 在 [建立私人端點 - 基本] 窗格中，輸入或選取下列詳細資料：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取資源群組。|
    | **執行個體詳細資料** |  |
    | 名稱 | 為私人端點輸入任何名稱。 如果此名稱已被使用，請建立不重複的名稱。 |
    |區域| 選取要部署 Private Link 的區域。 在虛擬網路所在的相同位置中建立私人端點。|
    |||
1. 完成時，選取 [下一步:資源]。
1. 在 [建立私人端點 - 資源] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 [連線到我目錄中的 Azure 資源]。 <br/><br/> 然後，您可選擇其中一個資源來設定 Private Link。 或者，您可使用其他人與您共用的資源識別碼或別名來連線到其資源。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 **Microsoft.AzureCosmosDB/databaseAccounts**。 |
    | 資源 |選取 Azure Cosmos 帳戶。 |
    |目標子資源 |選取想要對應的 Azure Cosmos DB API 類型。 這會預設為 SQL、MongoDB 和 Cassandra API 只有一個選項。 針對 Gremlin 和資料表 API，您還可選擇 **Sql**，因為這些 API 可與 SQL API 互通。 |
    |||

1. 完成時，選取 [下一步:組態]。
1. 在 [建立私人端點 - 設定] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路功能**| |
    | 虛擬網路| 選取虛擬網路。 |
    | 子網路 | 選取子網路。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]。 <br><br/> 若要私下與私人端點連線，則需要 DNS 記錄。 我們建議將私人端點與私人 DNS 區域整合。 您也可以使用自己的 DNS 伺服器，或藉由使用虛擬機器上的主機檔案來建立 DNS 記錄。 |
    |私人 DNS 區域 |選取 **privatelink.documents.azure.com**。 <br><br/> 系統會自動決定私人 DNS 區域。 您無法使用 Azure 入口網站來加以變更。|
    |||

1. 選取 [檢閱 + 建立]。 在 [檢閱 + 建立] 頁面上，Azure 會驗證設定。
1. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

當核准 Azure Cosmos 帳戶的 Private Link 之後，Azure 入口網站上 [防火牆和虛擬網路] 窗格中的 [所有網路] 選項將無法使用。

下表顯示不同 Azure Cosmos 帳戶 API 類型、所支援子資源和相對應私人區域名稱之間的對應。 您也可以透過 SQL API 來存取 Gremlin 和資料表 API 帳戶，因此這些 API 有兩個項目。

|Azure Cosmos 帳戶 API 類型  |支援的子資源 (或群組識別碼) |私人區域名稱  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>擷取私人 IP 位址

佈建私人端點之後，即可查詢 IP 位址。 若要從 Azure 入口網站檢視 IP 位址：

1. 選取 [所有資源]。
1. 搜尋稍早建立的私人端點。 在本案例中，這是 **cdbPrivateEndpoint3**。
1. 選取 [概觀] 索引標籤，以查看 DNS 設定和 IP 位址。

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Azure 入口網站中的私人 IP 位址":::

為每個私人端點建立多個 IP 位址：

* 針對 Azure Cosmos 帳戶的全域 (不限區域) 端點建立一個
* 針對 Azure Cosmos 帳戶部署所在的每個區域各建立一個

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>使用 Azure PowerShell 建立私人端點

執行下列 PowerShell 指令碼，為現有的 Azure Cosmos 帳戶建立名為 "MyPrivateEndpoint" 的私人端點。 將變數值取代為您環境的詳細資料。

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將私人端點與私人 DNS 區域整合

建立私人端點之後，您可使用下列 PowerShell 指令碼，將其與私人 DNS 區域整合：

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

### <a name="fetch-the-private-ip-addresses"></a>擷取私人 IP 位址

佈建私人端點之後，您可使用下列 PowerShell 指令碼來查詢 IP 位址和 FQDN 對應：

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>使用 Azure CLI 建立私人端點

執行下列 Azure CLI 指令碼，為現有的 Azure Cosmos 帳戶建立名為 "myPrivateEndpoint" 的私人端點。 將變數值取代為環境的詳細資料。

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將私人端點與私人 DNS 區域整合

建立私人端點之後，即可使用下列 Azure CLI 指令碼，將其與私人 DNS 區域整合：

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

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>使用 Resource Manager 範本建立私人端點

您可在虛擬網路子網路中建立私人端點來設定 Private Link。 您可使用 Azure Resource Manager 範本來達到這個目的。

使用下列程式碼，建立名為 "PrivateEndpoint_template.json" 的 Resource Manager 範本。 此範本會在現有虛擬網路中，為現有的 Azure Cosmos SQL API 帳戶建立私人端點。

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

**定義範本的參數檔案**

建立範本的參數檔案，並將其命名為 "PrivateEndpoint_parameters.json"。 將下列程式碼新增至該參數檔案：

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

**使用 PowerShell 指令碼部署範本**

使用下列程式碼建立 PowerShell 指令碼。 執行指令碼之前，將訂用帳戶識別碼、資源群組名稱及其他變數值取代為環境的詳細資料。

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

在 PowerShell 指令碼中，`GroupId` 變數只能包含一個值。 該值是帳戶的 API 類型。 允許的值包括：`Sql`、`MongoDB`、`Cassandra`、`Gremlin` 和 `Table`。 某些 Azure Cosmos 帳戶類型可透過多個 API 存取。 例如：

* 您可從 Gremlin 和 SQL API 帳戶存取 Gremlin API 帳戶。
* 您可從資料表和 SQL API 帳戶存取資料表 API 帳戶。

針對這些帳戶，您必須為每個 API 類型建立一個私人端點。 對應的 API 類型是在 `GroupId` 陣列中指定。

成功部署範本之後，您可看到類似下圖所示的輸出。 如果正確設定私人端點，則 `provisioningState` 值會是 `Succeeded`。

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Resource Manager 範本的部署輸出":::

部署範本之後，即會在子網路內保留私人 IP 位址。 Azure Cosmos 帳戶其防火牆規則已設定為只接受來自私人端點的連線。

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>將私人端點與私人 DNS 區域整合

使用下列程式碼，建立名為 "PrivateZone_template.json" 的 Resource Manager 範本。 此範本會在現有的虛擬網路中，為現有的 Azure Cosmos SQL API 帳戶建立私人 DNS 區域。

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

使用下列程式碼來建立名為 "PrivateZoneRecords_template.json" 的 Resource Manager 範本。

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

**定義範本的參數檔案**

為範本建立下列兩個參數檔案。 建立 "PrivateZone_parameters.json"。 取代為下列程式碼：

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

建立 "PrivateZoneRecords_parameters.json"。 取代為下列程式碼：

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

**使用 PowerShell 指令碼部署範本**

使用下列程式碼建立 PowerShell 指令碼。 執行指令碼之前，將訂用帳戶識別碼、資源群組名稱及其他變數值取代為環境的詳細資料。

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

您應該在已建立私人端點的子網路內使用私人 DNS 區域。 設定端點，讓每個私人 IP 位址對應至 DNS 項目 (請參閱稍早所示回應中的 `fqdns` 屬性)。

建立私人端點之後，即可在 Azure 中將其與私人 DNS 區域整合。 如果您選擇改用自訂 DNS 區域，則必須將其設定為針對所有保留給私人端點的私人 IP 位址新增 DNS 記錄。

> [!IMPORTANT]
> 這是您要求的 DNS 解析，可判斷這些要求是否經過您的私人端點，或採用標準的公用路由。 請確定您的本機 DNS 正確地參考了私人端點所對應的私人 IP 位址。

## <a name="private-link-combined-with-firewall-rules"></a>Private Link 搭配防火牆規則

當搭配防火牆規則使用 Private Link 時，可能會發生下列情況和結果：

* 如果您未設定任何防火牆規則，則根據預設，所有流量都可存取 Azure Cosmos 帳戶。

* 如果您設定公用流量或服務端點並建立私人端點，則不同類型之傳入流量會由對應類型的防火牆規則所授權。 如果私人端點是在同時設定服務端點的子網中設定：
  * 私人端點所對應之資料庫帳戶的流量會透過私人端點路由傳送。
  * 從子網流向其他資料庫帳戶的流量會透過服務端點路由傳送。

* 如果未設定任何公用流量或服務端點並建立私人端點，則只能透過私人端點來存取 Azure Cosmos 帳戶。 如果您未設定公用流量或服務端點，則在拒絕或刪除所有已核准的私人端點之後，除非將將 publicnetworkaccess 新增設為停用，否則帳戶會開啟至整個網路 (請參閱下一節) 。

## <a name="blocking-public-network-access-during-account-creation"></a>在帳戶建立期間封鎖公用網路存取

如上一節所述，除非已設定特定防火牆規則，否則新增私人端點會使 Azure Cosmos 帳戶只能透過私人端點存取。 這表示在建立 Azure Cosmos 帳戶之後到新增私人端點之前，都可從公用流量連線到該帳戶。 為確保即使在建立私人端點之前也會停用公用網路存取，您可在帳戶建立期間將 `publicNetworkAccess` 旗標設定為 `Disabled`。 請注意，此旗標優先于任何 IP 或虛擬網路規則;如果 `Disabled` 防火牆設定中允許來源 IP 或虛擬網路，則將旗標設定為時，所有公用和虛擬網路流量都會被封鎖。

如需說明如何使用此旗標的範例，請參閱[此 Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/)。

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>將私人端點新增至現有的 Cosmos 帳戶而不會有停機時間

根據預設，將私人端點新增至現有的帳戶會導致大約5分鐘的短暫停機時間。 請遵循下列指示來避免這種停機時間：

1. 在您的防火牆設定中新增 IP 或虛擬網路規則，以明確地允許您的用戶端連接。
1. 等候10分鐘，以確定已套用設定更新。
1. 設定新的私人端點。
1. 移除在步驟1設定的防火牆規則。

## <a name="port-range-when-using-direct-mode"></a>使用直接模式時的埠範圍

當您透過直接模式連線使用 Private Link 搭配 Azure Cosmos 帳戶時，您必須確定已開啟 (0-65535) 的完整 TCP 埠範圍。

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>新增或移除區域時更新私人端點

除非您使用私人 DNS 區域群組，否則在 Azure Cosmos 帳戶中新增或移除區域會要求您新增或移除該帳戶的 DNS 專案。 新增或移除區域之後，即可更新子網路的私人 DNS 區域，以反映所新增或移除 DNS 項目及其對應的私人 IP 位址。

例如，假設在三個區域中部署 Azure Cosmos 帳戶：「美國西部」、「美國中部」和「西歐」。 當為帳戶建立私人端點時，會在子網路中保留四個私人 IP。 這三個區域各有一個 IP，且全域/不限區域端點會有一個 IP。

稍後，您可將新的區域 (例如「美國東部」) 新增至 Azure Cosmos 帳戶。 新增區域之後，您必須將對應的 DNS 記錄新增至私人 DNS 區域或自訂 DNS。

當移除區域時，即可使用相同的步驟。 移除區域之後，您必須從私人 DNS 區域或自訂 DNS 中移除對應的 DNS 記錄。

## <a name="current-limitations"></a>目前的限制

當對 Azure Cosmos 帳戶使用 Private Link 時，適用下列限制：

* 單一 Azure Cosmos 帳戶上不能有超過200個私人端點。

* 當您透過直接模式連線使用 Private Link 搭配 Azure Cosmos 帳戶時，您只能使用 TCP 通訊協定。 目前不支援 HTTP 通訊協定。

* 當針對 MongoDB 帳戶使用 Azure Cosmos DB 的 API 時，只有伺服器 3.6 版上的帳戶 (也就是使用 `*.mongo.cosmos.azure.com` 格式端點的帳戶) 支援私人端點。 伺服器 3.2 版上的帳戶 (也就是使用 `*.documents.azure.com` 格式端點的帳戶) 不支援 Private Link。 若要使用 Private Link，您應該將舊版帳戶移轉到新版本。

* 當針對具有 Private Link 的 MongoDB 帳戶使用 Azure Cosmos DB 的 API 時，某些工具或程式庫可能無法正常運作，因為其會自動從連接字串中移除 `appName` 參數。 需要此參數才能透過私人端點連線到帳戶。 某些工具 (例如 Visual Studio Code) 並不會從連接字串中移除此參數，因此相容。

* 網路系統管理員至少應獲授與 Azure Cosmos 帳戶範圍的 `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` 權限，才能建立自動核准的私人端點。

### <a name="limitations-to-private-dns-zone-integration"></a>私人 DNS 區域整合的限制

除非您是使用私人 DNS 區域群組，否則當您刪除私人端點或從 Azure Cosmos 帳戶移除區域時，不會自動移除私人 DNS 區域中的 DNS 記錄。 您必須手動移除 DNS 記錄，才能：

* 新增連結至此私人 DNS 區域的私人端點。
* 將新區域新增至任何具有已連結至此私人 DNS 區域的私人端點其資料庫帳戶。

如果您未清除 DNS 記錄，則可能會發生未預期的資料平面問題。 這些問題包括在移除私人端點或移除區域之後新增區域的資料中斷。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 安全性功能，請參閱下列文章：

* 若要設定 Azure Cosmos DB 的防火牆，請參閱[防火牆支援](how-to-configure-firewall.md)。

* 若要了解如何設定 Azure Cosmos 帳戶的虛擬網路服務端點，請參閱[設定從虛擬網路存取](how-to-configure-vnet-service-endpoint.md)。

* 若要深入了解 Private Link，請參閱 [Azure Private Link](../private-link/private-link-overview.md) 文件。
