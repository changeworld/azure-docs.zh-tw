---
title: 進行應用程式升級
description: 瞭解如何使用 Azure SQL Database 異地複寫來支援雲端應用程式的輪流升級
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 44005dafb1e3eee60f163f80ad2e4282147233e4
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355613"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>使用 SQL Database 主動式異地複寫管理雲端應用程式的輪流升級
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

了解如何使用 Azure SQL Database 中的[主動式異地複寫](auto-failover-group-overview.md)來啟用雲端應用程式的輪流升級。 因為升級是干擾性作業，應該是您商務持續性規劃與設計的一部分。 在本文中，我們將探討兩種不同的升級程序協調方法，並討論每個選項的優點和取捨。 基於本文的目的，我們偏好使用一個應用程式，它是由連線到單一資料庫作為其資料層的網站所組成。 我們的目標是將應用程式的版本 1 (V1) 升級到版本 2 (V2)，而不對使用者體驗造成顯著的影響。

評估升級選項時，請考量下列因素：

* 在升級期間對應用程式可用性的影響，例如應用程式函式可能受限制或降級的時間長度。
* 升級失敗時的復原能力。
* 升級期間發生不相關的重大失敗時，應用程式的弱點。
* 總金額成本。 此因素包括額外資料庫備援，以及升級程序所用暫時性元件的累加成本。

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>升級依賴資料庫備份以進行災害復原的應用程式

如果您的應用程式依賴自動資料庫備份，並針對災害復原使用異地復原，則會部署到單一 Azure 區域。 為了將使用者干擾降到最低，請在該區域中建立預備環境，該環境具有升級相關的所有應用程式元件。 下圖說明升級程序之前的作業環境。 端點 `contoso.azurewebsites.net` 代表 Web 應用程式的生產環境。 若要能夠復原升級，您必須建立含有資料庫完整同步複本的預備環境。 依照下列步驟來建立升級的預備環境：

1. 在相同區域中建立次要資料庫。 監視次要資料庫，查看植入程序是否完成 (1)。
2. 為您的 Web 應用程式建立新環境，稱之為「預備」。 它會在 Azure DNS 中登錄，其 URL 為 `contoso-staging.azurewebsites.net` (2)。

> [!NOTE]
> 這些準備步驟不會影響生產環境，其可在完整存取模式下運作。

![圖顯示雲端嚴重損壞修復的 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option1-1.png)

準備步驟完成時，應用程式就已準備好實際升級。 下圖說明升級程序的相關步驟：

1. 將主要資料庫設為唯讀模式 (3)。 這個模式保證 Web 應用程式的生產環境 (V1) 在升級期間會保持唯讀狀態，藉此防止 V1 與 V2 資料庫執行個體間的資料分歧。
2. 使用規劃的終止模式 (4) 中斷次要資料庫的連線。 此動作會建立主要資料庫的完全同步獨立複本。 此資料庫將會升級。
3. 將次要資料庫切換為讀寫模式，然後執行升級指令碼 (5)。

![圖顯示執行升級腳本的雲端嚴重損壞修復 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option1-2.png)

如果升級順利完成，您現在已準備好將使用者切換至升級後的應用程式複本，其可成為生產環境。 切換涉及其他一些步驟，如下圖所示：

1. 啟用 Web 應用程式的生產與預備環境之間的交換作業 (6)。 這項作業可切換兩個環境的 URL。 現在 `contoso.azurewebsites.net` 會指向網站和資料庫 (生產環境) 的 V2 版本。 
2. 如果您不再需要 V1 版本 (其變成交換後的暫存複本)，您可以解除委任預備環境 (7)。

![可供雲端災害復原的 SQL Database 異地複寫組態。](./media/manage-application-rolling-upgrade/option1-3.png)

如果升級程序失敗 (例如，由於升級指令碼發生錯誤)，請將預備環境視為遭到危害。 若要將應用程式復原到升級前狀態，請將生產環境中的應用程式還原至完整存取。 下圖顯示還原步驟：

1. 將資料庫複本設定為讀寫模式 (8)。 此動作可還原生產複本的完整 V1 功能。
2. 執行根本原因分析並解除委任預備環境 (9)。

此時應用程式已可完全運作，而您可以重複升級步驟。

> [!NOTE]
> 復原不需要 DNS 變更，因為您尚未執行交換作業。

![圖表顯示在預備環境已解除委任的情況中，雲端災難復原的 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option1-4.png)

這個選項的重要優點是您可以依照一組簡單的步驟，在單一區域中升級應用程式。 升級的金額成本相對較低。 

主要的取捨是如果在升級期間發生嚴重失敗，復原到升級前狀態會需要將不同區域的應用程式重新部署，並使用異地復原從備份還原資料庫。 此程序會產生顯著的停機時間。

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>升級依賴資料庫異地複寫來進行災害復原的應用程式

如果您的應用程式使用主動式異地複寫或自動容錯移轉群組來達到商務持續性，則該應用程式會部署到至少兩個不同的區域。 主要區域中有作用中的主要資料庫，而備份區域中有唯讀的次要資料庫。 除了本文開頭所提的因素，升級程序也必須保證：

* 升級程序期間，應用程式隨時受到保護以防嚴重失敗。
* 應用程式的異地備援元件會與作用中元件以平行方式升級。

若要達成這些目標，除了使用 Web Apps 環境，您還要利用 Azure 流量管理員，其使用含有一個作用中端點和一個備份端點的容錯移轉設定檔。 下圖說明升級程序之前的作業環境。 `contoso-1.azurewebsites.net` 和 `contoso-dr.azurewebsites.net` 網站代表具有完整異地備援的應用程式生產環境。 此生產環境包括下列元件：

* 主要區域中 Web 應用程式 `contoso-1.azurewebsites.net` 的生產環境 (1)
* 主要區域中的主要資料庫 (2)
* 備份區域中 Web 應用程式的待命執行個體 (3)
* 備份區域中異地複寫的次要資料庫 (4)
* 具有線上端點 (稱為 `contoso-1.azurewebsites.net`) 和離線端點 (稱為 `contoso-dr.azurewebsites.net`) 的流量管理員效能設定檔

若要能夠復原升級，您必須建立含有應用程式完整同步複本的預備環境。 因為您必須確保應用程式可以快速復原，以防在升級程序期間發生嚴重失敗，因此預備環境也需要異地備援。 建立升級的預備環境需要執行下列步驟：

1. 在主要區域中部署 Web 應用程式的預備環境 (6)。
2. 在主要區域中建立次要資料庫 (7)。 設定 Web 應用程式的預備環境以連線到該環境。 
3. 複寫主要區域中的次要資料庫，在備份區域中建立另一個異地備援次要資料庫 (8)。 (這個方法稱為「鏈結異地複寫」**) (8)。
4. 在備份區域中部署 Web 應用程式執行個體的預備環境 (9)，並將它設定為連線在 (8) 建立的異地備援次要資料庫。

> [!NOTE]
> 這些準備步驟不會影響生產環境中的應用程式。 它仍會以讀寫模式完全運作。

![圖顯示使用完整同步處理的應用程式複本，進行雲端嚴重損壞修復的 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option2-1.png)

準備步驟完成時，預備環境就準備好升級。 下圖說明這些升級步驟：

1. 將生產環境中的主要資料庫設定為唯讀模式 (10)。 這個模式保證生產資料庫 (V1) 在升級期間不會變更，藉此防止 V1 與 V2 資料庫執行個體間的資料分歧。

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. 中斷次要 (11) 的連線，以終止異地複寫。 此動作會建立獨立但完全同步的生產資料庫複本。 此資料庫將會升級。 下列範例使用 Transact-sql，但 [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) 也可供使用。 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. 對 `contoso-1-staging.azurewebsites.net`、`contoso-dr-staging.azurewebsites.net` 和預備主要資料庫執行升級指令碼 (12)。 資料庫變更會自動複寫至預備次要資料庫。

![圖顯示使用複寫至預備環境的資料庫變更，進行雲端災難復原的 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option2-2.png)

如果升級順利完成，您現在已準備好將使用者切換至應用程式的 V2 版本。 下圖說明相關步驟：

1. 在主要區域 (13) 和備份區域 (14) 中啟用 Web 應用程式的生產與預備環境之間的交換作業。 應用程式的 V2 現在成為備份區域中具有備援複本的生產環境。
2. 如果您不再需要 V1 應用程式 (15 和 16)，您可以解除委任預備環境。

![圖顯示雲端嚴重損壞修復的 SQL Database 異地複寫設定，並選擇性解除委任預備環境。](./media/manage-application-rolling-upgrade/option2-3.png)

如果升級程序失敗 (例如由於升級指令碼發生錯誤)，請將預備環境視為不一致狀態。 若要將應用程式復原到升級前狀態，請還原為在生產環境中使用應用程式的 V1。 下圖顯示必要步驟：

1. 將生產環境中的主要資料庫複本設定為讀寫模式 (17)。 此動作可還原生產環境的完整 V1 功能。
2. 執行根本原因分析並修復或移除預備環境 (18 和 19)。

此時應用程式已可完全運作，而您可以重複升級步驟。

> [!NOTE]
> 復原不需要 DNS 變更，因為您尚未執行交換作業。

![此圖顯示在還原升級程式時，雲端災難復原的 SQL Database 異地複寫設定。](./media/manage-application-rolling-upgrade/option2-4.png)

這個選項的重要優點是您可以以平行方式升級應用程式及其異地備援複本，而不會在升級期間危及商務持續性。

主要的取捨是它需要每個應用程式元件的雙重備援，因此會產生較高的金額成本。 它也涉及更複雜的工作流程。

## <a name="summary"></a>摘要

本文中所描述的兩種升級方法有不同的複雜度與金額成本，但它們都著重於將使用者受限為唯讀作業的時間降到最低。 該時間由升級指令碼的期間直接定義。 時間不會取決於資料庫大小、您選擇的服務層級、網站設定，或其他您無法輕鬆控制的因素。 所有準備步驟會與升級步驟分離，且不會影響生產應用程式。 升級指令的效率是決定升級期間使用者的體驗的重要因素。 因此改進該體驗的最佳方式，舊是將工作焦點放在盡可能提高升級指令碼的效率。

## <a name="next-steps"></a>後續步驟

* 如需商務持續性的總覽和案例，請參閱 [商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
* 若要瞭解 Azure SQL Database 主動式異地複寫，請參閱 [使用主動式異地複寫建立可讀取的次要資料庫](active-geo-replication-overview.md)。
* 若要了解 Azure SQL Database 自動容錯移轉群組，請參閱[使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉](auto-failover-group-overview.md)。
* 若要了解 Azure App Service 中的預備環境，請參閱[在 Azure App Service 中設定預備環境](../../app-service/deploy-staging-slots.md)。
* 若要了解 Azure 流量管理員設定檔，請參閱[管理 Azure 流量管理員設定檔](../../traffic-manager/traffic-manager-manage-profiles.md)。
