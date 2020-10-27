---
title: 管理 VNet 端點-Azure CLI-適用於 MySQL 的 Azure 資料庫
description: 本文說明如何使用 Azure CLI 命令列，建立及管理適用於 MySQL 的 Azure 資料庫的 VNet 服務端點和規則。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e43dd8693a55db282b0333dc447be23373b1f8f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546478"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>使用 Azure CLI 建立及管理適用於 MySQL 的 Azure 資料庫的 VNet 服務端點
虛擬網路 (VNet) 服務端點和規則會將虛擬網路的私人位址空間延伸到您適用於 MySQL 的 Azure 資料庫伺服器。 您可以使用 Azure 命令列介面 (CLI) 命令，來建立、更新、刪除、列出及顯示 VNet 服務端點和規則，以管理您的伺服器。 如需適用於 MySQL 的 Azure 資料庫的 VNet 服務端點概觀 (包含限制)，請參閱[適用於 MySQL 的 Azure 資料庫伺服器的 VNet 服務端點](concepts-data-access-and-security-vnet.md)。 VNet 服務端點在「適用於 MySQL 的 Azure 資料庫」的所有支援區域皆可使用。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- 安裝 [Azure CLI](/cli/azure/install-azure-cli) 或在瀏覽器中使用 Azure Cloud Shell。
- [適用於 MySQL 的 Azure 資料庫伺服器和資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)。

> [!NOTE]
> VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。
> 在 VNet 對等互連的案例中，如果流量流經含有服務端點的通用 VNet 閘道，且應流往同儕節點，請建立 ACL/VNet 規則以允許閘道 VNet 中的 Azure 虛擬機器存取適用於 MySQL 的 Azure 資料庫伺服器。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的 VNet 服務端點
[az network vnet](/cli/azure/network/vnet) \(英文\) 命令會用來設定虛擬網路。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

若您在本機上執行 CLI，則需要使用 [az login](/cli/azure/authenticate-azure-cli) 命令登入您的帳戶。 請記下命令輸出中的 **識別碼** 屬性，以取得對應的訂用帳戶名稱。
```azurecli-interactive
az login
```

如果您有多個訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [az account set](/cli/azure/account#az-account-set) 命令來選取您帳戶底下的特定訂用帳戶 ID。 以訂用帳戶之 **az login** 輸出中的 **識別碼** 屬性，替代訂用帳戶識別碼的預留位置。

- 該帳戶必須擁有必要的權限，才能建立虛擬網路和服務端點。

擁有虛擬網路寫入權的使用者可以任意地在虛擬網路上設定服務端點。

若要將 Azure 服務資源放到 VNet 保護，使用者必須擁有所要新增之子網路的 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" 權限。 此權限預設會隨附在內建的服務管理員角色中，可藉由建立自訂角色加以修改。

深入了解[內建角色](../role-based-access-control/built-in-roles.md)以及如何將特定權限指派給[自訂角色](../role-based-access-control/custom-roles.md)。

VNet 和 Azure 服務資源不一定要位於相同訂用帳戶中。 如果 VNet 和 Azure 服務資源位於不同的訂用帳戶中，則資源應該位於相同的 Active Directory (AD) 租用戶底下。 請確定這兩個訂用帳戶都已註冊 **Microsoft.Sql** 資源提供者。 如需詳細資訊，請參閱 [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> 強烈建議您在執行下方的範例指令碼或設定服務端點之前，先閱讀這篇有關服務端點設定和考量的文章。 **虛擬網路服務端點：** [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)是一個子網路，其屬性值包含一或多個正式的 Azure 服務類型名稱。 VNet 服務端點使用 **Microsoft.Sql** 服務類型名稱，它參考名為 SQL Database 的 Azure 服務。 此服務標籤也會套用到 Azure SQL Database、適用於 PostgreSQL 和 MySQL 的 Azure 資料庫服務。 請務必注意，當您將 **Microsoft.Sql** 服務標籤套用到 VNet 服務端點時，它會設定所有 Azure 資料庫服務的服務端點流量，包括子網路上的 Azure SQL Database、適用於 PostgreSQL 的 Azure 資料庫和適用於 MySQL 的 Azure 資料庫伺服器。 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>建立適用於 MySQL 資料庫的 Azure 資料庫、建立 VNet、VNet 服務端點，並使用 VNet 規則保護子網路伺服器的範例指令碼
在此範例指令碼中，請變更醒目提示的命令列以自訂系統管理員使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代 `az account set --subscription` 命令中使用的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>清除部署
在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md