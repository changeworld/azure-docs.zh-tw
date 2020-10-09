---
title: 最佳作法
description: 了解開發 Azure Batch 解決方案的最佳做法和實用秘訣。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 695f213c0683bd158539b97719f2c2d8c0210edf
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849484"
---
# <a name="azure-batch-best-practices"></a>Azure Batch 最佳做法

本文將根據 Batch 的實際使用體驗，討論有效使用 Azure Batch 服務的最佳做法集合。 閱讀本文可讓您在開發和使用 Batch 時避開設計錯誤、潛在效能問題和反向模式。

## <a name="pools"></a>集區

[集區](nodes-and-pools.md#pools)是在 Batch 服務上執行作業的計算資源。 下列各節將提供使用 Batch 集區時的建議。

### <a name="pool-configuration-and-naming"></a>集區設定和命名

- **集區配置模式** 建立 Batch 帳戶時，您可以選擇兩個集區配置模式：**Batch 服務** 或**使用者訂用帳戶**。 在大部分情況下，您應該使用預設 Batch 服務模式，以在幕後將集區配置在 Batch 管理的訂用帳戶中。 在其他使用者訂用帳戶模式中，在建立集區時，會直接在您的訂用帳戶中建立 Batch VM 和其他資源。 使用者訂用帳戶主要是用來啟用重要但較小的案例子集。 若要深入了解使用者訂用帳戶模式，請參閱[使用者訂用帳戶模式的其他設定](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)。

- **判斷作業與集區的對應時，請考慮作業和工作執行時間。**
    如果您的作業主要是由短期執行的作業所組成，而且預期的總工作計數很少，所以作業的整體預期執行時間不會太長，那麼請不要為每個工作配置新的集區。 節點的配置時間將會降低作業的執行時間。

- **集區應具有一個以上的計算節點。**
    個別節點不保證一律可供使用。 雖然不常見，但硬體故障、作業系統更新和具有其他問題的主機可能會導致個別節點離線。 如果您的 Batch 工作負載需要具決定性且保證的進度，您應該配置具有多個節點的集區。

- **請勿重複使用資源名稱。**
    Batch 資源 (作業、集區等) 通常會隨時間而不同。 例如，您可以在星期一建立集區、在星期二將其刪除，然後在星期四建立另一個集區。 您所建立的每個新資源，都應該指定您之前未曾用過的唯一名稱。 這可以藉由使用 GUID 來完成 (作為整個資源名稱或其中一部分)，或在資源名稱中內嵌資源的建立時間。 Batch 支援 [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)，此屬性可為資源提供人類看得懂的名稱 (即使實際的資源識別碼不是人類易懂的內容)。 使用唯一名稱可讓您更輕鬆地區分特定資源在記錄和計量中的作用。 如果您必須為資源提出支援案例，此方式也有助於避免模稜兩可的情況。

- **集區維護和失敗期間的持續性。**
    您的作業應以動態方式使用集區。 如果您的作業針對所有項目使用相同集區，則當集區發生問題時，您的作業可能會無法執行。 對於重視時間性的工作負載而言，這一點特別重要。 若要修正此問題，請在為每個作業進行排程時動態地選取或建立集區，或使用方法來覆寫集區名稱，以便略過狀況不良的集區。

- **集區維護和失敗時的商務持續性** 有許多可能的原因會使集區無法成長到您所需要的大小，例如內部錯誤和容量限制等。基於這個理由，您應該準備好在不同集區上重定作業目標，以備不時之需 (可能會使用不同 VM 大小 - Batch 會透過 [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)來支援此作業)。 請避免在預期永遠不會刪除且永遠不會變更的情況下使用靜態集區識別碼。

### <a name="pool-lifetime-and-billing"></a>集區存留期和計費

集區存留期可能會因為配置的方法和套用至集區設定的選項而有所不同。 集區在任何時間點都可以有不固定的存留期和不同數目的計算節點。 您必須負責明確地或透過服務所提供的功能 (自動調整或 autopool)，來管理集區中的計算節點。

- **讓集區保持在最新狀態。**
    您應該每隔幾個月就將集區大小調整為零，以確保您能取得最新的節點代理程式更新和錯誤修正程式。 如果未重新建立集區或將其大小調整為 0 個計算節點，您的集區將不會收到節點代理程式更新。 在您重新建立集區或調整其大小之前，建議您下載所有節點代理程式記錄來進行偵錯，如[節點](#nodes)一節中所述。

- **集區重新建立** 同樣地，我們不建議您每日刪除並重新建立您的集區。 相反地，請建立新的集區，並更新現有作業來指向新的集區。 當所有工作都已移至新集區之後，再刪除舊的集區。

- **集區效率和計費** Batch 本身不會產生額外費用，但所使用的計算資源會產生費用。 您需針對集區中的每個計算節點支付費用，而且不論其狀態為何。 這包括執行節點所需的任何費用，例如儲存體和網路成本。 若要了解更多最佳做法，請參閱 [Azure Batch 的 成本分析和預算](budget.md)。

### <a name="pool-allocation-failures"></a>集區配置失敗

集區配置失敗可能會在第一次配置或後續調整大小的任何時間點發生。 這可能是因為在某個區域發生暫時容量耗盡問題，或 Batch 所依賴的其他 Azure 服務發生失敗。 您的核心配額不是保證，而是限制。

### <a name="unplanned-downtime"></a>非計畫的停機時間

Batch 集區可能會在 Azure 中遇到停機事件。 在規劃和開發 Batch 案例或工作流程時，請記住這一點。

如果節點失敗，Batch 會自動嘗試代表您復原這些計算節點。 這可能會在復原的節點上，觸發任何正在執行的工作進行重新排程。 如需有關中斷工作的詳細資訊，請參閱[重試設計](#design-for-retries-and-re-execution)。

### <a name="custom-image-pools"></a>自訂映射集區

當您使用虛擬機器設定建立 Azure Batch 集區時，需指定 VM 映像，以提供集區中每個計算節點的作業系統。 您可以使用支援的 Azure Marketplace 映射來建立集區，也可以 [使用共用映射庫映射來建立自訂映射](batch-sig-images.md)。 雖然您也可以使用 [受控映射](batch-custom-images.md) 來建立自訂映射集區，但建議您盡可能使用共用映射庫來建立自訂映射。 使用共用映射庫可協助您更快速地布建集區、調整更大量的 Vm，以及改善布建 Vm 時的可靠性。

### <a name="third-party-images"></a>協力廠商映射

您可以使用發佈至 Azure Marketplace 的協力廠商映射來建立集區。 使用使用者訂用帳戶模式 Batch 帳戶時，您可能會在使用某些協力廠商映射建立集區時看到「因為 marketplace 購買資格檢查，導致配置失敗」錯誤。 若要解決此錯誤，請接受映射發行者所設定的條款。 您可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/image/terms)來這麼做。

### <a name="azure-region-dependency"></a>Azure 區域相依性

如果您的工作負載重視時間性或用於生產，建議您不要依賴單一 Azure 區域。 雖然很罕見，但還是有可能發生會影響整個區域的問題。 例如，如果您的處理需要在特定時間啟動，請考慮在「開始時間之前」，就在您主要區域中擴大集區。 如果該集區調整失敗，您可以切換回備份區域中來擴大集區。 集區如果橫跨不同區域中的多個帳戶，則可在另一個集區發生問題時，提供已就緒且容易存取的備份。 如需詳細資訊，請參閱[設計應用程式以獲得高可用性](high-availability-disaster-recovery.md)。

## <a name="jobs"></a>工作

[作業](jobs-and-tasks.md#jobs)是一種容器，其設計目的是要包含數百個、數千個或甚至數百萬個工作。 建立作業時，請遵循這些指導方針。

### <a name="fewer-jobs-more-tasks"></a>作業少，工作多

使用一項作業執行單一工作相當沒效率。 例如，相較於建立各包含 10 個工作的 100 項作業，使用包含 1000 個工作的單一作業會更有效率。 執行各包含單一工作的 1000 個作業是最不具效率、最緩慢且成本最高的方法。

因此，請勿設計需要數千個作業同時作業的 Batch 解決方案。 工作沒有任何配額限制，因此請盡可能在較少的作業下執行許多工作，以有效率地使用您的[作業和作業排程配額](batch-quota-limit.md#resource-quotas)。

### <a name="job-lifetime"></a>作業存留期

Batch 作業在從系統中刪除之前，會有無限的存留期。 其狀態會表明其是否可以接受更多工作來進行排程。

除非明確終止，否則作業不會自動移至已完成狀態。 這可透過 [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) 屬性或 [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints) 來自動觸發。

其中有預設的[作用中作業和作業排程配額](batch-quota-limit.md#resource-quotas)。 處於 [已完成] 狀態的作業和作業排程不會計入此配額。

## <a name="tasks"></a>工作

[工作](jobs-and-tasks.md#tasks)是組成作業的個別作用單位。 工作會由使用者提交，並由 Batch 安排到計算節點上。 建立和執行工作時，須注意幾個設計考量。 下列各節將說明常見的案例，以及如何設計工作來處理問題並有效率地執行。

### <a name="save-task-data"></a>儲存工作資料

就本質來說，計算節點的存在時間相當短暫。 Batch 中有許多功能 (例如 autopool 和自動調整) 可讓節點輕鬆地消失。 節點離開集區時 (由於調整大小或集區刪除)，也會一併刪除這些節點上的所有檔案。 因此，工作應該在完成之前，將其輸出移出其執行所在的節點，然後移至長期存放區。 同樣地，如果工作失敗，則應該將診斷失敗所需的記錄移到長期存放區。

Batch 已整合支援，可讓 Azure 儲存體透過 [OutputFiles](batch-task-output-files.md) 和各種共用檔案系統上傳資料，或是，您也可以在工作中自行執行上傳作業。

### <a name="manage-task-lifetime"></a>管理工作存留期

不再需要工作時，請刪除工作，或設定 [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) 工作限制。 如果設定了 `retentionTime`，Batch 會在 `retentionTime` 到期時，自動清除工作所使用的磁碟空間。

刪除工作可達到兩件事。 其可確保您在作業中沒有建置工作，這可能會使查詢/尋找您感興趣的工作變得更困難 (因為您必須篩選完成的工作)。 也會清除節點上對應的工作資料 (尚未達到已提供的`retentionTime`)。 這有助於確保您的節點不會填滿工作資料並耗盡磁碟空間。

### <a name="submit-large-numbers-of-tasks-in-collection"></a>在集合中提交大量工作

工作可以個別提交或以集合的方式提交。 在提交大量工作時，您可以透過[集合](/rest/api/batchservice/task/addcollection)提交最多 100 個工作，以減少額外負荷和提交時間。

### <a name="set-max-tasks-per-node-appropriately"></a>適當設定每個節點的工作數目上限

Batch 支援節點上有超載的工作 (執行的工作數目比節點具有的核心還多)。 您可以選擇是否要確保工作數目「符合」集區中的節點數目。 例如，如果您嘗試將八個各耗用 25% CPU 使用量的工作安排到一個節點上 (在具有 `taskSlotsPerNode = 8` 的集區中) 時，您可能會感受到降級的體驗。

### <a name="design-for-retries-and-re-execution"></a>重試和重新執行的設計

Batch 可以自動重試工作。 重試的類型有兩種：使用者控制和內部。 使用者控制的重試是由工作的 [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) 來指定。 當工作中指定的程式以非零結束代碼結束時，工作會重試到達到 `maxTaskRetryCount` 的值。

雖然很罕見，但工作會因為計算節點發生失敗而在內部重試，例如無法在工作執行時更新內部狀態或節點上發生失敗。 工作會盡可能在相同的計算節點上重試，直到達到內部限制的次數後才放棄，然後延遲工作，直到 Batch 進行重新排程 (可能是在不同的計算節點上進行)。

無論在專用或低優先順序的節點上執行您的工作，設計上都不會有任何差異。 無論是在低優先順序的節點上執行工作時遭到佔用，或因為專用節點失敗而造成工作中斷，這兩種情況都是藉由設計能承受失敗的工作來降低風險。

### <a name="build-durable-tasks"></a>建置耐用的工作

工作應設計為可承受失敗並可配合重試。 這對於長時間執行的工作而言特別重要。 若要達到此目的，請確定工作會產生相同的單一結果，即使多次執行工作也一樣。 達成此目標的其中一種方式，就是讓您的工作進行「目標搜尋」。 另一種方式是確定您的工作具有等冪性 (不論工作執行多少次，都會有相同的結果)。

常見的範例是將檔案複製到計算節點的工作。 其中一個簡單的方法就是工作在每次執行時複製所有指定檔案，但這樣不僅沒效率，而且無法承受失敗。 應改為建立能確定檔案位於計算節點上的工作；不會重新複製已存在檔案的工作。 如此一來，工作就會從中斷的地方繼續進行。

### <a name="avoid-short-execution-time"></a>避免短時間執行

只執行一到兩秒的工作並不理想。 您應該嘗試在個別工作中執行大量的工作 (最少 10 秒，最多可以到數小時或數天)。 如果每個工作都執行一分鐘 (或以上)，則排程負荷在整體計算時間中所佔的部份就會比較小。

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>針對 Windows 節點上的簡短工作使用集區範圍

在 Batch-節點上排程工作時，您可以選擇是否要使用工作範圍或集區範圍來執行工作。 如果工作只會短暫執行一次，工作範圍可能會因為建立該工作的自動使用者帳戶所需的資源而沒有效率。 為了提高效率，請考慮將這些工作設定為集區範圍。 如需詳細資訊，請參閱以 [具有集區範圍的自動使用者的形式來執行工作](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope)。

## <a name="nodes"></a>節點

[計算節點](nodes-and-pools.md#nodes)是 Azure 虛擬機器 (VM) 或雲端服務 VM，專門用來處理您應用程式的部分工作負載。 使用節點時，請遵循這些指導方針。

### <a name="idempotent-start-tasks"></a>等冪啟動工作

就像其他工作一樣，[啟動工作](jobs-and-tasks.md#start-task)應該具有等冪性，因為其會在每次節點啟動時重新執行。 等冪工作只是個執行多次仍會產生一致結果的工作。

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>透過作業系統服務介面管理長時間執行的服務

有時候，您需要在節點中同時執行 Batch 代理程式與另一個代理程式。 例如，您可以從節點收集資料並報告該資料。 建議您將這些代理程式部署為作業系統服務，例如 Windows 服務或 Linux `systemd` 服務。

這些服務在執行時，不得在節點上由 Batch 管理的目錄中，對任何檔案採取檔案鎖定，否則 Batch 將因為檔案鎖定而無法刪除這些目錄。 例如，如果在啟動工作中安裝 Windows 服務，而不是直接從 [啟動工作] 工作目錄中啟動服務，應將檔案複製到其他位置 (或是如果檔案存在，請直接略過複製動作)。 然後從該位置安裝服務。 當 Batch 重新執行啟動工作時，其會刪除 [啟動工作] 工作目錄，並重新建立一個。 這是因為服務在另一個目錄上有檔案鎖定，但在 [啟動工作] 作業目錄上沒有。

### <a name="avoid-creating-directory-junctions-in-windows"></a>避免在 Windows 中建立目錄連接

目錄連接 (有時稱為目錄永久連結) 在清除工作和作業時很難處理。 請使用符號連結 (軟連結)，而不是永久連結。

### <a name="collect-the-batch-agent-logs"></a>收集 Batch 代理程式記錄

如果您注意到節點行為有異或在節點上執行的工作有問題，請在解除配置問題節點之前，先收集 Batch 代理程式記錄。 您可以使用上傳 Batch 服務記錄 API 來收集 Batch 代理程式記錄。 這些記錄可以作為 Microsoft 支援票證的一部分提供，並有助於對問題進行疑難排解。

### <a name="manage-os-upgrades"></a>管理作業系統升級

若為使用者訂用帳戶模式 Batch 帳戶，自動 OS 升級可能會中斷工作進度，特別是當工作長時間執行時。 [建立等冪](#build-durable-tasks) 工作有助於減少這些中斷所造成的錯誤。 我們也建議您 [排程 OS 映射升級，以找出不應執行工作的時間](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades)。

## <a name="isolation-security"></a>隔離安全性

基於隔離的目的，如果您的案例需要隔離作業，請將這些作業放在不同的集區中。 集區是 Batch 中的安全性隔離界限，而且根據預設，兩個集區無法看到彼此或進行通訊。 請避免使用不同 Batch 帳戶來作為隔離方法。

## <a name="moving-batch-accounts-across-regions"></a>跨區域移動 Batch 帳戶

在某些情況下，將現有的 Batch 帳戶從一個區域移至另一個區域可能會很有幫助。 例如，您可以基於災害復原規劃而移到另一個區域。

Azure Batch 帳戶無法直接從一個區域移至另一個區域。 不過，您可以使用 Azure Resource Manager 範本來匯出 Batch 帳戶的現有設定。 接著，您可以將 Batch 帳戶匯出至範本、修改參數以符合目的地區域，然後將範本部署到新的區域，藉此將資源放在另一個區域中。

將範本上傳至新的區域之後，您必須重新建立憑證、作業排程和應用程式套件。 若要認可變更並完成 Batch 帳戶的移動，請記得刪除原始的 Batch 帳戶或資源群組。

如需有關 Resource Manager 和範本的詳細資訊，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

## <a name="connectivity"></a>連線能力

考慮您 Batch 解決方案中的連線能力時，請參閱下列指導方針。

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>網路安全性群組 (NSG) 和使用者定義路由 (UDR)

[在虛擬網路中佈建 Batch 集區](batch-virtual-network.md)時，請確定您已仔細遵循指導方針來使用 `BatchNodeManagement` 服務標記、連接埠、通訊協定和規則方向。
強烈建議您使用服務標記，而不是使用基礎 Batch 服務 IP 位址。 這是因為 IP 位址可能會隨著時間變更。 直接使用 Batch 服務 IP 位址可能會導致您的 Batch 集區不穩定或中斷。

針對使用者定義路由 (UDR)，請確定您已準備好在路由表中定期更新 Batch 服務 IP 位址的程序，因為這些位址會隨著時間而變更。 若要了解如何取得 Batch 服務 IP 位址的清單，請參閱[服務標記內部部署](../virtual-network/service-tags-overview.md)。 Batch 服務 IP 位址會與 `BatchNodeManagement` 服務標記 (或符合您 Batch 帳戶區域的地區變數) 相關聯。

### <a name="honoring-dns"></a>遵守 DNS

請確定您的系統會遵守 Batch 帳戶服務 URL 的 DNS 存留時間 (TTL)。 此外，請確定 batch 服務用戶端和 Batch 服務的其他連線機制不依賴 IP 位址 (或 [建立具有靜態公用 IP 位址的集](create-pool-public-ip.md) 區，如下) 所述。

如果您的要求收到 5xx 層級的 HTTP 回應，而且回應中有「連線：關閉」標頭，則 Batch 服務用戶端應該遵循建議來關閉現有連線、重新解析 Batch 帳戶服務 URL 的 DNS，然後在新的連線上嘗試下列要求。

### <a name="retry-requests-automatically"></a>自動重試要求

請確定您的 Batch 服務用戶端已有適當的重試原則，即使在正常作業期間也可以自動重試您的要求，而不是僅限於任何服務維護期間。 這些重試原則應具有至少 5 分鐘的間隔。 自動重試功能可透過各種 Batch SDK 提供，例如 [.NET RetryPolicyProvider 類別](/dotnet/api/microsoft.azure.batch.retrypolicyprovider)。

### <a name="static-public-ip-addresses"></a>靜態公用 IP 位址

一般而言，Batch 集區中的虛擬機器是透過可在集區存留期內變更的公用 IP 位址來存取。 這可能會讓您難以與資料庫或其他外部服務互動，以限制對特定 IP 位址的存取。 若要確保集區中的公用 IP 位址不會意外變更，您可以使用一組您所控制的靜態公用 IP 位址來建立集區。 如需詳細資訊，請參閱 [使用指定的公用 IP 位址建立 Azure Batch 集](create-pool-public-ip.md)區。

## <a name="batch-node-underlying-dependencies"></a>Batch 節點基礎相依性

設計 Batch 解決方案時，請考慮下列相依性和限制。

### <a name="system-created-resources"></a>系統建立的資源

Azure Batch 會在 VM 上建立和管理一組使用者和群組，而您不應修改這些項目。 如下所示：

#### <a name="windows"></a>Windows

- 名為 **PoolNonAdmin** 的使用者
- 名為 **WATaskCommon** 的使用者群組

#### <a name="linux"></a>Linux

- 名為 **_azbatch** 的使用者

### <a name="file-cleanup"></a>檔案清理

Batch 會在保留時間到期後，主動嘗試清除工作執行所在的工作目錄。 [您必須清除](#manage-task-lifetime)在此目錄外寫入的任何檔案，以避免填滿磁碟空間。

如果您是從 startTask 工作目錄執行 Windows 上的服務，則工作目錄的自動清除功能會遭到封鎖，因為資料夾仍在使用中。 這會導致效能降低。 若要修正此問題，請將該服務的目錄變更為不是由 Batch 管理的個別目錄。
