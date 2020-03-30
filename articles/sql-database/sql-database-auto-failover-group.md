---
title: 容錯移轉群組
description: 自動容錯移轉群組是 SQL Database 功能，允許您管理 SQL Database 伺服器上的一組資料庫，或受控執行個體中的所有資料庫的複寫和自動/協調容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269831"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉

自動容錯移轉組是一種 SQL 資料庫功能，允許您在 SQL 資料庫伺服器上管理一組資料庫的複製和容錯移轉，或者管理實例中的所有資料庫到另一個區域。 它是在現有[活動異地複製](sql-database-active-geo-replication.md)功能之上的聲明性抽象，旨在簡化大規模地理複製資料庫的部署和管理。 您可以手動起始容錯移轉，也可以根據使用者定義的原則將其委派給 SQL Database 服務。 後項可讓您在導致主要區域中完全或部分喪失 SQL Database 服務可用性的嚴重失敗或其他非計劃事件之後，自動復原次要地區中的多個相關資料庫。 容錯移轉組可以包括一個或多個資料庫，通常由同一應用程式使用。 此外，您可以使用可讀取次要資料庫來卸載唯讀查詢工作負載。 因為自動容錯移轉群組牽涉到多個資料庫，所以這些資料庫必須在主要伺服器上進行設定。 自動容錯移轉群組支援將群組中的所有資料庫都只複寫到不同區域的一部次要伺服器。

> [!NOTE]
> 在 SQL Database 伺服器上使用單一或集區資料庫時，若您希望在相同或不同區域中有多個次要資料庫，請使用[主動式異地複寫](sql-database-active-geo-replication.md)。 

當您使用自動容錯移轉群組與自動容錯移轉原則時，影響群組中一或多個資料庫的任何中斷，都會導致自動容錯移轉。 通常，這些事件無法通過內置自動高可用性操作自行緩解。 容錯移轉觸發器的示例包括由於多個計算節點上的 OS 內核記憶體洩漏導致 SQL 租戶環或控制環停機而導致的事件，或者由於網路電纜在會議期間被切斷錯誤的網路電纜而關閉一個或多個租戶環引起的事件常規硬體退役。  有關詳細資訊，請參閱[SQL 資料庫高可用性](sql-database-high-availability.md)。

此外，自動容錯移轉群組還提供在容錯移轉期間仍保持不變的讀寫和唯讀接聽程式端點。 無論您使用手動或自動啟動容錯移轉，容錯移轉都會將群組中所有次要資料庫切換到主要資料庫。 資料庫容錯移轉完成後，DNS 記錄會自動更新以將端點重新導向至新的區域。 針對特定的 RPO 和 RTO 資料，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

當您使用自動容錯移轉群組與自動容錯移轉原則時，任何影響 SQL Database 伺服器或受控執行個體中資料庫的中斷，都會導致自動容錯移轉。 您可以使用下列方法管理自動容錯移轉群組：

- [Azure 門戶](sql-database-implement-geo-distributed-database.md)
- [Azure CLI：容錯移轉組](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell：容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API：容錯移轉群組](/rest/api/sql/failovergroups)

容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 有關為災害復原設計解決方案的詳細資訊，請參閱[使用活動異地複製設計用於災害復原的雲解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="auto-failover-group-terminology-and-capabilities"></a>自動容錯移轉群組術語和功能

- **容錯移轉組 （FOG）**

  容錯移轉組是由單個 SQL 資料庫伺服器或單個託管實例管理的命名資料庫組，當主區域中的所有或某些主資料庫由於中斷而變得無法接通，這些資料庫可以作為一個單元容錯移轉到另一個區域。 為託管實例創建時，容錯移轉組包含實例中的所有使用者資料庫，因此只能在實例上配置一個容錯移轉組。
  
  > [!IMPORTANT]
  > 容錯移轉組的名稱必須在`.database.windows.net`域中全域唯一。

- **SQL 資料庫伺服器**

     使用 SQL Database 伺服器，可以將單一伺服器上的部分或所有使用者資料庫放在容錯移轉群組中。 此外，SQL Database 伺服器在單一 SQL Database 伺服器上可支援多個容錯移轉群組。

- **主要**

  承載容錯移轉組中主資料庫的 SQL 資料庫伺服器或託管實例。

- **次要**

  承載容錯移轉組中次要資料庫的 SQL 資料庫伺服器或託管實例。 次要資料庫和主要資料庫不能位於相同區域。

- **將單一資料庫新增至容錯移轉群組**

  您可以將多個在相同 SQL Database 伺服器上的單一資料庫放入相同的容錯移轉群組。 如果您在容錯移轉群組中新增單一資料庫，它會使用相同的版本自動建立次要資料庫，並在次要伺服器上計算大小。  當建立容錯移轉群組時，您會指定該伺服器。 如果您新增在次要伺服器中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增在伺服器中已經有次要資料庫但不屬於容錯移轉群組一部分的資料庫時，會在次要伺服器中建立新的次要資料庫。

  > [!IMPORTANT]
  > 請確保次要伺服器沒有同名資料庫，除非它是現有的次要資料庫。 在託管實例的容錯移轉組中，將複製所有使用者資料庫。 您無法在容錯移轉群組中選擇要複寫的使用者資料庫子集。

- **將彈性集區中的資料庫新增到容錯移轉群組**

  您可以將彈性集區中的所有或多個資料庫放入相同的容錯移轉群組。 如果主要資料庫在彈性集區中，則會使用相同名稱在該彈性集區中建立次要資料庫 (次要集區)。 您必須確保次要伺服器包含名稱完全相同的彈性集區，且有足夠的可用容量裝載容錯移轉群組將建立的次要資料庫。 如果您在集區中新增在次要集區中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增的資料庫在伺服器中已經有次要資料庫且不屬於容錯移轉群組一部分時，系統會在次要集區中建立新的次要資料庫。
  
- **初始播種** 

  將資料庫、彈性池或託管實例添加到容錯移轉組時，在開始資料複製之前，將有一個初始種子設定階段。 初始播種階段是最長和最昂貴的操作。 初始種子設定完成後，資料將同步，然後僅複製後續資料更改。 初始種子完成所需的時間取決於資料的大小、複製的資料庫數以及容錯移轉組中的實體之間的連結速度。 在正常情況下，單個資料庫或彈性池的典型種子設定速度為每小時 50-500 GB，託管實例為每小時 18-35 GB。 並行對所有資料庫執行種子設定。 您可以使用聲明的設定種子設定速度以及資料庫數和資料的總大小來估計初始種子設定階段在開始資料複製之前需要多長時間。

  對於託管實例，在估計初始種子設定階段的時間時，還需要考慮兩個實例之間的快速路由連結的速度。 如果兩個實例之間的連結速度低於必要的速度，則播種時間可能會顯著受到影響。 您可以使用聲明的設定種子設定速度、資料庫數、資料總大小和連結速度來估計初始種子設定階段在開始資料複製之前需要多長時間。 例如，對於單個 100 GB 資料庫，如果鏈路能夠按每小時 35 GB 推送，則初始種子階段需要 2.8 - 5.5 小時。 如果鏈路每小時只能傳輸 10 GB，則設定 100 GB 資料庫大約需要 10 小時。 如果有多個資料庫要複製，則種子設定將並存執行，並且，當與較慢的連結速度結合使用時，初始種子設定階段可能需要相當長的時間，特別是如果來自所有資料庫的資料的並行種子設定超過可用鏈路頻寬。 如果兩個實例之間的網路頻寬有限，並且您將多個託管實例添加到容錯移轉組，請考慮逐個向容錯移轉組添加多個託管實例。

  
- **DNS 區域**

  創建新實例時自動生成的唯一 ID。 此實例的多域 （SAN） 證書被預配，用於驗證用戶端連接到同一 DNS 區域中的任何實例。 同一容錯移轉組中的兩個託管實例必須共用 DNS 區域。
  
  > [!NOTE]
  > 為 SQL 資料庫伺服器創建容錯移轉組不需要 DNS 區域 ID。

- **容錯移轉群組讀寫接聽程式**

  指向當前主的 URL 的 DNS CNAME 記錄。 在創建容錯移轉組時自動創建它，並允許讀寫 SQL 工作負載在容錯移轉後主資料庫發生更改時透明地重新連接到主資料庫。 在 SQL 資料庫伺服器上創建容錯移轉組時，攔截器 URL 的 DNS CNAME`<fog-name>.database.windows.net`記錄將形成為 。 在託管實例上創建容錯移轉組時，攔截器 URL 的 DNS CNAME 記錄`<fog-name>.zone_id.database.windows.net`將形成為 。

- **容錯移轉群組唯讀接聽程式**

  形成的 DNS CNAME 記錄指向唯讀接聽程式 (指向次要 URL)。 在創建容錯移轉組時自動創建它，並允許唯讀 SQL 工作負載使用指定的負載平衡規則透明地連接到次要資料庫。 在 SQL 資料庫伺服器上創建容錯移轉組時，攔截器 URL 的 DNS CNAME`<fog-name>.secondary.database.windows.net`記錄將形成為 。 在託管實例上創建容錯移轉組時，攔截器 URL 的 DNS CNAME 記錄`<fog-name>.zone_id.secondary.database.windows.net`將形成為 。

- **自動容錯移轉原則**

  容錯移轉群組預設是利用自動容錯移轉原則設定。 SQL Database 服務在偵測到失敗且寬限期已過期之後，會觸發容錯移轉。 系統必須就影響級別，藉由 [SQL Database 服務的內建高可用性基礎結構](sql-database-high-availability.md)，來驗證中斷情況不會趨緩。 如果您想要控制應用程式的容錯移轉工作流程，可以關閉自動容錯移轉。
  
  > [!NOTE]
  > 由於驗證中斷的規模以及緩解速度涉及操作團隊的人工作業，因此寬限期不能設置為低於一小時。  此限制適用于容錯移轉組中的所有資料庫，而不考慮其資料同步狀態。 

- **唯讀的容錯移轉原則**

  根據預設，唯讀接聽程式的容錯移轉已停用。 它可確保在次要伺服器離線時不會影響主要伺服器的性能。 不過，這也表示在次要伺服器恢復之前，唯讀的工作階段將無法連線。 如果不能容忍唯讀會話的停機時間，並且可以臨時使用主讀和讀寫流量，但會犧牲主會話的性能可能降低，則可以通過配置`AllowReadOnlyFailoverToPrimary`屬性為唯讀攔截器啟用容錯移轉。 在這種情況下，如果輔助流量不可用，則唯讀流量將自動重定向到主流量。

- **計劃性容錯移轉**

   計劃性容錯移轉會在主要資料庫和次要資料庫之間執行完整同步處理，然後將次要資料庫切換為主要角色。 這可確保不會遺失資料。 計劃性容錯移轉會用於下列案例：

  - 在資料遺失不可接受時，在生產環境中執行災害復原 (DR) 演練
  - 將資料庫重新放置到不同的區域
  - 在中斷情況趨緩 (容錯回復) 後，將資料庫傳回到主要區域。

- **計畫外容錯移轉**

   非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 這項作業會導致資料遺失。 非計劃性容錯移轉是主要資料庫無法存取時，用來作為中斷期間的復原方法。 當原始主資料庫重新連線時，它將自動重新連接而不同步，並成為新的次要資料庫。

- **手動容錯移轉**

  無論自動容錯移轉設定為何，您都可以在任何時候手動起始容錯移轉。 如果未設定自動容錯移轉原則，就需要手動容錯移轉才能將容錯移轉群組中的資料庫復原至次要資料庫。 您可以起始強制或易用容錯移轉 (具有完整的資料同步處理)。 後者可用來將主要資料庫重新放置到次要區域。 容錯移轉完成時，DNS 記錄會自動更新以確保能連線到新的主要伺服器

- **資料遺失的寬限期**

  因為主要和次要資料庫是使用非同步複寫進行同步處理，容錯移轉可能會導致資料遺失。 您可以自訂自動容錯移轉原則，以反映應用程式對資料遺失的容錯程度。 通過配置`GracePeriodWithDataLossHours`，您可以控制系統在啟動可能導致資料丟失的容錯移轉之前等待的時間。

- **多個容錯移轉群組**

  您可以為相同的兩部伺服器設定多個容錯移轉群組來控制容錯移轉的規模。 每個群組分別進行容錯移轉。 如果您的多租用戶應用程式使用彈性集區，可以使用這項功能來混合每個集區中的主要和次要資料庫。 如此一來，可以讓中斷只影響一半的租用戶。

  > [!NOTE]
  > 受控執行個體不支援多個容錯移轉群組。
  
## <a name="permissions"></a>權限

容錯移轉組的許可權通過[基於角色的存取控制 （RBAC）](../role-based-access-control/overview.md)進行管理。 [SQL 伺服器參與者](../role-based-access-control/built-in-roles.md#sql-server-contributor)角色具有管理容錯移轉組的所有必要許可權。

### <a name="create-failover-group"></a>建立容錯移轉群組

要創建容錯移轉組，您需要 RBAC 寫入對主伺服器和次要伺服器以及容錯移轉組中的所有資料庫的寫入存取權限。 對於託管實例，您需要 RBAC 寫入對主託管實例和輔助託管實例的訪問，但單個資料庫的許可權不相關，因為無法將單個託管實例資料庫添加到容錯移轉組或從容錯移轉組中刪除。 

### <a name="update-a-failover-group"></a>更新容錯移轉組

要更新容錯移轉組，您需要 RBAC 寫入對容錯移轉組以及當前主伺服器或託管實例上的所有資料庫的寫入存取權限。  

### <a name="failover-a-failover-group"></a>容錯移轉組容錯移轉

要容錯移轉組容錯移轉，您需要 RBAC 寫入對新主伺服器或託管實例上的容錯移轉組的訪問。

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>將容錯移轉群組與單一資料庫和彈性集區一起使用的最佳做法

自動容錯移轉群組必須在主要 SQL Database 伺服器上設定，並將其連接至不同 Azure 區域中的次要 SQL Database 伺服器。 群組可以包含這些伺服器中的所有或部分資料庫。 下圖說明使用多個資料庫和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> 有關將單個資料庫添加到容錯移轉組的詳細分步教程，請參閱[將單個資料庫添加到容錯移轉組](sql-database-single-database-failover-group-tutorial.md)。

在商務持續性前提下設計服務時，請遵循這些一般指導方針：

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>使用一個或多個容錯移轉組管理多個資料庫的容錯移轉

可以在不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。 容錯移轉群組會使用和主要資料庫相同的服務目標，來建立異地次要資料庫。 如果您在容錯移轉群組中新增現有的異地複寫關聯性，請確定異地次要資料庫所設定的服務層級與計算大小和主要資料庫相同。
  
> [!IMPORTANT]
> 對於單個資料庫和彈性池，當前不支援在不同訂閱中的兩個伺服器之間創建容錯移轉組。 如果在創建容錯移轉組後將主伺服器或次要伺服器移動到其他訂閱，則可能導致容錯移轉請求和其他操作失敗。

### <a name="using-read-write-listener-for-oltp-workload"></a>將讀寫攔截器用於 OLTP 工作負載

在執行 OLTP 作業時使用 `<fog-name>.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 請注意，容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。

### <a name="using-read-only-listener-for-read-only-workload"></a>將唯讀攔截器用於唯讀工作負載

如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 針對唯讀工作階段，請使用 `<fog-name>.secondary.database.windows.net` 作為伺服器 URL，連線會自動導向至次要伺服器。 還建議您使用`ApplicationIntent=ReadOnly`指示連接字串讀取意圖。 如果要確保唯讀工作負載可以在容錯移轉後重新連接，或者如果次要伺服器離線，請確保配置容錯移轉策略`AllowReadOnlyFailoverToPrimary`的屬性。

### <a name="preparing-for-performance-degradation"></a>準備性能下降

典型的 Azure 應用程式使用多個 Azure 服務，並且由多個元件組成。 容錯移轉組的自動容錯移轉僅根據 Azure SQL 元件的狀態觸發。 主區域中的其他 Azure 服務可能不受中斷的影響，其元件可能仍可用該區域。 主資料庫切換到 DR 區域後，從屬元件之間的延遲可能會增加。 為了避免延遲增加對應用程式性能的影響，請確保 DR 區域中所有應用程式元件的冗余，並遵循這些[網路安全準則](#failover-groups-and-network-security)。

### <a name="preparing-for-data-loss"></a>為數據丟失做好準備

如果檢測到中斷，SQL 將等待您指定的`GracePeriodWithDataLossHours`期間。 預設值為 1 小時。 如果無法承受資料丟失，請確保設置為`GracePeriodWithDataLossHours`足夠大的數位，例如 24 小時。 使用手動群組容錯移轉，從次要伺服器容錯回復到主要伺服器。

> [!IMPORTANT]
> 具有 800 個或更少 DTU 且使用異地複寫的資料庫超過 250 個的彈性集區，可能會遇到的問題包括規劃的容錯移轉時間較久與效能降低。  當地理複寫端點依地理位置廣泛相隔，或當每個資料庫使用多個次要端點時，較可能會發生這些寫入大量工作負載的問題。  異地複寫延遲隨時間而增加時，就可看出這些問題的徵兆。  您可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 來監視這個延遲。  如果發生這些問題，則風險降低方式包括增加集區 DTU 數目，或減少相同集區內異地複寫的資料庫數目。

### <a name="changing-secondary-region-of-the-failover-group"></a>更改容錯移轉組的次要區域

為了說明更改順序，我們將假定伺服器 A 是主伺服器，伺服器 B 是現有的次要伺服器，伺服器 C 是第三個區域中的新次要伺服器。  要進行轉換，請按照以下步驟操作：

1.  使用[活動異地複製](sql-database-active-geo-replication.md)在伺服器 A 上創建每個資料庫的其他次要資料庫到伺服器 C。 伺服器 A 上的每個資料庫將有兩個次要資料庫，一個在伺服器 B 上，一個在伺服器 C 上。這將保證主資料庫在轉換期間保持受保護狀態。
2.  刪除容錯移轉組。 此時，登錄將失敗。 這是因為容錯移轉組攔截器的 SQL 別名已被刪除，並且閘道將無法識別容錯移轉組名稱。
3.  在伺服器 A 和 C 之間重新創建具有相同名稱的容錯移轉組。此時，登錄將停止失敗。
4.  將伺服器 A 上的所有主資料庫添加到新的容錯移轉組。
5.  丟棄伺服器 B。B 上的所有資料庫將自動刪除。 


### <a name="changing-primary-region-of-the-failover-group"></a>更改容錯移轉組的主區域

為了說明更改順序，我們將假定伺服器 A 是主伺服器，伺服器 B 是現有的次要伺服器，伺服器 C 是第三個區域中的新主伺服器。  要進行轉換，請按照以下步驟操作：

1.  執行計畫容錯移轉以將主伺服器切換到 B. 伺服器 A 將成為新的次要伺服器。 容錯移轉可能會導致幾分鐘的停機時間。 實際時間取決於容錯移轉組的大小。
2.  使用[活動異地複製](sql-database-active-geo-replication.md)在伺服器 B 上創建每個資料庫的其他次要資料庫到伺服器 C。 伺服器 B 上的每個資料庫將有兩個次要資料庫，一個在伺服器 A 上，一個在伺服器 C 上。這將保證主資料庫在轉換期間保持受保護狀態。
3.  刪除容錯移轉組。 此時，登錄將失敗。 這是因為容錯移轉組攔截器的 SQL 別名已被刪除，並且閘道將無法識別容錯移轉組名稱。
4.  在伺服器 A 和 C 之間重新創建具有相同名稱的容錯移轉組。此時，登錄將停止失敗。
5.  將 B 上的所有主資料庫添加到新的容錯移轉組。 
6.  執行容錯移轉組的計畫容錯移轉以切換 B 和 C。現在伺服器 C 將成為主伺服器和 B - 次要伺服器。 伺服器 A 上的所有次要資料庫將自動連結到 C 上的主資料庫。與步驟 1 一樣，容錯移轉可能會導致幾分鐘的停機時間。
6.  丟棄伺服器 A。A 上的所有資料庫將自動刪除。

> [!IMPORTANT]
> 刪除容錯移轉組時，攔截器終結點的 DNS 記錄也會被刪除。 此時，其他人創建同名容錯移轉組或伺服器別名的概率為非零，這將阻止您再次使用它。 為了將風險降至最低，請不要使用通用容錯移轉組名稱。

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>使用具有託管實例的容錯移轉組的最佳做法

自動容錯移轉群組必須在主要執行個體上設定，並將其連接至不同 Azure 區域中的次要執行個體。  執行個體中的所有資料庫都將複寫至次要執行個體。

下圖說明使用受控執行個體和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> 有關添加託管實例以使用容錯移轉組的詳細分步教程，請參閱[將託管實例添加到容錯移轉組](sql-database-managed-instance-failover-group-tutorial.md)。

如果應用程式使用託管實例作為資料層，則在設計業務連續性時請遵循以下一般準則：

### <a name="creating-the-secondary-instance"></a>創建輔助實例 

若要確保容錯移轉之後與主要執行個體的連線不中斷，主要和次要執行個體必須位於相同的 DNS 區域。 它將保證可以使用同一多域 （SAN） 證書對容錯移轉組中的兩個實例中的任一的用戶端連接進行身份驗證。 當您的應用程式準備好進行生產環境部署時，請在不同區域中建立次要執行個體，並確保它與主要執行個體共用 DNS 區域。 可以通過使用 Azure 門戶、PowerShell 或 REST API 指定`DNS Zone Partner`可選參數來執行此操作。

> [!IMPORTANT]
> 在子網中創建的第一個實例確定同一子網中所有後續實例的 DNS 區域。 這意味著來自同一子網的兩個實例不能屬於不同的 DNS 區域。

有關在與主實例相同的 DNS 區域中創建輔助實例的詳細資訊，請參閱[創建輔助託管實例](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)。

### <a name="enabling-replication-traffic-between-two-instances"></a>在兩個實例之間啟用複製流量

因為每個執行個體都在其自己的 VNet中隔離，因此必須允許這些 Vnet 之間的雙向流量。 請參閱 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>在不同的訂閱中的託管實例之間創建容錯移轉組

您可以在兩個不同的訂閱中的託管實例之間創建容錯移轉組。 使用 PowerShell API 時，可以通過指定輔助`PartnerSubscriptionId`實例的參數來執行此操作。 使用 REST API 時，`properties.managedInstancePairs`參數中包含的每個實例 ID 都可以有自己的訂閱 ID。
  
> [!IMPORTANT]
> Azure 門戶不支援跨不同訂閱創建容錯移轉組。 此外，對於跨不同訂閱和/或資源組的現有容錯移轉組，無法通過主實例的門戶手動啟動容錯移轉。 而是從異地輔助實例啟動它。

### <a name="managing-failover-to-secondary-instance"></a>管理容錯移轉到輔助實例

容錯移轉群組將會管理執行個體中所有資料庫的容錯移轉。 建立群組時，執行個體中的每個資料庫將會自動異地複寫到次要執行個體。 您無法使用容錯移轉群組來起始資料庫子集的部分容錯移轉。

> [!IMPORTANT]
> 如果從主要執行個體中移除資料庫，則它也將自動在異地次要執行個體上卸除。

### <a name="using-read-write-listener-for-oltp-workload"></a>將讀寫攔截器用於 OLTP 工作負載

在執行 OLTP 作業時使用 `<fog-name>.zone_id.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。 由於輔助實例與主實例共用 DNS 區域，因此用戶端應用程式將能夠使用相同的 SAN 證書重新連接到它。

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>使用唯讀攔截器連接到輔助實例

如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 若要直接連接到異地複寫的次要執行個體，請使用 `server.secondary.zone_id.database.windows.net` 作為伺服器 URL，並直接連接到異地複寫的次要執行個體。

> [!NOTE]
> 在特定的服務層級，Azure SQL Database 支援使用[唯讀複本](sql-database-read-scale-out.md)來使用一個唯讀複本的容量，並使用連接字串中的 `ApplicationIntent=ReadOnly` 參數，對唯讀查詢工作負載進行負載平衡。 當您已設定異地複寫的次要執行個體時，可以使用此功能連接至主要位置或異地複寫位置中的唯讀複本。
> - 若要連線至主要位置的唯讀複本，請使用 `<fog-name>.zone_id.database.windows.net`。
> - 要連接到輔助位置中的唯讀副本，請使用`<fog-name>.secondary.zone_id.database.windows.net`。

### <a name="preparing-for-performance-degradation"></a>準備性能下降

典型的 Azure 應用程式使用多個 Azure 服務，並且由多個元件組成。 容錯移轉組的自動容錯移轉僅根據 Azure SQL 元件的狀態觸發。 主區域中的其他 Azure 服務可能不受中斷的影響，其元件可能仍可用該區域。 主資料庫切換到 DR 區域後，從屬元件之間的延遲可能會增加。 為了避免延遲增加對應用程式性能的影響，請確保 DR 區域中所有應用程式元件的冗余，並遵循這些[網路安全準則](#failover-groups-and-network-security)。

### <a name="preparing-for-data-loss"></a>為數據丟失做好準備

如果系統偵測到服務中斷，SQL 會在就我們所知並無資料遺失時，自動觸發讀寫容錯移轉。 否則，它會等候您透過 `GracePeriodWithDataLossHours` 所指定的這段時間。 如果您指定 `GracePeriodWithDataLossHours`，請針對資料遺失做好準備。 服務中斷期間，Azure 一般會傾向維持可用性。 如果您無法承擔資料遺失情況，請務必將 GracePeriodWithDataLossHours 設定為足夠大的數字，例如 24 小時。

在起始容錯移轉之後，將立即發生讀寫接聽程式的 DNS 更新。 這項作業不會導致資料遺失。 但是，在正常情況下，切換資料庫角色的程序最多可能需要 5 分鐘的時間。 在完成之前，新的主要執行個體中的某些資料庫仍會處於唯讀模式。 如果使用 PowerShell 啟動容錯移轉，則整個操作是同步的。 如果使用 Azure 門戶啟動，UI 將指示完成狀態。 如果使用 REST API 來起始，請使用標準 Azure Resource Manager 的輪詢機制來監視完成情況。

> [!IMPORTANT]
> 使用手動群組容錯移轉，將主要資料庫移回原始位置。 當造成容錯移轉的中斷情況趨緩時，您可以將主要資料庫移動到原始位置。 若要這樣做，您應該起始群組的手動容錯移轉。
  
### <a name="changing-secondary-region-of-the-failover-group"></a>更改容錯移轉組的次要區域

假設實例 A 是主實例，實例 B 是現有的輔助實例，實例 C 是第三個區域中的新輔助實例。  要進行轉換，請按照以下步驟操作：

1.  創建大小與 A 和在同一 DNS 區域中的實例 C。 
2.  刪除實例 A 和 B 之間的容錯移轉組。此時，登錄將失敗，因為容錯移轉組攔截器的 SQL 別名已被刪除，閘道將無法識別容錯移轉組名稱。 次要資料庫將與主資料庫斷開連接，並將成為讀寫資料庫。 
3.  在實例 A 和 C 之間創建具有相同名稱的容錯移轉組。[使用託管實例教程按照容錯移轉組中](sql-database-managed-instance-failover-group-tutorial.md)的說明操作。 這是一個資料大小操作，將在實例 A 中的所有資料庫都設定種子並同步時完成。
4.  如果需要，請刪除實例 B 以避免不必要的費用。

> [!NOTE]
> 在步驟 2 之後，直到步驟 3 完成，實例 A 中的資料庫將保持未受保護的狀態，不會受到實例 A 的災難性故障的影響。

### <a name="changing-primary-region-of-the-failover-group"></a>更改容錯移轉組的主區域

假設實例 A 是主實例，實例 B 是現有的輔助實例，實例 C 是第三個區域中的新主實例。  要進行轉換，請按照以下步驟操作：

1.  創建大小與 B 和在同一 DNS 區域中的實例 C。 
2.  連接到實例 B 並手動容錯移轉以將主實例切換到 B。 實例 A 將自動成為新的輔助實例。
3.  刪除實例 A 和 B 之間的容錯移轉組。此時，登錄將失敗，因為容錯移轉組攔截器的 SQL 別名已被刪除，閘道將無法識別容錯移轉組名稱。 次要資料庫將與主資料庫斷開連接，並將成為讀寫資料庫。 
4.  在實例 A 和 C 之間創建具有相同名稱的容錯移轉組。使用[託管實例教程按照容錯移轉組中](sql-database-managed-instance-failover-group-tutorial.md)的說明操作。 這是一個資料大小操作，將在實例 A 中的所有資料庫都設定種子並同步時完成。
5.  如果不需要刪除實例 A 以避免不必要的費用。

> [!NOTE] 
> 在步驟 3 之後，直到步驟 4 完成，實例 A 中的資料庫將保持未受保護的狀態，不會受到實例 A 的災難性故障的影響。

> [!IMPORTANT]
> 刪除容錯移轉組時，攔截器終結點的 DNS 記錄也會被刪除。 此時，其他人創建同名容錯移轉組或伺服器別名的概率為非零，這將阻止您再次使用它。 為了將風險降至最低，請不要使用通用容錯移轉組名稱。

## <a name="failover-groups-and-network-security"></a>容錯移轉群組和網路安全性

對於某些應用程式，安全規則要求對資料層的網路訪問僅限於特定的元件或元件，如 VM、Web 服務等。此要求對業務連續性設計和容錯移轉組的使用提出了一些挑戰。 實現此類受限訪問時，請考慮以下選項。

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用容錯移轉群組和虛擬網路規則

如果您使用[虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)來限制存取您的 SQL 資料庫，請留意，每個虛擬網路服務端點只適用於一個 Azure 區域。 端點無法讓其他區域接受來自子網路的通訊。 因此，只有部署到相同區域中的用戶端應用程式可以連線到主要資料庫。 因為容錯移轉會導致 SQL 用戶端工作階段重新路由至不同 (次要) 區域中的伺服器，所以如果這些工作階段是來自該區域外的用戶端，就會失敗。 基於這個理由，如果參與的伺服器包含在虛擬網路規則中，則無法啟用自動容錯移轉原則。 若要支援手動容錯移轉，請遵循下列步驟：

1. 在次要地區中佈建您應用程式 (Web 服務、虛擬機器等) 前端元件的備援副本
2. 分別設定主要和次要伺服器的[虛擬網路規則](sql-database-vnet-service-endpoint-rule-overview.md)
3. 啟用[使用流量管理員設定的前端容錯移轉](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 偵測到中斷情況時，起始手動容錯移轉。 這個選項最適合用於在前端和資料層之間需要一致延遲的應用程式，且支援當前端、資料層或兩者受到中斷影響時進行復原。

> [!NOTE]
> 如果您使用**唯讀接聽程式**對唯讀工作負載進行負載平衡，請確保此工作負載會在 VM 或是次要地區的其他資源中執行，使其得以連線到次要資料庫。

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>使用容錯移轉群組和 SQL 資料庫防火牆規則

如果您的商務持續性計劃需要使用具有自動容錯移轉的群組，可以使用傳統的防火牆規則，來限制對您 SQL 資料庫的存取。 若要支援自動容錯移轉，請遵循下列步驟：

1. [創建公共 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [建立公用負載平衡器](../load-balancer/quickstart-load-balancer-standard-public-portal.md)並為其指派公用 IP。
3. [建立虛擬網路和虛擬機器](../load-balancer/quickstart-load-balancer-standard-public-portal.md)以用於前端元件
4. [建立網路安全性群組](../virtual-network/security-overview.md)並設定輸入連線。
5. 請使用 'Sql' [服務標籤](../virtual-network/security-overview.md#service-tags)確定會開啟到 Azure SQL 資料庫的輸出連線。
6. 建立 [SQL 資料庫防火牆規則](sql-database-firewall-configure.md)以允許您在步驟 1 中所建立公用 IP 位址的輸入流量。

如需如何設定輸出存取，以及在防火牆規則中使用哪些 IP 的相關詳細資訊，請參閱[負載平衡器連出連線](../load-balancer/load-balancer-outbound-connections.md)。

上述組態可確保自動容錯移轉不會封鎖來自前端元件的連線，並假設應用程式可以容忍前端與資料層之間較長的延遲。

> [!IMPORTANT]
> 若要保證區域性中斷時的商務持續性，您必須確定前端元件與資料庫的異地備援。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>在託管實例及其 VNet 之間啟用異地複製

在兩個不同區域的主託管實例和輔助託管實例之間設置容錯移轉組時，每個實例都使用獨立的虛擬網路進行隔離。 要允許這些 VNet 之間的複製流量，請確保滿足以下先決條件：

1. 兩個託管實例需要位於不同的 Azure 區域中。
2. 兩個託管實例需要是相同的服務層，並且具有相同的存儲大小。
3. 輔助託管實例必須為空（沒有使用者資料庫）。
4. 託管實例使用的虛擬網路需要通過[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[快速路由](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)進行連接。 當兩個虛擬網路通過本地網路連接時，請確保沒有防火牆規則阻止埠 5022 和 11000-11999。 目前不支援轉移的全域 VNet 對等互連。
5. 兩個託管實例 VNets 不能具有重疊的 IP 位址。
6. 您需要設置網路安全性群組 （NSG），以便埠 5022 和範圍 11000_12000 已打開入站和出站，以便從其他託管實例的子網連接。 這是允許實例之間的複製流量。

   > [!IMPORTANT]
   > 設定錯誤的 NSG 安全性規則會導致資料庫複製作業停滯。

7. 輔助實例配置了正確的 DNS 區域 ID。 DNS 區域是託管實例和虛擬叢集的屬性，其 ID 包含在主機名稱位址中。 當在每個 VNet 中創建第一個託管實例並將相同的 ID 分配給同一子網中的所有其他實例時，區域 ID 將作為隨機字串生成。 分配後，無法修改 DNS 區域。 同一容錯移轉組中包含的託管實例必須共用 DNS 區域。 在創建輔助實例時，通過將主實例的區域 ID 作為 DnsZonePartner 參數的值來實現此目的。 

   > [!NOTE]
   > 有關使用託管實例配置容錯移轉組的詳細教程，請參閱[將託管實例添加到容錯移轉組](sql-database-managed-instance-failover-group-tutorial.md)。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層級內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 升級時，我們建議您首先升級所有次要資料庫，然後升級主資料庫。 降級時，將撤銷順序：首先降級主資料庫，然後降級所有次要資料庫。 當您將資料庫升級或降級到不同的服務層級時，會強制執行這項建議。

特別建議使用此序列來避免較低 SKU 處的次要資料庫超載且必須在升級或降級過程中重新設定種子的問題。 您還可以通過使主讀唯讀來避免問題，而代價是將所有讀寫工作負載都針對主資料庫。

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 調用`sp_wait_for_database_copy_sync`阻止調用執行緒，直到最後一個提交的事務傳輸到次要資料庫。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 `sp_wait_for_database_copy_sync`範圍為特定的連續複製連結。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> `sp_wait_for_database_copy_sync`防止容錯移轉後資料丟失，但不保證讀取存取完全同步。 `sp_wait_for_database_copy_sync`程序呼叫引起的延遲可能很大，具體取決於調用時事務日誌的大小。

## <a name="failover-groups-and-point-in-time-restore"></a>容錯移轉群組和時間點還原

如需使用容錯移轉群組中的時間點還原的相關資訊，請參閱[時間點復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="limitations-of-failover-groups"></a>容錯移轉組的限制

請留意以下限制：

- 容錯移轉組不能在同一 Azure 區域中的兩個伺服器或實例之間創建。
- 無法重命名容錯移轉組。 您需要刪除該組，然後使用不同的名稱重新創建該組。 
- 容錯移轉組中的實例不支援資料庫重命名。 您需要臨時刪除容錯移轉組才能重命名資料庫。

## <a name="programmatically-managing-failover-groups"></a>以程式設計方式管理容錯移轉群組

如前所述，自動容錯移轉群組和主動式異地複寫也可以使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../role-based-access-control/overview.md)。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用單一資料庫與彈性集區管理 SQL 資料庫容錯移轉

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [刪除-AzSql資料庫故障組](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 從伺服器中刪除容錯移轉組 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 檢索容錯移轉組的配置 |
| [設置-AzSql資料庫容錯移轉組](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改容錯移轉組的配置 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 觸發容錯移轉組容錯移轉到次要伺服器 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|將一個或多個資料庫添加到容錯移轉組|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>使用託管實例管理 SQL 資料庫容錯移轉組

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |此命令創建容錯移轉組，並在主實例和輔助實例上註冊它|
| [設置-AzSql 資料庫實例容錯移轉組](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |修改容錯移轉組的配置|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |檢索容錯移轉組的配置|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |觸發容錯移轉組容錯移轉到輔助實例|
| [刪除-AzSql 資料庫實例容錯移轉組](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 移除容錯移轉群組|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用單一資料庫與彈性集區管理 SQL 資料庫容錯移轉

| Command | 描述 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [az sql 容錯移轉組刪除](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 從伺服器中刪除容錯移轉組 |
| [az sql 容錯移轉組顯示](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 檢索容錯移轉組配置 |
| [az sql 容錯移轉組更新](/cli/azure/sql/failover-group#az-sql-failover-group-update) |修改容錯移轉組的配置和/或將一個或多個資料庫添加到容錯移轉組|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 觸發容錯移轉組容錯移轉到次要伺服器 |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>使用託管實例管理 SQL 資料庫容錯移轉組

| Command | 描述 |
| --- | --- |
| [az sql 實例容錯移轉組創建](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 此命令創建容錯移轉組，並在主實例和輔助實例上註冊它 |
| [az sql 實例容錯移轉組更新](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 修改容錯移轉組的配置|
| [az sql 實例容錯移轉組顯示](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 檢索容錯移轉組的配置|
| [az sql 實例-容錯移轉組集主](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 觸發容錯移轉組容錯移轉到輔助實例|
| [az sql 實例容錯移轉組刪除](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 移除容錯移轉群組 |

* * *

> [!IMPORTANT]
> 如需範例指令碼，請參閱[設定單一資料庫的容錯移轉群組並進行容錯移轉](scripts/sql-database-add-single-db-to-failover-group-powershell.md)。

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API：使用單個資料庫和池資料庫管理 SQL 資料庫容錯移轉組

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 從伺服器中刪除容錯移轉組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 通過完全資料同步觸發容錯移轉從當前主伺服器到次要伺服器。|
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 觸發容錯移轉從當前主伺服器到次要伺服器，而無需同步資料。 此操作可能會導致資料丟失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 檢索容錯移轉組的配置。 |
| [依伺服器列出容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出伺服器上的容錯移轉組。 |
| [更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新容錯移轉組的配置。 |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API：使用託管實例管理容錯移轉組

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 創建或更新容錯移轉組的配置 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 從實例中刪除容錯移轉組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 通過完全資料同步觸發容錯移轉從當前主實例到此實例。 |
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 觸發容錯移轉從當前主實例到輔助實例而不同步資料。 此操作可能會導致資料丟失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 檢索容錯移轉組的配置。 |
| [列出容錯移轉群組 - 依位置列出](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 列出位置中的容錯移轉群組。 |

## <a name="next-steps"></a>後續步驟

- 有關詳細教程，請參閱
    - [將單個資料庫添加到容錯移轉組](sql-database-single-database-failover-group-tutorial.md)
    - [將彈性集區新增到容錯移轉群組](sql-database-elastic-pool-failover-group-tutorial.md)
    - [將託管實例添加到容錯移轉組](sql-database-managed-instance-failover-group-tutorial.md)
- 如需範例指令碼，請參閱：
  - [使用 PowerShell 為 Azure SQL Database 中的單一資料庫設定作用中異地複寫](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [使用 PowerShell 為 Azure SQL Database 中的集區資料庫設定作用中異地複寫](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [使用 PowerShell 將 Azure SQL Database 單一資料庫新增至容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
- 要瞭解 Azure SQL 資料庫自動備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)。
- 要瞭解如何使用自動備份進行恢復，請參閱[從服務啟動的備份還原資料庫](sql-database-recovery-using-backups.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
