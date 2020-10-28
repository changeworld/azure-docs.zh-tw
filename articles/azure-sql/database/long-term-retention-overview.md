---
title: 長期備份保留期
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解 Azure SQL Database & Azure SQL 受控執行個體如何透過長期保留原則，支援最多10年的時間儲存完整的資料庫備份。
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 05/18/2019
ms.openlocfilehash: 8250fc39fe58168ddc13b7bcf5c040b57d5e92fb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782615"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>長期保留-Azure SQL Database 和 Azure SQL 受控執行個體

許多應用程式具有法規、合規性或其他商務用途，需要您保留超過7-35 天的資料庫備份，Azure SQL Database 和 Azure SQL 受控執行個體 [自動備份](automated-backups-overview.md)所提供的備份。 藉由使用長期保留 (LTR) 功能，您可以在 Azure Blob 儲存體中儲存指定的 SQL Database 和 SQL 受控執行個體完整備份，並設定最多10年的 [冗余](automated-backups-overview.md#backup-storage-redundancy) 。 之後您可以將任何備份還原為新的資料庫。

您可以針對 Azure SQL Database 啟用長期保留，且在 Azure SQL 受控執行個體的有限公開預覽中。 本文提供長期保留的概念性總覽。 若要設定長期保留，請參閱 [設定 AZURE SQL DATABASE ltr](long-term-backup-retention-configure.md) 並 [設定 AZURE SQL 受控執行個體 ltr](../managed-instance/long-term-backup-retention-configure.md)。 

> [!NOTE]
> 您可以使用 SQL Agent 作業來排程[僅限複製的資料庫備份](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)，以替代超過 35 天的 LTR。


## <a name="how-long-term-retention-works"></a>長期保留的運作方式
     
長期備份保留 (LTR) 會運用[自動建立](automated-backups-overview.md)的完整資料庫備份來啟用時間點還原 (PITR)。 如果已設定 LTR 原則，則會將這些備份複製到不同的 blob 以供長期儲存。 複製是背景工作，對資料庫工作負載沒有任何效能影響。 SQL Database 中每個資料庫的 LTR 原則也可以指定 LTR 備份的建立頻率。

若要啟用 LTR，您可以使用四個參數的組合來定義原則：每週備份保留 (W) 、每月備份保留 (M) 、每年備份保留 (Y) 和年中的周 (WeekOfYear) 。 如果指定 W，每週一次的備份會複製到長期儲存體。 如果您指定 M，每個月的第一次備份將會複製到長期儲存體。 如果指定 Y，在以 WeekOfYear 指定的當週進行的一次備份會複製到長期儲存體。 如果在設定原則時，指定的 WeekOfYear 是過去的，則會在下一年建立第一個 LTR 備份。 每個備份都會根據建立 LTR 備份時所設定的原則參數，保留在長期儲存體中。

> [!NOTE]
> LTR 原則的任何變更只適用于未來的備份。 例如，如果每週備份保留 (W) 、每月備份保留 (M) ，或每年備份保留 (Y) 修改，則新的保留設定只會套用至新的備份。 將不會修改現有備份的保留。 如果您想要在保留期限到期之前刪除舊的 LTR 備份，您將需要 [手動刪除備份](./long-term-backup-retention-configure.md#delete-ltr-backups)。
> 

LTR 原則的範例：

-  W=0, M=0, Y=5, WeekOfYear=3

   每年的第三次完整備份將會保留五年。
   
- W=0, M=3, Y=0

   每個月的第一個完整備份將會保留三個月。

- W=12, M=0, Y=0

   每個每週完整備份皆會保留 12 週。

- W=6, M=12, Y=10, WeekOfYear=16

   每週完整備份會保留六周。 但每月的第 1 個完整備份除外，這個備份會保留 12 個月。 以及每年的第 16 週的完整備份除外，這個備份會保留 10 年。 

下表說明以下原則的長期備份日程和到期日：

W=12 週 (84 天)、M=12 個月 (365 天)、Y=10 年 (3650 天)、WeekOfYear=15 (4 月 15 日後那一周)

   ![ITR 範例](./media/long-term-retention-overview/ltr-example.png)


如果您修改上述原則，並設定 W = 0 (沒有每週備份) ，備份複本的步調將會變更，如上表所示的反白顯示日期。 用來保留這些備份所需的儲存體數量會跟著減少。 

> [!IMPORTANT]
> 個別 LTR 備份的時間是由 Azure 所控制。 您無法手動建立 LTR 備份，或控制備份建立的時間。 設定 LTR 原則之後，最多可能需要7天的時間，第一個 LTR 備份才會顯示在可用備份清單中。  


## <a name="geo-replication-and-long-term-backup-retention"></a>異地複寫和長期備份保留

如果您使用主動式異地複寫或容錯移轉群組做為您的商務持續性解決方案，您應該為最終容錯移轉做好準備，並在次要資料庫或實例上設定相同的 LTR 原則。 您的 LTR 儲存體成本不會增加，因為不會從次要複本產生備份。 只有當次要複本變成主要複本時，才會建立備份。 它可確保在觸發容錯移轉且主要區域移至次要區域時，不會中斷的 LTR 備份產生。 

> [!NOTE]
> 當原始的主資料庫從造成容錯移轉的中斷中復原時，它會變成新的次要資料庫。 因此，備份的建立不會繼續，而且現有的 LTR 原則將不會生效，除非它再次變成主要資料庫。 

## <a name="sql-managed-instance-support"></a>SQL 受控執行個體支援

搭配 Azure SQL 受控執行個體使用長期備份保留有下列限制：

- **有限的公開預覽** -此預覽版僅適用于 EA 和 CSP 訂用帳戶，且受限於有限的可用性。  
- [**僅限 PowerShell**](../managed-instance/long-term-backup-retention-configure.md) -目前沒有 Azure 入口網站支援。 必須使用 PowerShell 來啟用 LTR。 

若要要求註冊，請建立 [Azure 支援票證](https://azure.microsoft.com/support/create-ticket/)。 針對 [問題類型] 選取 [技術問題]，針對 [服務選擇 SQL 受控執行個體]，針對問題類型選取 [ **備份]、[還原] 和 [商務持續性/長期備份保留** ]。 在您的要求中，請確定您想要註冊至 SQL 受控執行個體的限時公開預覽。

## <a name="configure-long-term-backup-retention"></a>設定長期備份保留期

您可以使用適用于 Azure SQL Database 的 Azure 入口網站和 PowerShell，以及適用于 Azure SQL 受控執行個體的 PowerShell 來設定長期備份保留。 若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 您可以將資料庫還原到與原始資料庫位於相同訂用帳戶下的任何現有伺服器或受控實例。

若要瞭解如何設定長期保留或從備份還原資料庫，以使用 Azure 入口網站或 PowerShell 進行 SQL Database，請參閱 [管理 Azure SQL Database 長期備份保留](long-term-backup-retention-configure.md)

若要瞭解如何使用 PowerShell 設定 SQL 受控執行個體的長期保留或從備份還原資料庫，請參閱 [管理 AZURE sql 受控執行個體長期備份保留](../managed-instance/long-term-backup-retention-configure.md)。

若要從 LTR 儲存體還原資料庫，可以依時間戳記選取特定備份。 可將資料庫還原至原始資料庫相同訂用帳戶底下的任何現有伺服器。 若要瞭解如何使用 Azure 入口網站或 PowerShell 從 LTR 備份還原資料庫，請參閱 [管理 Azure SQL Database 長期備份保留](long-term-backup-retention-configure.md)。 在您的要求中，請確定您想要在 SQL 受控執行個體的限時公開預覽版中註冊。

## <a name="next-steps"></a>後續步驟

因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
