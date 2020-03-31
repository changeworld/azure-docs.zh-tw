---
title: 專用鏈路 - Azure CLI - MariaDB 的 Azure 資料庫
description: 瞭解如何從 Azure CLI 為 MariaDB 配置 Azure 資料庫的專用連結
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371693"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>使用 CLI 為 MariaDB 創建和管理 Azure 資料庫的專用連結

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 在本文中，您將學習如何使用 Azure CLI 在 Azure 虛擬網路中創建 VM，以及使用 Azure 專用終結點為 MariaDB 伺服器創建 Azure 資料庫。

> [!NOTE]
> 此功能在所有 Azure 區域都可用，其中 MariaDB 的 Azure 資料庫支援通用和記憶體優化定價層。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本作法指南，您需要︰

- [MariaDB 伺服器的 Azure 資料庫](quickstart-create-mariadb-server-database-using-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

建立任何資源之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 本示例*在西歐*位置創建名為*myResourceGroup*的資源組：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
使用 [az network vnet create](/cli/azure/network/vnet) 建立虛擬網路。 此範例會建立一個名為 myVirtualNetwork** 的預設虛擬網路，其中含有一個名為 mySubnet** 的子網路：

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

## <a name="create-an-azure-database-for-mariadb-server"></a>建立適用於 MariaDB 的 Azure 資料庫伺服器 
使用 az Mariadb 伺服器創建命令為 MariaDB 創建 Azure 資料庫。 請記住，MariaDB 伺服器的名稱必須在 Azure 中是唯一的，因此請將括弧中的預留位置值替換為您自己的唯一值： 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

請注意，MariaDB 伺服器 ID ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.```類似于您將在下一步中使用 MariaDB 伺服器 ID。 

## <a name="create-the-private-endpoint"></a>建立私人端點 
為虛擬網路中的 MariaDB 伺服器創建專用終結點： 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域 
為 MariDB 伺服器域創建專用 DNS 區域，並與虛擬網路創建關聯連結。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> 客戶 DNS 設置中的 FQDN 不會解析為配置的專用 IP。 您必須為配置的 FQDN 設置 DNS 區域，[如下所示](../dns/dns-operations-recordsets-portal.md)。

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm**，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm**。

1. 選取 [連線] **** 按鈕。 選取 [連線]**** 按鈕之後，隨即會開啟 [連線至虛擬機器]****。

1. 選擇**下載 RDP 檔**。 Azure 會建立一個「遠端桌面通訊協定」(*.rdp*) 檔案，並下載至您的電腦。

1. 開啟 *downloaded.rdp* 檔案。

    1. 如果出現提示，請選取 [連接]****。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選擇**更多選項** > **"使用其他帳戶**"來指定創建 VM 時輸入的憑據。

1. 選取 [確定]****。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是]**** 或 [繼續]****。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>從 VM 私下訪問 MariaDB 伺服器

1. 在 myVM ** 的遠端桌面中，開啟 PowerShell。

2. 輸入  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`。 

    您將收到如下訊息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用用戶端測試 MariaDB 伺服器的專用鏈路連接。 在下面的示例中，我使用[MySQL 工作臺](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html)執行操作。

4. 在 **"新建連接**"中，輸入或選擇此資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 連線名稱| 選擇您選擇的連接名稱。|
    | 主機名稱 | 選擇*mydemoserver.privatelink.mariadb.database.azure.com* |
    | 使用者名稱 | 輸入在*username@servername*MariaDB 伺服器創建期間提供的使用者名。 |
    | 密碼 | 輸入在 MariaDB 伺服器創建期間提供的密碼。 |
    ||

5. 選擇 **"測試連接**"**或"確定**"。

6. （可選）從左側功能表流覽資料庫，並從 MariaDB 資料庫中創建或查詢資訊

8. 關閉遠端桌面連線到 myVm。

## <a name="clean-up-resources"></a>清除資源 
您可以使用 az group delete 來移除不再需要的資源群組，以及其所具有的所有資源： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>後續步驟
瞭解有關什麼是[Azure 專用終結點](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
