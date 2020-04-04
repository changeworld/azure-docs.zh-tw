---
title: 將資料移至 Azure 檔與 Azure 資料框同步
description: 以與 Azure 檔同步相容的方式遷移批量數據。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656756"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>使用 Azure DataBox 將大量資料遷移至 Azure 檔案同步
您可以透過兩種方式將批次資料移到 Azure 檔同步:

* **使用 Azure 檔同步上傳檔。** 這是最簡單的方法。 將檔案本地移動到 Windows Server 2012 R2 或更高版本,然後安裝 Azure 檔同步代理。 設置同步後,您的檔將從伺服器上載。 (我們的客戶目前平均上傳速度約為每兩天 1 TiB。為了確保伺服器不會為資料中心使用過多的頻寬,您可能需要設定[頻寬限制計畫](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **離線傳輸檔。** 如果沒有足夠的頻寬,可能無法在合理的時間內將檔上載到 Azure。 挑戰在於整個檔集的初始同步。 要克服此挑戰,請使用離線批次移至[Azure 資料盒列](https://azure.microsoft.com/services/storage/databox)。 

本文介紹如何以與 Azure 檔同步相容的方式脫機遷移檔。請按照這些說明操作,以避免文件衝突,並在啟用同步後保留檔和資料夾存取控制清單 (ACL) 和時間戳。

## <a name="migration-tools"></a>移轉工具
本文中介紹的過程不僅適用於數據框,也適用於其他脫機遷移工具。 它還適用於阿茲貝貝、Robocopy 等工具,或直接在互聯網上工作的合作夥伴工具和服務。 但是,為了克服初始上傳難題,請按照本文中的步驟以與 Azure 檔同步相容的方式使用這些工具。

在某些情況下,在採用 Azure 檔同步之前,需要從一個 Windows 伺服器移動到另一個 Windows 伺服器。[存儲遷移服務](https://aka.ms/storagemigrationservice)(SMS) 可以提供説明。 無論您是需要遷移到 Azure 檔同步支援的伺服器作業系統版本(Windows Server 2012R2 及更高版本),還是由於為 Azure 檔同步購買新系統而只需遷移,SMS 都具有許多功能和優點,可説明順利完成遷移。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具離線傳輸資料的好處
以下是使用傳輸工具(如資料框)進行離線移的主要好處:

- 您不必透過網路上載所有檔。 對於大型命名空間,此工具可以節省大量的網路頻寬和時間。
- 使用 Azure 檔同步時,無論您使用哪種傳輸工具(資料框、Azure 匯入/匯出服務等),即時伺服器僅上傳將資料移至 Azure 後更改的檔案。
- Azure 檔案同步同步檔案和資料夾 ACL,即使離線批量遷移工具不傳輸 ACL 也是如此。
- 數據框和 Azure 檔同步不需要停機。 使用數據框將數據傳輸到 Azure 時,可以有效地使用網路頻寬並保留檔保真度。 通過僅上傳將數據移動到 Azure 後更改的檔,還可以使命名空間保持最新。

## <a name="prerequisites-for-the-offline-data-transfer"></a>離線資料傳輸的先決條件
在完成離線數據傳輸之前,不應在要遷移的伺服器上啟用同步。 開始之前需要考慮的其他事項如下:

- 如果您計劃使用資料框進行批次移轉: 查看[資料框的部署先決條件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 規劃最終 Azure 檔案同步拓撲:[規劃 Azure 檔同步部署](storage-sync-files-planning.md)
- 選取 Azure 儲存體帳戶，該帳戶將保留您要同步的檔案共用。 確定您的大量移轉發生於相同儲存體帳戶中的暫存預備共用。 大量移轉只能利用位於相同儲存體帳戶中的最終和預備共用來啟用。
- 當您建立與伺服器位置的新同步關聯性時，才可以利用大量移轉。 您無法啟用具有現有同步關聯性的大量移轉。


## <a name="process-for-offline-data-transfer"></a>離線資料傳輸流程
下面瞭解如何以與批次移轉工具(如 Azure 資料框)相容的方式設定 Azure 檔同步:

![展示如何設定 Azure 檔案同步的圖表](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步驟 | 詳細資料 |
|---|---------------------------------------------------------------------------------------|
| ![步驟 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [訂購資料箱](../../databox/data-box-deploy-ordered.md)。 數據盒系列提供[多種產品](https://azure.microsoft.com/services/storage/databox/data),以滿足您的需求。 收到資料盒時,請按照此[文件複製到](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)資料框中的這個 UNC 路徑*\\\>\<:<设备\>\<ipAddreStorageAccountName_AzFile\>共享名稱*。 此處 *,ShareName*是暫存共用的名稱。 將資料箱送回 Azure。 |
| ![步驟 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等待檔案顯示在您選擇的暫存分享的 Azure 檔案共享中。 *不要啟用同步到這些共用。* |
| ![步驟 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>為資料框為您創建的每個文件共用創建新的空共用。 此新共用應與數據盒共用位於同一存儲帳戶中。 [如何建立新的 Azure 檔案共用](storage-how-to-create-file-share.md)。</li><li>[在儲存同步服務中建立同步群組](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 將空共享作為雲終結點引用。 針對每個資料箱檔案共用，重複此步驟。 [設定 Azure 檔案同步](storage-sync-files-deployment-guide.md)。</li></ul> |
| ![步驟 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [將您的即時伺服器目錄新增為伺服器端點](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在此過程中,指定將文件移動到 Azure,並引用暫存共用。 您可以根據需要啟用或禁用雲分層。 在即時伺服器上創建伺服器終結點時,引用暫存共用。 在 **「添加伺服器終結點**」邊欄選項卡上,在 **「離線資料傳輸****」下**選擇啟用,然後選擇必須位於與雲端的記憶體分享。 在這裡,可用共用清單由存儲帳戶和尚未同步的共用進行篩選。 下表下面的螢幕截圖演示如何在 Azure 門戶中的伺服器終結點創建期間引用 DataBox 共用。 |
| ![步驟 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 在上一步驟中添加伺服器終結點后,數據將自動從正確的源流動。 ["同步分享](#syncing-the-share)"部分將解釋資料從 DataBox 共用或 Windows 伺服器串流的資料時間 |
| |

![Azure 門戶使用者介面的螢幕截圖,示範如何在建立新伺服器終結點時啟用離線資料傳輸](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共用
創建伺服器終結點后,將啟動同步。 同步過程確定伺服器上的每個檔是否也存在於數據盒存放檔的暫存共享中。 如果檔存在,同步進程將從暫存共用複製該檔,而不是從伺服器上載該檔。 如果檔不存在在暫存共用中,或者如果本地伺服器上有較新版本可用,同步過程將從本地伺服器上載該檔。

同步共用時,同步將從本地伺服器上的檔變體合併任何丟失的文件屬性、許可權或時間戳,將它們與 DataBox 共用中的檔案對應方合併。 這可確保每個檔和資料夾到達時,Azure 文件共用中的所有可能的檔保真度都可用。

> [!IMPORTANT]
> 只能在創建伺服器終結點時啟用批量遷移模式。 建立伺服器終結點后,無法將來自已同步伺服器的大宗遷移數據集成到命名空間中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>檔案和資料夾的 ACL 和時間戳記
Azure 檔案同步可確保檔案和資料夾 ACL 從即時伺服器同步,即使您使用的批量移轉工具最初未傳輸 ACL 也是如此。 因此,暫存共用不需要包含檔和資料夾的任何 ACL。 創建新伺服器終結點時啟用離線資料遷移功能時,所有檔 ACL 都在伺服器上同步。 新創建和修改的時間戳也會同步。

## <a name="shape-of-the-namespace"></a>命名空間的形狀
啟用同步時,伺服器的內容將確定命名空間的形狀。 如果在 Data Box 快照和遷移完成後從本地伺服器中刪除檔,這些檔不會移動到即時同步命名空間中。 它們保留在暫存共用中,但不會複製它們。 這是必需的,因為同步會根據即時伺服器保留命名空間。 數據框*快照*只是高效檔副本的暫存地。 它不是即時命名空間形狀的權威。

## <a name="cleaning-up-after-bulk-migration"></a>批次移至移至後 
當伺服器完成命名空間的初始同步時,數據箱大遷移檔將使用暫存檔共用。 在 Azure 門戶中的 **「伺服器終結點內容**」邊欄選項卡上,在 **「離線資料傳輸**」部分中,狀態從 **「正在進行」** 更改為 **「已完成**」 。 

![伺服器終結點屬性邊欄選項卡的螢幕截圖,其中狀態和禁用離線資料傳輸控制項的位置](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

現在,您可以清理暫存共享以節省成本:

1. 在 **「伺服器終結點屬性**」 「邊欄選項卡上,當狀態**完成**時,選擇**關閉離線資料傳輸**。
2. 請考慮刪除暫存共用以節省成本。 暫存共用可能不包含檔和資料夾 ACL,因此不太可能有用。 出於備份時間點的目的,請建立同步 Azure[檔案分享的真實快照](storage-snapshots-files.md)。 可以[設定 Azure 備份以按計畫拍攝快照]( ../../backup/backup-afs.md)。

僅當狀態**已完成**或由於配置錯誤而要取消時,才禁用離線數據傳輸模式。 如果在部署期間禁用該模式,檔將開始從伺服器上載,即使您的過渡共用仍然可用。

> [!IMPORTANT]
> 禁用離線數據傳輸模式後,即使批量遷移的暫存共用仍然可用,也不能再次啟用它。

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
