---
title: 使用 Azure 入口網站在 Azure 中管理 SQL Server Vm |Microsoft Docs
description: 瞭解如何在 Azure 入口網站中，針對裝載于 Azure 上的 SQL Server VM 存取 SQL 虛擬機器資源。
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
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243207"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 入口網站管理 Azure 中的 SQL Server Vm

在[Azure 入口網站](https://portal.azure.com)中， **SQL 虛擬機器**資源是獨立的管理服務。 您可以使用它同時查看所有 SQL Server 的 Vm，並修改 SQL Server 專用的設定： 

![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>備註

- 我們建議您在 Azure 中使用**SQL 虛擬機器**資源來查看和管理您的 SQL Server vm。 但目前**SQL 虛擬機器**資源不支援管理 SQL Server vm 的[支援](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 若要管理 SQL Server Vm 之結束支援的設定，請改用已淘汰的 [ [SQL Server 設定]](#access-the-sql-server-configuration-tab)索引標籤。 
- **Sql 虛擬機器**資源僅適用于已[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)的 SQL Server vm。 


## <a name="access-the-sql-virtual-machines-resource"></a>存取 SQL 虛擬機器資源
若要存取**SQL 虛擬機器**資源，請執行下列動作：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]。 
1. 在 [搜尋] 方塊中輸入**SQL 虛擬機器**。
1. （選擇性）：選取 [ **SQL 虛擬機器**] 旁的星號，將此選項新增至您的 [我的最愛 **]** 功能表。 
1. 選取 **[SQL 虛擬機器**]。 

   ![在所有服務中尋找 SQL Server 的虛擬機器](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 入口網站會列出訂用帳戶內可用的所有 SQL Server Vm。 選取您想要管理的帳戶，以開啟 [ **SQL 虛擬機器**] 資源。 如果您的 SQL Server VM 未出現，請使用 [搜尋] 方塊。 

   ![所有可用的 SQL Server Vm](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   選取您的 SQL Server VM 會開啟 [ **SQL 虛擬機器**] 資源： 


   ![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 虛擬機器**資源適用于專用的 SQL Server 設定。 在 [**虛擬機器**] 方塊中選取 vm 的名稱，以開啟 vm 特定的設定，但不限於 SQL Server。 

## <a name="access-the-sql-server-configuration-tab"></a>存取 SQL Server 設定 索引標籤
[ **SQL Server**設定] 索引標籤已被取代。 目前，這是管理 SQL Server Vm[終止支援](virtual-machines-windows-sql-server-2008-eos-extend-support.md)的唯一方法，以及尚未[向 SQL VM 資源提供者註冊](virtual-machines-windows-sql-register-with-resource-provider.md)的 SQL Server vm。

若要存取已淘汰的**SQL Server**設定 索引標籤，請移至 **虛擬機器** 資源。 使用下列步驟：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。 
1. 選取 [所有服務]。 
1. 在搜尋方塊中輸入 [**虛擬機器**]。
1. （選擇性）：選取 [**虛擬機器**] 旁的星號，將此選項新增至您的 [我的最愛 **]** 功能表。 
1. 選取 [虛擬機器]。 

   ![搜尋虛擬機器](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 入口網站會列出訂用帳戶中的所有虛擬機器。 選取您想要管理的帳戶，以開啟 [**虛擬機器**] 資源。 如果您的 SQL Server VM 未出現，請使用 [搜尋] 方塊。 
1. 在 設定 窗格中選取**SQL Server** **設定**，以管理您的 SQL Server VM。 

   ![SQL Server 設定](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


