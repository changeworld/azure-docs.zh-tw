---
title: 管理虛擬網路-Azure CLI-適用於 PostgreSQL 的 Azure 資料庫-彈性的伺服器
description: 使用 Azure CLI 為適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器建立及管理虛擬網路
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: eb22946bb3f0858a545d5b854afe48b2e1e61927
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109227"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 為適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器建立及管理虛擬網路

> [!IMPORTANT]
> 適用於 PostgreSQL 的 Azure 資料庫 - 彈性伺服器為預覽狀態

適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器支援兩種互斥的網路連線方法，以連接到您的彈性伺服器。 這兩個選項是：

* 公用存取 (允許的 IP 位址)
* 私人存取 (VNet 整合)

在本文中，我們將著重于使用 **(VNet 整合)** 使用 Azure CLI 來建立于 postgresql 伺服器。 使用 *私人存取 (VNet 整合)*，您可以將彈性的伺服器部署到您自己的 [Azure 虛擬網路](../../virtual-network/virtual-networks-overview.md)。 Azure 虛擬網路提供私人且安全的網路通訊。 在 [私用存取] 中，于 postgresql 伺服器的連線僅限於您的虛擬網路內。 若要深入瞭解，請參閱 [私人存取 (VNet 整合) ](./concepts-networking.md#private-access-vnet-integration)。

在適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器中，您只能在建立伺服器時將伺服器部署至虛擬網路和子網。 將彈性伺服器部署到虛擬網路和子網之後，您就無法將它移到另一個虛擬網路、子網或 *公用存取 (允許的 IP 位址)*。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤開啟 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後選取 **Enter** 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>Prerequisites

您必須使用 [az login](/cli/azure/reference-index#az-login) 命令登入您的帳戶。 請注意 **id** 屬性，它會參考您 Azure 帳戶的訂用帳戶 **識別碼** 。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account#az-account-set) 命令來選取您帳戶底下的特定訂用帳戶。 記下 **az 登** 入輸出中的 **識別碼** 值，以做為命令中 **訂閱** 引數的值。 如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 若要取得您的所有訂用帳戶，請使用 [az account list](/cli/azure/account#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>使用 CLI 建立適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器
您可以使用 `az postgres flexible-server` 命令，利用 *私人存取 (VNet 整合)* 來建立彈性的伺服器。 此命令會使用私人存取 (VNet 整合) 作為預設的連線方法。 如果未提供，系統會為您建立虛擬網路和子網。 您也可以使用子網識別碼來提供現有的虛擬網路和子網。 <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 使用 CLI 建立彈性伺服器的選項有很多種，如下列範例所示。

>[!Important]
> 使用此命令會將子網委派給 **DBforPostgreSQL/flexibleServers**。 此委派表示只有適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器可以使用該子網。 委派的子網路中不可以有其他 Azure 資源類型。
>
請參閱 Azure CLI 參考檔 <!--FIXME --> 以取得可設定的 CLI 參數完整清單。 例如，在下列命令中，您可以選擇性地指定資源群組。

- 使用預設的虛擬網路、具有預設位址首碼的子網建立彈性的伺服器
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- 使用現有的虛擬網路和子網建立彈性的伺服器。 如果提供的虛擬網路和子網不存在，則會建立具有預設位址首碼的虛擬網路和子網。
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- 使用現有的虛擬網路、子網和子網識別碼來建立彈性的伺服器。 提供的子網不應部署任何其他資源，而且此子網將會委派給 **DBforPostgreSQL/flexibleServers**（如果尚未委派）。
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 虛擬網路和子網應該與您彈性的伺服器位於相同的區域和訂用帳戶中。

- 使用新的虛擬網路、具有非預設位址首碼的子網建立彈性的伺服器
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
請參閱 Azure CLI [參考檔](/cli/azure/postgres/flexible-server) ，以取得可設定的 CLI 參數的完整清單。

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫具彈性的伺服器中的網路功能](./concepts-networking.md)。
- [使用 Azure 入口網站建立及管理適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器虛擬網路](./how-to-manage-virtual-network-portal.md)。
- 深入瞭解 [適用於 PostgreSQL 的 Azure 資料庫彈性的伺服器虛擬網路](./concepts-networking.md#private-access-vnet-integration)。