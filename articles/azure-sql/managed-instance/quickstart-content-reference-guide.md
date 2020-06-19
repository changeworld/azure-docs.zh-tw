---
title: 內容參考的使用者入門
titleSuffix: Azure SQL Managed Instance
description: '可協助您開始使用 Azure SQL 受控執行個體的內容參考。 '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: 7c7268aa361c77f1d466ab7a58b74aa91090dc4b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708564"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>開始使用 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL 受控執行個體](sql-managed-instance-paas-overview.md)可建立幾乎與最新的 SQL Server (企業版) 資料庫引擎 100% 相容的資料庫，並提供可因應常見之安全性考量的原生[虛擬網路 (VNet)](../../virtual-network/virtual-networks-overview.md) 實作，以及有利於現有 SQL Server 客戶的[商務模型](https://azure.microsoft.com/pricing/details/sql-database/)。

在本文中，您會找到內容參考，教您如何快速地設定及建立 SQL 受控執行個體，並移轉資料庫。

## <a name="quickstart-overview"></a>快速入門概觀

下列快速入門可讓您快速地建立 SQL 受控執行個體、為用戶端應用程式設定虛擬機器或點對站 VPN 連線，以及使用 `.bak` 檔案將資料庫還原至新的 SQL 受控執行個體。

### <a name="configure-environment"></a>設定環境

首先，您必須建立第一個 SQL受控執行個體及其放置所在的網路環境，並啟用從您執行查詢的電腦或虛擬機器到 SQL受控執行個體的連線。 您可以使用下列指南：

- [使用 Azure 入口網站建立 SQL 受控執行個體](instance-create-quickstart.md)。 在 Azure 入口網站中，您可以設定必要參數 (使用者名稱/密碼、核心數目和儲存體數量上限)，並自動建立 Azure 網路環境，而無需了解網路詳細資料和基礎結構需求。 您只須確認自己具有目前允許用來建立 SQL 受控執行個體的[訂用帳戶類型](resource-limits.md#supported-subscription-types)。 若您想要使用自己的網路，或想要自訂網路，請參閱[針對 Azure SQL 受控執行個體設定現有虛擬網路](vnet-existing-add-subnet.md) (英文) 或[建立 Azure SQL 受控執行個體的虛擬網路](virtual-network-subnet-create-arm-template.md) (英文)。
- SQL 受控執行個體會建立在沒有公用端點的自有 VNet 中。 針對用戶端應用程式的存取，您可以**在相同 VNet (不同子網路) 中建立 VM**，或使用以下其中一個快速入門來**建立用戶端電腦到 VNet 的點對站 VPN 連線**：
  - 在您的 SQL 受控執行個體上啟用[公用端點](public-endpoint-configure.md)，以便直接從您的環境存取您的資料。
  - 在 [SQL 受控執行個體 VNet 中建立 Azure 虛擬機器](connect-vm-instance-configure.md)，以進行用戶端應用程式連線，包括 SQL Server Management Studio。
  - 從具有 SQL Server Management Studio 與其他用戶端連線應用程式的用戶端電腦中，[對您的 SQL 受控執行個體設定點對站 VPN 連線](point-to-site-p2s-configure.md)。 這是其他兩個可連線到您 SQL 受控執行個體與其 VNet 的選項。

  > [!NOTE]
  > - 您也可以從區域網路使用 ExpressRoute 或站對站連線，但這些方法並不在這些快速入門所涵蓋的範圍內。
  > - 如果您將保留期間從 0 (無限制的保留) 變更為任何其他值，請注意保留期只會套用至保留值變更之後所寫入的記錄 (設定為無限制的期間所寫入的記錄會予以保留，即使已啟用保留期)。

作為手動建立 SQL 受控執行個體的替代方式，您可以使用 [PowerShell](scripts/create-configure-managed-instance-powershell.md)、[PowerShell 搭配 Resource Manager 範本](scripts/create-powershell-azure-resource-manager-template.md)或 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) 來撰寫指令碼並自動化此程序。

### <a name="migrate-your-databases"></a>移轉資料庫

在您建立 SQL 受控執行個體並設定存取後，即可開始移轉 SQL Server 資料庫。 若您想要移轉之來源資料庫具有不支援的功能，移轉就會失敗。 若要避免失敗並檢查相容性，您可以使用 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，以分析您位於 SQL Server 上的資料庫，並找出可能會封鎖移轉到 SQL 受控執行個體的所有問題 (例如存在 [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 或多個記錄檔)。 若您解決這些問題，您的資料庫便已準備好移轉到 SQL 受控執行個體。 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)是另一個實用的工具，可記錄您在 SQL Server 上的工作負載，並在 SQL 受控執行個體上重播，讓您可以判斷若移轉到 SQL 受控執行個體是否會發生任何效能問題。

一旦您確定可以將資料庫移轉到 SQL 受控執行個體後，即可使用原生的 SQL Server 還原功能，從 `.bak` 檔案將資料庫還原到 SQL 受控執行個體。 您可以使用此方法，從安裝在內部部署或 Azure 虛擬機器的 SQL Server 資料庫引擎移轉資料庫。 如需快速入門，請參閱[從備份還原至 SQL 受控執行個體](restore-sample-database-quickstart.md)。 在此快速入門中，您將使用 `RESTORE` Transact-SQL 命令，從儲存在 Azure Blob 儲存體中的 `.bak` 檔案進行還原。

> [!TIP]
> 若要使用 `BACKUP` TRANSACT-SQL 命令來為 Azure Blob 儲存體中的資料庫建立備份，請參閱 [SQL Server 備份至 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

這些快速入門可讓您快速地建立、設定，並還原資料庫備份到 SQL 受控執行個體。 在某些情況下，您必須對 SQL 受控執行個體與必要的網路環境進行自訂或自動化部署。 這些案例將於下方詳述。

## <a name="customize-network-environment"></a>自訂網路環境

雖然 VNet/子網路可以在[使用 Azure 入口網站建立](instance-create-quickstart.md)執行個體時進行自動設定，但建議開始在 SQL 受控執行個體中建立執行個體前，先建立 VNet/子網路，因為您可以設定 VNet 與子網路的參數。 建立並設定網路環境最簡單的方式，便是使用 [Azure 資源部署](virtual-network-subnet-create-arm-template.md)範本，其會建立和設定要放置執行個體的網路與子網路。 您只需要按下 Azure Resource Manager 部署按鈕，並將參數填入表單中。

或者，您也可以使用此 [PowerShell 指令碼](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) \(英文\) 來將網路的建立自動化。

若您已經有要部署 SQL 受控執行個體的 VNet 與子網路，您必須確定 VNet 與子網路皆能滿足[網路需求](connectivity-architecture-overview.md#network-requirements)。 使用這個 [PowerShell 指令碼來確認子網路已正確設定](vnet-existing-add-subnet.md)。 此指令碼會驗證您的網路並回報任何問題，還會告訴您應變更的項目，然後提供 VNet/子網路所必要的變更。 如果您不想要手動設定 VNet/子網路，請執行此指令碼。 您也可以在大幅地重新設定網路基礎結構後，執行此指令碼。 若要建立及設定您自己的網路，請參閱[連線架構](connectivity-architecture-overview.md)與這篇[建立及設定 SQL 受控執行個體環境的終極指南](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)。

## <a name="migrate-to-a-sql-managed-instance"></a>移轉到 SQL 受控執行個體

先前提及的快速入門可讓您快速地設定 SQL 受控執行個體，並使用原生`RESTORE`功能來移動資料庫。 如果您想要完成快速概念證明並確認您的解決方案可以在受控執行個體上運行，這是一個很好的起點。

但是，若要移轉您要用於某些效能測試的生產資料庫或甚至開發/測試資料庫，您需要考慮使用一些額外的技術，例如：

- 效能測試 - 您應測量來源 SQL Server 執行個體上的基準效能計量，並將其與移轉資料庫之目的地 SQL 受控執行個體的效能計量進行比較。 深入了解[進行效能比較的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) \(英文\)。
- 線上移轉 - 使用此文章中描述的原生 `RESTORE`，您必須等待資料庫進行還原並複製到 Azure Blob 儲存體 (如果資料庫尚未儲存在該處)。 這會導致您的應用程式有停機時間，特別是處理較大型資料庫的時候。 若要移動您的生產資料庫，請使用[資料移轉服務 (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)，才能以最短的停機時間來遷移資料庫。 DMS 會用累加方式將來源資料庫中之變更推送到已還原的 SQL 受控執行個體資料庫，以完成移轉。 透過此方法，您可以在最短停機時間的前提下，快速地將應用程式從來源資料庫切換到目標資料庫。

深入了解[建議的移轉程序](migrate-to-instance-from-sql-server.md)。

## <a name="next-steps"></a>後續步驟

- 在[這裡取得 SQL 受控執行個體支援功能的高階清單](../database/features-comparison.md)，並在[這裡取得詳細資料與已知問題](transact-sql-tsql-differences-sql-server.md)。
- 了解 [SQL 受控執行個體的技術特性](resource-limits.md#service-tier-characteristics)。
- 如需更進階的操作說明，請參閱[如何使用 SQL 受控執行個體](how-to-content-reference-guide.md)。
- [為您的內部部署資料庫識別正確的 Azure SQL 受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
