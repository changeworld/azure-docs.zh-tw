---
title: 將 Azure 事件中心與 Azure 專用連結服務整合
description: 瞭解如何將 Azure 事件中心與 Azure 專用連結服務整合
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383640"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>將 Azure 事件中心與 Azure 專用連結(預覽)整合
Azure 專用連結服務使您能夠透過虛擬網路中的**專用終結點**訪問 Azure 服務(例如,Azure 事件中心、Azure 儲存和 Azure 宇宙資料庫)和 Azure 託管的客戶/合作夥伴服務。

專用終結點是一個網路介面,可私下安全地連接到由 Azure 專用連結提供支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

> [!NOTE]
> 此功能僅支援**專用**層。 有關專用層的詳細資訊,請參閱[活動中心專用概述](event-hubs-dedicated-overview.md)。 
>
> 此功能目前處於**預覽狀態**。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 門戶新增專用終結點

### <a name="prerequisites"></a>Prerequisites

要將事件中心命名空間與 Azure 專用連結整合,您需要以下實體或許可權:

- 事件中樞命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。
- 命名空間和虛擬網路的擁有者或參與者許可權。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 您的命名空間可以位於其他區域。

您的私人端點會使用您虛擬網路中的私人 IP 位址。

### <a name="steps"></a>步驟
如果您已經有一個事件中心命名空間,可以透過以下步驟建立專用連結連線:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋列中,鍵入**事件中心**。
3. 從您要加入專用的清單中選擇**命名空間**。
4. 選擇 **「設定**」下的 **「網路**」選項卡。
5. 選擇頁面頂部的**專用終結點連接(預覽)** 選項卡。 如果不使用活動中心專用層,則會看到一條消息:**事件中心上的專用終結點連接僅由在專用群集下創建的命名空間支援**。
6. 選擇頁面頂部的 **「+ 專用終結點」** 按鈕。

    ![映像](./media/private-link-service/private-link-service-3.png)
7. 在 **"基礎知識"** 頁上,按照以下步驟操作: 
    1. 選擇此選項, 您可以建立專用的動作的**Azure 訂閱**。 
    2. 選擇專用的清單**中的資源群組**。
    3. 輸入專用的終結點**的名稱**。 
    5. 為專用的終結點選擇**區域**。 專用終結點必須與虛擬網路位於同一區域中,但可以位於要連接到的專用連結資源的不同區域中。 
    6. 選擇 **「下一步」頁面底部的資源>** 按鈕。

        ![建立專用的終結點 -基礎知識頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 **「資源」** 頁上,按照以下步驟操作:
    1. 對於連接方法,如果選擇 **「連接到目錄中的 Azure 資源**」,請按照以下步驟操作: 
        1. 選擇**事件中心命名空間**的**Azure 訂閱**。 
        2. 此**資源類型**,選擇 **「資源類型**」的**Microsoft.eventHub/命名空間**。
        3. 對於**資源**,從下拉清單中選擇事件中心命名空間。 
        4. 確認**目標子資源**設定為**命名空間**。
        5. 選擇 **「下一步」:頁面底部的「設定>」** 按鈕。 
        
            ![建立您的專屬的設定點 - 資源頁面](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果選擇 **「按資源代碼或別名連線到 Azure 資源**」,請按照以下步驟操作:
        1. 輸入**資源識別碼**或**別名**。 它可以是一些人與您共享的資源 ID 或別名。
        2. 此**目標子資源**,輸入**命名空間**。 它是專用終結點可以訪問的子資源的類型。
        3. ( 選擇性的 )輸入**要求訊息**。 資源擁有者在管理專用終結點連接時看到此消息。
        4. 然後,選擇**頁面底部的"下一步:配置>** 按鈕。

            ![建立您的專屬的動作 - 使用資源代碼連線](./media/private-link-service/connect-resource-id.png)
9. 在 **「設定」** 頁上,選擇虛擬網路中的子網路,以選擇要部署專用終結點的位置。 
    1. 選擇**虛擬網路**。 下拉清單中僅列出當前選擇的訂閱和位置中的虛擬網路。 
    2. 選擇**子網路**。 
    3. 選擇 **「下一步」:在頁面底部標記>** 按鈕。 

        ![建立專用的佈圖 - 設定頁面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 **「標記」** 頁上,創建要與專用終結點資源關聯的任何標記(名稱和值)。 然後,選擇頁面底部的 **「查看 + 創建**」按鈕。 
11. 在 **「審閱 + 創建**」上,查看所有設置,然後選擇 **「創建」** 以創建專用終結點。
    
    ![建立專用的終結點 - 查看與建立頁面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 確認您看到創建的專用終結點連接顯示在終結點清單中。 在此範例中,私有終結點是自動批准的,因為您連接到目錄中的 Azure 資源,並且您具有足夠的許可權。 

    ![已建立專用終結點](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 新增專用終結點
下面的範例展示如何使用 Azure PowerShell 創建專用終結點連接。 它不會為您創建專用群集。 按照[本文](event-hubs-dedicated-cluster-create-portal.md)中的步驟創建專用的事件中心群集。 

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

### <a name="configure-the-private-dns-zone"></a>設定專用 DNS 區域
為事件中心網域建立專用 DNS 區域,並與虛擬網路建立關聯連結:

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

## <a name="manage-private-endpoints-using-azure-portal"></a>使用 Azure 門戶管理專用終結點

當您建立私人端點時，必須核准連線。 如果要為其創建專用終結點的資源位於目錄中,則可以批准連接請求,前提是您具有足夠的許可權。 如果要連接到另一個目錄中的 Azure 資源,則必須等待該資源的擁有者批准連接請求。

佈建狀態有四種：

| 服務操作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連線會手動建立並等待 Private Link 資源擁有者進行核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒絕或移除專用終結點連線

1. 登入 Azure 入口網站。
2. 在搜尋列中,鍵入**事件中心**。
3. 選擇要管理的**命名空間**。
4. 選擇 **「網路」** 選項卡。
5. 跳到下面的相應部分,具體取決於您想要的操作:批准、拒絕或刪除。

### <a name="approve-a-private-endpoint-connection"></a>批准專用終結點連線
1. 如果存在任何掛起的連接,您將看到與 **「掛起」** 處於預配狀態的連接。 
2. 選擇要批准的**專用終結點**
3. 選擇「**批准**」按鈕。

    ![映像](./media/private-link-service/approve-private-endpoint.png)
4. 在 **「批准連接**」頁上,添加註釋(可選),然後選擇 **"是**"。 如果選擇 **"否**",則不發生任何操作。 
5. 您應該看到清單中的專用終結點連接的狀態更改為 **「已批准**」 。 

### <a name="reject-a-private-endpoint-connection"></a>拒絕專用終結點連線

1. 如果要拒絕任何專用終結點連接,無論是掛起的請求還是現有連接,請選擇連接並按下 **「拒絕**」按鈕。

    ![映像](./media/private-link-service/private-endpoint-reject-button.png)
2. 在 **「拒絕連接」** 頁上,輸入註釋(可選),然後選擇 **"是**"。 如果選擇 **"否**",則不發生任何操作。 
3. 您應該看到清單中的專用終結點連接的狀態更改為 **「已拒絕**」。 

### <a name="remove-a-private-endpoint-connection"></a>移除專用終結點連線

1. 要刪除專用終結點連接,請在清單中選擇它,並在工具列上選擇 **「刪除**」。。
2. 在 **「刪除連接**」頁上,選擇 **「以**確認刪除專用終結點。 如果選擇 **"否**",則不發生任何操作。
3. 您應該看到狀態更改為 **「已斷開連接**」。 然後,您將看到終結點從清單中消失。

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作

應驗證專用終結點資源的同一子網中的資源是否通過專用 IP 位址連接到事件中心命名空間,並且它們具有正確的專用 DNS 區域集成。

首先，依照[在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟，建立虛擬機器。

在 **「網路」** 選項卡中:

1. 指定**虛擬網路與****子網路**。 您可以建立新的虛擬機器，或選取現有虛擬機器。 如果選取現有虛擬機器，請確定區域相符。
1. 指定**公共 IP**資源。
1. 在**NIC 網路安全組中**,選擇 **"無**"。
1. 在 **「負載平衡**」中,選擇 **「否**」。。

開啟命令列並執行下列命令：

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果執行 ns 搜尋指令以在公共終結點上解析事件中心命名空間的 IP 位址,您將看到如下所示的結果:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果執行 ns 查找指令以解決事件中心命名空間在專用終結點上的 IP 位址,您將看到如下所示的結果:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制與設計注意事項

**價格**：如需定價資訊，請參閱 [Azure 私人連結定價](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**: Azure 事件中心的私人終結點處於公共預覽版。 此功能已可在所有 Azure 公用區域中使用。

**每個事件中心命名空間的最大專用終結點數**:120。

如需詳細資訊，請參閱 [Azure Private Link 服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link](../private-link/private-link-service-overview.md)
- 瞭解有關[Azure 事件中心數](event-hubs-about.md)
