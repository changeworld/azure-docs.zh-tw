---
title: 整合 Azure 服務匯流排與 Azure 私人連結服務
description: 瞭解如何整合 Azure 服務匯流排與 Azure 私人連結服務
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: 33e6ce1d5feb50080b00fcbecdeb9e512980eab6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141939"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>整合 Azure 服務匯流排與 Azure 私人連結（預覽）

Azure 私用連結服務可讓您透過虛擬網路中的**私人端點**，存取 azure 服務（例如 Azure 服務匯流排、Azure 儲存體和 Azure Cosmos DB）和 azure 託管的客戶/合作夥伴服務。

私人端點是一種網路介面，可讓您私下且安全地連線到 Azure 私用連結所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute 或 VPN 連線或公用 IP 位址。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您可連線到 Azure 資源的執行個體，以取得最高層級的存取控制細微性。

如需詳細資訊，請參閱[何謂 Azure Private Link？](../private-link/private-link-overview.md)

>[!WARNING]
> 執行私人端點可防止其他 Azure 服務與服務匯流排進行互動。
>
> 使用虛擬網路時，不支援受信任的 Microsoft 服務。
>
> 無法與「虛擬網路」搭配運作的常見 Azure 案例 (請注意，這**不是**完整的清單) -
> - 與 Azure 事件方格的整合
> - Azure IoT 中樞路由
> - Azure IoT Device Explorer
>
> 虛擬網路上必須有下列 Microsoft 服務
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Azure 服務匯流排的**高階層級**支援這項功能。 如需高階層的詳細資訊，請參閱[服務匯流排 premium 和標準通訊層](service-bus-premium-messaging.md)一文。
>
> 這項功能目前為**預覽**狀態。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 入口網站新增私人端點

### <a name="prerequisites"></a>先決條件

若要將服務匯流排命名空間與 Azure 私人連結整合，您將需要下列實體或許可權：

- 服務匯流排命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。
- 服務匯流排命名空間和虛擬網路的擁有者或參與者許可權。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 您的服務匯流排命名空間可以在不同的區域中。 而且，您的私用端點會在您的虛擬網路中使用私人 IP 位址。

### <a name="steps"></a>steps

如果您已經有現有的命名空間，您可以遵循下列步驟來建立私用端點：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋列中，輸入**服務匯流排**。
3. 從清單中選取您想要新增私人端點的**命名空間**。
4. 選取 [**設定**] 底下的 [**網路**] 索引標籤。
5. 選取頁面頂端的 [**私人端點連接（預覽）** ] 索引標籤
6. 選取頁面頂端的 [ **+ 私用端點**] 按鈕。

    ![[新增私用端點] 按鈕](./media/private-link-service/private-link-service-3.png)
7. 在 [**基本**] 頁面上，依照下列步驟執行： 
    1. 選取您要在其中建立私人端點的**Azure 訂**用帳戶。 
    2. 選取私人端點資源的**資源群組**。
    3. 輸入私人端點的 [**名稱**]。 
    5. 選取私人端點的 [**區域**]。 您的私用端點必須與您的虛擬網路位於相同區域，但可能位於與您所連線之私人連結資源不同的區域中。 
    6. 選取頁面底部的 **[下一步：資源 >]** 按鈕。

        ![建立私人端點-基本概念頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 [**資源**] 頁面上，依照下列步驟執行：
    1. 針對 [連線方法]，如果您選取 **[連線到我的目錄中的 Azure 資源]**，請遵循下列步驟：   
        1. 選取您的**服務匯流排命名空間**所在的**Azure 訂**用帳戶。 
        2. 針對 [**資源類型**]，針對 [**資源類型**] 選取 [ **Microsoft]。**
        3. 針對 [**資源**]，從下拉式清單中選取服務匯流排的命名空間。 
        4. 確認**目標 subresource**已設定為**命名空間**。
        5. 選取頁面底部的 **[下一步：設定 >]** 按鈕。 
        
            ![建立私用端點-資源頁面](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. 如果您選取 **[依資源識別碼或別名連線至 Azure 資源]**，請遵循下列步驟：
        1. 輸入**資源識別碼**或**別名**。 它可以是其他人與您共用的資源識別碼或別名。 取得資源識別碼的最簡單方式是流覽至 Azure 入口網站中的服務匯流排命名空間，並從`/subscriptions/`開始複製 URI 的部分。 如需範例，請參閱下圖。 
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
12. 確認已建立私用端點。 如果您是資源的擁有者，並已在連線**方法****中選取 [連線到我的目錄中的 Azure 資源]** 選項，則應該**自動核准**端點連線。 如果它處於 [**暫**止] 狀態，請參閱[使用 Azure 入口網站管理私人端點](#manage-private-endpoints-using-azure-portal)一節。

    ![已建立私人端點](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 新增私人端點
下列範例示範如何使用 Azure PowerShell 來建立服務匯流排命名空間的私用端點連接。

您的私人端點和虛擬網路必須位於相同區域。 您的服務匯流排命名空間可以在不同的區域中。 而且，您的私用端點會在您的虛擬網路中使用私人 IP 位址。

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
1. 在搜尋列中，輸入**服務匯流排**。
1. 選取您要管理的**命名空間**。
1. 選取 [**網路**] 索引標籤。
5. 根據您想要執行的作業： [核准]、[拒絕] 或 [移除]，移至下面的適當章節。 

### <a name="approve-a-private-endpoint-connection"></a>核准私人端點連接

1. 如果有任何暫止的連線，您會在布建狀態中看到以**擱置**方式列出的連線。 
2. 選取您想要核准的**私人端點**
3. 選取 [**核准**] 按鈕。

    ![核准私用端點](./media/private-link-service/private-endpoint-approve.png)
4. 在 [**核准連接**] 頁面上，輸入選擇性的**批註**，然後選取 **[是]**。 如果您選取 [否]，則**不**會發生任何事。 

    ![[核准連接] 頁面](./media/private-link-service/approve-connection-page.png)
5. 您應該會在清單中看到連線的狀態已變更為 [**已核准**]。 

    ![線上狀態-已核准](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>拒絕私人端點連接

1. 如果您想要拒絕任何私人端點連線，不論是擱置中的要求還是先前已核准的現有連線，請選取端點連線，然後按一下 [**拒絕**] 按鈕。

    ![拒絕按鈕](./media/private-link-service/private-endpoint-reject.png)
2. 在 [**拒絕連接**] 頁面上，輸入選擇性的批註，然後選取 **[是]**。 如果您選取 [否]，則**不**會發生任何事。 

    ![拒絕連接頁面](./media/private-link-service/reject-connection-page.png)
3. 您應該會在 [已**拒絕**] 清單中看到連接的狀態。 

    ![已拒絕端點](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>移除私人端點連接

1. 若要移除私用端點連接，請在清單中選取它，然後選取工具列上的 [**移除**]。 

    ![[移除] 按鈕](./media/private-link-service/remove-endpoint.png)
2. 在 [**刪除**連線] 頁面上，選取 [**是]** 以確認刪除私人端點。 如果您選取 [否]，則**不**會發生任何事。 

    ![刪除連接頁面](./media/private-link-service/delete-connection-page.png)
3. 您應該會看到狀態已變更為 [已**中斷**連線]。 然後，您會看到端點從清單中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作

您應該驗證私人端點資源的相同子網內的資源是否透過私人 IP 位址連線到您的服務匯流排命名空間，以及它們是否具有正確的私人 DNS 區域整合。

首先，依照[在 Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟，建立虛擬機器。

在 [**網路**功能] 索引標籤中：

1. 指定 [**虛擬網路**] 和 [**子網**]。 您可以建立新的虛擬機器，或選取現有虛擬機器。 如果選取現有虛擬機器，請確定區域相符。
1. 指定**公用 IP**資源。
1. 針對 [ **NIC 網路安全性群組**]，選取 [**無**]。
1. 針對 [**負載平衡**]，選取 [**否**]。

開啟命令列並執行下列命令：

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

如果您執行 ns lookup 命令以透過公用端點解析服務匯流排命名空間的 IP 位址，您會看到如下所示的結果：

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

如果您執行 ns lookup 命令以透過私人端點解析服務匯流排命名空間的 IP 位址，您會看到如下所示的結果：

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和設計考量

**價格**：如需定價資訊，請參閱 [Azure 私人連結定價](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 服務匯流排的私人端點處於公開預覽狀態。 此功能已可在所有 Azure 公用區域中使用。

**每個服務匯流排命名空間的私人端點數目上限**：120。

如需詳細資訊，請參閱 [Azure Private Link 服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link](../private-link/private-link-service-overview.md)
- 深入瞭解[Azure 服務匯流排](service-bus-messaging-overview.md)
