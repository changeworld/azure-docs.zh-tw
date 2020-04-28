---
title: 嚴重損壞修復演練
description: 使用 Azure SQL Database 來執行災害復原鑽研的學習指引和最佳做法。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825860"
---
# <a name="performing-disaster-recovery-drill"></a>執行災害復原演練

建議您定期驗證應用程式的復原工作流程整備。 最佳設計作法是，驗證容錯移轉所涉及之資料遺失和 (或) 中斷的應用程式行為和影響。 這也是大多數業界標準在商務持續性認證中的規定。

災害復原演練內容包括：

* 模擬資料層中斷情況
* 復原
* 驗證復原後的應用程式完整性

執行演練的工作流程會因您 [設計商務持續性之應用程式](sql-database-business-continuity.md)的方式而異。 本文說明在 Azure SQL Database 的內容中進行災害復原演練的最佳做法。

## <a name="geo-restore"></a>異地還原

為了避免在進行災害復原演練時可能遺失資料，請使用測試環境來執行演練，方法是建立生產環境的複本，然後使用這個複本來驗證應用程式的容錯移轉工作流程。

### <a name="outage-simulation"></a>中斷模擬

若要模擬中斷，您可以重新命名來源資料庫。 變更此名稱會導致應用程式連線失敗。

### <a name="recovery"></a>修復

* 如[這裡](sql-database-disaster-recovery.md)所述，將資料庫異地還原到不同的伺服器。
* 將應用程式組態變更為連接到復原資料庫，並遵循[在復原後設定資料庫](sql-database-disaster-recovery.md)指南以完成復原。

### <a name="validation"></a>驗證

驗證復原後的應用程式完整性 (包括連接字串、登入、基本功能測試或標準應用程式登出程序的其他驗證部分)，完成演練。

## <a name="failover-groups"></a>容錯移轉群組

針對使用容錯移轉群組所保護的資料庫，本演練內容涵蓋規劃容錯移轉至次要伺服器。 計劃性容錯移轉可確保在切換角色時，容錯移轉群組中的主要與次要資料庫會保持同步。 與未計畫的容錯移轉不同的是，這項作業不會導致資料遺失，所以可以在生產環境中執行這項演練。

### <a name="outage-simulation"></a>中斷模擬

若要模擬中斷，您可以停用連接到資料庫的 Web 應用程式或虛擬機器。 這種中斷模擬會導致 Web 用戶端的連線失敗。

### <a name="recovery"></a>修復

* 請確定 DR 區域中的應用程式組態指向先前的次要資料庫，該資料庫會變成可完全存取的新主要資料庫。
* 從次要伺服器起始容錯移轉群組的[計劃性容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)。
* 請遵循 [在復原後設定資料庫](sql-database-disaster-recovery.md) 指南以完成復原。

### <a name="validation"></a>驗證

驗證復原後的應用程式完整性 (包括連線能力、基本功能測試或演練登出所需的其他驗證) 來完成演練。

## <a name="next-steps"></a>後續步驟

* 若要了解商務持續性案例，請參閱[持續性案例](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要瞭解如何使用自動備份進行復原，請參閱[從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-active-geo-replication.md)和[自動容錯移轉群組](sql-database-auto-failover-group.md)。
