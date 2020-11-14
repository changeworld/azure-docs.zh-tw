---
title: 使用 Azure 資料箱將資料移轉至 Azure 檔案同步
description: 離線遷移與 Azure 檔案同步相容的大量資料。啟用同步之後，請避免檔案衝突，並保留檔案和資料夾 Acl 和時間戳記。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630221"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>使用 Azure DataBox 將大量資料遷移至 Azure 檔案同步
您可以透過兩種方式將大量資料移轉至 Azure 檔案同步：

* **使用 Azure 檔案同步上傳您的檔案。** 這是最簡單的方法。 將您的檔案在本機移至 Windows Server 2012 R2 或更新版本，然後安裝 Azure 檔案同步代理程式。 設定同步處理之後，您的檔案將會從伺服器上傳。  (我們的客戶目前每隔兩天都遇到 1 TiB 的平均上傳速度。 ) 為了確保您的伺服器不會針對資料中心使用太多頻寬，您可能會想要設定 [頻寬節流排程](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **離線傳輸您的檔案。** 如果您沒有足夠的頻寬，可能無法在合理的時間內將檔案上傳至 Azure。 這是一組完整檔案的初始同步處理。 若要解決這項挑戰，請使用離線大量遷移工具，例如 [Azure 資料箱系列](https://azure.microsoft.com/services/storage/databox)。 

本文說明如何以與 Azure 檔案同步相容的方式離線遷移檔案。請遵循下列指示，以避免發生檔案衝突，以及在啟用同步處理之後，將檔案和資料夾存取控制清單 (Acl) 和時間戳記。

## <a name="migration-tools"></a>移轉工具
我們在本文中描述的程式不僅適用于資料箱，也適用于其他離線遷移工具。 它也適用于直接透過網際網路運作的 AzCopy、Robocopy 或合作夥伴工具和服務等工具。 不過，若要克服最初的上傳挑戰，請遵循本文中的步驟，以與 Azure 檔案同步相容的方式來使用這些工具。

在某些情況下，您必須先從一部 Windows Server 移至另一部 Windows Server，再採用 Azure 檔案同步。 [儲存體遷移服務](/windows-server/storage/storage-migration-service/overview) (SMS) 可協助您解決這種情況。 無論您是否需要遷移至 Azure 檔案同步 (Windows Server 2012R2 和) 更新版本所支援的伺服器作業系統版本，或是因為您購買了新系統以進行 Azure 檔案同步，SMS 有許多功能和優點可協助您順利完成遷移。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具離線傳送資料的優點
以下是使用傳輸工具（例如資料箱進行離線遷移）的主要優點：

- 您不需要透過網路上傳所有檔案。 針對大型命名空間，這項工具可以節省大量的網路頻寬和時間。
- 當您使用 Azure 檔案同步時，無論您使用哪一種傳輸工具 (資料箱、Azure 匯入/匯出服務，以及) ，您的即時伺服器只會上傳將資料移至 Azure 之後所變更的檔案。
- Azure 檔案同步同步處理您的檔案和資料夾 Acl，即使離線大量遷移工具不會傳輸 Acl。
- 資料箱和 Azure 檔案同步不需要停機。 當您使用資料箱將資料傳輸至 Azure 時，您可以有效率地使用網路頻寬並保留檔案精確度。 您也可以只上傳將資料移至 Azure 後變更的檔案，讓您的命名空間保持最新狀態。

## <a name="prerequisites-for-the-offline-data-transfer"></a>離線資料傳輸的必要條件
在您完成離線資料傳輸之前，不應該在您要遷移的伺服器上啟用同步處理。 開始之前要考慮的其他事項如下：

- 如果您打算使用資料箱進行大量遷移：請參閱 [資料箱的部署必要條件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 規劃最終的 Azure 檔案同步拓撲： [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- 選取 Azure 儲存體帳戶，該帳戶將保留您要同步的檔案共用。 確定您的大量移轉發生於相同儲存體帳戶中的暫存預備共用。 大量移轉只能利用位於相同儲存體帳戶中的最終和預備共用來啟用。
- 當您建立與伺服器位置的新同步關聯性時，才可以利用大量移轉。 您無法啟用具有現有同步關聯性的大量移轉。


## <a name="process-for-offline-data-transfer"></a>離線資料傳輸的進程
以下說明如何以與大量遷移工具（例如 Azure 資料箱）相容的方式來設定 Azure 檔案同步：

![顯示如何設定 Azure 檔案同步的圖表](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步驟 | 詳細資料 |
|---|---------------------------------------------------------------------------------------|
| ![步驟 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [訂購資料箱](../../databox/data-box-deploy-ordered.md)。 資料箱系列提供 [數種產品](https://azure.microsoft.com/services/storage/databox/data) 來滿足您的需求。 當您收到資料箱時，請依照其 [檔將您的資料複製](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)到資料箱上的此 UNC 路徑： *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>* 。 在這裡， *共用* 名是暫存共用的名稱。 將資料箱送回 Azure。 |
| ![步驟 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等候您的檔案顯示在您選擇作為暫時暫存共用的 Azure 檔案共用中。 *請勿啟用這些共用的同步處理。* |
| ![步驟 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>針對資料箱為您建立的每個檔案共用，建立新的空白共用。 這個新的共用應該位於與資料箱共用相同的儲存體帳戶中。 [如何建立新的 Azure 檔案共用](storage-how-to-create-file-share.md)。</li><li>在儲存體同步服務中[建立同步處理群組](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 參考空的共用作為雲端端點。 針對每個資料箱檔案共用，重複此步驟。 [設定 Azure 檔案同步](storage-sync-files-deployment-guide.md)。</li></ul> |
| ![步驟 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [將您的即時伺服器目錄新增為伺服器端點](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在此程式中，指定您已將檔案移至 Azure，並參考預備共用。 您可以視需要啟用或停用雲端階層處理。 在您的即時伺服器上建立伺服器端點時，請參考預備共用。 在 [ **新增伺服器端點** ] 分頁的 [ **離線資料傳輸** ] 下，選取 [ **已啟用** ]，然後選取與雲端端點必須位於相同儲存體帳戶中的暫存共用。 在這裡，可用的共用清單會依儲存體帳戶和尚未同步的共用進行篩選。 下表中的螢幕擷取畫面顯示如何在 Azure 入口網站中的伺服器端點建立期間參考資料箱共用。 |
| ![步驟 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 當您在上一個步驟中新增伺服器端點之後，資料就會自動從正確的來源開始流動。 [ [同步共用](#syncing-the-share) ] 區段說明從資料箱共用或從 Windows Server 流向資料流程的時間 |
| |

![Azure 入口網站使用者介面的螢幕擷取畫面，顯示如何在建立新的伺服器端點時啟用離線資料傳輸](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共用
建立伺服器端點之後，將會開始同步處理。 同步處理常式會判斷伺服器上的每個檔案是否也存在於資料箱儲存檔案的預備共用中。 如果檔案存在於該處，同步處理常式會從暫存共用複製檔案，而不是從伺服器上傳檔案。 如果檔案不存在於暫存共用中，或本機伺服器上有較新的版本，同步處理常式就會從本機伺服器上傳檔案。

同步共用時，同步處理會從本機伺服器上的檔案變異合併任何遺失的檔案屬性、許可權或時間戳記，並將它們與資料箱共用的檔案對應專案結合。 這可確保每個檔案和資料夾都能以 Azure 檔案共用中所有可能的檔案精確度來送達。

> [!IMPORTANT]
> 您只可以在建立伺服器端點時啟用大量移轉模式。 建立伺服器端點之後，您就無法將大量遷移的資料從已同步處理的伺服器整合到命名空間中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>檔案和資料夾的 ACL 和時間戳記
Azure 檔案同步可確保即使您使用的大量遷移工具一開始未傳輸 Acl，檔案和資料夾 Acl 也會從即時伺服器同步處理。 因此，暫存共用不需要包含任何檔案和資料夾的 Acl。 當您在建立新的伺服器端點時啟用離線資料移轉功能時，伺服器上的所有檔案 Acl 都會進行同步處理。 新建立和修改的時間戳記也會進行同步處理。

## <a name="shape-of-the-namespace"></a>命名空間的形狀
當您啟用同步處理時，伺服器的內容會決定命名空間的形狀。 如果在資料箱快照集和遷移完成之後，從本機伺服器刪除檔案，這些檔案不會移入即時的同步命名空間。 它們會留在暫存共用中，但不會複製它們。 這是必要的，因為同步處理會根據即時伺服器保留命名空間。 資料箱 *快照* 集只是有效率的檔案複製的預備基礎。 它不是即時命名空間圖形的授權單位。

## <a name="cleaning-up-after-bulk-migration"></a>大量遷移之後進行清除 
當伺服器完成其命名空間的初始同步處理時，資料箱大量遷移的檔案會使用暫存檔案共用。 在 [ **伺服器端點屬性** ] 視窗中 Azure 入口網站的 [ **離線資料傳輸** ] 區段中，狀態會從 [ **進行中** ] 變更為 [ **已完成** ]。 

![[伺服器端點屬性] 分頁的螢幕擷取畫面，其中的 [離線資料傳輸] 的狀態和停用控制項位於](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

現在您可以清除暫存共用以節省成本：

1. 在 [ **伺服器端點屬性** ] 分頁上，當狀態為 [ **已完成** ] 時，選取 [ **停用離線資料傳輸** ]。
2. 請考慮刪除暫存共用以節省成本。 暫存共用可能不包含檔案和資料夾 Acl，所以不太可能很有用。 基於備份時間點的目的，請建立 [同步處理 Azure 檔案共用](storage-snapshots-files.md)的真實快照集。 您可以 [設定 Azure 備份，以依排程拍攝快照集]( ../../backup/backup-afs.md) 。

只有當狀態為 [ **已完成** ] 或因為設定不正確而要取消時，才停用離線資料傳輸模式。 如果您在部署期間停用模式，即使您的預備共用仍然可用，檔案仍會開始從伺服器上傳。

> [!IMPORTANT]
> 停用離線資料傳輸模式後，即使大量遷移的預備共用仍然可用，仍無法再次啟用。

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>在雲端中 Azure 檔案同步和預先植入的檔案

如果您的 Azure 檔案共用中有植入的檔案，其方式與資料箱不同，例如透過 AzCopy、從雲端備份或任何其他方法進行 RoboCopy，您仍然應該遵循本文中所述的 [離線資料傳輸](#process-for-offline-data-transfer) 程式。 您只需要在檔案移至雲端的方法時，忽略資料箱。 不過，請務必確保您仍然遵循將檔案植入 *暫存共用* 的程式，而不是最後一個 Azure 檔案同步連線共用。

> [!WARNING]
> **遵循將檔案植入暫存共用的程式，而不是最後一個** Azure 檔案同步連線共用。 如果不這麼做，則可能會發生檔案衝突 (這兩個檔案版本都將儲存) 以及在即時伺服器上刪除的檔案，如果它們仍然存在於您較舊的植入的檔案中，則可能會回來。 此外，資料夾變更會彼此合併，因此很難在這類錯誤之後分隔命名空間。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)