---
title: 將 Azure 服務匯流排與 Azure 專用連結服務集成
description: 瞭解如何將 Azure 服務匯流排與 Azure 專用連結服務集成
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478002"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>將 Azure 服務匯流排與 Azure 專用連結集成（預覽）

Azure 專用連結服務使您能夠通過虛擬網路中的**專用終結點**訪問 Azure 服務（例如，Azure 服務匯流排、Azure 存儲和 Azure 宇宙資料庫）和 Azure 託管的客戶/合作夥伴服務。

專用終結點是一個網路介面，可私下安全地連接到由 Azure 專用連結提供支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

有關詳細資訊，請參閱什麼是[Azure 專用連結？](../private-link/private-link-overview.md)

> [!NOTE]
> Azure 服務匯流排**的高級**層支援此功能。 有關高級層的詳細資訊，請參閱[服務匯流排高級和標準消息級別](service-bus-premium-messaging.md)一文。
>
> 此功能當前處於**預覽狀態**。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 門戶添加專用終結點

### <a name="prerequisites"></a>Prerequisites

要將服務匯流排命名空間與 Azure 專用連結集成，您需要以下實體或許可權：

- 服務匯流排命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。
- 服務匯流排命名空間和虛擬網路的擁有者或參與者許可權。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 服務匯流排命名空間可以位於其他區域。 此外，您的專用終結點在虛擬網路中使用私人 IP 位址。

### <a name="steps"></a>steps

如果已有現有命名空間，則可以按照以下步驟創建專用終結點：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
2. 在搜索欄中，鍵入**服務匯流排**。
3. 從要向其添加專用終結點的清單中選擇**命名空間**。
4. 選擇 **"設置**"下的 **"網路**"選項卡。
5. 選擇頁面頂部的**專用終結點連接（預覽）** 選項卡
6. 選擇頁面頂部的 **"+ 專用終結點"** 按鈕。

    ![添加專用終結點按鈕](./media/private-link-service/private-link-service-3.png)
7. 在 **"基礎知識"** 頁上，按照以下步驟操作： 
    1. 選擇要在其中創建專用終結點的**Azure 訂閱**。 
    2. 選擇專用終結點**資源的資源組**。
    3. 輸入專用終結點**的名稱**。 
    5. 為專用終結點選擇**區域**。 專用終結點必須與虛擬網路位於同一區域中，但可以位於要連接到的專用連結資源的不同區域中。 
    6. 選擇 **"下一步："頁面底部的資源>** 按鈕。

        ![創建專用終結點 - 基礎知識頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 **"資源"** 頁上，按照以下步驟操作：
    1. 對於連接方法，如果選擇 **"連接到目錄中的 Azure 資源**"，請按照以下步驟操作：   
        1. 選擇**服務匯流排命名空間**存在的**Azure 訂閱**。 
        2. 對於**資源類型**，選擇**Microsoft.服務匯流排/****資源類型的**命名空間。
        3. 對於**資源**，從下拉清單中選擇服務匯流排命名空間。 
        4. 確認**目標子資源**設置為**命名空間**。
        5. 選擇 **"下一步"：頁面底部的"配置>"** 按鈕。 
        
            ![創建專用終結點 - 資源頁面](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. 如果選擇 **"按資源識別碼 或別名連接到 Azure 資源**"，請按照以下步驟操作：
        1. 輸入**資源識別碼**或**別名**。 它可以是一些人與您共用的資源識別碼 或別名。
        2. 對於**目標子資源**，輸入**命名空間**。 它是專用終結點可以訪問的子資源的類型。 
        3. （可選）輸入**請求消息**。 資源擁有者在管理專用終結點連接時看到此消息。 
        4. 然後，選擇**頁面底部的"下一步：配置>** 按鈕。 

            ![創建專用終結點 - 使用資源識別碼 進行連接](./media/private-link-service/connect-resource-id.png)
9. 在 **"配置"** 頁上，選擇虛擬網路中的子網，以選擇要部署專用終結點的位置。 
    1. 選擇**虛擬網路**。 下拉清單中僅列出當前選擇的訂閱和位置中的虛擬網路。 
    2. 在所選虛擬網路中選擇**子網**。 
    3. 選擇 **"下一步"：在頁面底部標記>** 按鈕。 

        ![創建專用終結點 - 配置頁面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 **"標記"** 頁上，創建要與專用終結點資源關聯的任何標記（名稱和值）。 然後，選擇頁面底部的 **"查看 + 創建**"按鈕。 
11. 在 **"審閱 + 創建**"上，查看所有設置，然後選擇 **"創建"** 以創建專用終結點。
    
    ![創建專用終結點 - 查看和創建頁面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 確認已創建專用終結點。 如果您是資源的擁有者，並且選擇了**連接方法****的目錄選項中的 Azure 資源**，則終結點連接應**自動批准**。 如果處於**掛起**狀態，請參閱[使用 Azure 門戶部分管理專用終結點](#manage-private-endpoints-using-azure-portal)。

    ![已創建專用終結點](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加專用終結點
下面的示例演示如何使用 Azure PowerShell 創建到服務匯流排命名空間的專用終結點連接。

您的私人端點和虛擬網路必須位於相同區域。 服務匯流排命名空間可以位於其他區域。 此外，您的專用終結點在虛擬網路中使用私人 IP 位址。

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>使用 Azure 門戶管理專用終結點

當您建立私人端點時，必須核准連線。 如果要為其創建專用終結點的資源位於目錄中，則可以批准連接請求，前提是您具有足夠的許可權。 如果要連接到另一個目錄中的 Azure 資源，則必須等待該資源的擁有者批准連接請求。

佈建狀態有四種：

| 服務操作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 連線會手動建立並等待 Private Link 資源擁有者進行核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | 私人連結資源擁有者已拒絕連線。 |
| 移除 | 已中斷連接 | 私人連結資源擁有者已移除連線，而私人端點變成參考性，且應該刪除以進行清除。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒絕或刪除專用終結點連接

1. 登入 Azure 入口網站。
1. 在搜索欄中，鍵入**服務匯流排**。
1. 選擇要管理的**命名空間**。
1. 選擇 **"網路"** 選項卡。
5. 轉到下面的相應部分，具體取決於您想要的操作：批准、拒絕或刪除。 

### <a name="approve-a-private-endpoint-connection"></a>批准專用終結點連接

1. 如果存在任何掛起的連接，您將看到與 **"掛起"** 處於預配狀態的連接。 
2. 選擇要批准的**專用終結點**
3. 選擇"**批准**"按鈕。

    ![批准專用終結點](./media/private-link-service/private-endpoint-approve.png)
4. 在 **"批准連接**"頁上，輸入可選**注釋**，然後選擇 **"是**"。 如果選擇 **"否**"，則不發生任何操作。 

    ![批准連接頁](./media/private-link-service/approve-connection-page.png)
5. 您應該看到清單中的連接狀態更改為 **"已批准**"。 

    ![連接狀態 - 已批准](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>拒絕專用終結點連接

1. 如果要拒絕任何專用終結點連接，無論是掛起的請求還是之前已批准的現有連接，請選擇終結點連接並按一下 **"拒絕**"按鈕。

    ![拒絕按鈕](./media/private-link-service/private-endpoint-reject.png)
2. 在 **"拒絕連接"** 頁上，輸入可選注釋，然後選擇 **"是**"。 如果選擇 **"否**"，則不發生任何操作。 

    ![拒絕連接頁](./media/private-link-service/reject-connection-page.png)
3. 您應該在清單中看到連接的狀態已更改**為"已拒絕**"。 

    ![終結點被拒絕](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>刪除專用終結點連接

1. 要刪除專用終結點連接，請在清單中選擇它，並在工具列上選擇 **"刪除**"。 

    ![[移除] 按鈕](./media/private-link-service/remove-endpoint.png)
2. 在 **"刪除連接**"頁上，選擇 **"是**"以確認刪除專用終結點。 如果選擇 **"否**"，則不發生任何操作。 

    ![刪除連接頁](./media/private-link-service/delete-connection-page.png)
3. 您應該看到狀態更改為 **"已中斷連線**"。 然後，您將看到終結點從清單中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作

應驗證專用終結點資源的同一子網中的資源是否通過私人 IP 位址連接到服務匯流排命名空間，並且它們具有正確的專用 DNS 區域集成。

首先，依照[在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟，建立虛擬機器。

在 **"網路"** 選項卡中：

1. 指定**虛擬網路和****子網**。 您可以建立新的虛擬機器，或選取現有虛擬機器。 如果選取現有虛擬機器，請確定區域相符。
1. 指定**公共 IP**資源。
1. 對於**NIC 網路安全性群組**，選擇 **"無**"。
1. 對於**負載平衡**，選擇 **"否**"。

開啟命令列並執行下列命令：

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

如果運行 ns 查找命令來解決公共終結點上的服務匯流排命名空間的 IP 位址，您將看到如下所示的結果：

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

如果運行 ns 查找命令以解決專用終結點上的服務匯流排命名空間的 IP 位址，您將看到如下所示的結果：

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和設計考量

**定價**：有關定價資訊，請參閱[Azure 專用連結定價](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 服務匯流排的專用終結點處於公共預覽版。 此功能已可在所有 Azure 公用區域中使用。

**每個服務匯流排命名空間的最大專用終結點數**：120。

有關詳細資訊，請參閱[Azure 專用連結服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link](../private-link/private-link-service-overview.md)
- 瞭解有關[Azure 服務匯流排](service-bus-messaging-overview.md)的更多
