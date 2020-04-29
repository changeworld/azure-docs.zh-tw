---
title: 設定 azure Cosmos 帳戶的 Azure 私人連結
description: 瞭解如何使用虛擬網路中的私人 IP 位址，設定 Azure 私用連結來存取 Azure Cosmos 帳戶。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 4b49d2aa61587d0156755bdd5c47b3eeb90090a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81270684"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>設定 azure Cosmos 帳戶的 Azure 私人連結

藉由使用 Azure 私人連結，您可以透過私人端點連接到 Azure Cosmos 帳戶。 私人端點是虛擬網路內子網中的一組私人 IP 位址。 接著，您可以透過私人 IP 位址來限制對 Azure Cosmos 帳戶的存取。 當私用連結與受限制的 NSG 原則結合時，它有助於降低資料外泄的風險。 若要深入瞭解私人端點，請參閱[Azure 私用連結](../private-link/private-link-overview.md)一文。

私人連結可讓使用者從虛擬網路內或從任何對等互連虛擬網路存取 Azure Cosmos 帳戶。 對應至私用連結的資源也可以透過 VPN 或 Azure ExpressRoute 在私人對等互連上存取。 

您可以使用 [自動] 或 [手動] 核准方法，連線到以私人連結設定的 Azure Cosmos 帳戶。 若要深入瞭解，請參閱私用連結檔的[核准工作流程](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)一節。 

本文說明建立私用端點的步驟。 它假設您使用的是自動核准方法。

> [!NOTE]
> 私人端點支援目前僅適用于閘道連線模式。 就直接模式而言，它會以預覽功能的形式提供。

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>使用 Azure 入口網站建立私用端點

使用下列步驟，使用 Azure 入口網站建立現有 Azure Cosmos 帳戶的私人端點：

1. 從 [**所有資源**] 窗格中，選擇 Azure Cosmos 帳戶。

1. 從設定清單中選取 [**私人端點連接**]，然後選取 [**私人端點**]：

   ![在 Azure 入口網站中建立私用端點的選取專案](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. 在 [**建立私人端點-基本**] 窗格中，輸入或選取下列詳細資料：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取資源群組。|
    | **執行個體詳細資料** |  |
    | Name | 輸入私人端點的任何名稱。 如果採用這個名稱，請建立一個唯一的名稱。 |
    |區域| 選取您要部署私人連結的區域。 在您的虛擬網路所在的相同位置中建立私人端點。|
    |||
1. 完成時，選取 [下一步:  資源]。
1. 在 [建立私人端點 - 資源]  中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |連線方法  | 選取 **[連線到我的目錄中的 Azure 資源]**。 <br/><br/> 接著，您可以選擇其中一個資源來設定私人連結。 或者，您也可以使用與您共用的資源識別碼或別名，連接到其他人的資源。|
    | 訂用帳戶| 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [ **AzureCosmosDB/databaseAccounts**]。 |
    | 資源 |選取您的 Azure Cosmos 帳戶。 |
    |目標子資源 |選取您想要對應的 Azure Cosmos DB API 類型。 這預設為 SQL、MongoDB 和 Cassandra Api 的一個選項。 針對 Gremlin 和資料表 Api，您也可以選擇 [ **sql** ]，因為這些 api 可與 Sql API 互通。 |
    |||

1. 完成時，選取 [下一步:  組態]。
1. 在 [**建立私人端點-** 設定] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    |**網路功能**| |
    | 虛擬網路| 選取您的虛擬網路。 |
    | 子網路 | 選取您的子網。 |
    |**私人 DNS 整合**||
    |與私人 DNS 區域整合 |選取 [是]  。 <br><br/> 若要私下與您的私人端點連接，您需要 DNS 記錄。 我們建議您將私人端點與私人 DNS 區域整合。 您也可以使用您自己的 DNS 伺服器，或使用虛擬機器上的主機檔案來建立 DNS 記錄。 |
    |私人 DNS 區域 |選取 [ **privatelink.documents.azure.com**]。 <br><br/> 私人 DNS 區域會自動決定。 您無法使用 Azure 入口網站來變更它。|
    |||

1. 選取 [檢閱 + 建立]  。 在 [**審查 + 建立**] 頁面上，Azure 會驗證您的設定。
1. 當您看到 [驗證成功]  訊息時，請選取 [建立]  。

當您已核准 Azure Cosmos Azure 入口網站帳戶的私人連結時，[**防火牆和虛擬網路**] 窗格中的 [**所有網路**] 選項將無法使用。

下表顯示不同 Azure Cosmos 帳戶 API 類型、支援的子資源和對應的私人區功能變數名稱稱之間的對應。 您也可以透過 SQL API 存取 Gremlin 和資料表 API 帳戶，因此這些 Api 有兩個專案。

|Azure Cosmos 帳戶 API 類型  |支援的子資源（或群組識別碼） |私人區功能變數名稱稱  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Table    |    Table     |   privatelink.table.cosmos.azure.com    |
|Table     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>提取私人 IP 位址

布建私人端點之後，您就可以查詢 IP 位址。 若要從 Azure 入口網站中查看 IP 位址：

1. 選取 [所有資源]****。
1. 搜尋您稍早建立的私用端點。 在此情況下，它是**cdbPrivateEndpoint3**。
1. 選取 [**總覽**] 索引標籤，以查看 DNS 設定和 IP 位址。

![Azure 入口網站中的私人 IP 位址](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

每個私人端點都會建立多個 IP 位址：

* 一個用於 Azure Cosmos 帳戶的全域（區域中立）端點
* 一個用於部署 Azure Cosmos 帳戶的每個區域

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>使用 Azure PowerShell 建立私用端點

執行下列 PowerShell 腳本，為現有的 Azure Cosmos 帳戶建立名為 "MyPrivateEndpoint" 的私用端點。 以您環境的詳細資料取代變數值。

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>整合私人端點與私人 DNS 區域

建立私用端點之後，您可以使用下列 PowerShell 腳本，將它與私人 DNS 區域整合：

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

### <a name="fetch-the-private-ip-addresses"></a>提取私人 IP 位址

布建私人端點之後，您可以使用下列 PowerShell 腳本來查詢 IP 位址和 FQDN 對應：

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>使用 Azure CLI 建立私用端點

執行下列 Azure CLI 腳本，為現有的 Azure Cosmos 帳戶建立名為 "myPrivateEndpoint" 的私用端點。 以您環境的詳細資料取代變數值。

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>整合私人端點與私人 DNS 區域

建立私用端點之後，您可以使用下列 Azure CLI 腳本，將它與私人 DNS 區域整合：

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

您可以藉由在虛擬網路子網中建立私人端點來設定私用連結。 您可以使用 Azure Resource Manager 範本來達到這個目的。

使用下列程式碼建立名為 "PrivateEndpoint_template" 的 Resource Manager 範本。 此範本會在現有的虛擬網路中建立現有 Azure Cosmos SQL API 帳戶的私人端點。

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

建立範本的參數檔案，並將它命名為 "PrivateEndpoint_parameters. json"。 將下列程式碼新增至參數檔案：

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

使用下列程式碼建立 PowerShell 腳本。 執行腳本之前，請以您環境的詳細資料取代訂用帳戶識別碼、資源組名和其他變數值。

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

在 PowerShell 腳本中， `GroupId`變數只能包含一個值。 該值是帳戶的 API 類型。 允許的值為`Sql`： `MongoDB`、 `Cassandra`、 `Gremlin`、和`Table`。 某些 Azure Cosmos 帳戶類型可透過多個 Api 存取。 例如：

* 您可以從 Gremlin 和 SQL API 帳戶存取 Gremlin API 帳戶。
* 您可以從資料表和 SQL API 帳戶存取資料表 API 帳戶。

針對這些帳戶，您必須為每個 API 類型建立一個私人端點。 對應的 API 類型指定于`GroupId`陣列中。

成功部署範本之後，您可以看到類似下圖所示的輸出。 如果`provisioningState`已正確`Succeeded`設定私用端點，則值為。

![Resource Manager 範本的部署輸出](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

部署範本之後，私人 IP 位址會保留在子網內。 Azure Cosmos 帳戶的防火牆規則已設定為只接受來自私人端點的連線。

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>整合私人端點與私人 DNS 區域

使用下列程式碼建立名為 "PrivateZone_template" 的 Resource Manager 範本。 此範本會在現有的虛擬網路中，為現有的 Azure Cosmos SQL API 帳戶建立私人 DNS 區域。

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

使用下列程式碼建立名為 "PrivateZoneRecords_template" 的 Resource Manager 範本。

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

為範本建立下列兩個參數檔案。 建立 "PrivateZone_parameters json." 取代為下列程式碼：

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

建立 "PrivateZoneRecords_parameters json." 取代為下列程式碼：

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

使用下列程式碼建立 PowerShell 腳本。 執行腳本之前，請以您環境的詳細資料取代訂用帳戶識別碼、資源組名和其他變數值。

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

在您已建立私人端點的子網中，您應該使用私人 DNS 區域。 設定端點，讓每個私人 IP 位址對應至 DNS 專案。 （請參閱`fqdns`稍早所示回應中的屬性）。

當您建立私用端點時，您可以將它與 Azure 中的私人 DNS 區域整合。 如果您選擇改為使用自訂的 DNS 區域，您必須將它設定為為私人端點保留的所有私人 IP 位址新增 DNS 記錄。

## <a name="private-link-combined-with-firewall-rules"></a>結合防火牆規則的私用連結

當您使用私用連結搭配防火牆規則時，可能會發生下列情況和結果：

* 如果您未設定任何防火牆規則，則根據預設，所有流量都可以存取 Azure Cosmos 帳戶。

* 如果您設定公用流量或服務端點，並建立私人端點，則不同類型的連入流量會由對應類型的防火牆規則所授權。

* 如果您未設定任何公用流量或服務端點，並建立私人端點，則只能透過私人端點來存取 Azure Cosmos 帳戶。 如果您未設定公用流量或服務端點，則在拒絕或刪除所有已核准的私人端點之後，就會對整個網路開啟該帳戶。

## <a name="blocking-public-network-access-during-account-creation"></a>在帳戶建立期間封鎖公用網路存取

如上一節所述，除非已設定特定的防火牆規則，否則新增私人端點會使您的 Azure Cosmos 帳戶只能透過私人端點存取。 這表示在建立 Azure Cosmos 帳戶後，以及在新增私人端點之前，都可以從公用流量連線到該帳戶。 若要確保即使在建立私人端點之前，也會停用公用網路存取，您可以在`publicNetworkAccess`帳戶建立`Disabled`期間將旗標設定為。 如需顯示如何使用此旗標的範例，請參閱[此 Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/)。

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>當您新增或移除區域時，更新私人端點

若要在 Azure Cosmos 帳戶中新增或移除區域，您需要新增或移除該帳戶的 DNS 專案。 新增或移除區域之後，您可以更新子網的私人 DNS 區域，以反映新增或移除的 DNS 專案及其對應的私人 IP 位址。

例如，假設您在三個區域中部署 Azure Cosmos 帳戶：「美國西部」、「美國中部」和「西歐」。 當您建立帳戶的私人端點時，會在子網中保留四個私人 Ip。 這三個區域中的每個都有一個 IP，而且有一個 IP 用於全域/區域無關的端點。

稍後，您可以將新的區域（例如「美國東部」）新增至 Azure Cosmos 帳戶。 新增區域之後，您必須將對應的 DNS 記錄新增至您的私人 DNS 區域或自訂 DNS。

移除區域時，您可以使用相同的步驟。 移除區域之後，您必須從私人 DNS 區域或自訂 DNS 移除對應的 DNS 記錄。

## <a name="current-limitations"></a>目前的限制

當您使用 Azure Cosmos 帳戶的私用連結時，適用下列限制：

* 當您透過使用直接模式連線搭配 Azure Cosmos 帳戶來使用私用連結時，只能使用 TCP 通訊協定。 尚不支援 HTTP 通訊協定。

* 私人端點支援目前僅適用于閘道連線模式。 就直接模式而言，它會以預覽功能的形式提供。

* 當您使用 Azure Cosmos DB 適用于 MongoDB 的 API 帳戶時，僅支援伺服器版本3.6 帳戶的私用端點（也就是使用格式`*.mongo.cosmos.azure.com`的端點帳戶）。 伺服器版本3.2 上的帳戶不支援私用連結（也就是使用格式`*.documents.azure.com`的端點帳戶）。 若要使用私用連結，您應該將舊的帳戶遷移至新版本。

* 當您針對具有私人連結的 MongoDB 帳戶使用 Azure Cosmos DB 的 API 時，您無法使用 Robo 3T、Studio 3T 和 Mongoose 等工具。 只有在`appName=<account name>`指定參數時，端點才可支援私用連結。 例如 `replicaSet=globaldb&appName=mydbaccountname`。 因為這些工具不會將連接字串中的應用程式名稱傳遞給服務，所以您無法使用私用連結。 但您仍然可以使用3.6 版本的 SDK 驅動程式來存取這些帳戶。

* 如果虛擬網路包含私人連結，您就無法移動或刪除它。

* 如果 Azure Cosmos 帳戶已附加至私人端點，您就無法將其刪除。

* 您無法將 Azure Cosmos 帳戶損毀修復至未對應至附加至帳戶之所有私人端點的區域。

* 至少應將 Azure Cosmos 帳戶範圍的 "*/PrivateEndpointConnectionsApproval" 許可權授與網路系統管理員，才能建立自動核准的私人端點。

### <a name="limitations-to-private-dns-zone-integration"></a>私人 DNS 區域整合的限制

當您刪除私人端點或從 Azure Cosmos 帳戶移除區域時，不會自動移除私人 DNS 區域中的 DNS 記錄。 您必須先手動移除 DNS 記錄：

* 加入連結至此私人 DNS 區域的新私人端點。
* 將新區域新增至任何已連結至此私人 DNS 區域之私人端點的資料庫帳戶。

如果您未清除 DNS 記錄，可能會發生非預期的資料平面問題。 這些問題包括在移除私人端點或移除區域之後新增的區域資料中斷。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Cosmos DB 的安全性功能，請參閱下列文章：

* 若要設定 Azure Cosmos DB 的防火牆，請參閱[防火牆支援](firewall-support.md)。

* 若要瞭解如何為您的 Azure Cosmos 帳戶設定虛擬網路服務端點，請參閱[設定從虛擬網路的存取](how-to-configure-vnet-service-endpoint.md)。

* 若要深入瞭解私人連結，請參閱[Azure 私人連結](../private-link/private-link-overview.md)檔。
