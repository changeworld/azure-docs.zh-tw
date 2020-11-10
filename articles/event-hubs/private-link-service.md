---
title: 將 Azure 事件中樞與 Azure Private Link 服務整合
description: 了解如何將 Azure 事件中樞與 Azure Private Link 服務整合
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427746"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>允許透過私人端點存取 Azure 事件中樞命名空間 
Azure Private Link 服務可讓您透過虛擬網路中的 **私人端點** 存取各 Azure 服務 (例如 Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB)，以及 Azure 裝載的客戶/合作夥伴服務。

私人端點是一種網路介面，可讓您以私人且安全的方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您虛擬網路中的私人 IP 位址，有效地將服務帶入您的虛擬網路。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

> [!WARNING]
> 啟用私人端點可防止其他 Azure 服務與事件中樞互動。  封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 例外狀況是，即使啟用私人端點，您還是可以允許從某些受信任的服務存取事件中樞資源。 如需信任的服務清單，請參閱 [信任的服務](#trusted-microsoft-services)。

>[!NOTE]
> **標準** 層和 **專用** 層都支援這項功能。 **基本** 層中不支援此功能。

## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 入口網站新增私人端點

### <a name="prerequisites"></a>Prerequisites

若要將事件中樞命名空間與 Azure Private Link 整合，您將需要下列實體或權限：

- 事件中樞命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。 您可以使用 **預設** 子網。 
- 命名空間和虛擬網路的擁有者或參與者權限。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 您的命名空間可以位於不同區域。

您的私人端點會使用您虛擬網路中的私人 IP 位址。

### <a name="steps"></a>步驟
如果您已經有事件中樞命名空間，您可以遵循下列步驟來建立私人連結連線：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋列中輸入 **事件中樞** 。
3. 從清單中選取您要新增私人端點的 **命名空間** 。
4. 在左側功能表的 [ **設定** ] 底下選取 [ **網路** ]。

    > [!NOTE]
    > 您只會看到標準或 **專用** 命名空間的 [ **網路** ] 索引 **卷** 標。 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="[網路] 索引標籤-選取的網路選項" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > 預設會選取 [ **選取的網路** ] 選項。 如果您未指定 IP 防火牆規則或新增虛擬網路，可以透過公用網際網路來存取命名空間。 
1. 選取頁面頂端的 [私人端點連線] 索引標籤。 
1. 選取頁面頂端的 [+ 私人端點] 按鈕。

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="網路頁面-私人端點連線索引標籤-新增私人端點連結":::
7. 在 [基本] 頁面上，遵循下列步驟： 
    1. 選取您要在其中建立私人端點的 **Azure 訂閱** 。 
    2. 選取私人端點資源的 **資源群組** 。
    3. 輸入私人端點的 **名稱** 。 
    5. 選取私人端點的 **區域** 。 私人端點必須與虛擬網路位於相同區域，但可與將要連線的目的地私人連結資源位於不同區域。 
    6. 完成時，選取 [下一步:資源 >] 按鈕 (位於頁面底部)。

        ![建立私人端點 - [基本] 頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 [資源] 頁面上，遵循下列步驟：
    1. 針對 [連線方法]，如果您選取 [連線至我目錄中的 Azure 資源]，請遵循下列步驟： 
        1. 選取您的 **事件中樞命名空間** 所在的 **Azure 訂用帳戶** 。 
        2. 針對 [資源類型]，選取 [Microsoft.Storage/namespaces] 作為 **資源類型** 。
        3. 針對 [資源]，從下拉式清單中選取事件中樞命名空間。 
        4. 確認 [目標 SubResource] 已設定為 [命名空間]。
        5. 完成時，選取 [下一步:設定 >] 按鈕 (位於頁面底部)。 
        
            ![建立私人端點 - [資源] 頁面](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果選取 [依資源識別碼或別名連線至 Azure 資源]，請遵循下列步驟：
        1. 輸入 **資源識別碼** 或 **別名** 。 其可以是某人與您共用的資源識別碼或別名。 取得資源識別碼最簡單的方式，便是瀏覽到 Azure 入口網站中的事件中樞命名空間，然後複製 URI 從 `/subscriptions/` 開始的部分。 如需範例，請參閱下圖。 
        2. 針對 [目標子資源]，請輸入 **命名空間** 。 其為您的私人端點可以存取的子資源類型。
        3. (選擇性) 輸入 **要求訊息** 。 資源擁有者會在管理私人端點連線時看見此訊息。
        4. 然後，選取 [下一步:設定 >] 按鈕 (位於頁面底部)。

            ![建立私人端點 - 使用資源識別碼連線](./media/private-link-service/connect-resource-id.png)
9. 在 [設定] 頁面上，您會選取在虛擬網路中要部署私人端點的子網路。 
    1. 選取 **虛擬網路** 。 下拉式清單只會列出目前所選訂閱與位置中的虛擬網路。 
    2. 選取您所選虛擬網路中的 **子網路** 。 
    3. 完成時，選取 [下一步:標記 >] 按鈕 (位於頁面底部)。 

        ![建立私人端點 - [設定] 頁面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 [標記] 頁面上，建立想要與私人端點資源建立關聯的任何標記 (名稱和值)。 然後選取頁面底部的 [檢閱 + 建立] 按鈕。 
11. 在 [檢閱 + 建立] 上，檢閱所有設定，然後選取 [建立] 來建立私人端點。
    
    ![建立私人端點 - 檢閱及建立頁面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 確認您可以在端點清單中看見您所建立的私人端點連線。 在此範例中，系統會自動核准私人端點，因為您是連線到您目錄中的 Azure 資源，且您具有足夠的權限。 

    ![已建立私人端點](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

若要允許信任的服務存取您的命名空間，請切換至 [ **網路** ] 頁面上的 [ **防火牆與虛擬網路** ] 索引標籤，然後選取 [ **是]** ， **允許信任的 Microsoft 服務略過此防火牆？** 。 

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 新增私人端點
下列範例會示範如何使用 Azure PowerShell 來建立私人端點連線。 其不會為您建立專用叢集。 請遵循[此文章](event-hubs-dedicated-cluster-create-portal.md)中的步驟，以建立專用的事件中樞叢集。 

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
建立事件中樞網域的私人 DNS 區域，然後與虛擬網路建立關聯連結：

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

當您建立私人端點時，必須核准連線。 如果正要建立私人端點的資源位於目錄中，則在具有足夠權限的情況下，即可核准連線要求。 如果正要連線到位於另一個目錄中的 Azure 資源，則必須等候該資源的擁有者核准連線要求。

佈建狀態有四種：

| 服務動作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連線會手動建立並等待 Private Link 資源擁有者進行核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>核准、拒絕或移除私人端點連線

1. 登入 Azure 入口網站。
2. 在搜尋列中輸入 **事件中樞** 。
3. 選取您要管理的 **命名空間** 。
4. 選取 [網路] 索引標籤。
5. 根據您想要執行的作業 (核准、拒絕或移除)，移至下列適當的小節。

### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連線
1. 如果有任何暫止的連線，您會在佈建狀態中看到以 [擱置] 列出的連線。 
2. 選取您想要核准的 **私人端點**
3. 選取 [核准] 按鈕。

    ![核准私人端點](./media/private-link-service/approve-private-endpoint.png)
4. 在 [核准連線] 頁面上，新增註解 (選擇性)，然後選取 [是]。 如果您選取 [否]，則不會發生任何事。 
5. 您應該會看見清單中私人端點連線的狀態變更為 [已核准]。 

### <a name="reject-a-private-endpoint-connection"></a>拒絕私人端點連線

1. 如果您想拒絕任何私人端點連線 (不論其為暫止要求或現有連線)，請選取該連線，然後按一下 [拒絕] 按鈕。

    ![拒絕私人端點](./media/private-link-service/private-endpoint-reject-button.png)
2. 在 [拒絕連線] 頁面上，輸入註解 (選擇性)，然後選取 [是]。 如果您選取 [否]，則不會發生任何事。 
3. 您應該會看見清單中私人端點連線的狀態變更為 [已拒絕]。 

### <a name="remove-a-private-endpoint-connection"></a>移除私人端點連線

1. 若要移除私人端點連線，請在清單中加以選取，然後選取工具列上的 [移除]。
2. 在 [刪除連線] 頁面上，選取 [是] 以確認刪除私人端點。 如果您選取 [否]，則不會發生任何事。
3. 您應該會看見狀態變更為 [已中斷連線]。 然後，您將會看見端點從清單中消失。

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作

您應驗證私人端點的虛擬網路內的資源是透過私人 IP 位址連線到您的事件中樞命名空間，而且它們具有正確的私人 DNS 區域整合。

首先，依照[在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟，建立虛擬機器。

在 [網路] 索引標籤中： 

1. 指定 [虛擬網路] 和 [子網路]。 您必須選取您已在其上方部署私人端點的虛擬網路。
2. 指定 **公用 IP** 資源。
3. 針對 [NIC 網路安全性群組]，選取 [無]。
4. 針對 [負載平衡]，選取 [否]。

連線到 VM、開啟命令列，然後執行下列命令：

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

您應該會看到如下所示的結果。 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和設計考量

**價格** ：如需定價資訊，請參閱 [Azure 私人連結定價](https://azure.microsoft.com/pricing/details/private-link/)。

**限制** ：此功能已可在所有 Azure 公用區域中使用。

**每個事件中樞命名空間的私人端點數目上限** ：120。

如需詳細資訊，請參閱 [Azure Private Link 服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link](../private-link/private-link-service-overview.md)
- 深入了解 [Azure 事件中樞](event-hubs-about.md)
