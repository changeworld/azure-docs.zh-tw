---
title: 使用 Azure 門戶管理 Azure 中的 SQL 伺服器 VM |微軟文檔
description: 瞭解如何訪問 Azure 門戶中託管在 Azure 上的 SQL Server VM 的 SQL 虛擬機器資源。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243207"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>使用 Azure 門戶管理 Azure 中的 SQL 伺服器 VM

在[Azure 門戶](https://portal.azure.com)中 **，SQL 虛擬機器**資源是一個獨立的管理服務。 您可以使用它同時查看所有 SQL Server VM 並修改專用於 SQL Server 的設置： 

![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>備註

- 我們建議您使用**SQL 虛擬機器**資源在 Azure 中查看和管理 SQL Server VM。 但是，目前 SQL**虛擬機器**資源不支援[管理支援終止](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server VM。 要管理支援終止 SQL Server VM 的設置，請使用已棄用的[SQL Server 配置選項卡](#access-the-sql-server-configuration-tab)。 
- **SQL 虛擬機器**資源僅適用于[已註冊到 SQL VM 資來源提供者的](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM。 


## <a name="access-the-sql-virtual-machines-resource"></a>訪問 SQL 虛擬機器資源
要訪問**SQL 虛擬機器**資源，可以執行以下操作：

1. 打開[Azure 門戶](https://portal.azure.com)。 
1. 選擇**所有服務**。 
1. 在搜索框中輸入**SQL 虛擬機器**。
1. （可選）：選擇**SQL 虛擬機器**旁邊的星形，將此選項添加到**我的最愛**功能表中。 
1. 選擇**SQL 虛擬機器**。 

   ![在所有服務中查找 SQL Server 虛擬機器](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. 門戶列出了訂閱中可用的所有 SQL Server VM。 選擇要管理以打開**SQL 虛擬機器**資源。 如果 SQL Server VM 未顯示，請使用搜索框。 

   ![所有可用的 SQL 伺服器 VM](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   選擇 SQL Server VM 將打開**SQL 虛擬機器**資源： 


   ![SQL 虛擬機器資源](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 虛擬機器**資源用於專用 SQL Server 設置。 在**虛擬機器**框中選擇 VM 的名稱以打開特定于 VM 但不特定于 SQL Server 的設置。 

## <a name="access-the-sql-server-configuration-tab"></a>訪問 SQL 伺服器配置選項卡
已棄用**SQL Server 配置**選項卡。 此時，它是管理[支援終止](virtual-machines-windows-sql-server-2008-eos-extend-support.md)SQL Server VM 和尚未[向 SQL VM 資來源提供者註冊的](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM 的唯一方法。

要訪問已棄用的**SQL Server 配置**選項卡，請訪問**虛擬機器**資源。 使用下列步驟：

1. 打開[Azure 門戶](https://portal.azure.com)。 
1. 選擇**所有服務**。 
1. 在搜索框中輸入**虛擬機器**。
1. （可選）：選擇**虛擬機器**旁邊的星形，將此選項添加到**我的最愛**功能表。 
1. 選擇**虛擬機器**。 

   ![搜索虛擬機器](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. 門戶列出訂閱中的所有虛擬機器。 選擇要管理以打開**虛擬機器**資源。 如果 SQL Server VM 未顯示，請使用搜索框。 
1. 在 **"設置"** 窗格中選擇**SQL Server 配置**以管理 SQL Server VM。 

   ![SQL Server 設定](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


