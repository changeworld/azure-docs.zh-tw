---
title: 建立受控實例的虛擬網路
description: 本文說明如何建立可在其中部署 Azure SQL Database 受控執行個體的虛擬網路。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878845"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>建立 Azure SQL Database 受控執行個體的虛擬網路

本文說明如何建立可在其中部署 Azure SQL Database 受控執行個體的有效虛擬網路和子網路。

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路](../virtual-network/virtual-networks-overview.md) 內。 此部署適用於下列案例：

- 安全的私人 IP 位址
- 從內部部署網路直接連線到受控執行個體
- 將受控執行個體連線到連結的伺服器或其他內部部署資料存放區
- 將受控執行個體連線到 Azure 資源  

> [!NOTE]
> 請先[決定受控執行個體的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)，再部署第一個執行個體。 在其中放入資源後，就無法再調整子網路的大小。
>
> 如果您打算使用現有的虛擬網路，您需要修改該網路組態以順應您的受控執行個體。 如需詳細資訊，請參閱[針對受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
>
> 建立受控實例之後，不支援將受控實例或 VNet 移至另一個資源群組或訂用帳戶。  也不支援將受控實例移至另一個子網。
>

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立並設定虛擬網路最簡單的方式是使用 Azure Resource Manager 部署範本。

1. 登入 Azure 入口網站。

2. 選取 [部署至 Azure]**** 按鈕：

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   此按鈕將會開啟一個表單，讓您用來設定可在其中部署受控執行個體的網路環境。

   > [!Note]
   > 此 Azure Resource Manager 範本將會部署具有兩個子網路的虛擬網路。 其中一個子網路 (名為 **ManagedInstances**) 已針對受控執行個體保留，且具有預先設定的路由表。 另一個名為 **Default** 的子網路用於應該存取受控執行個體 (例如 Azure 虛擬機器) 的其他資源。

3. 設定網路環境。 在下一張表單上，您可以設定網路環境的參數：

   ![可供設定 Azure 網路的 Resource Manager 範本](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   您可以變更虛擬網路和子網路的名稱，以及調整與您的網路資源關聯的 IP 位址。 選取 [購買]**** 按鈕後，此表單就會建立並設定您的環境。 若您不需要兩個子網路，可將預設子網路刪除。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 深入了解[受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[為受控執行個體修改現有的虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需 DNS 問題，請參閱設定[自訂 DNS](sql-database-managed-instance-custom-dns.md)。
