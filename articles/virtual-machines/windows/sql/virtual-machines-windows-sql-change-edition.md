---
title: SQL 伺服器版本的就地更改
description: 瞭解如何在 Azure 中更改 SQL Server 虛擬機器的版本。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605452"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>在 Azure VM 上就地更改 SQL Server 版本

本文介紹如何在 Azure 中的 Windows 虛擬機器上更改 SQL Server 版本。 

SQL Server 的版本由產品金鑰確定，並在使用安裝介質的安裝過程中指定。 該版本規定了 SQL Server 產品中可用的[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 您可以使用安裝介質更改 SQL Server 版本，並降級以降低成本或升級以啟用更多功能。

在內部將 SQL Server 版本更改為 SQL Server VM 後，必須更新 Azure 門戶中的 SQL Server 的版本屬性，以便計費。 

## <a name="prerequisites"></a>Prerequisites

要對 SQL Server 版本進行就地更改，您需要執行以下操作： 

- [Azure 訂閱](https://azure.microsoft.com/free/)。
- 在 SQL [VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊的[Windows 上的 SQL Server VM。](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)
- 使用**所需版本的**SQL Server 設置媒體。 擁有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可以從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)獲取其安裝介質。 沒有軟體保證的客戶可以使用 Azure 應用商店 SQL Server VM 映射中的設置媒體，該映射具有所需的版本（通常位於`C:\SQLServerFull`中）。 


## <a name="upgrade-an-edition"></a>升級版本

> [!WARNING]
> 升級 SQL Server 版本將重新開機 SQL Server 的服務以及任何關聯的服務，如分析服務和 R 服務。 

要升級 SQL Server 版本，獲取所需版本的 SQL Server 的 SQL Server 設置媒體，然後執行以下操作：

1. 從 SQL 伺服器安裝介質打開安裝程式.exe。 
1. 轉到**維護**並選擇 **"版本升級**"選項。 

   ![升級 SQL 伺服器版本的選擇](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 選擇 **"下一步**"，直到到達 **"準備升級版**"頁面，然後選擇 **"升級**"。 當更改生效時，設置視窗可能會停止回應幾分鐘。 **"完整"** 頁面將確認您的版本升級已完成。 

升級 SQL Server 版本後，修改 Azure 門戶中的 SQL Server 虛擬機器的版本屬性。 這將更新與此 VM 關聯的中繼資料和帳單。

## <a name="downgrade-an-edition"></a>降級版本

要降級 SQL Server 版本，您需要完全卸載 SQL Server，然後使用所需的版本設置媒體重新安裝它。 

> [!WARNING]
> 卸載 SQL Server 可能會帶來額外的停機時間。 

您可以按照以下步驟降級 SQL Server 版本：

1. 備份所有資料庫，包括系統資料庫。 
1. 將系統資料庫（主資料庫、模型和 msdb）移動到新位置。 
1. 完全卸載 SQL Server 和所有相關服務。 
1. 重新啟動虛擬機器。 
1. 使用具有所需版本的 SQL Server 的媒體安裝 SQL Server。
1. 安裝最新的服務包和累積更新。  
1. 將安裝期間創建的新系統資料庫替換為以前移動到其他位置的系統資料庫。 

降級 SQL Server 版本後，在 Azure 門戶中修改 SQL Server 虛擬機器的版本屬性。 這將更新與此 VM 關聯的中繼資料和帳單。

## <a name="change-edition-in-portal"></a>門戶中的更改版本 

使用安裝介質更改 SQL Server 版本，並將 SQL Server VM 註冊到 SQL VM[資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)後，可以使用 Azure 門戶修改 SQL Server VM 的 Edition 屬性進行計費。 若要這樣做，請依照下列步驟執行： 

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
1. 轉到 SQL Server 虛擬機器資源。 
1. 在 **"設置"** 下，選擇 **"配置**"。 然後從 **"版本**"下的下拉清單中選擇所需的 SQL Server 版本。 

   ![更改版中繼資料](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 查看警告，指出必須首先更改 SQL Server 版本，並且版本屬性必須與 SQL Server 版本匹配。 
1. 選擇 **"應用"** 以應用版本中繼資料更改。 


## <a name="remarks"></a>備註

- SQL Server VM 的版本屬性必須匹配為所有 SQL Server 虛擬機器安裝的 SQL Server 實例的版本，包括即用即付和自帶許可證類型的許可證。
- 如果刪除 SQL Server VM 資源，將返回映射的硬編碼版本設置。
- 更改版本的功能是 SQL VM 資來源提供者的一項功能。 通過 Azure 門戶部署 Azure 應用商店映射會自動向資來源提供者註冊 SQL Server VM。 但是，自行安裝 SQL Server 的客戶將需要手動[註冊其 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md)
- 將 SQL Server VM 添加到可用性集需要重新創建 VM。 添加到可用性集的任何 VM 都將回到預設版本，並且需要再次修改該版本。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL 伺服器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 伺服器常見問題解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 伺服器定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 伺服器的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


