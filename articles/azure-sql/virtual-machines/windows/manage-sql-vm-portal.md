---
title: 使用 Azure 入口網站管理 Azure 中的 SQL Server VM |Microsoft Docs
description: 了解如何在 Azure 入口網站中，針對裝載於 Azure 上的 SQL Server VM 存取 SQL 虛擬機器資源。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6cf6fac84abd1d996c77aae7240c0322c5ec53fb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84036859"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 入口網站管理 Azure 中的 SQL Server VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 [Azure 入口網站](https://portal.azure.com)中，**SQL 虛擬機器**資源是獨立的管理服務。 您可以使用它同時檢視所有 SQL Server VM，並修改 SQL Server 專用的設定： 

![SQL 虛擬機器資源](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>備註

- 建議您使用 **SQL 虛擬機器**資源，以在 Azure 中檢視及管理您的 SQL Server VM。 但目前，**SQL 虛擬機器**資源不支援管理[已終止支援](sql-server-2008-extend-end-of-support.md)的 SQL Server VM。 若要管理已終止支援 SQL Server VM 的設定，請改為使用已過時的 [SQL Server [設定] 索引標籤](#access-the-sql-server-configuration-tab)。 
- **SQL 虛擬機器**資源僅適用於[已向 SQL VM 資源提供者註冊](sql-vm-resource-provider-register.md)的 SQL Server VM。 


## <a name="access-the-sql-virtual-machines-resource"></a>存取 SQL 虛擬機器資源
若要存取 **SQL 虛擬機器**資源，請執行下列動作：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]。 
1. 在 [搜尋] 方塊中，輸入 **SQL 虛擬機器**。
1. (選擇性)：選取 [SQL 虛擬機器] 旁的星號，以將此選項新增至您的 [我的最愛] 功能表。 
1. 選取 [SQL 虛擬機器]。 

   ![在所有服務中尋找 SQL Server 虛擬機器](./media/manage-sql-vm-portal/sql-vm-search.png)

1. 入口網站會列出訂用帳戶內所有可用的 SQL Server VM。 選取您要管理的 VM，以開啟 [SQL 虛擬機器] 資源。 如果您的 SQL Server VM 未顯示，請使用 [搜尋] 方塊。 

   ![所有可用的 SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   選取您的 SQL Server VM 會開啟 [SQL 虛擬機器] 資源： 


   ![SQL 虛擬機器資源](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 虛擬機器**資源適用於專用的 SQL Server 設定。 在 [虛擬機器] 方塊中選取 VM 的名稱，以開啟 VM 特定但並非 SQL Server 專用的設定。 

## <a name="access-the-sql-server-configuration-tab"></a>存取 SQL Server [設定] 索引標籤
SQL Server [設定] 索引標籤已過時。 目前，它是管理[已終止支援](sql-server-2008-extend-end-of-support.md)之 SQL Server VM，以及尚未[向 SQL VM 資源提供者註冊](sql-vm-resource-provider-register.md)之 SQL Server VM 的唯一方法。

若要存取已過時的 SQL Server [設定] 索引標籤，請移至 [虛擬機器] 資源。 使用下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]。 
1. 在 [搜尋] 方塊中，輸入**虛擬機器**。
1. (選擇性)：選取 [虛擬機器] 旁的星號，以將此選項新增至您的 [我的最愛] 功能表。 
1. 選取 [虛擬機器]。 

   ![搜尋虛擬機器](./media/manage-sql-vm-portal/vm-search.png)

1. 入口網站會列出訂用帳戶中的所有虛擬機器。 選取您要管理的虛擬機器，以開啟 [虛擬機器] 資源。 如果您的 SQL Server VM 未顯示，請使用 [搜尋] 方塊。 
1. 在 [設定] 窗格中選取 [SQL Server 設定]，以管理您的 SQL Server VM。 

   ![SQL Server 設定](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上 SQL Server 的常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](doc-changes-updates-release-notes.md)


