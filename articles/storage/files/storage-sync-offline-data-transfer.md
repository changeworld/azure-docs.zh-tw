---
title: 使用 Azure 資料箱將資料移轉至 Azure 檔案同步
description: 以與 Azure 檔案同步相容的方式遷移大量資料。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656756"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>使用 Azure DataBox 將大量資料遷移至 Azure 檔案同步
您可以透過兩種方式將大量資料移轉至 Azure 檔案同步：

* **使用 Azure 檔案同步上傳您的檔案。** 這是最簡單的方法。 將檔案從本機移至 Windows Server 2012 R2 或更新版本，並安裝 Azure 檔案同步代理程式。 設定同步處理之後，就會從伺服器上傳您的檔案。 （我們的客戶目前每兩天都會經歷 1 TiB 的平均上傳速度）。為了確保您的伺服器不會使用太多頻寬來處理資料中心，您可能會想要設定[頻寬節流排程](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **離線傳輸您的檔案。** 如果您沒有足夠的頻寬，您可能無法在合理的時間內將檔案上傳至 Azure。 挑戰是整個檔案集的初始同步處理。 若要克服這項挑戰，請使用離線大量遷移工具，例如[Azure 資料箱系列](https://azure.microsoft.com/services/storage/databox)。 

本文說明如何以與 Azure 檔案同步相容的方式，離線遷移檔案。請遵循這些指示來避免檔案衝突，並在啟用同步處理之後，保留您的檔案和資料夾存取控制清單（Acl）和時間戳記。

## <a name="migration-tools"></a>移轉工具
本文中所述的程式不僅適用于資料箱，也適用于其他離線遷移工具。 它也適用于透過網際網路直接運作的 AzCopy、Robocopy 或合作夥伴工具和服務等工具。 不過，若要克服最初的上傳挑戰，請遵循本文中的步驟，以與 Azure 檔案同步相容的方式來使用這些工具。

在某些情況下，您必須先從一部 Windows 伺服器移至另一部 Windows Server，再採用 Azure 檔案同步。[儲存體遷移服務](https://aka.ms/storagemigrationservice)（SMS）可以提供協助。 無論您是否需要遷移至 Azure 檔案同步（Windows Server 2012R2 和更新版本）支援的伺服器作業系統版本，或只需要遷移，因為您要為 Azure 檔案同步購買新系統，SMS 有許多功能和優點，可協助您順利完成遷移。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具離線傳輸資料的優點
以下是使用傳輸工具（如資料箱進行離線遷移）的主要優點：

- 您不需要透過網路上傳所有檔案。 對於大型命名空間，此工具可以節省大量的網路頻寬和時間。
- 當您使用 Azure 檔案同步時，無論您使用哪一種傳輸工具（資料箱、Azure 匯入/匯出服務等等），您的即時伺服器只會上傳在將資料移至 Azure 之後所變更的檔案。
- 即使離線大量遷移工具不會傳輸 Acl，Azure 檔案同步也會同步處理您的檔案和資料夾 Acl。
- 資料箱和 Azure 檔案同步不需要停機。 當您使用資料箱將資料傳輸到 Azure 時，您會有效率地使用網路頻寬並保留檔案精確度。 您也可以只上傳在將資料移至 Azure 之後變更的檔案，讓命名空間保持在最新狀態。

## <a name="prerequisites-for-the-offline-data-transfer"></a>離線資料傳輸的必要條件
在您完成離線資料傳輸之前，不應該在您要遷移的伺服器上啟用同步處理。 開始之前要考慮的其他事項如下所示：

- 如果您打算使用資料箱進行大量遷移：請參閱資料箱的[部署必要條件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 規劃您的最終 Azure 檔案同步拓朴：[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- 選取 Azure 儲存體帳戶，該帳戶將保留您要同步的檔案共用。 確定您的大量移轉發生於相同儲存體帳戶中的暫存預備共用。 大量移轉只能利用位於相同儲存體帳戶中的最終和預備共用來啟用。
- 當您建立與伺服器位置的新同步關聯性時，才可以利用大量移轉。 您無法啟用具有現有同步關聯性的大量移轉。


## <a name="process-for-offline-data-transfer"></a>離線資料傳輸的進程
以下是設定 Azure 檔案同步的方式，與大量遷移工具（例如 Azure 資料箱）相容：

![顯示如何設定 Azure 檔案同步的圖表](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步驟 | 詳細資料 |
|---|---------------------------------------------------------------------------------------|
| ![步驟 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [訂購資料箱](../../databox/data-box-deploy-ordered.md)。 資料箱系列提供[數項產品](https://azure.microsoft.com/services/storage/databox/data)以符合您的需求。 當您收到資料箱時，請遵循其檔，將[您的資料複製](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)到資料箱上的此 UNC 路徑： * \\<DeviceIPAddres\>\<\>\<StorageAccountName_AzFile 共用名稱\>*。 在這裡，「*共用*名」是預備共用的名稱。 將資料箱送回 Azure。 |
| ![步驟 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等到您的檔案顯示在您選擇做為暫時暫存共用的 Azure 檔案共用中。 *請勿啟用同步處理到這些共用。* |
| ![步驟 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>為資料箱為您建立的每個檔案共用建立新的空白共用。 這個新的共用應位於與資料箱共用相同的儲存體帳戶中。 [如何建立新的 Azure 檔案共用](storage-how-to-create-file-share.md)。</li><li>在儲存體同步服務中[建立同步群組](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 參考空的共用作為雲端端點。 針對每個資料箱檔案共用，重複此步驟。 [設定 Azure 檔案同步](storage-sync-files-deployment-guide.md)。</li></ul> |
| ![步驟 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [將您的即時伺服器目錄新增為伺服器端點](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在程式中，指定您要將檔案移至 Azure，並參考預備共用。 您可以視需要啟用或停用雲端階層處理。 在您的即時伺服器上建立伺服器端點時，請參考預備共用。 在 [**新增伺服器端點**] 分頁的 [**離線資料傳輸**] 底下，選取 [**已啟用**]，然後選取與雲端端點必須位於相同儲存體帳戶中的預備共用。 在這裡，可用的共用清單會依儲存體帳戶和尚未同步的共用進行篩選。 下表的螢幕擷取畫面顯示在 Azure 入口網站中建立伺服器端點期間，如何參考 DataBox 共用。 |
| ![步驟 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 在上一個步驟中加入伺服器端點之後，資料就會開始自動從正確的來源流動。 [同步共用](#syncing-the-share)一節說明從 DataBox 共用或從 Windows Server 流向資料的時機 |
| |

![Azure 入口網站使用者介面的螢幕擷取畫面，顯示如何在建立新的伺服器端點時啟用離線資料傳輸](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共用
建立伺服器端點之後，同步處理將會啟動。 同步處理常式會判斷伺服器上的每個檔案是否也存在於資料箱儲存檔案的暫存共用中。 如果檔案存在於該處，同步處理常式會從暫存共用複製檔案，而不是從伺服器上傳該檔案。 如果檔案不存在於暫存共用中，或本機伺服器上有較新的版本可用，則同步處理常式會從本機伺服器上傳檔案。

同步共用時，同步會從本機伺服器上的檔案變體中合併任何遺失的檔案屬性、許可權或時間戳記，並將其與 DataBox 共用的檔案對應專案結合。 這可確保每個檔案和資料夾都會抵達 Azure 檔案共用中的所有可能檔案精確度。

> [!IMPORTANT]
> 只有在建立伺服器端點時，才可以啟用大量移轉模式。 建立伺服器端點之後，您就無法將大量遷移的資料從已經同步處理的伺服器整合到命名空間。

## <a name="acls-and-timestamps-on-files-and-folders"></a>檔案和資料夾的 ACL 和時間戳記
Azure 檔案同步可確保即使您使用的大量遷移工具最初並未傳輸 Acl，檔案和資料夾 Acl 還是會從即時伺服器進行同步處理。 因此，暫存共用不需要包含檔案和資料夾的任何 Acl。 當您在建立新的伺服器端點時啟用離線資料移轉功能時，所有的檔案 Acl 都會在伺服器上同步處理。 新建立和修改的時間戳記也會一併同步處理。

## <a name="shape-of-the-namespace"></a>命名空間的形狀
當您啟用同步處理時，伺服器的內容會決定命名空間的形狀。 如果資料箱快照集和遷移完成後，從本機伺服器刪除檔案，這些檔案不會移至即時的同步命名空間。 它們會留在預備共用中，但不會複製。 這是必要的，因為同步處理會根據即時伺服器保留命名空間。 資料箱*快照*集只是用於有效率檔案複製的預備基礎。 這不是即時命名空間的形狀的授權單位。

## <a name="cleaning-up-after-bulk-migration"></a>大量遷移之後的清理 
當伺服器完成其命名空間的初始同步處理時，資料箱大量遷移的檔案會使用暫存檔案共用。 在 Azure 入口網站的 [**伺服器端點屬性**] 分頁上，[**離線資料傳輸**] 區段中的狀態會從 [**進行中**] 變更為 [**已完成**]。 

![[伺服器端點屬性] 分頁的螢幕擷取畫面，其中的 [狀態] 和 [停用] 控制項皆位於](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

現在您可以清除預備共用以節省成本：

1. 在 [**伺服器端點屬性**] 分頁上，當狀態為 [**已完成**] 時，選取 [**停用離線資料傳輸**]。
2. 請考慮刪除暫存共用以節省成本。 預備共用可能不包含檔案和資料夾 Acl，因此不太可能會很有用。 針對備份時點的目的，請建立[同步處理 Azure 檔案共用](storage-snapshots-files.md)的實際快照集。 您可以[設定 Azure 備份來依排程拍攝快照集]( ../../backup/backup-afs.md)。

只有在狀態為 [**已完成**] 時，或當您想要取消設定錯誤時，才停用離線資料傳輸模式。 如果您在部署期間停用模式，即使您的預備共用仍然可用，檔案也會從伺服器開始上傳。

> [!IMPORTANT]
> 停用離線資料傳輸模式之後，即使大量遷移的暫存共用仍然可用，您也無法再次啟用它。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
