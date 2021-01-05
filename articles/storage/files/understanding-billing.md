---
title: 瞭解 Azure 檔案儲存體計費 |Microsoft Docs
description: 瞭解如何解讀 Azure 檔案共用的已布建和隨用隨付計費模型。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/1/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 880ec90ce1cf0efffce0cfd6800bdbaed23f8dd0
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831460"
---
# <a name="understanding-azure-files-billing"></a>瞭解 Azure 檔案儲存體計費
Azure 檔案儲存體提供兩個不同的計費模型：已布建和隨用隨付。 布建的模型僅適用于 premium 檔案共用，也就是部署在 **FileStorage** 儲存體帳戶類型中的檔案共用。 隨用隨付模型僅適用于標準檔案共用，也就是在 **一般用途第2版 (GPv2)** 儲存體帳戶類型中部署的檔案共用。 本文說明這兩種模型的運作方式，以協助您瞭解每月的 Azure 檔案儲存體帳單。

Azure 檔案儲存體的目前定價可在 [ [Azure 檔案儲存體定價] 頁面](https://azure.microsoft.com/pricing/details/storage/files/)上找到。

## <a name="provisioned-billing"></a>已布建帳單
Azure 檔案儲存體使用 premium 檔案共用的布建模型。 在已布建的商務模型中，您會主動指定 Azure 檔案儲存體服務的儲存體需求，而不是根據您使用的內容計費。 這類似于在內部部署環境中購買硬體，因為當您布建具有特定儲存體數量的 Azure 檔案共用時，無論您是否使用該儲存體，您都必須支付該儲存體的費用，就像您在開始使用空間時，不會開始支付實體媒體內部部署的成本。 不同于在內部部署環境中購買實體媒體，布建的檔案共用可以根據您的儲存體和 IO 效能特性，動態地相應增加或減少。

當您布建 premium 檔案共用時，您會指定工作負載所需的 Gib 數目。 您布建的每個 GiB 都可讓您以固定的比率提供額外的 IOPS 和輸送量。 除了您保證的基準 IOPS，每個 premium 檔案共用都能以最佳的方式來支援高載。 IOPS 和輸送量的公式如下：

- 基準 IOPS = 400 + 1 * 布建 GiB。  (最高 100000 IOPS) 。
- 高載限制 = 最大 (4000，3 * 基準 IOPS) 。
- 輸出速率 = 60 MiB/秒 + 0.06 * 布建的 GiB。
- 輸入速率 = 40 MiB/秒 + 0.04 * 布建的 GiB。

您可以隨時增加檔案共用的布建大小，但可在上一次增加之後24小時後減少。 等候24小時之後，如果沒有增加配額，您可以依需要減少共用配額的次數，直到再次增加為止。 IOPS/輸送量調整變更將在布建的大小變更後的幾分鐘內生效。

您可以將布建的共用大小減少到您使用的 GiB 下。 如果您這樣做，將不會遺失資料，但您仍需支付所使用的大小，並可獲得所布建共用的效能 (基準 IOPS、輸送量和高載 IOPS) ，而不是使用的大小。

下表說明這些 homebrew 公式針對布建的共用大小的一些範例：

|容量 (GiB)  | 基準 IOPS | 高載 IOPS | 輸出 (MiB/秒)  | 輸入 (MiB/秒)  |
|---------|---------|---------|---------|---------|
|100         | 500     | 最高 4,000     | 66   | 44   |
|500         | 900     | 最高 4,000  | 90   | 60   |
|1,024       | 1424   | 最高 4,000   | 122   | 81   |
|5,120       | 5520   | 最高15360  | 368   | 245   |
|10240      | 10640  | 最高30720  | 675   | 450   |
|33792      | 34192  | 最高100000 | 2088 | 1392   |
|51200      | 51600  | 最高100000 | 3,132 | 2088   |
|102400     | 100,000 | 最高100000 | 6204 | 4136   |

有效的檔案共用效能受制于電腦網路限制、可用的網路頻寬、IO 大小、平行處理，還有許多其他因素。 例如，根據具有8個 KiB 讀取/寫入 IO 大小的內部測試，不啟用 SMB 多重通道的單一 Windows 虛擬機器（ *標準 F16s_v2*）會透過 smb 連線至 premium 檔案共用，以達到20K 的讀取 IOPS 和15K 寫入 iops。 使用 512 MiB 讀取/寫入 IO 大小時，相同的 VM 可以達到 1.1 GiB/s 輸出和 370 MiB/s 輸入輸送量。 如果 premium 共用上已啟用 SMB 多重通道，則相同的用戶端最多可達到 \~ 3 倍的效能。 若要達到最大效能調整，請 [啟用 SMB 多重](storage-files-enable-smb-multichannel.md) 通道，並將負載分散到多個 vm。 請參閱 [SMB 多重通道效能](storage-files-smb-multichannel-performance.md) 和 [疑難排解指南](storage-troubleshooting-files-performance.md) ，以瞭解一些常見的效能問題和因應措施。

### <a name="bursting"></a>爆破
如果您的工作負載需要額外的效能來滿足尖峰需求，則您的共用可以使用高載點數來進行以上的共用基準 IOPS 限制，以提供符合需求所需的共用效能。 Premium 檔案共用可將其 IOPS 高載至4000或最多三個因數（以較高的值為准）。 高載是自動化的，而且會根據信用系統來運作。 高載會以最佳的方式運作，而高載限制不保證，檔案共用 *最多可以高達* 60 分鐘的最大持續時間上限。

當檔案共用的流量低於基準 IOPS 時，會在高載 bucket 中累積點數。 例如，100 GiB 共用具有500基準 IOPS。 如果共用上的實際流量是特定1秒間隔的 100 IOPS，則400未使用的 IOPS 會被計入高載值區。 同樣地，閒置的 1 TiB 共用，會在 1424 IOPS 上累算高載點數。 之後將會在作業超過基準 IOPS 時使用這些點數。

每當共用超過基準 IOPS，且在高載值區中有點數時，它會以允許的尖峰高載速率來高載。 只要剩餘信用額度（最多可達60分鐘的持續時間），共用可以持續高載，但這是以累積的高載點數數量為基礎。 除了基準 IOPS 以外的每個 IO 都會耗用一項點數，一旦取用所有信用額度之後，該共用會回到基準 IOPS。

共用信用額度有三種狀態：

- 當檔案共用使用小於基準 IOPS 時產生。
- 當檔案共用使用超過基準 IOPS，且處於高載模式時拒絕。
- 常數，當檔案共用完全使用基準 IOPS 時，就不會有任何已累積或未使用的點數。

新檔案共用的開頭為其高載值區中的完整點數。 如果共用 IOPS 低於基準 IOPS （因為伺服器進行節流），將不會產生高載信用額度。

## <a name="pay-as-you-go-billing"></a>隨用隨付計費
Azure 檔案儲存體使用標準檔案共用的隨用隨付商務模型。 在隨用隨付商務模型中，您支付的金額取決於實際使用的數量，而不是根據布建的金額。 概括而言，您需支付儲存在磁片上的資料量費用，然後根據您的資料使用方式來支付一組額外的交易。 隨用隨付模型可符合成本效益，因為您不需要過度布建，以考慮未來的成長或效能需求，或取消布建您的工作負載是否會隨著時間而改變。 另一方面，隨用隨付計費模型是由使用者耗用量所驅動，因此隨用隨付的計費模型也可能很難規劃為預算流程的一部分。

### <a name="differences-in-standard-tiers"></a>標準層的差異
當您建立標準檔案共用時，您會在交易優化、經常性存取和非經常性存取層之間進行選擇。 這三層全都儲存在完全相同的標準儲存體硬體上。 這三個層級的主要差異在於其待用資料儲存體價格 (較少存取層的價格較低) 和交易價格 (較少存取層的價格較高)。 這表示：

- 如其名稱所示，交易最佳化可將高交易工作負載的價格最佳化。 交易最佳化具有最高的待用資料儲存體價格，但交易價格最低。
- 經常性存取適用於不牽涉大量交易的作用中工作負載，而且具有些微較低的待用資料儲存體價格，但相較於交易最佳化，交易價格稍高。 將它視為交易優化和非經常性存取層之間的中間。
- 針對沒有太多活動的工作負載提供最低的價格，並提供最低的資料待用儲存體價格，但最高的交易價格。

如果您將不常存取的工作負載放在交易優化層中，在您對共用進行交易的每個月中，幾乎不需要支付任何費用，但您會支付大量的資料儲存成本。 如果您要將這個相同的共用移至非經常性存取層，您仍然可以對交易成本支付幾乎沒有的費用，因為您對此工作負載的交易不常發生，但非經常性存取層有更便宜的資料儲存體價格。 為您的使用案例選取適當的層級，可讓您大幅降低成本。 為您的使用案例選取適當的層級，可讓您大幅降低成本。

同樣地，如果您將高度存取的工作負載放在非經常性存取層，您會在交易成本中支付更多費用，但對於資料儲存成本則較少。 這可能會導致一種情況，就是交易價格增加的成本超過減少資料儲存體價格的節省量，讓您在非交易優化的情況下支付更多費用。 雖然經常性存取層是最符合成本效益的層級，但非經常性存取層的成本會比交易優化更昂貴，但某些使用層級可能會是最符合成本效益的層級。

您的工作負載和活動層級將會為標準檔案共用決定最具成本效益的層。 在實務上，挑選最符合成本效益的層的最佳方式，就是查看共用的實際資源耗用量 (儲存的資料、寫入交易等 ) 。

### <a name="what-are-transactions"></a>什麼是交易？
交易是針對 Azure 檔案儲存體的作業或要求，以上傳、下載或以其他方式操作檔案共用的內容。 針對檔案共用所採取的每個動作都會轉譯成一或多個交易，而在使用隨用隨付計費模型的標準共用上，會轉譯為交易成本。

共有五個基本交易類別： write、list、read、other 和 delete。 透過 REST API 或 SMB 完成的所有作業都會貯存成下列4種類別的其中一種，如下所示：

| 作業類型 | 寫入交易 | 列出交易 | 讀取交易 | 其他交易 | 刪除交易 |
|-|-|-|-|-|-|
| 管理作業 | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| 資料作業 | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> NFS 4.1 僅適用于使用已布建計費模型的 premium 檔案共用，交易不會影響 premium 檔案共用的計費。

## <a name="see-also"></a>請參閱
- [Azure 檔案儲存體定價] 頁面](https://azure.microsoft.com/pricing/details/storage/files/)。
- [規劃 Azure 檔案儲存體部署](./storage-files-planning.md) 以及 [規劃 Azure 檔案同步部署](./storage-sync-files-planning.md)。
- [建立檔案共用](./storage-how-to-create-file-share.md) 並 [部署 Azure 檔案同步](./storage-sync-files-deployment-guide.md)。