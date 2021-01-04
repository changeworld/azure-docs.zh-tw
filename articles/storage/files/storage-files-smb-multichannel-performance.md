---
title: SMB 多重通道效能-Azure 檔案儲存體
description: 瞭解 SMB 多重通道效能。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 4f4cd8189c9166ee08c1e4ccd800a1202d3b5893
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724811"
---
# <a name="smb-multichannel-performance"></a>SMB 多重通道效能

Azure 檔案儲存體 SMB 多重通道 (preview) 可讓 SMB 3.x 用戶端在 FileStorage 帳戶中建立多個與 premium 檔案共用的網路連線。 SMB 3.0 通訊協定引進了 Windows Server 2012 和 Windows 8 用戶端的 SMB 多重通道功能。 因此，任何支援 SMB 多重通道 Azure 檔案儲存體 SMB 3.x 用戶端都可以利用其 Azure premium 檔案共用的功能。 在儲存體帳戶上啟用 SMB 多重通道不會產生額外費用。

## <a name="benefits"></a>優點

Azure 檔案儲存體 SMB 多重通道可讓用戶端使用多個網路連線，以提供更高的效能，同時降低擁有權的成本。 提高效能是透過多個 Nic 的頻寬匯總來達成，並利用接收端調整 (RSS) 支援 Nic 將 IO 負載分散到多個 Cpu。

- **提高輸送量**：多個連線可讓資料以平行方式傳輸至多個路徑，進而大幅提升使用較大檔案大小（較大的 IO 大小）的工作負載，並需要單一 VM 或一組較小的 vm 的高輸送量。 其中一些工作負載包括媒體和娛樂的內容建立或轉碼、genomics 和金融服務風險分析。
- **較高的 IOPS**： NIC RSS 功能可在多個具有多個連接的 cpu 之間進行有效的負載分配。 這有助於達到更高的 IOPS 規模和有效的 VM Cpu 使用率。 這適用于具有少量 IO 大小的工作負載，例如資料庫應用程式。
- **網路容錯**：多個連接可減輕中斷的風險，因為用戶端不再依賴個別連接。
- **自動** 設定：當在用戶端和儲存體帳戶上啟用 SMB 多重通道時，它允許動態探索現有的連線，而且可以視需要建立額外的連接路徑。
- **成本優化**：工作負載可在連線至 premium 共用時，從單一 VM 或一組小型 vm 達到更高的規模。 這可以藉由減少執行和管理工作負載所需的 Vm 數目，來降低擁有權總成本。

若要深入瞭解 SMB 多重通道，請參閱 [Windows 檔](/azure-stack/hci/manage/manage-smb-multichannel)集。

這項功能可為多執行緒應用程式提供更佳的效能優勢，但通常不會協助單一執行緒應用程式。 如需詳細資訊，請參閱 [效能比較](#performance-comparison) 一節。

## <a name="limitations"></a>限制

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>區域可用性

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>設定

只有在用戶端 (您的用戶端) 和服務端 (您的 Azure 儲存體帳戶) 時，SMB 多重通道才能運作。

在 Windows 用戶端上，預設會啟用 SMB 多重通道。 您可以執行下列 PowerShell 命令來確認您的設定： 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
在您的 Azure 儲存體帳戶上，您將需要啟用 SMB 多重通道。 請參閱 [啟用 SMB 多重通道 (預覽) ](storage-files-enable-smb-multichannel.md)。

### <a name="disable-smb-multichannel"></a>停用 SMB 多重通道
在大部分的情況下，特別是多執行緒的工作負載，用戶端應該會看到使用 SMB 多重通道的改進效能。 不過，在某些特定案例中，例如單一執行緒的工作負載或用於測試用途，您可能會想要停用 SMB 多重通道。 如需詳細資料，請參閱 [效能比較](#performance-comparison) 。

## <a name="verify-smb-multichannel-is-configured-correctly"></a>確認已正確設定 SMB 多重通道

1. 建立 premium 檔案共用或使用現有的檔案共用。
1. 確定您的用戶端支援 SMB 多重通道 (一或多個網路介面卡已啟用接收端調整) 。 如需詳細資訊，請參閱 [Windows 檔](/azure-stack/hci/manage/manage-smb-multichannel) 。
1. 將檔案共用掛接至您的用戶端。
1. 使用您的應用程式產生負載。
    複製工具（例如 robocopy/MT）或任何效能工具（例如 Diskspd 至讀取/寫入檔案）可能會產生負載。
1. 以系統管理員身分開啟 PowerShell，並使用下列命令： `Get-SmbMultichannelConnection |fl`
1. 尋找 **MaxChannels** 和 **CurrentChannels** 屬性

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection 結果的螢幕擷取畫面。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>表現比較

讀取/寫入工作負載模式有兩種類別：單一執行緒和多執行緒。 大部分的工作負載都會使用多個檔案，但可能會有特定的使用案例，其中的工作負載可搭配共用中的單一檔案運作。 本節涵蓋不同的使用案例和每個使用案例的效能影響。 一般而言，大部分的工作負載都是多執行緒，並將工作負載分散在多個檔案中，因此它們應該會發現 SMB 多重通道的顯著效能改善。

- **多執行緒/多個** 檔案：視工作負載模式而定，您應該會看到透過多個通道的讀取和寫入 io 效能大幅改善。 效能提升會隨著 IOPS、輸送量和延遲而從2x 到4x 之間的任何位置進行。 針對此類別，應啟用 SMB 多重通道以獲得最佳效能。
- **多執行緒/單一** 檔案：針對此類別中的大部分使用案例，工作負載會因為啟用 SMB 多重通道而受益，特別是當工作負載的平均 IO 大小 > ~ 16k 時。 從 SMB 多重通道獲益的幾個範例案例是備份或復原單一大型檔案。 您可能想要停用 SMB 多重通道的例外狀況是您的工作負載是否為較小的 IOs。 在這種情況下，您可能會發現效能稍微下降 ~ 10%。 視使用案例而定，請考慮將負載分散到多個檔案，或停用此功能。 如需詳細 [資訊，請參閱設定一節](#configuration) 。
- **單一執行緒/多個檔案或單一** 檔案：適用于大部分的單一執行緒工作負載，因為缺少平行處理原則，所以會有最小的效能優勢，但如果已啟用 SMB 多重通道，則效能會稍微降低 ~ 10%。 在此情況下，很適合用來停用 SMB 多重通道，但有一個例外狀況。 如果單一執行緒的工作負載可將負載分散到多個檔案，並使用平均較大的 IO 大小 ( # A0 ~ 16k) ，則 SMB 多重通道應該會有些許的效能優勢。

### <a name="performance-test-configuration"></a>效能測試設定

針對本文中的圖表，使用了下列設定：單一標準 D32s v3 VM，並已啟用單一 RSS 的 NIC 與四個通道。 負載是使用 diskspd.exe、多重執行緒（IO 深度10）和隨機 io （具有各種 IO 大小）產生。

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps (GiB 中的快取大小)  | 最大取消快取的磁碟輸送量：IOPS/MBps | 最大 NIC|預期的網路頻寬 (Mbps)  |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)     | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="顯示效能測試設定的螢幕擷取畫面。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>多重-具有 SMB 多重通道的執行緒/多個檔案

針對10個具有各種 IO 大小的檔案所產生的負載。 擴大測試結果會在啟用 SMB 多重通道的 IOPS 和輸送量測試結果中顯示顯著的改善。 下列圖表描述結果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="效能圖表" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="輸送量效能圖表。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 在單一 NIC 上，針對讀取，觀察到3倍的效能，並在 IOPS 和輸送量方面獲得3倍的寫入。
- 即使是單一 NIC 和四個通道限制，SMB 多重通道仍允許 IOPS 和輸送量達到 VM 限制。
- 因為輸出 (或讀取至儲存體) 不會計量，所以讀取輸送量能夠超過 VM 發佈的限制 16000 Mbps (2 GiB/s) 。 >2.7 GiB/s 的測試完成。 輸入 (或寫入儲存體) 仍受限於 VM 限制。
- 將負載分散到多個檔案，以進行大幅改進。

這項測試中使用的範例命令為： 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>具有 SMB 多重執行緒的多執行緒/單一檔案工作負載

負載是針對單一 128 GiB 檔產生的。 在啟用 SMB 多重執行緒的情況下，使用多執行緒/單一檔案的擴大測試會顯示大部分情況下的增強功能。 下列圖表描述結果：

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS 效能圖表。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="單一檔案輸送量效能的圖表。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 在具有較大平均 IO 大小 ( # A0 ~ 16k) 的單一 NIC 上，讀取和寫入都有顯著的改善。
- 針對較小的 IO 大小，當啟用 SMB 多重通道時，效能上的效能會稍微影響 ~ 10%。 您可以藉由將負載分散到多個檔案，或停用此功能，來減輕這種情況。
- 效能仍然受單一檔案  [限制所限制](storage-files-scale-targets.md#file-level-limits)。

## <a name="optimizing-performance"></a>最佳化效能

下列秘訣可協助您優化效能：

- 確定您的儲存體帳戶和用戶端都在相同的 Azure 區域中，以降低網路延遲。
- 使用多執行緒應用程式，並將負載分散到多個檔案。
- SMB 多重通道的效能優點會隨著散發載入的檔案數目而增加。
- Premium 共用效能會依布建的共用大小（ (IOPS/輸出/輸入) 和單一檔案限制）進行系結。 如需詳細資訊，請參閱 [瞭解 premium 檔案共用的](understanding-billing.md#provisioned-billing)布建。
- 單一 VM 用戶端的最大效能仍系結至 VM 限制。 例如， [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) 可支援 16000 MBps 的最大頻寬 (或 2GBps) ，從 VM 到儲存體) 的輸出 (寫入儲存體 (會進行計量，而輸入) 儲存體讀取則否。 檔案共用效能受制于電腦網路限制、Cpu、內部儲存體可用的網路頻寬、IO 大小、平行處理原則，以及其他因素。
- 初始測試通常是準備工作，捨棄其結果並重複測試。
- 如果效能受限於單一用戶端，而且工作負載仍低於布建的共用限制，則可透過將負載分散到多個用戶端來達到更高的效能。

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS、輸送量和 IO 大小之間的關聯性

**輸送量 = IO 大小 * IOPS**

較高的 IO 大小會產生較高的輸送量，而且會有較高的延遲，因此會產生較低的淨 IOPS 數目。 較小的 IO 大小會產生較高的 IOPS，但會導致較低的淨輸送量和延遲。

## <a name="next-steps"></a>後續步驟

- [在 FileStorage 帳戶上啟用 SMB 多重通道 (預覽) ](storage-files-enable-smb-multichannel.md)
- 請參閱 [Windows 檔](/azure-stack/hci/manage/manage-smb-multichannel) ，以深入瞭解 SMB 多重通道。
