---
title: SQL Server 版本的就地變更
description: 瞭解如何在 Azure 中變更 SQL Server 虛擬機器的版本。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605452"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM 上 SQL Server 版本的就地變更

本文說明如何在 Azure 中變更 Windows 虛擬機器上的 SQL Server 版本。 

SQL Server 的版本取決於產品金鑰，並使用安裝媒體在安裝過程中指定。 版本規定 SQL Server 產品中提供哪些[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 您可以使用安裝媒體變更 SQL Server edition，並進行降級以降低成本或升級，以啟用更多功能。

一旦 SQL Server 的版本在內部變更為 SQL Server VM，您就必須在 Azure 入口網站中更新 SQL Server 的版本屬性，以供計費之用。 

## <a name="prerequisites"></a>先決條件

若要進行 SQL Server 版本的就地變更，您需要下列各項： 

- [Azure 訂](https://azure.microsoft.com/free/)用帳戶。
- 已向[SQL vm 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊之[Windows 上的 SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。
- 使用所需的 SQL Server**版本**來設定媒體。 具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可以從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得其安裝媒體。 沒有軟體保證的客戶可以從具有所需版本的 Azure Marketplace SQL Server VM 映射使用安裝媒體（通常位於`C:\SQLServerFull`）。 


## <a name="upgrade-an-edition"></a>升級版本

> [!WARNING]
> 升級 SQL Server 版本將會重新開機 SQL Server 的服務，以及任何相關聯的服務，例如 Analysis Services 和 R Services。 

若要升級 SQL Server 版本，請取得所需版本 SQL Server 的 SQL Server 安裝媒體，然後執行下列動作：

1. 從 SQL Server 安裝媒體開啟 Setup.exe。 
1. 移至 [**維護**]，然後選擇 [**版本升級**] 選項。 

   ![升級 SQL Server 版本的選取專案](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 選取 **[下一步]** ，直到您到達 [**準備升級版本**] 頁面，然後選取 [**升級**]。 當變更生效時，安裝視窗可能會停止回應數分鐘。 [**完成**] 頁面會確認您的版本升級已完成。 

升級 SQL Server 版本之後，請在 Azure 入口網站中修改 SQL Server 虛擬機器的版本屬性。 這會更新與此 VM 相關聯的中繼資料和計費。

## <a name="downgrade-an-edition"></a>降級版本

若要降級 SQL Server 的版本，您必須完全卸載 SQL Server，然後使用所需的版本設定媒體重新安裝它。 

> [!WARNING]
> 卸載 SQL Server 可能會產生額外的停機時間。 

您可以遵循下列步驟，將 SQL Server 的版本降級：

1. 備份所有資料庫，包括系統資料庫。 
1. 將系統資料庫（master、model 和 msdb）移至新位置。 
1. 完全卸載 SQL Server 和所有相關聯的服務。 
1. 重新啟動虛擬機器。 
1. 使用具有所需 SQL Server 版本的媒體來安裝 SQL Server。
1. 安裝最新的 service pack 和累積更新。  
1. 將安裝期間所建立的新系統資料庫，取代為您先前移至不同位置的系統資料庫。 

在降級 SQL Server 版本之後，請在 Azure 入口網站中修改 SQL Server 虛擬機器的版本屬性。 這會更新與此 VM 相關聯的中繼資料和計費。

## <a name="change-edition-in-portal"></a>在入口網站中變更版本 

一旦您使用安裝媒體變更了 SQL Server 版本，並向[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊您的 SQL Server VM 之後，您就可以使用 Azure 入口網站來修改 SQL Server VM 的版本屬性，以供計費之用。 若要這樣做，請依照下列步驟執行： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 移至您的 SQL Server 虛擬機器資源。 
1. 在 [**設定**] 底下，選取 [**設定**]。 然後從 [**版本**] 下的下拉式清單中，選取您想要的 SQL Server 版本。 

   ![變更版本中繼資料](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 請參閱警告，指出您必須先變更 SQL Server edition，而且版本屬性必須符合 SQL Server 版。 
1. 選取 [套用] 以套用**您的版本**中繼資料變更。 


## <a name="remarks"></a>備註

- SQL Server VM 的 [版本] 屬性必須符合針對所有 SQL Server 虛擬機器安裝的 SQL Server 實例版本，包括隨用隨付和自備授權類型的授權。
- 如果您卸載 SQL Server 的 VM 資源，將會回到映射的硬式編碼版本設定。
- 變更版本的能力是 SQL VM 資源提供者的一項功能。 透過 Azure 入口網站部署 Azure Marketplace 映射，會自動向資源提供者註冊 SQL Server VM。 不過，自行安裝 SQL Server 的客戶必須手動[註冊其 SQL SERVER VM](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 將 SQL Server VM 新增至可用性設定組需要重新建立 VM。 任何新增至可用性設定組的 Vm 都會回到預設版本，而且必須再次修改版本。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 總覽](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


