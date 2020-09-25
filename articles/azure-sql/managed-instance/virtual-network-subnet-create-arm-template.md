---
title: 建立虛擬網路
titleSuffix: Azure SQL Managed Instance
description: 本文說明如何建立設定為支援部署 Azure SQL 受控執行個體的虛擬網路。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2688d6a3c339153c155a6ced8a555242a069769f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323136"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>建立 Azure SQL 受控執行個體的虛擬網路
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文說明如何建立可在其中部署 Azure SQL 受控執行個體的有效虛擬網路和子網。

Azure SQL 受控執行個體必須部署在 Azure [虛擬網路](../../virtual-network/virtual-networks-overview.md)內。 此部署適用於下列案例：

- 安全的私人 IP 位址
- 直接從內部部署網路連接到 SQL 受控執行個體
- 將 SQL 受控執行個體連接到連結的伺服器或其他內部部署資料存放區
- 將 SQL 受控執行個體連接至 Azure 資源  

> [!NOTE]
> 您應該先 [決定 SQL 受控執行個體的子網大小](vnet-subnet-determine-size.md) ，然後再部署第一個實例。 在其中放入資源後，就無法再調整子網路的大小。
>
> 如果您打算使用現有的虛擬網路，您必須修改該網路設定以容納 SQL 受控執行個體。 如需詳細資訊，請參閱 [修改現有的虛擬網路以進行 SQL 受控執行個體](vnet-existing-add-subnet.md)。
>
> 建立受控實例之後，不支援將受控實例或虛擬網路移至另一個資源群組或訂用帳戶。  此外，也不支援將受控實例移至另一個子網。
>

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立並設定虛擬網路最簡單的方式是使用 Azure Resource Manager 部署範本。

1. 登入 Azure 入口網站。

2. 選取 [部署至 Azure]**** 按鈕：

   [![顯示標示為「部署至 Azure」的按鈕影像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   此按鈕會開啟一個表單，讓您用來設定可在其中部署 SQL 受控執行個體的網路環境。

   > [!Note]
   > 此 Azure Resource Manager 範本將會部署具有兩個子網路的虛擬網路。 一個子網（稱為 **ManagedInstances**）會保留給 SQL 受控執行個體，並具有預先設定的路由表。 另一個子網（稱為 **預設值**）會用於其他應存取 SQL 受控執行個體的資源 (例如，Azure 虛擬機器) 。

3. 設定網路環境。 在下一張表單上，您可以設定網路環境的參數：

   ![可供設定 Azure 網路的 Resource Manager 範本](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   您可以變更虛擬網路和子網路的名稱，以及調整與您的網路資源關聯的 IP 位址。 選取 [購買]**** 按鈕後，此表單就會建立並設定您的環境。 若您不需要兩個子網路，可將預設子網路刪除。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱 [何謂 SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 深入瞭解 [SQL 受控執行個體的連接架構](connectivity-architecture-overview.md)。
- 瞭解如何 [針對 SQL 受控執行個體修改現有的虛擬網路](vnet-existing-add-subnet.md)。
- 如需說明如何建立虛擬網路、建立受控實例，以及從資料庫備份還原資料庫的教學課程，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 針對 DNS 問題，請參閱 [設定自訂 DNS](custom-dns-configure.md)。
