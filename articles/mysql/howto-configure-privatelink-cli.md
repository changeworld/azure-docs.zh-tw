---
title: 私用連結-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何從 Azure CLI 設定適用於 MySQL 的 Azure 資料庫的私人連結
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f83f52f1c1800803c5e1d47f1931f7b13b2c11de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79368004"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>使用 CLI 建立和管理適用於 MySQL 的 Azure 資料庫的私用連結

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 在本文中，您將瞭解如何使用 Azure CLI 來建立 Azure 虛擬網路中的 VM，以及具有 Azure 私用端點的適用於 MySQL 的 Azure 資料庫伺服器。

> [!NOTE]
> 這項功能適用于所有 Azure 區域，其中適用於 MySQL 的 Azure 資料庫支援一般用途和記憶體優化定價層。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

建立任何資源之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 這個範例會在*westeurope*位置建立名為*myResourceGroup*的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
使用[az Network vnet create](/cli/azure/network/vnet)來建立虛擬網路。 此範例會建立一個名為 myVirtualNetwork** 的預設虛擬網路，其中含有一個名為 mySubnet** 的子網路：

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>停用子網路的私人端點原則 
Azure 會將資源部署到虛擬網路內的子網路，因此您必須建立或更新子網路，以停用私人端點網路原則。 使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 來更新名為 mySubnet** 的子網路設定：

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>建立 VM 
使用 az vm create 建立 VM。 出現提示時，請提供密碼以作為 VM 的登入認證。 此範例會建立名為 myVm** 的 VM： 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
請記下 VM 的公用 IP 位址。 在下一個步驟中，您將使用此位址來從網際網路連線至 VM。

## <a name="create-an-azure-database-for-mysql-server"></a>建立適用於 MySQL 的 Azure 資料庫伺服器 
使用 az MySQL server create 命令來建立適用於 MySQL 的 Azure 資料庫。 請記住，您的 MySQL 伺服器名稱在 Azure 中必須是唯一的，因此請以您自己唯一的值取代括弧中的預留位置值： 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

請注意，MySQL 伺服器識別碼類似于 ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.```您在下一個步驟中將使用 MYSQL 伺服器識別碼。 

## <a name="create-the-private-endpoint"></a>建立私人端點 
在您的虛擬網路中建立 MySQL 伺服器的私用端點： 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域 
建立 MySQL 伺服器網域的私人 DNS 區域，並使用虛擬網路建立關聯連結。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> 客戶 DNS 設定中的 FQDN 不會解析為設定的私人 IP。 您將必須為設定的 FQDN 設定 DNS 區域[，如下所示。](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm**，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm  。

1. 選取 [連線]  按鈕。 選取 [連線]  按鈕之後，隨即會開啟 [連線至虛擬機器]  。

1. 選取 [下載 RDP 檔案]  。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]  。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇]   > [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]  。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]  或 [繼續]  。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>從 VM 私下存取 MySQL 伺服器

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。

2. 輸入  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用的用戶端來測試 MySQL 伺服器的私人連結連線。 在下列範例中，我使用了[MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)來執行操作。


4. 在 [**新增連接**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 連線名稱| 選取您選擇的 [連接名稱]。|
    | 主機名稱 | 選取*mydemoserver.privatelink.mysql.database.azure.com* |
    | 使用者名稱 | 輸入在建立*username@servername* MySQL 伺服器期間所提供的使用者名稱。 |
    | 密碼 | 輸入建立 MySQL 伺服器期間所提供的密碼。 |
    ||

5. 選取 [連接]  。

6. 瀏覽左側功能表中的資料庫。

7. 也從 MySQL 資料庫建立或查詢資訊。

8. 關閉對 myVm 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源 
您可以使用 az group delete 來移除不再需要的資源群組，以及其所具有的所有資源： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>後續步驟
- 深入瞭解[什麼是 Azure 私用端點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
