---
title: 管理 VNet 終結點 - Azure 門戶 - MariaDB 的 Azure 資料庫
description: 使用 Azure 入口網站建立及管理適用於 MariaDB 的 Azure 資料庫的 VNet 服務端點和規則
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530592"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>使用 Azure 入口網站建立及管理適用於 MariaDB 的 Azure 資料庫的 VNet 服務端點和 VNet 規則

虛擬網路 (VNet) 服務端點和規則會將虛擬網路的私人位址空間延伸到您適用於 MariaDB 的 Azure 資料庫伺服器。 如需適用於 MariaDB 的 Azure 資料庫的 VNet 服務端點概觀 (包含限制)，請參閱[適用於 MariaDB 的 Azure 資料庫伺服器的 VNet 服務端點](concepts-data-access-security-vnet.md)。 VNet 服務端點在「適用於 MariaDB 的 Azure 資料庫」的所有支援區域皆可使用。

> [!NOTE]
> VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>建立 VNet 規則並啟用服務端點

1. 在 [MariaDB 伺服器] 頁面的 [設定] 標題下，按一下 [連線安全性]****，以開啟適用於 MariaDB 的 Azure 資料庫的 [連線安全性] 窗格。

2. 確保允許訪問 Azure 服務控制項設置為**OFF**。

> [!Important]
> 如果將其設置為 ON，則 Azure MariaDB 資料庫伺服器將接受來自任何子網的通信。 就安全性觀點而言，讓此控制項保持 [開啟] 可能使存取過多。 Microsoft Azure 虛擬網路服務終結點功能與 MariaDB Azure 資料庫的虛擬網路規則功能協調，可共同減少安全表面積。

3. 接下來，按一下 [+ 新增現有的虛擬網路]****。 如果您沒有現有的 VNet，就可以按一下 [+ 建立新的虛擬網路]**** 來建立新的 VNet。 請參閱[快速入門：使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)

   ![Azure 入口網站 - 按一下 [連線安全性]](./media/howto-manage-vnet-portal/1-connection-security.png)

4. 輸入 VNet 規則名稱，選取訂用帳戶、虛擬網路和子網路名稱，然後按一下 [啟用]****。 這會使用 **Microsoft.SQL** 服務標籤自動啟用子網路上的 VNet 服務端點。

   ![Azure 入口網站 - 設定 VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   該帳戶必須擁有必要的權限，才能建立虛擬網路和服務端點。

   擁有虛擬網路寫入權的使用者可以任意地在虛擬網路上設定服務端點。
    
   若要將 Azure 服務資源放到 VNet 保護，使用者必須擁有所要新增之子網路的 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" 權限。 此權限預設會隨附在內建的服務管理員角色中，可藉由建立自訂角色加以修改。
    
   深入了解[內建角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及如何將特定權限指派給[自訂角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。
    
   VNet 和 Azure 服務資源不一定要位於相同訂用帳戶中。 如果 VNet 和 Azure 服務資源位於不同的訂用帳戶中，則資源應該位於相同的 Active Directory (AD) 租用戶底下。 確保兩個訂閱都註冊了**Microsoft.Sql**資來源提供者。 有關詳細資訊，請參閱[資源管理器註冊][resource-manager-portal]

   > [!IMPORTANT]
   > 強烈建議您在設定服務端點之前，先閱讀這篇有關服務端點設定和考量的文章。 **虛擬網路服務終結點：**[虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)是屬性值包含一個或多個正式 Azure 服務類型名稱的子網。 VNet 服務端點使用 **Microsoft.Sql** 服務類型名稱，它參考名為 SQL Database 的 Azure 服務。 此服務標籤也會套用到 Azure SQL Database、適用於 MariaDB、PostgreSQL 和 MySQL 的 Azure 資料庫服務。 請務必注意，當您將 **Microsoft.Sql** 服務標籤套用到 VNet 服務端點時，它會設定所有 Azure 資料庫服務的服務端點流量，包括子網路上的 Azure SQL Database、適用於 PostgreSQL 的 Azure 資料庫、適用於 MariaDB 的 Azure 資料庫，和適用於 MySQL 的 Azure 資料庫伺服器。
   > 

5. 啟用之後，按一下 [確定]****，您將會看到 VNet 服務端點和 VNet 規則一起啟用。

   ![VNet 服務端點已啟用且 VNet 規則已建立](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>後續步驟
- 深入了解如何[在適用於 MariaDB 的 Azure 資料庫上設定 SSL](howto-configure-ssl.md)
- 同樣地，您可以編寫指令碼，以[使用 Azure CLI 啟用 VNet 服務端點及建立適用於 MariaDB 的 Azure 資料庫的 VNET 規則](howto-manage-vnet-cli.md)。

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md