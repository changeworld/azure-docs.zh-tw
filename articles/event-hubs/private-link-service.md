---
title: 整合 Azure 事件中樞與 Azure 私人連結服務
description: 瞭解如何整合 Azure 事件中樞與 Azure 私人連結服務
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: 110d4b94eda8315c20f4baa70256f7e5ed378530
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106469"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>整合 Azure 事件中樞與 Azure 私人連結（預覽）
Azure 私用連結服務可讓您透過虛擬網路中的**私人端點**，存取 azure 服務（例如 Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB）和 azure 託管的客戶/合作夥伴服務。

私人端點是一種網路介面，可讓您私下且安全地連線到 Azure 私用連結所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

> [!IMPORTANT]
> 只有**專用**層才支援這項功能。 如需專用層的詳細資訊，請參閱[事件中樞專用的總覽](event-hubs-dedicated-overview.md)。 
>
> 這項功能目前為**預覽**狀態。 

>[!WARNING]
> 啟用私人端點可防止其他 Azure 服務與事件中樞進行互動。
>
> 實作「虛擬網路」時，不支援受信任的 Microsoft 服務。
>
> 無法與「虛擬網路」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - Azure 監視器（診斷設定）
> - Azure 串流分析
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 下列 Microsoft 服務必須位於虛擬網路上
> - Azure Web Apps
> - Azure Functions

## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 入口網站新增私人端點

### <a name="prerequisites"></a>Prerequisites

若要將事件中樞命名空間與 Azure 私人連結整合，您將需要下列實體或許可權：

- 事件中樞命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。
- 命名空間和虛擬網路的擁有者或參與者許可權。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 您的命名空間可以在不同的區域中。

您的私人端點會使用您虛擬網路中的私人 IP 位址。

### <a name="steps"></a>步驟
如果您已經有事件中樞命名空間，您可以遵循下列步驟來建立私人連結連線：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋列中，輸入**事件中樞**。
3. 從清單中選取您想要新增私人端點的**命名空間**。
4. 選取 [**設定**] 底下的 [**網路**] 索引標籤。
5. 選取頁面頂端的 [**私人端點連接（預覽）** ] 索引標籤。 如果您不是使用專用層的事件中樞，您會看到訊息：**只有在專用叢集底下建立的命名空間才支援事件中樞上的私人端點**連線。
6. 選取頁面頂端的 [ **+ 私用端點**] 按鈕。

    ![映像](./media/private-link-service/private-link-service-3.png)
7. 在 [**基本**] 頁面上，依照下列步驟執行： 
    1. 選取您要在其中建立私人端點的**Azure 訂**用帳戶。 
    2. 選取私人端點資源的**資源群組**。
    3. 輸入私人端點的 [**名稱**]。 
    5. 選取私人端點的 [**區域**]。 您的私用端點必須與您的虛擬網路位於相同區域，但可位於與您所連線的私人連結資源不同的區域中。 
    6. 選取頁面底部的 **[下一步：資源 >]** 按鈕。

        ![建立私人端點-基本概念頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 [**資源**] 頁面上，依照下列步驟執行：
    1. 針對 [連線方法]，如果您選取 **[連線到我的目錄中的 Azure 資源]**，請遵循下列步驟： 
        1. 選取您的**事件中樞命名空間**所在的**Azure 訂**用帳戶。 
        2. 針對 [**資源類型**]，選取 [node.js **/命名空間**] 作為 [**資源類型**]。
        3. 針對 [**資源**]，從下拉式清單中選取事件中樞的命名空間。 
        4. 確認**目標 subresource**已設定為**命名空間**。
        5. 選取頁面底部的 **[下一步：設定 >]** 按鈕。 
        
            ![建立私用端點-資源頁面](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果您選取 **[依資源識別碼或別名連線至 Azure 資源]**，請遵循下列步驟：
        1. 輸入**資源識別碼**或**別名**。 它可以是與您共用的資源識別碼或別名。
        2. 針對 [**目標子資源**]，輸入**命名空間**。 這是您的私用端點可以存取的子資源類型。
        3. 選擇性輸入**要求訊息**。 資源擁有者在管理私人端點連線時看到此訊息。
        4. 然後選取頁面底部的 **[下一步：設定 >]** 按鈕。

            ![建立私用端點-使用資源識別碼連接](./media/private-link-service/connect-resource-id.png)
9. **在 [設定**] 頁面上，您可以選取要在其中部署私人端點之虛擬網路中的子網。 
    1. 選取**虛擬網路**。 下拉式清單中只會列出目前所選訂用帳戶和位置中的虛擬網路。 
    2. 選取您選取之虛擬網路中的**子網**。 
    3. 選取頁面底部的 **[下一步：標記 >]** 按鈕。 

        ![建立私人端點-設定頁面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 [**標記**] 頁面上，建立您想要與私用端點資源建立關聯的任何標記（名稱和值）。 然後選取頁面底部的 [**審核] + [建立**] 按鈕。 
11. 在 [**審核**] 和 [建立] 上，檢查所有設定，然後選取 [**建立**] 以建立私人端點。
    
    ![建立私人端點-審查和建立頁面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 確認您已在端點清單中看到您所建立的私人端點連線。 在此範例中，因為您已連線到目錄中的 Azure 資源，而且您有足夠的許可權，所以私人端點會自動核准。 

    ![已建立私人端點](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 新增私人端點
下列範例示範如何使用 Azure PowerShell 來建立私用端點連接。 它不會為您建立專用的叢集。 依照[這篇文章](event-hubs-dedicated-cluster-create-portal.md)中的步驟，建立專用的事件中樞叢集。 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域
建立事件中樞網域的私人 DNS 區域，並建立與虛擬網路的關聯連結：

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>使用 Azure 入口網站管理私人端點

當您建立私人端點時，必須核准連線。 如果您要建立私人端點的資源位於您的目錄中，則您可以核准連線要求，前提是您有足夠的許可權。 如果您要連線到另一個目錄中的 Azure 資源，您必須等候該資源的擁有者核准您的連線要求。

佈建狀態有四種：

| 服務動作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連線會手動建立並等待 Private Link 資源擁有者進行核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>核准、拒絕或移除私人端點連接

1. 登入 Azure 入口網站。
2. 在搜尋列中，輸入**事件中樞**。
3. 選取您要管理的**命名空間**。
4. 選取 [**網路**] 索引標籤。
5. 根據您想要執行的作業： [核准]、[拒絕] 或 [移除]，移至下面的適當章節。

### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連接
1. 如果有任何暫止的連線，您會在布建狀態中看到以**擱置**方式列出的連線。 
2. 選取您想要核准的**私人端點**
3. 選取 [**核准**] 按鈕。

    ![映像](./media/private-link-service/approve-private-endpoint.png)
4. 在 [**核准連接**] 頁面上，新增批註（選擇性），然後選取 **[是]**。 如果您選取 [否]，則**不**會發生任何事。 
5. 您應該會在清單中看到私人端點連接的狀態已變更為 [**已核准**]。 

### <a name="reject-a-private-endpoint-connection"></a>拒絕私人端點連接

1. 如果您想要拒絕任何私人端點連線，不論是擱置中的要求或現有的連線，請選取連接，然後按一下 [**拒絕**] 按鈕。

    ![映像](./media/private-link-service/private-endpoint-reject-button.png)
2. 在 [**拒絕連接**] 頁面上，輸入批註（選擇性），然後選取 **[是]**。 如果您選取 [否]，則**不**會發生任何事。 
3. 您應該會在清單中看到私人端點連接的狀態已變更為 [已**拒絕**]。 

### <a name="remove-a-private-endpoint-connection"></a>移除私人端點連接

1. 若要移除私用端點連接，請在清單中選取它，然後選取工具列上的 [**移除**]。
2. 在 [**刪除**連線] 頁面上，選取 [**是]** 以確認刪除私人端點。 如果您選取 [否]，則**不**會發生任何事。
3. 您應該會看到狀態已變更為 [已**中斷**連線]。 然後，您會看到端點從清單中消失。

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作

您應該驗證私人端點資源的相同子網內的資源是否透過私人 IP 位址連線到您的事件中樞命名空間，以及它們是否具有正確的私人 DNS 區域整合。

首先，依照[在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟，建立虛擬機器。

在 [**網路**功能] 索引標籤中：

1. 指定 [**虛擬網路**] 和 [**子網**]。 您可以建立新的虛擬機器，或選取現有虛擬機器。 如果選取現有虛擬機器，請確定區域相符。
1. 指定**公用 IP**資源。
1. 在 [ **NIC 網路安全性群組**] 中，選取 [**無**]。
1. 在 [**負載平衡**] 中，選取 [**否**]。

開啟命令列並執行下列命令：

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果您執行 ns lookup 命令以透過公用端點解析事件中樞命名空間的 IP 位址，您會看到如下所示的結果：

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果您執行 ns lookup 命令以透過私人端點解析事件中樞命名空間的 IP 位址，您會看到如下所示的結果：

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和設計考慮

**價格**：如需定價資訊，請參閱 [Azure 私人連結定價](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 事件中樞的私人端點處於公開預覽狀態。 此功能已可在所有 Azure 公用區域中使用。

**每個事件中樞命名空間的私人端點數目上限**：120。

如需詳細資訊，請參閱 [Azure Private Link 服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link](../private-link/private-link-service-overview.md)
- 深入瞭解[Azure 事件中樞](event-hubs-about.md)
