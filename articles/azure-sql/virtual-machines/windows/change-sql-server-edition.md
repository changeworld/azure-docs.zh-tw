---
title: SQL Server 版本的就地變更
description: 瞭解如何將 Azure 中 SQL Server 虛擬機器的版本變更為降級，以降低成本或升級以啟用更多功能。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 997e867798922975757a588ef50248f0d09a96e0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789840"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM 上 SQL Server 版本的就地變更
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文描述如何變更 Azure 中 Windows 虛擬機器上 SQL Server 的版本。 

SQL Server 的版本是由產品金鑰所決定，且會在使用安裝媒體進行安裝的流程期間指定。 版本會指定在 SQL Server 產品中可供使用的[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 您可使用安裝媒體來變更 SQL Server 版本，並進行降級以減少成本，或升級以啟用更多功能。

當 SQL Server 的版本於內部變更為 SQL Server VM 時，您接著即必須在 Azure 入口網站中更新 SQL Server 的版本屬性，以供計費使用。 

## <a name="prerequisites"></a>必要條件

若要執行 SQL Server 版本的就地變更，您需要下列項目： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- 向 [SQL VM 資源提供者](sql-vm-resource-provider-register.md)註冊的 [Windows 上的 SQL Server VM](./create-sql-vm-portal.md)。
- 包含所需 SQL Server **版本** 的安裝媒體。 具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得安裝媒體。 沒有軟體保證的客戶可使用安裝媒體，其來自包含所需版本 Azure Marketplace SQL Server VM 映像 (通常位於 `C:\SQLServerFull`)。 


## <a name="upgrade-an-edition"></a>升級版本

> [!WARNING]
> 升級 SQL Server 版本將會重新啟動 SQL Server 服務，以及任何相關聯的服務 (例如 Analysis Services 和 R Services)。 

若要升級 SQL Server 的版本，請取得所需 SQL Server 版本的 SQL Server 安裝媒體，然後執行下列操作：

1. 開啟 SQL Server 安裝媒體上的 Setup.exe。 
1. 前往 [維護]，然後選擇 [版本升級] 選項。 

   ![選取升級 SQL Server 的版本](./media/change-sql-server-edition/edition-upgrade.png)

1. 選取 [下一步]，直到到達 [已準備好升級版本] 頁面，然後選取 [升級]。 變更生效期間，安裝視窗可能會停止回應數分鐘。 [完成] 頁面會確認版本升級已完成。 

升級 SQL Server 版本後，請在 Azure 入口網站中修改 SQL Server 虛擬機器的版本屬性。 這會更新與此 VM 建立關聯的中繼資料和計費。

## <a name="downgrade-an-edition"></a>降級版本

若要降級 SQL Server 版本，則需要完全解除安裝 SQL Server，然後使用所需版本的安裝媒體來重新安裝。 

> [!WARNING]
> 解除安裝 SQL Server 可能會產生額外的停機。 

您可透過遵循下列步驟來降級 SQL Server 版本：

1. 備份所有資料庫 (包括系統資料庫)。 
1. 將系統資料庫 (master、model 和 msdb) 移動到新的位置。 
1. 完全解除安裝 SQL Server 和所有相關聯的服務。 
1. 重新啟動虛擬機器。 
1. 使用包含所需 SQL Server 版本的媒體安裝 SQL Server。
1. 安裝最新的 Service Pack 和累積更新。  
1. 將在安裝期間建立新系統資料庫取代成先前移動到不同位置的系統資料庫。 

在降級 SQL Server 版本後，請在 Azure 入口網站中修改 SQL Server 虛擬機器的版本屬性。 這會更新與此 VM 建立關聯的中繼資料和計費。

## <a name="change-edition-in-portal"></a>在入口網站中變更版本 

在使用安裝媒體變更 SQL Server 的版本，且向 [SQL VM 資源提供者](sql-vm-resource-provider-register.md)註冊 SQL Server VM 後，接著即可使用 Azure 入口網站來修改 SQL Server VM 的版本屬性，以供計費使用。 若要這樣做，請依照下列步驟執行： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 前往 SQL Server 虛擬機器資源。 
1. 在 [設定] 下方，選取 [設定]。 然後從 [版本] 下方的下拉式清單中，選取所需的 SQL Server 版本。 

   ![變更版本中繼資料](./media/change-sql-server-edition/edition-change-in-portal.png)

1. 檢閱提示您必須先變更 SQL Server 版本的警告，並確認版本屬性與 SQL Server 版本相符。 
1. 選取 [套用] 以套用版本中繼資料變更。 


## <a name="remarks"></a>備註

- SQL Server VM 版本屬性必須與為所有 SQL Server 虛擬機器安裝的 SQL Server 執行個體版本相符 (包括隨用隨付和自備授權授權類型)。
- 如果卸除 SQL Server VM 資源，則會回復為映像的硬式編碼版本設定。
- 變更版本是 SQL VM 資源提供者的功能之一。 透過 Azure 入口網站部署 Azure Marketplace 映像，會自動向資源提供者註冊 SQL Server VM。 但自行安裝 SQL Server 的客戶將必須手動[註冊其 SQL Server VM](sql-vm-resource-provider-register.md)。
- 將 SQL Server VM 新增至可用性設定組，則需要重新建立 VM。 任何新增至可用性設定組的 VM 都會回到預設版本，且必須再次修改版本。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定價指導](pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](doc-changes-updates-release-notes.md)