---
title: 快速入門 - 使用 Azure CLI 建立 Azure 私人端點
description: 在本快速入門中了解 Azure 私人端點
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502659"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>快速入門：使用 Azure CLI 建立私人端點

私人端點是 Azure 中私人連結的基本要素。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。 在本快速入門中，您將了解如何使用 Azure CLI 在虛擬網路上建立 VM，以及建立 SQL Database 中具有私人端點的伺服器。 然後，您可以存取 VM，並安全地存取私人連結資源 (在此範例中為 SQL Database 中的私人伺服器)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

建立任何資源之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 此範例會在 westcentralus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](/cli/azure/network/vnet) 建立虛擬網路。 此範例會建立一個名為 myVirtualNetwork 的預設虛擬網路，其中含有一個名為 mySubnet 的子網路：

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>停用子網路的私人端點原則

Azure 會將資源部署到虛擬網路內的子網路，因此您必須建立或更新子網路，以停用私人端點網路原則。 使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 來更新名為 mySubnet 的子網路設定：

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>建立 VM

使用 az vm create 建立 VM。 出現提示時，請提供密碼以作為 VM 的登入認證。 此範例會建立名為 myVm 的 VM：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

請記下 VM 的公用 IP 位址。 在下一個步驟中，您將使用此位址來從網際網路連線至 VM。

## <a name="create-a-server-in-sql-database"></a>在 SQL Database 中建立伺服器

使用 theaz sql 建立命令檔，在 SQL Database 中建立伺服器。 請記住，您的伺服器名稱在整個 Azure 中必須是唯一的，因此請以您自己的唯一值取代括弧中的預留位置值：

```azurecli-interactive
# Create a server in the resource group
az sql server create \
    --name "myserver"\
    --resource-group myResourceGroup \
    --location WestUS \
    --admin-user "sqladmin" \
    --admin-password "CHANGE_PASSWORD_1"

# Create a database in the server with zone redundancy as false
az sql db create \
    --resource-group myResourceGroup  \
    --server myserver \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen4 \
    --capacity 1
```

伺服器識別碼類似於  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` 您將在下一個步驟中使用該伺服器識別碼。

## <a name="create-the-private-endpoint"></a>建立私人端點

在您的虛擬網路中建立邏輯 SQL 伺服器的私人端點：

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

建立 SQL Database 網域的私人 DNS 區域、建立與虛擬網路的關聯連結，以及建立 DNS 區域群組，以將私人端點與私人 DNS 區域建立關聯。 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM：myVm，如下所示：

1. 在入口網站的搜尋列中，輸入 myVm。

1. 選取 [連線]  按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp* 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-sql-database-privately-from-the-vm"></a>從 VM 私人存取 SQL Database

在本節中，您將會使用私人端點從 VM 連線到 SQL Database。

1. 在 myVm 的遠端桌面中，開啟 PowerShell。
2. 輸入 nslookup myserver.database.windows.net

   您將收到如下訊息：

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. 安裝 SQL Server Management Studio
4. 在 [連線至伺服器] 中，輸入或選取這項資訊：

   - 伺服器類型：選取 [資料庫引擎]。
   - 伺服器名稱：選取 myserver.database.windows.net
   - 使用者名稱：輸入在建立期間提供的使用者名稱。
   - 密碼：輸入在建立期間提供的密碼。
   - 記住密碼：選取 [是]。

5. 選取 [連接]。
6. 瀏覽左側功能表中的**資料庫**。
7. (選擇性) 從 mydatabase 建立或查詢資訊
8. 關閉對 myVm 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源

您可以使用 az group delete 來移除不再需要的資源群組，以及其所具有的所有資源：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Private Link](private-link-overview.md)
