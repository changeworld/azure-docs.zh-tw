---
title: 整合 Azure 轉送與 Azure 私人連結服務
description: 瞭解如何整合 Azure 轉送與 Azure 私人連結服務
services: service-bus-relay
author: spelluru
ms.author: spelluru
ms.date: 05/07/2020
ms.service: service-bus-relay
ms.topic: article
ms.openlocfilehash: ad57ea4ddeab2fb2af0da68d199445d9737a67cd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984456"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>整合 Azure 轉送與 Azure 私人連結（預覽）
Azure**私用連結服務**可讓您透過虛擬網路中的私人端點，存取 azure 服務（例如 Azure 轉送、Azure 服務匯流排、Azure 事件中樞、Azure 儲存體和 Azure Cosmos DB）和 azure 託管的客戶/合作夥伴服務。 如需詳細資訊，請參閱[何謂 Azure Private Link (預覽)？](../private-link/private-link-overview.md)

**私人端點**是一種網路介面，可讓您在虛擬網路中執行的工作負載，私下且安全地連線到具有**私人連結資源**的服務（例如，轉送命名空間）。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務的所有流量都可以透過私人端點路由傳送，因此不需要閘道、NAT 裝置、ExpressRoute、VPN 連線或公用 IP 位址。 您的虛擬網路與服務之間的流量會透過 Microsoft 骨幹網路來進行，以消除公開網際網路的暴露。 您可以藉由允許連接到特定 Azure 轉送命名空間，在存取控制中提供細微性層級。 


> [!IMPORTANT]
> 這項功能目前為**預覽**狀態。 
>
> 我們目前在傳送者用戶端上支援私用連結連接。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 入口網站新增私人端點

### <a name="prerequisites"></a>Prerequisites
若要整合 Azure 轉送命名空間與 Azure 私人連結（預覽），您將需要下列實體或許可權：

- Azure 轉送命名空間。
- Azure 虛擬網路。
- 虛擬網路中的子網路。
- 虛擬網路上的擁有者或參與者許可權。

您的私人端點和虛擬網路必須位於相同區域。 當您使用入口網站選取私人端點的區域時，其只會自動篩選該區域中的虛擬網路。 您的命名空間可以在不同的區域中。

您的私人端點會使用您虛擬網路中的私人 IP 位址。

### <a name="steps"></a>步驟
如需在其中建立新的 Azure 轉送命名空間和實體的逐步指示，請參閱[使用 Azure 入口網站建立 Azure 轉送命名空間](relay-create-namespace-portal.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在搜尋**列中，輸入轉送。**
3. 從清單中選取您想要新增私人端點的**命名空間**。
4. 選取 [**設定**] 底下的 [**網路**] 索引標籤。
5. 選取頁面頂端的 [**私人端點連接（預覽）** ] 索引標籤
6. 選取頁面頂端的 [ **+ 私用端點**] 按鈕。

    ![[新增私用端點] 按鈕](./media/private-link-service/add-private-endpoint-button.png)
7. 在 [**基本**] 頁面上，依照下列步驟執行： 
    1. 選取您要在其中建立私人端點的**Azure 訂**用帳戶。 
    2. 選取私人端點資源的**資源群組**。
    3. 輸入私人端點的 [**名稱**]。 
    5. 選取私人端點的 [**區域**]。 您的私用端點必須與您的虛擬網路位於相同區域，但可以位於與您所連線之 Azure 轉送命名空間不同的區域中。 
    6. 選取頁面底部的 **[下一步：資源 >]** 按鈕。

        ![建立私人端點-基本概念頁面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 [**資源**] 頁面上，依照下列步驟執行：
    1. 針對 [連線方法]，如果您選取 **[連線到我的目錄中的 Azure 資源]**，則您會有命名空間的擁有者或參與者存取權，而且該命名空間位於與私人端點相同的目錄中，請遵循下列步驟： 
        1. 選取您的**Azure 轉送命名空間**所在的**Azure 訂**用帳戶。 
        2. 針對 [**資源類型**]，選取 [ **Microsoft 轉送/命名空間**] 作為 [**資源類型**]。
        3. 針對 [**資源**]，從下拉式清單中選取轉送命名空間。 
        4. 確認**目標 subresource**已設定為**命名空間**。
        5. 選取頁面底部的 **[下一步：設定 >]** 按鈕。 
        
            ![建立私用端點-資源頁面](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果您選取 [**依資源識別碼或別名連線至 Azure 資源]** ，因為命名空間不在與私人端點相同的目錄下，請遵循下列步驟：
        1. 輸入**資源識別碼**或**別名**。 它可以是其他人與您共用的資源識別碼或別名。 取得資源識別碼的最簡單方式是流覽至 Azure 入口網站中的 Azure 轉送命名空間，並從`/subscriptions/`開始複製 URI 的部分。 範例如下：`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. 針對 [**目標子資源**]，輸入**命名空間**。 這是您的私用端點可以存取的子資源類型。
        3. 選擇性輸入**要求訊息**。 資源擁有者在管理私人端點連線時看到此訊息。
        4. 然後選取頁面底部的 **[下一步：設定 >]** 按鈕。

            ![建立私用端點-使用資源識別碼連接](./media/private-link-service/connect-resource-id.png)
9. **在 [設定**] 頁面上，您可以選取要在其中部署私人端點之虛擬網路中的子網。 
    1. 選取**虛擬網路**。 下拉式清單中只會列出目前所選訂用帳戶和位置中的虛擬網路。 
    2. 選取您選取之虛擬網路中的**子網**。 
    3. 如果您想要整合私人端點與私人 DNS 區域，請啟用 [**與私人 dns 區域整合**]。 
    
        若要私下與您的私人端點連接，您需要 DNS 記錄。 我們建議您將私人端點與**私人 DNS 區域**整合。 您也可以利用自己的 DNS 伺服器，或使用虛擬機器上的主機檔案來建立 DNS 記錄。 如需詳細資訊，請參閱[Azure 私人端點 DNS](../private-link/private-endpoint-dns.md)設定。 在此範例中，會選取 [**與私人 dns 區域整合**] 選項，並為您建立私人 dns 區域。 
    3. 選取頁面底部的 **[下一步：標記 >]** 按鈕。 

        ![建立私人端點-設定頁面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 [**標記**] 頁面上，建立您想要與私人端點和私人 DNS 區域相關聯的任何標記（名稱和值）（如果您已啟用此選項）。 然後選取頁面底部的 [**審核] + [建立**] 按鈕。 
11. 在 [**審核**] 和 [建立] 上，檢查所有設定，然後選取 [**建立**] 以建立私人端點。
    
    ![建立私人端點-審查和建立頁面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 在 [**私人端點**] 頁面上，您可以看到私人端點連接的狀態。 如果您是轉送命名空間的擁有者，或具有 [管理] 許可權，並已選取 [**在我的目錄中連接到 Azure 資源]** **選項，則**應**自動核准**端點連線。 如果它處於 [**暫**止] 狀態，請參閱[使用 Azure 入口網站管理私人端點](#manage-private-endpoints-using-azure-portal)一節。

    ![私人端點頁面](./media/private-link-service/private-endpoint-page.png)
13. 流覽回**命名空間**的 [**網路**功能] 頁面，然後切換至 [**私人端點連線（預覽）** ] 索引標籤。您應該會看到您所建立的私用端點。 

    ![已建立私人端點](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 新增私人端點
下列範例示範如何使用 Azure PowerShell 來建立 Azure 轉送命名空間的私用端點連接。

您的私人端點和虛擬網路必須位於相同區域。 您的 Azure 轉送命名空間可以在不同的區域中。 而且，您的私用端點會在您的虛擬網路中使用私人 IP 位址。

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

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

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

當您建立私人端點時，必須核准連線。 如果您要建立私人端點的資源（轉送命名空間）位於您的目錄中，則您可以核准透過轉送命名空間管理許可權所提供的連線要求。 如果您要連接到您沒有 [管理] 存取權的轉送命名空間，您必須等候該資源的擁有者核准您的連線要求。

佈建狀態有四種：

| 服務動作 | 服務取用者私人端點狀態 | 描述 |
|--|--|--|
| None | Pending | 會手動建立連線，並等待 Azure 轉送命名空間擁有者核准。 |
| 核准 | 已核准 | 已自動或手動核准連線並可供使用。 |
| 拒絕 | 已拒絕 | Azure 轉送命名空間擁有者拒絕連接。 |
| 移除 | 已中斷連接 | 連接已由 Azure 轉送命名空間擁有者移除，私用端點會變成資訊性，而且應該刪除以進行清除。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>核准、拒絕或移除私人端點連接

1. 登入 Azure 入口網站。
1. 在搜尋列中，輸入**轉送**。
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

### <a name="reject-a-private-endpoint-connection"></a>拒絕私人端點連接

1. 如果您想要拒絕任何私人端點連線，不論是擱置中的要求還是先前已核准的現有連線，請選取端點連線，然後按一下 [**拒絕**] 按鈕。

    ![拒絕按鈕](./media/private-link-service/private-endpoint-reject.png)
2. 在 [**拒絕連接**] 頁面上，輸入選擇性的批註，然後選取 **[是]**。 如果您選取 [否]，則**不**會發生任何事。 

    ![拒絕連接頁面](./media/private-link-service/reject-connection-page.png)
3. 您應該會在 [已**拒絕**] 清單中看到連接的狀態。


### <a name="remove-a-private-endpoint-connection"></a>移除私人端點連接

1. 若要移除私用端點連接，請在清單中選取它，然後選取工具列上的 [**移除**]。 

    ![[移除] 按鈕](./media/private-link-service/remove-endpoint.png)
2. 在 [**刪除**連線] 頁面上，選取 [**是]** 以確認刪除私人端點。 如果您選取 [否]，則**不**會發生任何事。 

    ![刪除連接頁面](./media/private-link-service/delete-connection-page.png)
3. 您應該會看到狀態已變更為 [已**中斷**連線]。 然後，您會看到端點從清單中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>驗證私人連結連線是否正常運作
您應該驗證私人端點的相同子網內的資源是否透過其私人 IP 位址連接到您的 Azure 轉送命名空間。

針對這項測試，請遵循在[Azure 入口網站中建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)中的步驟來建立虛擬機器

在 [**網路**功能] 索引標籤中： 

1. 指定 [**虛擬網路**] 和 [**子網**]。 您必須選取已部署私人端點的虛擬網路。
2. 指定**公用 IP**資源。
3. 針對 [ **NIC 網路安全性群組**]，選取 [**無**]。
4. 針對 [**負載平衡**]，選取 [**否**]。

連接到 VM 並開啟命令列，然後執行下列命令：

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

您應該會看到如下所示的結果。 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和設計考量

### <a name="design-considerations"></a>設計考量
- Azure 轉送的私人端點處於**公開預覽**狀態。 
- 如需定價資訊，請參閱 [Azure 私人連結 (預覽) 定價](https://azure.microsoft.com/pricing/details/private-link/)。

### <a name="limitations"></a>限制 
- 每個 Azure 轉送命名空間的私人端點數目上限：64。
- 每個訂用帳戶具有私人端點的 Azure 轉送命名空間數目上限：64。
- 網路安全性群組（NSG）規則和使用者定義的路由不適用於私人端點。 如需詳細資訊，請參閱[Azure 私人連結服務：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Private Link (預覽)](../private-link/private-link-service-overview.md)
- 深入瞭解[Azure 轉送](relay-what-is-it.md)
