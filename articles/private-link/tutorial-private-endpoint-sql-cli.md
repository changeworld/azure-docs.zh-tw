---
title: 教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - Azure CLI
description: 使用此教學課程來了解如何使用 Azure CLI 建立具有私人端點的 Azure SQL 伺服器
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.openlocfilehash: 8cfe44b9433ee1daac028253aa45c97804c88ae5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544100"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>教學課程 - 使用 Azure 私人端點連線到 Azure SQL 伺服器 - Azure CLI

Azure 私人端點是 Azure 中私人連結的基本建置組塊。 其可讓 Azure 資源 (例如虛擬機器 (VM)) 與私人連結資源進行私密通訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路和堡壘主機。
> * 建立虛擬機器。
> * 建立 Azure SQL 伺服器和私人端點。
> * 測試 SQL 伺服器私人端點的連線能力。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 登入 Azure 入口網站，並執行 `az login` 檢查您的訂用帳戶是否有效。
* 執行 `az --version`，在終端機或命令視窗中檢查您的 Azure CLI 版本。 如需最新版本，請參閱[最新版本資訊](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果您沒有最新版本，請遵循[適用於您作業系統或平台的安裝指南](/cli/azure/install-azure-cli)，更新您的安裝。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組：

* 具名的 **CreateSQLEndpointTutorial-rg**。 
* 在 **eastus** 位置中。

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>建立虛擬網路和堡壘主機

在本節中，您會建立虛擬網路、子網路和堡壘主機。 

堡壘主機將用來安全地連線到虛擬機器，以測試私人端點。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 建立虛擬網路

* 具名 **myVNet**。
* 位址前置詞 **10.0.0.0/16**。
* 名為 **MyBackendSubnet** 的子網路。
* 子網路前置詞 **10.0.0.0/24**。
* 在 **CreateSQLEndpointTutorial-rg** 資源群組中。
* **eastus** 的位置。

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)，更新子網路以停用私人端點的私人端點網路原則：

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 建立堡壘主機的公用 IP 位址：

* 建立名為 **myBastionIP** 的標準區域備援公用 IP 位址。
* 在 **CreateSQLEndpointTutorial-rg** 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 建立 Bastion 子網路：

* 命名為 **AzureBastionSubnet**。
* 位址前置詞 **10.0.1.0/24**。
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreateSQLEndpointTutorial-rg** 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 建立堡壘主機：

* 命名為 **myBastionHost**。
* 在 **CreateSQLEndpointTutorial-rg** 中。
* 與公用 IP **myBastionIP** 相關聯。
* 與虛擬網路 **myVNet** 相關聯。
* 在 **eastus** 位置中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主機需要幾分鐘的時間。

## <a name="create-test-virtual-machine"></a>建立測試虛擬機器

在本節中，您將建立將用來測試私人端點的虛擬機器。

使用  [az vm create](/cli/azure/vm#az_vm_create) 建立 VM。 出現提示時，請提供密碼以作為 VM 的認證：

* 具名的 **myVM**。
* 在 **CreateSQLEndpointTutorial-rg** 中。
* 在虛擬網路 **myVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 伺服器映像 **Win2019Datacenter**。

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>建立 Azure SQL Server

在本節中，您將會建立 SQL Server 和資料庫。

使用 [az sql server create](/cli/azure/sql/server#az_sql_server_create) 建立 SQL server：

* 以您的唯一伺服器名稱取代 **\<sql-server-name>** 。
* 請以您的密碼取代 **\<your-password>** 。
* 在 **CreateSQLEndpointTutorial-rg** 中。
* 在 **eastus** 區域中。

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

使用 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 建立資料庫：

* 具名的 **myDataBase**。
* 在 **CreateSQLEndpointTutorial-rg** 中。
* 以您的唯一伺服器名稱取代 **\<sql-server-name>** 。

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會建立私人端點。

使用 [az sql server list](/cli/azure/sql/server#az_sql_server_list) 將 SQL server 的資源識別碼放入殼層變數中。

使用 [az network private-endpoint create立](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 建立端點和連線：

* 具名的 **myPrivateEndpoint**。
* 在資源群組 **CreateSQLEndpointTutorial-rg** 中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 名為 **myConnection** 的連線。

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域

在本節中，您會使用 [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)建立和設定私人 DNS 區域。  

您將使用 [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) 建立 DNS 區域的虛擬網路連結。

您將會使用 [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)建立 DNS 區域群組。

* 名為 **privatelink.database.windows.net** 的區域
* 在虛擬網路 **MyVNet** 中。
* 在資源群組 **CreateSQLEndpointTutorial-rg** 中。
* 名為 **myDNSLink** 的 DNS 連結。
* 與 **myPrivateEndpoint** 相關聯。
* 名為 **MyZoneGroup** 的區域群組。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>測試對私人端點的連線能力

在本節中，您將使用您在上一個步驟中建立的虛擬機器，連線到私人端點上的 SQL 伺服器。

1. 登入 [Azure 入口網站](https://portal.azure.com) 
 
2. 在左側瀏覽窗格中，選取 [資源群組]。

3. 選取 **CreateSQLEndpointTutorial-rg**。

4. 選取 [myVM]。

5. 在 **myVM** 的 [概觀] 頁面上，選取 [連線] 然後 [堡壘]。

6. 選取藍色 [使用堡壘] 按鈕。

7. 輸入您在虛擬機器建立期間輸入的使用者名稱和密碼。

8. 連線之後，在伺服器上開啟 Windows PowerShell。

9. 輸入 `nslookup <sqlserver-name>.database.windows.net`。 將 **\<sqlserver-name>** 取代為您在先前步驟中建立的 SQL 伺服器名稱。  您會收到類似下面所顯示的訊息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    會針對 SQL 伺服器名稱傳回 **10.0.0.5** 的私人 IP 位址。  此位址位於您先前所建立虛擬網路的子網路中。


10. 在 **myVM** 上安裝 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017)。

11. 開啟 [SQL Server Management Studio]  。

12. 在 [連線至伺服器] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 伺服器類型 | 選取 [資料庫引擎]。|
    | 伺服器名稱 | 輸入 **\<sql-server-name>.database.windows.net** |
    | 驗證 | 選取 [SQL Server 驗證]。 |
    | 使用者名稱 | 輸入您在伺服器建立期間輸入的使用者名稱 |
    | 密碼 | 輸入您在建立伺服器時輸入的密碼 |
    | 記住密碼 | 選取 [是]。 |

13. 選取 [連接]  。

14. 瀏覽左側功能表中的資料庫。

15. (選擇性) 從 **mysqldatabase** 建立或查詢資訊。

16. 關閉與 **myVM** 的 Bastion 連線。 

## <a name="clean-up-resources"></a>清除資源 

在私人端點、SQL Server 和 VM 使用完畢後，請刪除資源群組及其包含的所有資源： 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>後續步驟

您已在本教學課程中建立：

* 建立虛擬網路和堡壘主機。
* 虛擬機器。
* 具有私人端點的 Azure SQL 伺服器。

您已使用虛擬機器在私人端點上安全地測試與 SQL 伺服器的連線。

了解如何建立私人連結服務：
> [!div class="nextstepaction"]
> [建立 Private Link 服務](create-private-link-service-portal.md)