---
title: 自動容錯移轉群組
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 自動容錯移轉群組可讓您管理伺服器上的一組資料庫，或受控實例中的所有資料庫的複寫和自動/協調容錯移轉。
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 11/16/2020
ms.openlocfilehash: 35856a0d414e288fcd184164733e9430a6bee296
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653737"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

自動容錯移轉群組功能可讓您管理伺服器上的一組資料庫，或受控實例中的所有資料庫到另一個區域的複寫和容錯移轉。 它是在現有的 [主動式異地](active-geo-replication-overview.md) 複寫功能之上的宣告式抽象概念，旨在簡化異地複寫資料庫的大規模部署和管理。 您可以手動起始容錯移轉，也可以根據使用者定義的原則將其委派給 Azure 服務。 第二個選項可讓您在發生嚴重失敗或其他未規劃的事件之後，自動復原次要區域中的多個相關資料庫，而導致主要區域中的 SQL Database 或 SQL 受控執行個體可用性全部遺失或部分遺失。 容錯移轉群組可包含一或多個資料庫，通常是由相同的應用程式所使用。 此外，您可以使用可讀取次要資料庫來卸載唯讀查詢工作負載。 因為自動容錯移轉群組牽涉到多個資料庫，所以這些資料庫必須在主要伺服器上進行設定。 自動容錯移轉群組支援將群組中的所有資料庫複寫到不同區域中的一部次要伺服器或實例。

> [!NOTE]
> 如果您想要在相同或不同區域中有多個 Azure SQL Database 次要複本，請使用「 [主動式異地](active-geo-replication-overview.md)複寫」。

當您使用自動容錯移轉群組與自動容錯移轉原則時，影響群組中一或多個資料庫的任何中斷，都會導致自動容錯移轉。 這些事件通常是內建的自動高可用性作業無法自行緩和的事件。 容錯移轉觸發程式的範例包括因數個計算節點上的 OS 核心記憶體流失而導致 SQL Database 租使用者環形或控制環中斷的事件，或因為在例行硬體解除委任期間剪下了錯誤的網路纜線，而導致一或多個租使用者環形中斷的事件。  如需詳細資訊，請參閱 [SQL Database 高可用性](high-availability-sla.md)。

此外，自動容錯移轉群組還提供在容錯移轉期間仍保持不變的讀寫和唯讀接聽程式端點。 無論您使用手動或自動啟動容錯移轉，容錯移轉都會將群組中所有次要資料庫切換到主要資料庫。 資料庫容錯移轉完成後，DNS 記錄會自動更新以將端點重新導向至新的區域。 針對特定的 RPO 和 RTO 資料，請參閱[商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

當您使用自動容錯移轉群組與自動容錯移轉原則時，影響伺服器或受控實例上資料庫的任何中斷都會導致自動容錯移轉。 您可以使用下列方法管理自動容錯移轉群組：

- [Azure 入口網站](geo-distributed-application-configure-tutorial.md)
- [Azure CLI：容錯移轉群組](scripts/add-database-to-failover-group-cli.md)
- [PowerShell：容錯移轉群組](scripts/add-database-to-failover-group-powershell.md)
- [REST API：容錯移轉群組](/rest/api/sql/failovergroups)

在容錯移轉之後，請確定您的資料庫和伺服器的驗證需求，或在新的主要複本上設定實例。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](active-geo-replication-security-configure.md)。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需設計損毀修復解決方案的詳細資訊，請參閱 [使用主動式異地複寫設計災難復原的雲端解決方案](designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="terminology-and-capabilities"></a>術語和功能

- **容錯移轉群組 (霧化)**

  容錯移轉群組是由單一伺服器或受管理的實例所管理的一組資料庫，可在所有或某些主資料庫因為主要區域中斷而變成無法使用時，以一個單位的形式容錯移轉至另一個區域。 針對 SQL 受控執行個體建立時，容錯移轉群組會包含實例中的所有使用者資料庫，因此只能在實例上設定一個容錯移轉群組。
  
  > [!IMPORTANT]
  > 容錯移轉群組的名稱在網域內必須是全域唯一的 `.database.windows.net` 。

- **伺服器**

     使用伺服器時，伺服器上的部分或所有使用者資料庫都可以放在容錯移轉群組中。 此外，伺服器也支援單一伺服器上的多個容錯移轉群組。

- **主要**

  在容錯移轉群組中裝載主資料庫的伺服器或受控實例。

- **次要**

  在容錯移轉群組中裝載次要資料庫的伺服器或受控實例。 次要資料庫和主要資料庫不能位於相同區域。

- **將單一資料庫新增至容錯移轉群組**

  您可以將多個單一資料庫放在同一部伺服器上相同的容錯移轉群組中。 如果您在容錯移轉群組中新增單一資料庫，它會使用相同的版本自動建立次要資料庫，並在次要伺服器上計算大小。  當建立容錯移轉群組時，您會指定該伺服器。 如果您新增在次要伺服器中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增在伺服器中已經有次要資料庫但不屬於容錯移轉群組一部分的資料庫時，會在次要伺服器中建立新的次要資料庫。

  > [!IMPORTANT]
  > 請確定次要伺服器沒有具有相同名稱的資料庫，除非它是現有的次要資料庫。 在 SQL 受控執行個體的容錯移轉群組中，會複寫所有的使用者資料庫。 您無法在容錯移轉群組中選擇要複寫的使用者資料庫子集。

- **將彈性集區中的資料庫新增到容錯移轉群組**

  您可以將彈性集區中的所有或多個資料庫放入相同的容錯移轉群組。 如果主要資料庫在彈性集區中，則會使用相同名稱在該彈性集區中建立次要資料庫 (次要集區)。 您必須確保次要伺服器包含名稱完全相同的彈性集區，且有足夠的可用容量裝載容錯移轉群組將建立的次要資料庫。 如果您在集區中新增在次要集區中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增的資料庫在伺服器中已經有次要資料庫且不屬於容錯移轉群組一部分時，系統會在次要集區中建立新的次要資料庫。
  
- **初始植入**

  將資料庫、彈性集區或受控實例新增至容錯移轉群組時，會在資料複寫開始之前先進行初始植入階段。 初始植入階段是最長且最昂貴的作業。 初始植入完成之後，資料就會進行同步處理，然後只複寫後續的資料變更。 完成初始植入所需的時間取決於您的資料大小、複寫的資料庫數目，以及容錯移轉群組中的實體之間的連結速度。 在正常情況下，可能的植入速度為每小時 500 GB （SQL Database），最多可達 360 GB （SQL 受控執行個體）。 所有資料庫都會以平行方式執行植入。

  針對 SQL 受控執行個體，請在預估初始植入階段的時間時，考慮兩個實例之間的快速路由連結速度。 如果兩個實例之間的連結速度低於所需的速度，種子的時間可能會受到影響。 您可以使用所述的植入速度、資料庫數目、資料大小總計，以及在資料複寫開始之前估計初始植入階段所花費的時間長度的連結速度。 例如，針對單一 100 GB 的資料庫，如果連結可以每小時推送 84 GB，且沒有植入其他資料庫，則初始植入階段大約需要1.2 小時的時間。 如果連結每小時只能傳送 10 GB，則植入 100 GB 的資料庫大約需要10小時。 如果有多個要複寫的資料庫，則會以平行方式執行植入，並在結合緩慢的連結速度時，初始植入階段可能需要更長的時間，特別是當平行植入所有資料庫中的資料超過可用的連結頻寬時。 如果兩個實例之間的網路頻寬受到限制，而您要將多個受控實例新增至容錯移轉群組，請考慮將多個受控實例依序逐一新增至容錯移轉群組。 如果兩個受管理的實例之間有適當大小的閘道 SKU，而且如果公司網路頻寬允許它，就可以達到最高達 360 GB 一小時的速度。  

- **DNS 區域**

  建立新的 SQL 受控執行個體時自動產生的唯一識別碼。 此實例的多網域 (SAN) 憑證是布建的，用來驗證與相同 DNS 區域中任何實例的用戶端連接。 相同容錯移轉群組中的兩個受控實例必須共用 DNS 區域。
  
  > [!NOTE]
  > 針對 SQL Database 所建立的容錯移轉群組不需要 DNS 區域識別碼。

- **容錯移轉群組讀寫接聽程式**

  指向目前的主要 URL 的 DNS CNAME 記錄。 當容錯移轉群組建立時，它會自動建立，並允許讀寫工作負載在容錯移轉後的主要變更時，以透明的方式重新連接到主資料庫。 當您在伺服器上建立容錯移轉群組時，接聽程式 URL 的 DNS CNAME 記錄會形成 `<fog-name>.database.windows.net` 。 當您在 SQL 受控執行個體上建立容錯移轉群組時，接聽程式 URL 的 DNS CNAME 記錄會形成 `<fog-name>.<zone_id>.database.windows.net` 。

- **容錯移轉群組唯讀接聽程式**

  形成的 DNS CNAME 記錄指向唯讀接聽程式 (指向次要 URL)。 它會在建立容錯移轉群組時自動建立，並允許唯讀 SQL 工作負載使用指定的負載平衡規則，以透明的方式連線到次要資料庫。 當您在伺服器上建立容錯移轉群組時，接聽程式 URL 的 DNS CNAME 記錄會形成 `<fog-name>.secondary.database.windows.net` 。 當您在 SQL 受控執行個體上建立容錯移轉群組時，接聽程式 URL 的 DNS CNAME 記錄會形成 `<fog-name>.secondary.<zone_id>.database.windows.net` 。

- **自動容錯移轉原則**

  容錯移轉群組預設是利用自動容錯移轉原則設定。 Azure 會在偵測到失敗且寬限期已過期之後觸發容錯移轉。 系統必須確認內建的 [高可用性基礎結構](high-availability-sla.md) 因影響的規模而無法緩和中斷。 如果您想要控制應用程式或手動的容錯移轉工作流程，可以關閉自動容錯移轉。
  
  > [!NOTE]
  > 因為驗證中斷的規模以及可緩和的速度，牽涉到營運團隊的人為動作，所以寬限期不能在以下一小時內設定。 這項限制適用于容錯移轉群組中的所有資料庫（不論其資料同步處理狀態為何）。

- **唯讀的容錯移轉原則**

  根據預設，唯讀接聽程式的容錯移轉已停用。 它可確保在次要伺服器離線時不會影響主要伺服器的性能。 不過，這也表示在次要伺服器恢復之前，唯讀的工作階段將無法連線。 如果您無法容忍唯讀會話的停機時間，而且可以使用主要的唯讀和讀寫流量，但代價是主資料庫可能會降低效能，您可以藉由設定屬性來啟用唯讀接聽程式的容錯移轉 `AllowReadOnlyFailoverToPrimary` 。 在此情況下，如果次要資料庫無法使用，唯讀流量將會自動重新導向至主要複本。

  > [!NOTE]
  > `AllowReadOnlyFailoverToPrimary`只有在啟用自動容錯移轉原則，且 Azure 已觸發自動容錯移轉時，屬性才會生效。 在此情況下，如果屬性設定為 True，新的主要複本將會同時為讀寫和唯讀會話提供服務。

- **規劃的容錯移轉**

   計劃性容錯移轉會在主要資料庫和次要資料庫之間執行完整同步處理，然後將次要資料庫切換為主要角色。 這可確保不會遺失資料。 計劃性容錯移轉會用於下列案例：

  - 在資料遺失不可接受時，在生產環境中執行災害復原 (DR) 演練
  - 將資料庫重新放置到不同的區域
  - 在中斷情況趨緩 (容錯回復) 後，將資料庫傳回到主要區域。

- **未規劃的容錯移轉**

   非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 這項作業會導致資料遺失。 非計劃性容錯移轉是主要資料庫無法存取時，用來作為中斷期間的復原方法。 當原始的主要複本重新上線時，它會自動重新連線而不進行同步處理，並成為新的次要資料庫。

- **手動容錯移轉**

  無論自動容錯移轉設定為何，您都可以在任何時候手動起始容錯移轉。 如果未設定自動容錯移轉原則，就需要手動容錯移轉才能將容錯移轉群組中的資料庫復原至次要資料庫。 您可以起始強制或易用容錯移轉 (具有完整的資料同步處理)。 後者可用來將主要資料庫重新放置到次要區域。 當容錯移轉完成時，DNS 記錄會自動更新以確保能連線到新的主要複本。

- **資料遺失的寬限期**

  因為主要和次要資料庫是使用非同步複寫進行同步處理，容錯移轉可能會導致資料遺失。 您可以自訂自動容錯移轉原則，以反映應用程式對資料遺失的容錯程度。 藉由設定 `GracePeriodWithDataLossHours` ，您可以控制系統在起始可能造成資料遺失的容錯移轉之前等候的時間長度。

- **多個容錯移轉群組**

  您可以為同一組伺服器設定多個容錯移轉群組，以控制容錯移轉的範圍。 每個群組分別進行容錯移轉。 如果您的多租用戶應用程式使用彈性集區，可以使用這項功能來混合每個集區中的主要和次要資料庫。 如此一來，可以讓中斷只影響一半的租用戶。

  > [!NOTE]
  > SQL 受控執行個體不支援多個容錯移轉群組。
  
## <a name="permissions"></a>權限

容錯移轉群組的許可權是透過 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)來管理。 [SQL Server 參與者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)角色具有管理容錯移轉群組的所有必要許可權。

### <a name="create-failover-group"></a>建立容錯移轉群組

若要建立容錯移轉群組，您需要主要和次要伺服器的 RBAC 寫入存取權，以及容錯移轉群組中的所有資料庫。 針對 SQL 受控執行個體，您需要主要和次要 SQL 受控執行個體的 RBAC 寫入存取權，但個別資料庫的許可權並不相關，因為無法在容錯移轉群組中新增或移除個別 SQL 受控執行個體資料庫。

### <a name="update-a-failover-group"></a>更新容錯移轉群組

若要更新容錯移轉群組，您需要具有容錯移轉群組的 RBAC 寫入權限，以及目前主伺服器或受控實例上的所有資料庫。  

### <a name="fail-over-a-failover-group"></a>容錯移轉群組的容錯移轉

若要容錯移轉群組，您需要對新的主伺服器或受控實例上的容錯移轉群組進行 RBAC 寫入存取。

## <a name="best-practices-for-sql-database"></a>SQL Database 的最佳作法

您必須在主伺服器上設定自動容錯移轉群組，並將其連接到不同 Azure 區域中的次要伺服器。 群組可以包含這些伺服器中的所有或部分資料庫。 下圖說明使用多個資料庫和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![此圖顯示使用多個資料庫和自動容錯移轉群組之異地重複雲端應用程式的一般設定。](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> 請參閱 [將 SQL Database 新增至容錯移轉群組](failover-group-add-single-database-tutorial.md) ，以取得詳細的逐步教學課程，將 SQL Database 中的資料庫新增至容錯移轉群組。

在商務持續性前提下設計服務時，請遵循這些一般指導方針：

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>使用一或多個容錯移轉群組來管理多個資料庫的容錯移轉

可以在不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。 容錯移轉群組會使用和主要資料庫相同的服務目標，來建立異地次要資料庫。 如果您在容錯移轉群組中新增現有的異地複寫關聯性，請確定異地次要資料庫所設定的服務層級與計算大小和主要資料庫相同。
  
> [!IMPORTANT]
> Azure SQL Database 目前不支援在不同訂用帳戶中的兩部伺服器之間建立容錯移轉群組。 如果您在建立容錯移轉群組之後，將主要或次要伺服器移到不同的訂用帳戶，可能會導致容錯移轉要求和其他作業失敗。

### <a name="using-read-write-listener-for-oltp-workload"></a>針對 OLTP 工作負載使用讀寫接聽程式

在執行 OLTP 作業時使用 `<fog-name>.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 請注意，容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。

### <a name="using-read-only-listener-for-read-only-workload"></a>針對唯讀工作負載使用唯讀接聽程式

如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 針對唯讀工作階段，請使用 `<fog-name>.secondary.database.windows.net` 作為伺服器 URL，連線會自動導向至次要伺服器。 也建議您使用，在連接字串讀取意圖中表示 `ApplicationIntent=ReadOnly` 。

### <a name="preparing-for-performance-degradation"></a>準備效能降低

一般的 Azure 應用程式會使用多個 Azure 服務，並由多個元件組成。 容錯移轉群組的自動容錯移轉會根據 Azure SQL 元件本身的狀態觸發。 主要區域中的其他 Azure 服務可能不會受到中斷影響，且其元件可能仍可在該區域中使用。 主資料庫切換至 DR 區域之後，相依元件之間的延遲可能會增加。 若要避免對應用程式效能造成較高延遲的影響，請確定 DR 區域中所有應用程式元件的冗余，並遵循這些 [網路安全性指導方針](#failover-groups-and-network-security)。

### <a name="preparing-for-data-loss"></a>準備資料遺失

如果偵測到中斷，Azure 會等待您所指定的期間 `GracePeriodWithDataLossHours` 。 預設值為 1 小時。 如果您無法承受資料遺失的情況，請務必將設定 `GracePeriodWithDataLossHours` 為夠大的數位，例如24小時。 使用手動群組容錯移轉，從次要伺服器容錯回復到主要伺服器。

> [!IMPORTANT]
> 具有 800 個或更少 DTU 且使用異地複寫的資料庫超過 250 個的彈性集區，可能會遇到的問題包括規劃的容錯移轉時間較久與效能降低。  當地理複寫端點依地理位置廣泛相隔，或當每個資料庫使用多個次要端點時，較可能會發生這些寫入大量工作負載的問題。  異地複寫延遲隨時間而增加時，就可看出這些問題的徵兆。  您可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 來監視這個延遲。  如果發生這些問題，則風險降低方式包括增加集區 DTU 數目，或減少相同集區內異地複寫的資料庫數目。

### <a name="changing-secondary-region-of-the-failover-group"></a>變更容錯移轉群組的次要區域

為了說明變更順序，我們會假設伺服器 A 是主伺服器、伺服器 B 是現有的次要伺服器，而伺服器 C 是第三個區域中的新次要資料庫。  若要進行轉換，請遵循下列步驟：

1. 使用作用中異地複寫，為伺服器 A 上的每個資料庫建立額外的次要 [複本](active-geo-replication-overview.md)至伺服器 C。 伺服器 A 上的每個資料庫都有兩個次要複本，一個在伺服器 B 上，另一個在伺服器 C 上。這可保證主資料庫在轉換期間維持受保護。
2. 刪除容錯移轉群組。 此時，登入將會失敗。 這是因為容錯移轉群組接聽程式的 SQL 別名已刪除，而閘道將無法辨識容錯移轉組名。
3. 在伺服器 A 和 C 之間，重新建立具有相同名稱的容錯移轉群組。此時，登入將會停止失敗。
4. 將伺服器 A 上的所有主資料庫新增至新的容錯移轉群組。
5. 捨棄伺服器 B。B 上的所有資料庫都會自動刪除。

### <a name="changing-primary-region-of-the-failover-group"></a>變更容錯移轉群組的主要區域

為了說明變更順序，我們會假設伺服器 A 是主伺服器，伺服器 B 是現有的次要伺服器，而伺服器 C 是第三個區域中的新主資料庫。  若要進行轉換，請遵循下列步驟：

1. 執行計畫的容錯移轉，將主伺服器切換為 B。伺服器 A 會成為新的次要伺服器。 容錯移轉可能會導致幾分鐘的停機時間。 實際的時間會取決於容錯移轉群組的大小。
2. 使用 [主動式異地](active-geo-replication-overview.md)複寫，在伺服器 B 上的每個資料庫建立額外的次要複本。 伺服器 B 上的每個資料庫都有兩個次要複本，一個在伺服器 A 上，另一個在伺服器 C 上。這可保證主資料庫在轉換期間維持受保護。
3. 刪除容錯移轉群組。 此時，登入將會失敗。 這是因為容錯移轉群組接聽程式的 SQL 別名已刪除，而閘道將無法辨識容錯移轉組名。
4. 在伺服器 B 和 C 之間重新建立具有相同名稱的容錯移轉群組。此時，登入將會停止失敗。
5. 將 B 上的所有主資料庫新增至新的容錯移轉群組。
6. 執行容錯移轉群組的規劃容錯移轉，以切換 B 和 C。現在伺服器 C 會成為主要和 B-次要。 伺服器 A 上的所有次要資料庫都會自動連結到 C 上的主要複本。在步驟1中，容錯移轉可能會導致幾分鐘的停機時間。
7. 卸載伺服器 A。上的所有資料庫都會自動刪除。

> [!IMPORTANT]
> 刪除容錯移轉群組時，也會一併刪除接聽程式端點的 DNS 記錄。 屆時，其他人建立一個具有相同名稱的容錯移轉群組或伺服器別名的機率是非零的，這會讓您無法再次使用它。 若要將風險降至最低，請不要使用一般容錯移轉組名。

## <a name="best-practices-for-sql-managed-instance"></a>SQL 受控執行個體的最佳作法

自動容錯移轉群組必須在主要執行個體上設定，並將其連接至不同 Azure 區域中的次要執行個體。  執行個體中的所有資料庫都將複寫至次要執行個體。

下圖說明使用受控執行個體和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉圖表](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> 請參閱 [將受控實例新增至容錯移轉群組](../managed-instance/failover-group-add-instance-tutorial.md) ，以取得新增 SQL 受控執行個體以使用容錯移轉群組的詳細逐步教學課程。

如果您的應用程式使用 SQL 受控執行個體作為資料層，請遵循下列一般指導方針來設計商務持續性：

### <a name="creating-the-secondary-instance"></a>建立次要實例

為了確保在容錯移轉之後，主要 SQL 受控執行個體的非中斷連線，主要和次要實例都必須位於相同的 DNS 區域中。 它會保證相同的多網域 (SAN) 憑證可以用來驗證用戶端與容錯移轉群組中兩個實例的連接。 當您的應用程式準備好進行生產環境部署時，請在不同區域中建立次要 SQL 受控執行個體，並確定它會與主要 SQL 受控執行個體共用 DNS 區域。 您可以 `DNS Zone Partner` 使用 Azure 入口網站、PowerShell 或 REST API 指定選擇性參數來完成此動作。

> [!IMPORTANT]
> 在子網中建立的第一個受控實例會針對相同子網中的所有後續實例，決定 DNS 區域。 這表示來自相同子網的兩個實例不能屬於不同的 DNS 區域。

如需有關在與主要實例相同的 DNS 區域中建立次要 SQL 受控執行個體的詳細資訊，請參閱 [建立次要受控實例](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)。

### <a name="using-geo-paired-regions"></a>使用地理配對區域

基於效能考慮，這兩個受控執行個體都會部署到[配對區域](../../best-practices-availability-paired-regions.md)。 相較於非配對區域，位於異地配對區域中的受控執行個體會有更好的效能。 

### <a name="enabling-replication-traffic-between-two-instances"></a>在兩個實例之間啟用複寫流量

因為每個執行個體都在其自己的 VNet中隔離，因此必須允許這些 Vnet 之間的雙向流量。 請參閱 [Azure VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>在不同訂用帳戶中的受控執行個體之間建立容錯移轉群組

只要訂用帳戶與相同的 [Azure Active Directory 租](../../active-directory/fundamentals/active-directory-whatis.md#terminology)使用者相關聯，您就可以在兩個不同的訂用帳戶中建立 SQL 受控實例之間的容錯移轉群組。 使用 PowerShell API 時，您可以藉由指定 `PartnerSubscriptionId` 次要 SQL 受控執行個體的參數來進行。 使用 REST API 時，參數中包含的每個實例識別碼都 `properties.managedInstancePairs` 可以有自己的 subscriptionID。
  
> [!IMPORTANT]
> Azure 入口網站不支援跨不同訂用帳戶建立容錯移轉群組。 此外，對於跨不同訂用帳戶和/或資源群組的現有容錯移轉群組，無法透過入口網站從主要 SQL 受控執行個體手動起始容錯移轉。 請改為從異地次要執行個體起始。

### <a name="managing-failover-to-secondary-instance"></a>管理容錯移轉至次要執行個體

容錯移轉群組會管理 SQL 受控執行個體中所有資料庫的容錯移轉。 建立群組時，執行個體中的每個資料庫將會自動異地複寫到次要 SQL 執行個體。 您無法使用容錯移轉群組來起始資料庫子集的部分容錯移轉。

> [!IMPORTANT]
> 如果從主要 SQL 受控執行個體中移除資料庫，也會自動將它放在異地輔助 SQL 受控執行個體上。

### <a name="using-read-write-listener-for-oltp-workload"></a>針對 OLTP 工作負載使用讀寫接聽程式

在執行 OLTP 作業時使用 `<fog-name>.zone_id.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。 因為次要實例與主要實例共用 DNS 區域，所以用戶端應用程式將能夠使用相同的 SAN 憑證來與其重新連接。

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>使用唯讀接聽程式連接到次要實例

如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 若要直接連接到異地複寫的次要執行個體，請使用 `<fog-name>.secondary.<zone_id>.database.windows.net` 作為伺服器 URL，並直接連接到異地複寫的次要執行個體。

> [!NOTE]
> 在 Premium、Business Critical 和超大規模服務層級中，SQL Database 使用連接字串中的參數，支援使用一或多個唯讀複本的 [容量來執行唯讀查詢](read-scale-out.md) 工作負載 `ApplicationIntent=ReadOnly` 。 當您已設定異地複寫的次要執行個體時，可以使用此功能連接至主要位置或異地複寫位置中的唯讀複本。
>
> - 若要連接到主要位置的唯讀複本，請使用 `ApplicationIntent=ReadOnly` 和 `<fog-name>.<zone_id>.database.windows.net` 。
> - 若要連接到次要位置的唯讀複本，請使用 `ApplicationIntent=ReadOnly` 和 `<fog-name>.secondary.<zone_id>.database.windows.net` 。

### <a name="preparing-for-performance-degradation"></a>準備效能降低

一般的 Azure 應用程式會使用多個 Azure 服務，並由多個元件組成。 容錯移轉群組的自動容錯移轉會根據 Azure SQL 元件本身的狀態觸發。 主要區域中的其他 Azure 服務可能不會受到中斷影響，且其元件可能仍可在該區域中使用。 主資料庫切換至次要區域之後，相依元件之間的延遲可能會增加。 若要避免應用程式效能較高延遲的影響，請確定次要區域中所有應用程式元件的複本，並將應用程式元件與資料庫一起容錯移轉。 在設定時，請遵循 [網路安全性指導方針](#failover-groups-and-network-security) ，以確保與次要區域中資料庫的連接。

### <a name="preparing-for-data-loss"></a>準備資料遺失

如果偵測到中斷，如果資料遺失，就會觸發讀寫容錯移轉，以獲得我們的知識。 否則，就會在您使用指定的期間延遲容錯移轉 `GracePeriodWithDataLossHours` 。 如果您指定 `GracePeriodWithDataLossHours`，請針對資料遺失做好準備。 服務中斷期間，Azure 一般會傾向維持可用性。 如果您無法承受資料遺失的情況，請務必將 >graceperiodwithdatalosshours 設定為夠大的數位，例如24小時，或停用自動容錯移轉。

在起始容錯移轉之後，將立即發生讀寫接聽程式的 DNS 更新。 這項作業不會導致資料遺失。 但是，在正常情況下，切換資料庫角色的程序最多可能需要 5 分鐘的時間。 在完成之前，新的主要執行個體中的某些資料庫仍會處於唯讀模式。 如果使用 PowerShell 起始容錯移轉，則切換主要複本角色的作業是同步的。 如果是使用 Azure 入口網站起始，UI 將會指出完成狀態。 如果使用 REST API 來起始，請使用標準 Azure Resource Manager 的輪詢機制來監視完成情況。

> [!IMPORTANT]
> 使用手動群組容錯移轉，將主要資料庫移回原始位置。 當造成容錯移轉的中斷情況趨緩時，您可以將主要資料庫移動到原始位置。 若要這樣做，您應該起始群組的手動容錯移轉。
  
### <a name="changing-secondary-region-of-the-failover-group"></a>變更容錯移轉群組的次要區域

假設實例 A 是主要實例，實例 B 是現有的次要實例，而實例 C 是第三個區域中的新次要實例。  若要進行轉換，請遵循下列步驟：

1. 在相同的 DNS 區域中，建立與相同大小的實例 C。
2. 刪除實例 A 和 B 之間的容錯移轉群組。此時，登入將會失敗，因為容錯移轉群組接聽程式的 SQL 別名已刪除，而閘道將無法辨識容錯移轉組名。 次要資料庫將會從主要複本中斷連線，而且會變成讀寫資料庫。
3. 在實例 A 與 C 之間建立具有相同名稱的容錯移轉群組。請依照 [SQL 受控執行個體教學課程中的容錯移轉群組](../managed-instance/failover-group-add-instance-tutorial.md)中的指示進行。 這是資料大小的作業，當實例 A 中的所有資料庫都已植入和同步處理時，將會完成此作業。
4. 如果不需要，請刪除實例 B 以避免不必要的費用。

> [!NOTE]
> 在步驟2之後，在步驟3完成之前，實例 A 中的資料庫將保持不受保護，不受實例 A 的嚴重失敗影響。

### <a name="changing-primary-region-of-the-failover-group"></a>變更容錯移轉群組的主要區域

假設實例 A 是主要實例，實例 B 是現有的次要實例，而實例 C 是第三個區域中的新主要實例。  若要進行轉換，請遵循下列步驟：

1. 在相同的 DNS 區域中建立大小與 B 相同的實例 C。
2. 連接至實例 B 並手動容錯移轉，將主要實例切換為 B。實例 A 會自動成為新的次要實例。
3. 刪除實例 A 和 B 之間的容錯移轉群組。此時，登入將會失敗，因為容錯移轉群組接聽程式的 SQL 別名已刪除，而閘道將無法辨識容錯移轉組名。 次要資料庫將會從主要複本中斷連線，而且會變成讀寫資料庫。
4. 在實例 A 與 C 之間建立具有相同名稱的容錯移轉群組。請遵循 [具有受控實例的容錯移轉群組教學](../managed-instance/failover-group-add-instance-tutorial.md)課程中的指示。 這是資料大小的作業，當實例 A 中的所有資料庫都已植入和同步處理時，將會完成此作業。
5. 如果不需要，請刪除實例 A 以避免不必要的費用。

> [!CAUTION]
> 在步驟3之後，在步驟4完成之前，實例 A 中的資料庫將保持未受保護狀態，而不是實例 A 的災難性失敗。

> [!IMPORTANT]
> 刪除容錯移轉群組時，也會一併刪除接聽程式端點的 DNS 記錄。 屆時，其他人建立一個具有相同名稱的容錯移轉群組或伺服器別名的機率是非零的，這會讓您無法再次使用它。 若要將風險降至最低，請不要使用一般容錯移轉組名。

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>啟用相依于系統資料庫中物件的情節
系統資料庫不會複寫至容錯移轉群組中的次要實例。 若要啟用相依于系統資料庫中物件的案例，請在次要實例上，確定在次要資料庫上建立相同的物件。 例如，如果您打算在次要實例上使用相同的登入，請務必使用相同的 SID 來建立它們。 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>容錯移轉群組和網路安全性

針對某些應用程式，安全性規則會要求將資料層的網路存取限制為特定元件或元件（例如 VM、web 服務等等）。這項需求會對商務持續性設計以及使用容錯移轉群組帶來一些挑戰。 在執行這類限制存取時，請考慮下列選項。

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用容錯移轉群組和虛擬網路規則

如果您使用 [虛擬網路服務端點和規則](vnet-service-endpoint-rule-overview.md) 來限制 SQL DATABASE 或 SQL 受控執行個體中對資料庫的存取，請注意每個虛擬網路服務端點只適用于一個 Azure 區域。 端點無法讓其他區域接受來自子網路的通訊。 因此，只有部署到相同區域中的用戶端應用程式可以連線到主要資料庫。 因為容錯移轉會導致 SQL Database 用戶端會話重新路由至不同 (次要) 區域中的伺服器，所以如果來自該區域外的用戶端，這些會話將會失敗。 基於這個理由，如果參與的伺服器或實例包含在虛擬網路規則中，就無法啟用自動容錯移轉原則。 若要支援手動容錯移轉，請遵循下列步驟：

1. 在次要地區中佈建您應用程式 (Web 服務、虛擬機器等) 前端元件的備援副本
2. 分別設定主要和次要伺服器的[虛擬網路規則](vnet-service-endpoint-rule-overview.md)
3. 啟用[使用流量管理員設定的前端容錯移轉](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 偵測到中斷情況時，起始手動容錯移轉。 這個選項最適合用於在前端和資料層之間需要一致延遲的應用程式，且支援當前端、資料層或兩者受到中斷影響時進行復原。

> [!NOTE]
> 如果您使用 **唯讀接聽程式** 對唯讀工作負載進行負載平衡，請確保此工作負載會在 VM 或是次要地區的其他資源中執行，使其得以連線到次要資料庫。

### <a name="use-failover-groups-and-firewall-rules"></a>使用容錯移轉群組和防火牆規則

如果您的商務持續性計畫需要使用具有自動容錯移轉的群組進行容錯移轉，您可以使用傳統的防火牆規則來限制 SQL Database 中的資料庫存取。 若要支援自動容錯移轉，請遵循下列步驟：

1. [建立公用 IP](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [建立公用負載平衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)並為其指派公用 IP。
3. [建立虛擬網路和虛擬機器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)以用於前端元件
4. [建立網路安全性群組](../../virtual-network/network-security-groups-overview.md)並設定輸入連線。
5. 使用 ' Sql ' [服務標記](../../virtual-network/network-security-groups-overview.md#service-tags)，確定輸出連線已開放 Azure SQL Database。
6. 建立 [SQL Database 防火牆規則](firewall-configure.md) ，以允許您在步驟1中建立之公用 IP 位址的輸入流量。

如需有關如何設定輸出存取以及要在防火牆規則中使用哪個 IP 的詳細資訊，請參閱 [負載平衡器輸出連接](../../load-balancer/load-balancer-outbound-connections.md)。

上述組態可確保自動容錯移轉不會封鎖來自前端元件的連線，並假設應用程式可以容忍前端與資料層之間較長的延遲。

> [!IMPORTANT]
> 若要保證區域性中斷時的商務持續性，您必須確定前端元件與資料庫的異地備援。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>在受控執行個體與其 VNet 之間啟用異地複寫

當您在兩個不同區域中的主要和次要 SQL 受控實例之間設定容錯移轉群組時，每個實例都會使用獨立的虛擬網路進行隔離。 若要允許這些 Vnet 之間的複寫流量，請確定符合下列必要條件：

- SQL 受控執行個體的兩個實例必須位於不同的 Azure 區域。
- SQL 受控執行個體的兩個實例必須是相同的服務層級，而且具有相同的儲存體大小。
- 您的 SQL 受控執行個體的次要實例必須是空的， (沒有任何使用者資料庫) 。
- SQL 受控執行個體的實例所使用的虛擬網路必須透過 [VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)進行連線。 當兩個虛擬網路透過內部部署網路連線時，請確定防火牆規則不會封鎖連接埠 5022 和 11000-11999。 支援全域 VNet 對等互連，但有下列注意事項中所述的限制。

   > [!IMPORTANT]
   > [在9/22/2020 上，我們為新建立的虛擬叢集宣佈了全域虛擬網路對等互連](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 這表示在公告日期之後于空白子網中建立的 SQL 受控實例，以及這些子網中建立的所有後續受控實例，都支援全域虛擬網路對等互連。 針對所有其他 SQL 受控實例對等互連支援，受限於相同區域中的網路，原因是 [全域虛擬網路對等互連的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 另請參閱 [Azure 虛擬網路常見問題](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 文章中的相關章節，以取得詳細資料。 

- 這兩個 SQL 受控執行個體 Vnet 不能有重迭的 IP 位址。
- 您需要設定網路安全性群組 (NSG)，以便連接埠 5022 和範圍 11000 ~ 12000 開啟來自其他受控執行個體子網路的輸入和輸出連線。 這是為了允許執行個體之間的複寫流量。

   > [!IMPORTANT]
   > 設定錯誤的 NSG 安全性規則會導致資料庫複製作業停滯。

- 次要 SQL 受控執行個體是以正確的 DNS 區域識別碼進行設定。 DNS 區域是 SQL 受控執行個體和基礎虛擬叢集的屬性，其識別碼包含在主機名稱位址中。 在每個 VNet 中建立第一個 SQL 受控執行個體，且相同的識別碼指派給相同子網中的所有其他實例時，會以隨機字串的形式產生區域識別碼。 一旦指派之後，就無法修改 DNS 區域。 相同容錯移轉群組中包含的 SQL 受控實例必須共用 DNS 區域。 若要完成此動作，您可以在建立次要實例時，傳遞主要實例的區域識別碼作為 DnsZonePartner 參數的值。

   > [!NOTE]
   > 如需使用 SQL 受控執行個體設定容錯移轉群組的詳細教學課程，請參閱 [將 sql 受控執行個體新增至容錯移轉群組](../managed-instance/failover-group-add-instance-tutorial.md)。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層級內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 升級時，建議您先升級所有次要資料庫，然後再升級主資料庫。 降級時，請反轉順序：先降級主資料庫，然後再降級所有次要資料庫。 當您將資料庫升級或降級到不同的服務層級時，會強制執行這項建議。

建議您特別注意此順序，以避免較低 SKU 的次要複本發生超載，而必須在升級或降級程序期間重新植入的問題。 您也可以將主要資料庫狀態設為唯讀，以此方式影響對主要資料庫的所有讀寫工作負載，來避免此問題。

> [!NOTE]
> 如果您在容錯移轉群組設定中建立次要資料庫，則不建議您降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 呼叫會 `sp_wait_for_database_copy_sync` 封鎖呼叫的執行緒，直到最後認可的交易傳輸到次要資料庫為止。 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 `sp_wait_for_database_copy_sync` 範圍設定為特定的連續複製連結。 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> `sp_wait_for_database_copy_sync` 在容錯移轉之後防止資料遺失，但不保證讀取權限的完整同步處理。 程序呼叫所造成的延遲 `sp_wait_for_database_copy_sync` 可能很大，而且會根據呼叫時交易記錄檔的大小而定。

## <a name="failover-groups-and-point-in-time-restore"></a>容錯移轉群組和時間點還原

如需使用容錯移轉群組中的時間點還原的相關資訊，請參閱[時間點復原 (PITR)](recovery-using-backups.md#point-in-time-restore)。

## <a name="limitations-of-failover-groups"></a>容錯移轉群組的限制

請留意以下限制：

- 無法在相同 Azure 區域中的兩個伺服器或實例之間建立容錯移轉群組。
- 無法重新命名容錯移轉群組。 您將需要刪除群組，並使用不同的名稱重新建立。
- 容錯移轉群組中的實例不支援資料庫重新命名。 您將需要暫時刪除容錯移轉群組，才能重新命名資料庫。
- 系統資料庫不會複寫至容錯移轉群組中的次要實例。 因此，除非在次要資料庫上以手動方式建立物件，否則不可能在次要實例上相依于系統資料庫中物件的案例。

## <a name="programmatically-managing-failover-groups"></a>以程式設計方式管理容錯移轉群組

如前所述，自動容錯移轉群組和主動式異地複寫也可以使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](/rest/api/sql/) 和 [Azure PowerShell Cmdlet](/powershell/azure/)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需有關如何執行存取角色的詳細資訊，請參閱 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)。

### <a name="manage-sql-database-failover"></a>管理 SQL Database 容錯移轉

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [移除-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 從伺服器移除容錯移轉群組 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 抓取容錯移轉群組的設定 |
| [設定-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改容錯移轉群組的設定 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 觸發容錯移轉群組容錯移轉至次要伺服器 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|將一或多個資料庫新增至容錯移轉群組|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 命令 | 描述 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [az sql 容錯移轉-群組刪除](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 從伺服器移除容錯移轉群組 |
| [az sql 容錯移轉-群組顯示](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 抓取容錯移轉群組設定 |
| [az sql 容錯移轉-群組更新](/cli/azure/sql/failover-group#az-sql-failover-group-update) |修改容錯移轉群組的設定及/或將一或多個資料庫新增至容錯移轉群組|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 觸發容錯移轉群組容錯移轉至次要伺服器 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](/rest/api/sql/failovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](/rest/api/sql/failovergroups/delete) | 從伺服器移除容錯移轉群組 |
| [容錯移轉 (計劃性)](/rest/api/sql/failovergroups/failover) | 觸發從目前主伺服器到具有完整資料同步處理的次要伺服器的容錯移轉。|
| [強制容錯移轉允許資料遺失](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 觸發從目前的主伺服器容錯移轉至次要伺服器，而不需要同步處理資料。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](/rest/api/sql/failovergroups/get) | 抓取容錯移轉群組的設定。 |
| [依伺服器列出容錯移轉群組](/rest/api/sql/failovergroups/listbyserver) | 列出伺服器上的容錯移轉群組。 |
| [更新容錯移轉群組](/rest/api/sql/failovergroups/update) | 更新容錯移轉群組的設定。 |

---

### <a name="manage-sql-managed-instance-failover"></a>管理 SQL 受控執行個體容錯移轉


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要實例上註冊|
| [設定-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |修改容錯移轉群組的設定|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |抓取容錯移轉群組的設定|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |觸發容錯移轉群組容錯移轉至次要實例|
| [移除-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 移除容錯移轉群組|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 命令 | 描述 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [az sql 容錯移轉-群組刪除](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 從伺服器移除容錯移轉群組 |
| [az sql 容錯移轉-群組顯示](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 抓取容錯移轉群組設定 |
| [az sql 容錯移轉-群組更新](/cli/azure/sql/failover-group#az-sql-failover-group-update) |修改容錯移轉群組的設定及/或將一或多個資料庫新增至容錯移轉群組|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 觸發容錯移轉群組容錯移轉至次要伺服器 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](/rest/api/sql/instancefailovergroups/createorupdate) | 建立或更新容錯移轉群組的設定 |
| [刪除容錯移轉群組](/rest/api/sql/instancefailovergroups/delete) | 從實例移除容錯移轉群組 |
| [容錯移轉 (計劃性)](/rest/api/sql/instancefailovergroups/failover) | 使用完整資料同步處理，觸發從目前的主要實例到這個實例的容錯移轉。 |
| [強制容錯移轉允許資料遺失](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 觸發從目前的主要實例到次要實例的容錯移轉，而不需要同步處理資料。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](/rest/api/sql/instancefailovergroups/get) | 抓取容錯移轉群組的設定。 |
| [列出容錯移轉群組 - 依位置列出](/rest/api/sql/instancefailovergroups/listbylocation) | 列出位置中的容錯移轉群組。 |

---

## <a name="next-steps"></a>後續步驟

- 如需詳細的教學課程，請參閱
  - [將 SQL Database 新增至容錯移轉群組](failover-group-add-single-database-tutorial.md)
  - [將彈性集區新增到容錯移轉群組](failover-group-add-elastic-pool-tutorial.md)
  - [將 SQL 受控執行個體新增至容錯移轉群組](../managed-instance/failover-group-add-instance-tutorial.md)
- 如需範例指令碼，請參閱：
  - [使用 PowerShell 設定 Azure SQL Database 的主動式異地複寫](scripts/setup-geodr-and-failover-database-powershell.md)
  - [使用 PowerShell 為 Azure SQL Database 中的集區資料庫設定作用中異地複寫](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [使用 PowerShell 將 Azure SQL Database 新增至容錯移轉群組](scripts/add-database-to-failover-group-powershell.md)
- 如需商務持續性的總覽和案例，請參閱 [商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- 若要瞭解 Azure SQL Database 自動備份的相關資訊，請參閱 [SQL Database 自動備份](automated-backups-overview.md)。
- 若要瞭解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](recovery-using-backups.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](active-geo-replication-security-configure.md)。