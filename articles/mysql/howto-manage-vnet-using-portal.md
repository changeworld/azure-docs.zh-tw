---
title: 管理 VNet 端點-Azure 入口網站-適用於 MySQL 的 Azure 資料庫
description: 使用 Azure 入口網站建立及管理適用於 MySQL 的 Azure 資料庫的 VNet 服務端點和規則
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 4eda84803f5d132f9f4ea4f413da67b9585b4776
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546376"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>使用 Azure 入口網站建立及管理適用於 MySQL 的 Azure 資料庫的 VNet 服務端點和 VNet 規則
虛擬網路 (VNet) 服務端點和規則會將虛擬網路的私人位址空間延伸到您適用於 MySQL 的 Azure 資料庫伺服器。 如需適用於 MySQL 的 Azure 資料庫的 VNet 服務端點概觀 (包含限制)，請參閱[適用於 MySQL 的 Azure 資料庫伺服器的 VNet 服務端點](concepts-data-access-and-security-vnet.md)。 VNet 服務端點在「適用於 MySQL 的 Azure 資料庫」的所有支援區域皆可使用。

> [!NOTE]
> VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。
> 在 VNet 對等互連的案例中，如果流量流經含有服務端點的通用 VNet 閘道，且應流往同儕節點，請建立 ACL/VNet 規則以允許閘道 VNet 中的 Azure 虛擬機器存取適用於 MySQL 的 Azure 資料庫伺服器。


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>在 Azure 入口網站中建立 VNet 規則並啟用服務端點

1. 在 [MySQL 伺服器] 頁面的 [設定] 標題下，按一下 [連線安全性]  ，以開啟適用於 MySQL 的 Azure 資料庫的 [連線安全性] 窗格。 

2. 確定 [允許存取 Azure 服務] 控制項設定為 [ **關閉** ]。

> [!Important]
> 如果您將此控制項保持設定為開啟，則您的 Azure MySQL 資料庫伺服器會接受來自任何子網的通訊。 就安全性觀點而言，讓此控制項保持 [開啟] 可能使存取過多。 與適用於 MySQL 的 Azure 資料庫的虛擬網路規則功能協調 Microsoft Azure 虛擬網路服務端點功能，可將您的安全性介面區降到一。

3. 接下來，按一下 [+ 新增現有的虛擬網路]  。 如果您沒有現有的 VNet，就可以按一下 [+ 建立新的虛擬網路]  來建立新的 VNet。 請參閱[快速入門：使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure 入口網站按一下 [連接安全性]":::

4. 輸入 VNet 規則名稱，選取訂用帳戶、虛擬網路和子網路名稱，然後按一下 [啟用]  。 這會使用 **Microsoft.SQL** 服務標籤自動啟用子網路上的 VNet 服務端點。

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure 入口網站按一下 [連接安全性]" 權限。 此權限預設會隨附在內建的服務管理員角色中，可藉由建立自訂角色加以修改。
    
   深入了解[內建角色](../role-based-access-control/built-in-roles.md)以及如何將特定權限指派給[自訂角色](../role-based-access-control/custom-roles.md)。
    
   VNet 和 Azure 服務資源不一定要位於相同訂用帳戶中。 如果 VNet 和 Azure 服務資源位於不同的訂用帳戶中，則資源應該位於相同的 Active Directory (AD) 租用戶底下。 請確定這兩個訂用帳戶都已註冊 **Microsoft.Sql** 資源提供者。 如需詳細資訊，請參閱 [resource-manager-registration][resource-manager-portal]

   > [!IMPORTANT]
   > 強烈建議您在設定服務端點之前，先閱讀這篇有關服務端點設定和考量的文章。 **虛擬網路服務端點：** [虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)是一個子網路，其屬性值包含一或多個正式的 Azure 服務類型名稱。 VNet 服務端點使用 **Microsoft.Sql** 服務類型名稱，它參考名為 SQL Database 的 Azure 服務。 此服務標籤也會套用到 Azure SQL Database、適用於 PostgreSQL 和 MySQL 的 Azure 資料庫服務。 請務必注意，當您將 **Microsoft.Sql** 服務標籤套用到 VNet 服務端點時，它會設定所有 Azure 資料庫服務的服務端點流量，包括子網路上的 Azure SQL Database、適用於 PostgreSQL 的 Azure 資料庫和適用於 MySQL 的 Azure 資料庫伺服器。 
   > 

5. 啟用之後，按一下 [確定]  ，您將會看到 VNet 服務端點和 VNet 規則一起啟用。

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="Azure 入口網站按一下 [連接安全性]":::

## <a name="next-steps"></a>下一步
- 同樣地，您可以編寫指令碼，以[使用 Azure CLI 啟用 VNet 服務端點及建立適用於 MySQL 的 Azure 資料庫的 VNET 規則](howto-manage-vnet-using-cli.md)。
- 如需連線至「適用於 MySQL 的 Azure 資料庫」伺服器的說明，請參閱[「適用於 MySQL 的 Azure 資料庫」的連線庫](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md