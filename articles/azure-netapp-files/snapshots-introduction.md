---
title: Azure NetApp Files 快照集的運作方式 |Microsoft Docs
description: 說明 Azure NetApp Files 快照集的運作方式，包括建立快照集的方式、還原快照集的方式，以及如何在跨區域複寫設定中使用快照集。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: beadd250ec4472b894f0f474b1057ad44cf474ed
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133509"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp Files 快照集的運作方式

本文說明 Azure NetApp Files 快照集的運作方式。 Azure NetApp Files 快照技術可提供穩定性、可調整性及更快的復原能力，而不會影響效能。 Azure NetApp Files 快照技術提供資料保護解決方案的基礎，包括單一檔案還原、磁片區還原和複製，以及跨區域複寫。 

如需有關使用磁片區快照集的步驟，請參閱 [使用 Azure NetApp Files 管理快照](azure-netapp-files-manage-snapshots.md)集。 如需跨區域複寫中快照集管理的考慮，請參閱 [使用跨區域複寫的需求和考慮](cross-region-replication-requirements-considerations.md)。

## <a name="what-volume-snapshots-are"></a>哪些磁片區快照集  

Azure NetApp Files 快照集是時間點檔案系統 (磁片區) 映射。 它可以作為理想的線上備份。 您可以使用快照集來 [建立新的磁片](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume)區、 [還原](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)檔案，或 [復原磁碟](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)區。 在儲存在 Azure NetApp Files 磁片區上的特定應用程式資料中，可能需要額外的步驟，以確保應用程式的一致性。  

Underlaying 磁片區虛擬化技術（屬於 Azure NetApp Files 的一部分）的獨特功能，可以讓低額外負荷的快照集。 就像資料庫一樣，這一層會使用磁片上實際資料區塊的指標。 但是，與資料庫不同的是，它不會重寫現有的區塊;它會將更新的資料寫入新的區塊，並變更指標。 Azure NetApp Files 快照集會直接操作區塊指標，建立磁片區的「凍結」、唯讀視圖，讓應用程式不需要特殊程式設計即可存取舊版的檔案和目錄階層。 不會複製實際的資料區塊。 如此一來，快照集就會在建立它們所需的時間內有效率;無論磁片區大小為何，這些都是近乎即時的。 快照集在儲存空間中也很有效率。 它們本身不會使用任何空間，而且只會保留快照集與作用中磁片區之間的差異區塊。 

下圖說明這些概念：  

![顯示快照的重要概念的圖表](../media/azure-netapp-files/snapshot-concepts.png)

在圖表中，快照集是在圖1a 中取得。 在圖1b 中，變更的資料會寫入 *新的區塊* ，並更新指標。 但是快照指標仍然會指向 *先前寫入的區塊*，提供您資料的即時和歷程記錄。 圖1c 中會採用另一個快照集。 您現在可以存取三個資料層代 (即時資料、快照集2和快照集1，以 age) 的順序，而不需要佔用三個完整複本所需的磁片區空間。 

快照集只會取得磁片區中繼資料 (*inode 資料表*) 的複本。 無論磁片區大小、使用的容量或磁片區上的活動層級為何，都只需要幾秒鐘的時間就能建立。 因此，建立 100 TiB 磁片區的快照集會採用相同的 (，) 時間的零次，就是製作 100 GiB 磁片區的快照。 建立快照集之後，資料檔案的變更會反映在作用中的檔案版本，如同往常一般。

同時，從快照集指向的資料區塊仍維持穩定且不可變。 因為 Azure NetApp Files 磁片區的「寫入時重新導向」本質，所以快照集不會產生任何效能額外負荷，而且本身不會耗用任何空間。 您可以在一段時間內，每個磁片區最多儲存255個快照集，這些都可作為唯讀和線上版本的資料來存取，而耗用的容量幾乎是每個快照之間變更的區塊數目。 修改過的區塊會儲存在使用中的磁片區。 快照集中指向的區塊會保留 (為磁片區中的唯讀) ，因此只有在已清除作用中磁片區和快照)  (的所有指標時，才會重新安排用途。 因此，磁片區使用率會隨著時間增加，因為新的資料區塊或 (修改了保留在快照集中) 資料區塊。

 下圖顯示一段時間內磁片區的快照集和使用的空間： 

![顯示一段時間內磁片區快照和已使用空間的圖表](../media/azure-netapp-files/snapshots-used-space-over-time.png)

因為磁片區快照集只會記錄來自最新快照集之後的區塊變更，所以提供下列主要優點：

* 快照集是 ***儲存體有效率** 的 *。   
    快照集會耗用最少量的儲存空間，因為它不會複製整個磁片區的資料區塊。 依順序所建立的兩個快照集，只有在兩者之間的時間間隔中新增或變更的區塊不同。 此區塊-增量行為會限制相關聯的儲存體容量耗用量。 許多替代的快照集執行會使用等於使用中檔案系統的儲存體磁片區，進而提高儲存體容量需求。 根據每日 _block 層級 * 變更費率的應用程式，Azure NetApp Files 快照集會耗用更多或更少的容量，但僅限變更的資料。 針對許多應用程式磁片區，平均每日快照耗用量的範圍僅限1-5% 的已使用磁片區容量，或磁片區（例如 SAP Hana 資料庫磁片區）最高20-30%。 請務必 [監視您的磁片區和快照](azure-netapp-files-metrics.md#volumes) 集容量耗用量（相對於建立和維護的快照集數目）。   

* 快照集是 ***快速建立、複寫、還原或複製** _。   
    無論磁片區大小和活動層級為何，建立、複寫、還原或複製快照集只需要幾秒鐘的時間。 您可以 [視需要](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)建立磁片區快照集。 您也可以使用 [快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) 來指定 Azure NetApp Files 應自動建立快照集的時間，以及要保留多少快照集給磁片區。  您可以藉由使用 [AzAcSnap 工具](azacsnap-introduction.md) 進行 SAP Hana，以應用層協調快照集來達成應用程式一致性。

_ 快照集對磁片區 ***效能** _ 沒有任何影響。   
    由於 underlaying 技術的「寫入時重新導向」本質，儲存或保留 Azure NetApp Files 快照集並不會影響效能，即使是大量的資料活動也一樣。 在大部分的情況下，刪除快照集也不會影響效能。 

_ 快照集會提供 * 擴充 **性** _，因為它們可以經常建立，而且可以保留許多。   
    Azure NetApp Files 磁片區最多支援255個快照集。 儲存大量低影響、經常建立之快照集的能力，可提高成功復原所需的資料版本的可能性。

_ 快照集提供 ***使用者可見度** _ 和檔案復原 _*_能力_*_。   
Azure NetApp Files 快照技術的高效能、可調整性和穩定性，表示它為使用者導向的復原提供理想的線上備份。 您可以針對檔案、目錄或磁片區還原用途，將快照集設為可供使用者存取。 額外的解決方案可讓您將備份複製到離線儲存體，或複寫 [跨區域](cross-region-replication-introduction.md) 以進行保留或故障復原。

## <a name="ways-to-create-snapshots"></a>建立快照集的方式   

您可以使用數種方法來建立和維護快照集：

_ 以手動方式 (隨選) ，方法是使用：   
    * [Azure 入口網站](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)、 [REST API](/rest/api/netapp/snapshots)、 [Azure CLI](/cli/azure/netappfiles/snapshot)或[PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)工具
    * 腳本 (查看 [範例](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts)) 

* 自動，使用：
    * 快照集原則，透過 [Azure 入口網站](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)、 [REST API](/rest/api/netapp/snapshotpolicies)、 [Azure CLI](/cli/azure/netappfiles/snapshot/policy)或 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 工具
    * 應用程式一致快照集工具，例如 [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>如何跨區域複寫磁片區和快照集以進行 DR  

Azure NetApp Files 支援用於嚴重損壞修復的 [跨區域](cross-region-replication-introduction.md) 複寫 (DR) 用途。 Azure NetApp Files 跨區域複寫使用 SnapMirror 技術。 只有變更的區塊會以壓縮、有效率的格式透過網路傳送。 在磁片區之間起始跨區域複寫之後，整個磁片區內容 (也就是，實際儲存的資料區塊) 只會傳送一次。 這項作業稱為 *基準轉移*。 初始傳輸之後，只有已變更的區塊 (在快照集中捕捉) 會被傳輸。 結果是來源磁片區的非同步1:1 複本，包括所有快照集。 此行為會遵循完整和增量的複寫機制。 這項技術可將跨區域複寫所需的資料量降到最低，因此節省資料傳輸成本。 它也會縮短複寫時間。 您可以 (RPO) 取得較小的復原點目標，因為在資料傳輸受限的情況下，可以更頻繁地建立和傳輸更多快照集。 此外，它還需要以主機為基礎的複寫機制，避免虛擬機器和軟體授權成本。

下圖顯示跨區域複寫案例中的快照集流量： 

![顯示跨區域複寫案例中快照集流量的圖表](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>從快照集還原資料的方法  

Azure NetApp Files 快照技術可大幅提升備份的頻率和可靠性。 它會產生效能較低的額外負荷，而且可以安全地在使用中的磁片區上建立。 Azure NetApp Files 快照集可讓您近乎即時、安全且選擇性地進行使用者管理的還原。 本節說明可從 Azure NetApp Files 快照集存取或還原資料的各種方式。

### <a name="restoring-files-or-directories-from-snapshots"></a>從快照集還原檔案或目錄 

如果 [快照路徑可見度](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) 未設定為，則 `hidden` 使用者可以直接存取快照集，以復原意外刪除、損毀或修改資料。 檔案和目錄的安全性會保留在快照集內，而快照集會依設計而處於唯讀狀態。 因此，還原是安全且簡單的。 

下圖顯示快照集的檔案或目錄存取： 

![顯示快照集之檔案或目錄存取的圖表](../media/azure-netapp-files/snapshot-file-directory-access.png)

在圖表中，快照集1只會取用使用中的磁片區與快照集建立期間之間的差異區塊。 但是，當您透過磁片區快照集路徑存取快照集時，資料會 *顯示* 為建立快照集時的完整磁片區容量。 藉由存取快照集資料夾，使用者可以藉由將檔案和目錄複寫到選擇的快照集，來自行還原資料。

同樣地，目標跨區域複寫磁片區中的快照集可以針對 DR 區域中的資料復原存取唯讀。  

下圖顯示跨區域複寫案例中的快照集存取： 

![顯示跨區域複寫中快照集存取的圖表](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

請參閱 [使用用戶端](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) 從快照集還原個別檔案或目錄，以從快照集還原檔案。

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>還原 (複製) 快照集新增至新的磁片區

您可以將 Azure NetApp Files 快照集還原到另一個獨立的磁片區。 無論磁片區大小和耗用的容量為何，這項作業幾乎會立即進行。 新建立的磁片區幾乎立即可供存取，而實際的磁片區和快照集資料區塊則會進行複製。 根據磁片區大小和容量，此程式可能需要相當長的時間，才能刪除父磁片區和快照集。 不過，您可以在初始建立之後存取磁片區，而在背景中進行複製程式。 這項功能可讓您快速建立資料修復或磁片區複製以進行測試和開發。 根據資料複製程式的本質，當還原完成時，儲存體容量集區耗用量會加倍，而且新的磁片區會顯示原始快照集的完整作用中容量。 完成此程式之後，磁片區將會獨立且與原始磁片區解除關聯，而來源磁片區和快照集可以獨立于新磁片區之外管理或移除。

下圖顯示在快照集) 還原 (複製所建立的新磁片區：   

![顯示透過還原快照集建立之新磁片區的圖表](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

您可以在複寫的快照集上執行相同的作業， (DR) 磁片區的嚴重損壞修復。 任何快照集都可以還原至新的磁片區，即使跨區域複寫仍在使用中或進行中。 這項功能可讓您無干擾地在 DR 區域中建立測試和開發環境、將資料放在使用中，而複寫的磁片區則只用于 DR 用途。 此使用案例能讓測試和開發與生產環境隔離，而不會對生產環境造成潛在的影響。  

下圖顯示大量還原 (在進行跨區域複寫時使用 DR 目標磁片區快照集) 複製：  

![顯示使用 DR 目標磁片區快照集進行大量還原的圖表](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

請參閱將快照集還原到磁片區還原作業 [的新磁片](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) 區。

### <a name="restoring-reverting-a-snapshot-in-place"></a>還原 (就地還原快照集) 

在某些情況下，由於新的磁片區會耗用儲存體容量，因此可能不需要或不適合從快照集建立新的磁片區。 若要快速地從資料損毀復原 (例如，發生資料庫損毀或勒索軟體攻擊) ，則在磁片區本身內還原快照集可能比較適當。 您可以使用 Azure NetApp Files 快照集還原功能來完成此操作。 這項功能可讓您快速地將磁片區還原到取得特定快照集時的狀態。 在大部分的情況下，復原磁碟區的速度遠快于將個別檔案從快照集還原到 active 檔案系統，特別是在大型、多 TiB 的磁片區中。 

復原磁碟區快照集幾乎是瞬間完成，而且只需要幾秒鐘的時間才能完成，即使是最大的磁片區也一樣。 使用中的磁片區中繼資料 (*inode 資料表*) 會取代為快照集中繼資料（從快照集建立時開始），進而將磁片區回復至該特定時間點。 不需要複製任何資料區塊，還原就會生效。 因此，比起將快照集還原至新磁片區更能節省空間。 

下圖顯示磁片區還原為較早的快照集：  

![顯示磁片區還原至先前快照集的圖表](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 在建立所選快照集之後所撰寫的作用中檔案系統資料和快照集將會遺失。 快照集還原作業會將目標磁片區中的所有資料取代為所選快照集內的資料。 選取快照集時，您應該注意快照集的內容和建立日期。 您無法復原快照集還原作業。  

若要瞭解如何使用此功能，請參閱 [使用快照集還原復原磁碟](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) 區。

## <a name="how-snapshots-are-deleted"></a>快照集的刪除方式 

快照集會耗用儲存體容量。 因此，它們通常不會無限期地保留。 針對資料保護、保留和復原能力， (在不同時間點建立的快照集數目) 通常會根據 RPO、RTO 和保留期 SLA 需求，在特定期間內保持連線。 不過，較舊的快照集通常不需要保留在儲存體服務上，而且可能需要刪除才能釋放空間。 您可以隨時刪除任何快照集 (不一定會依系統管理員) 的建立順序。 

> [!IMPORTANT]
> 快照集刪除作業無法復原。 您應該保留磁片區的離線複本，以進行資料保護和保留的用途。 

刪除快照集時，所有從該快照集到現有資料區塊的指標都會被移除。 當資料區塊沒有指向 (由現用磁片區或) 磁片區中的其他快照所指向的指標時，資料區塊會傳回磁片區可用空間供日後使用。 因此，移除快照集通常可以釋出磁片區中的容量，而不是從使用中的磁片區刪除資料，因為資料區塊通常會在先前建立的快照集中捕獲。 

下圖顯示從磁片區刪除快照3的儲存體耗用量效果：  

![顯示快照刪除之儲存體耗用量效果的圖表](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

請務必 [監視磁片區和快照的耗用量](azure-netapp-files-metrics.md#volumes) ，並瞭解應用程式、作用中磁片區和快照耗用量的互動方式。 

請參閱 [刪除](azure-netapp-files-manage-snapshots.md#delete-snapshots) 快照集，以瞭解如何管理快照集刪除。 請參閱 [管理快照集原則](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) ，以瞭解如何將此程式自動化。

## <a name="next-steps"></a>後續步驟

* [使用 Azure NetApp Files 管理快照集](azure-netapp-files-manage-snapshots.md)
* [監視磁片區和快照集計量](azure-netapp-files-metrics.md#volumes)
* [針對快照集原則進行疑難排解](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files 的資源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 快照101影片](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [NetApp Snapshot-NetApp 影片庫](https://tv.netapp.com/detail/video/2579133646001/snapshot)



