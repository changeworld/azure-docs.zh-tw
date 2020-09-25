---
title: 了解 Azure 檔案同步雲端階層處理 | Microsoft Docs
description: 深入瞭解雲端階層處理，這是選擇性的 Azure 檔案同步功能。 經常存取的檔案會在伺服器上本機快取;其他則分層至 Azure 檔案儲存體。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9df06a9d81ef3c9fbe3380bab88325a586981db9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329307"
---
# <a name="cloud-tiering-overview"></a>雲端階層處理概觀
雲端階層處理是 Azure 檔案同步的一個選用功能，其中經常存取的檔案會快取到伺服器本機上，而其他的檔案會依原則設定分層處理至 Azure 檔案服務。 當檔案被分層之後，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 會將本機檔案取代為指標或重新分析點。 重新分析點代表的是針對 Azure 檔案服務中檔案的 URL。 階層式檔案在 NTFS 中具有「離線」屬性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 屬性集，因此協力廠商應用程式可以安全地識別階層式檔案。
 
當使用者開啟階層式檔案時，Azure 檔案同步不需要知道檔案儲存在 Azure 中，就能無縫地從 Azure 檔案儲存體重新叫用檔案資料。 
 
 > [!Important]  
 > Windows 系統磁碟區上不支援雲端階層處理。
    
 > [!Important]  
 > 若要重新叫用已階層式檔案，網路頻寬應至少為 1 Mbps。 如果網路頻寬小於 1 Mbps，檔案可能會因為逾時錯誤而無法重新叫用。

## <a name="cloud-tiering-faq"></a>雲端階層處理常見問題集

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>雲端階層處理如何運作？
Azure 檔案同步系統篩選器會在每個伺服器端點上建立您命名空間的「熱度圖」。 它會監視一段時間的存取 (讀取和寫入作業)，然後根據存取的頻率和近因，將熱度分數指派給每個檔案。 最近開啟過且經常被存取的檔案，將會被視為熱門，而很少被觸及與一段時間未被存取的檔案將被視為冷門。 當伺服器上的檔案磁碟區超過您設定的磁碟區可用空間閾值時，它會將最冷門的檔案分層處理至 Azure 檔案服務，直到符合您設定的可用空間百分比為止。

此外，您可以在每個伺服器端點上指定日期原則，以將任何未在指定天數記憶體取的檔案分層，而不論可用的本機儲存體容量為何。 如果您知道該伺服器端點中的檔案不需要在本機保留超過特定的存留期，則這是主動釋放本機磁碟空間的理想選擇。 這會釋出相同磁片區上其他端點的寶貴本機磁片容量，以快取更多檔案。

雲端階層處理熱度圖基本上是所有正在同步的檔案清單，並位於已啟用雲端階層處理的位置。 若要判斷該熱度圖中個別檔案的相對位置，系統會使用下列其中一個時間戳記的最大值（依該順序）：最大 (上次存取時間、上次修改時間、建立時間) 。 一般而言，會追蹤並提供上次存取時間。 不過，在建立新的伺服器端點時，在啟用雲端階層處理的情況下，一開始並沒有足夠的時間來觀察檔案存取。 在沒有上次存取時間的情況下，上次修改時間是用來評估熱度圖中的相對位置。 相同的回復適用于日期原則。 如果沒有上次存取時間，日期原則將會在上次修改的時間採取動作。 如果無法使用，則會切換回檔案的建立時間。 經過一段時間後，系統會觀察更多和更多的檔案存取要求，並將資料透視到主要使用自我追蹤的上次存取時間。

雲端階層處理並不依賴 NTFS 功能來追蹤上次存取時間。 這項 NTFS 功能預設是關閉的，而且基於效能考慮，我們不建議您手動啟用這項功能。 雲端階層處理會以分開且非常有效率的方式來追蹤上次存取時間。

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>檔案要階層式檔案大小下限為何？

針對代理程式版本12和更新版本，檔案到階層式檔案大小下限是以檔案系統叢集大小為基礎。 符合雲端階層處理的最小檔案大小是以叢集大小的2x 和至少 8 KB 來計算。 下表根據磁片區叢集大小說明可階層式最小檔案大小：

|磁片區叢集大小 (位元組)  |此大小或更大的檔案可以分層  |
|----------------------------|---------|
|4 KB 或更小的 (4096)       | 8 KB    |
|8 KB (8192)                  | 16 KB   |
|16 KB (16384)                | 32 KB   |
|32 KB (32768)                | 64 KB   |
|64 KB (65536) 和更大    | 128 KB  |

使用 Windows Server 2019 和 Azure 檔案同步代理程式版本12和更新版本時，也支援最多 2 MB 的叢集大小，並以相同的方式在這些較大的叢集大小上進行階層處理。 較舊的作業系統或代理程式版本支援的叢集大小上限為 64 KB，但除此之外，雲端階層處理無法運作。

Windows 使用的所有檔案系統，會根據叢集大小來組織您的硬碟 (也稱為配置單位大小) 。 叢集大小代表可以用來保存檔案的最小磁碟空間量。 當檔案大小未超過叢集大小的偶數倍數時，必須使用額外的空間將檔案保存到下一個叢集大小的倍數。

具有 Windows Server 2012 R2 和更新版本的 NTFS 磁片區支援 Azure 檔案同步。 下表說明當您建立新的 NTFS 磁片區時的預設群集大小。 

|磁碟區大小    |Windows Server 2012R2 和更新版本 |
|---------------|---------------|
|7 MB – 16 TB   | 4 KB          |
|16TB – 32 TB   | 8 KB          |
|32 TB – 64 TB   | 16 KB         |
|64TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 KB         |
|> 256 TB       | 不支援 |

在建立磁片區時，您可以手動將磁片區格式化為不同的叢集大小。 如果您的磁片區源自較舊版本的 Windows，則預設的叢集大小可能也會不同。 [本文有更多有關預設叢集大小的詳細資料。](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) 即使您選擇的叢集大小小於 4 KB，仍會套用 8 KB 的限制，作為可階層式最小檔案大小。 即使技術上2x 的叢集大小等於小於 8 KB，仍 (。 ) 

絕對最小值的原因可以是 NTFS 儲存極小檔案的方式，也就是 1 KB 到 4 KB 大小的檔案。 根據磁片區的其他參數，小型檔案可能不會儲存在磁片上的叢集中。 將這類檔案直接儲存在磁片區的主要檔案資料表或 "MFT 記錄" 中，可能會更有效率。 雲端階層處理重新分析點一律會儲存在磁片上，並只佔用一個叢集。 將這類小型檔案分層可能會導致無法節省空間。 極端的情況下，甚至可能會在啟用雲端階層處理時使用更多空間。 為了避免這種情況，雲端階層處理層級的檔案最小大小為 8 KB （4 KB 或更小的叢集大小）。

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>磁碟區可用空間階層處理原則如何運作？
磁碟區可用空間是伺服器端點所在磁碟區上，您希望保留的可用空間數量。 例如，如果在有一個伺服器端點的磁碟區上設定 20% 的磁碟區可用空間，則最近存取的檔案將會佔用最多 80% 的磁碟區空間，無法納入此空間的其餘檔案則會分層處理至 Azure。 磁碟區可用空間適用於磁碟區層級，而不是個別目錄或同步群組層級。 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>磁碟區可用空間階層處理原則會如何處理新的伺服器端點？
當佈建新的伺服器端點並連線至 Azure 檔案共用時，伺服器會先提取命名空間，然後將提取實際檔案，直到達到它的磁碟區可用空間閾值為止。 此程序也稱為快速災害復原，或快速命名空間還原。

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>如果磁碟區上有多個伺服器端點，如何解譯磁碟區可用空間？
當磁碟區上有一個以上的伺服器端點時，針對有效磁碟區上任何伺服器端點所指定的最大磁碟區可用空間，就是該磁碟區的可用空間臨界值。 檔案將依據其使用方式模式分層，不論其屬於哪一個伺服器端點。 例如，如果磁碟區上有兩個伺服器端點，端點 1 和端點 2，其中端點 1 有 25% 的磁碟區可用空間臨界值，端點 2 有 50% 的磁碟區可用空間臨界值，則這兩個伺服器端點的磁碟區可用空間臨界值就是 50%。 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>日期階層處理原則如何與磁碟區可用空間階層處理原則搭配運作？ 
在伺服器端點上啟用雲端階層處理時，您會設定磁碟區可用空間原則。 此原則一律優先於任何其他原則，包括日期原則。 （選擇性）您可以針對該磁片區上的每個伺服器端點啟用日期原則。 此原則會管理只有存取 (的檔案，在此原則所描述的天數內，讀取或寫入) 將會保留在本機。 使用指定天數來存取的檔案將會分層。 

雲端階層處理會使用上次存取時間來判斷應階層式檔案。 雲端階層處理篩選器驅動程式 ( # A0) 會追蹤上次存取時間，並將資訊記錄在雲端階層處理熱度存放區中。 您可以使用伺服器本機 PowerShell Cmdlet 取出熱度存放區，並將它儲存為 CSV 檔案。

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> 上次存取的時間戳記不是 NTFS 所追蹤的屬性，因此檔案總管中預設為不可見。 請勿使用檔案的上次修改時間戳記來檢查日期原則是否如預期般運作。 這個時間戳記只會追蹤寫入，而非讀取。 使用顯示的 Cmdlet 來取得此評估的最後存取時間戳記。

> [!WARNING]
> 請勿開啟追蹤上次存取的 NTFS 功能-檔案和資料夾的時間戳記。 這項功能預設為關閉，因為它會對效能造成很大的影響。 Azure 檔案同步會自動且非常有效率地追蹤上次存取時間，而且不會利用這項 NTFS 功能。

請記住，磁片區可用空間原則一律優先，而且磁片區上沒有足夠的可用空間可保留所需的檔案數目（依日期原則所述），Azure 檔案同步會繼續將最冷檔案分層，直到達到磁片區可用空間百分比為止。

例如，假設您有一個 60 天的日期型階層處理原則，以及 20% 的磁碟區可用空間原則。 如果套用日期原則之後，磁片區上的可用空間少於20%，磁片區可用空間原則就會開始，並覆寫日期原則。 這會導致有更多的檔案進行階層處理，以便讓伺服器上保存的資料量可從 60 天的資料縮減為 45 天。 相反地，即使您尚未觸達可用空間閾值，此原則仍會強制將落於時間範圍外的檔案進行階層處理 – 因此，即使磁碟區沒有資料，存在時間已有 61 天之久的檔案仍會進行階層處理。

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>如何決定適當的磁碟區可用空間？
您應在本機上保留的資料數量取決於幾項因素：您的頻寬、資料集的存取模式，和您的預算。 如果您使用低頻寬連線，您可能會想在本機上保留更多資料，以確保將使用者的延遲降到最低。 或者，您可以用指定期間內的流失率為基礎。 例如，如果您知道 1 TB 的資料集每個月約有 10% 的變更或是被積極地存取，則您可能想要保留 100 GB 在本機上，如此就不會經常重新叫用檔案。 如果您的磁碟區為 2TB，接著您想要保留 5% (或 100 GB) 在本機上，這表示其餘的 95% 是您的磁碟區可用空間百分比。 不過，建議您將緩衝區列入考量，以應付短暫的較高流失率。換句話說，先以較低的磁碟區可用空間百分比開始，稍後再視需要進行調整。 

將更多資料保留在本機代表較低的輸出成本，因為從 Azure 重新叫用的檔案會比較少，但您也需要維護更大量的內部部署儲存體，也就是要負擔其本身的成本。 一旦部署 Azure 檔案同步的實例之後，您就可以查看儲存體帳戶的輸出，大致測量您的磁片區可用空間設定是否適用于您的使用量。 假設儲存體帳戶只包含您的 Azure 檔案同步雲端端點 (亦即您的同步共用)，則高輸出代表從雲端叫用了許多檔案，您應該考慮增加您的本機快取。

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>我已加入新的伺服器端點。 我在此伺服器上的檔案會於存在多久後，才進行階層處理？

每個群組原則是否需要階層式檔案，會每小時評估一次。 建立新的伺服器端點時，您可能會遇到兩種情況：

1. 當您加入新的伺服器端點時，通常檔案會存在於該伺服器的位置。 必須先上傳，才能開始進行雲端階層處理。 磁片區可用空間原則將不會開始工作，直到所有檔案的初始上傳完成為止。 不過，在檔案上傳之後，選擇性的日期原則將開始以個別檔案為基礎。 一小時的間隔也適用于此處。 
2. 當您加入新的伺服器端點時，您可以將空的伺服器位置連線到 Azure 檔案共用中的資料。 無論是在第二部伺服器上，或是在發生嚴重損壞修復的情況下。 如果您選擇在初始下載至伺服器時下載命名空間和重新叫用內容，則在命名空間關閉之後，將會根據上次修改的時間戳記重新叫用檔案。 只有數量的檔案會在磁片區可用空間原則和選用的日期原則中重新叫用。

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>如何判斷檔案是否已分層？
有幾個方法可用來確認是否已將檔案分層到 Azure 檔案共用：
    
   *  **檢查檔案的檔案屬性。**
     請在檔案上按一下滑鼠右鍵，移至 [詳細資料]****，然後向下捲動至 [屬性]**** 屬性。 分層的檔案具有下列屬性組：     
        
        | 屬性代號 | 屬性 | 定義 |
        |:----------------:|-----------|------------|
        | A | 封存 | 指出該檔案應該由備份軟體進行備份。 不論檔案已分層還是完全儲存在磁碟上，一律會設定這個屬性。 |
        | P | 疏鬆檔案 | 指出該檔案是疏鬆檔案。 疏鬆檔案是 NTFS 所提供的特殊化檔案類型，可在磁碟資料流上的檔案大多空白時有效地加以使用。 Azure 檔案同步會使用疏鬆檔案，因為檔案已完全分層或已部分回收。 在完全分層的檔案中，檔案資料流會儲存於雲端。 在部分回收的檔案中，該部分的檔案已經在磁碟上。 如果檔案已完全回收到磁碟，Azure 檔案同步便會將它從疏鬆檔案轉換為一般檔案。 這個屬性只會在 Windows Server 2016 及更舊版本上設定。|
        | M | 在資料存取上回想 | 指出檔案的資料不會完全存在於本機儲存體上。 讀取檔案將會導致至少從伺服器端點所連接的 Azure 檔案共用提取部分檔案內容。 這個屬性只會在 Windows Server 2019 上設定。 |
        | L | 重新分析點 | 指出該檔案有重新分析點。 重新分析點是可供檔案系統篩選器使用的特殊指標。 Azure 檔案同步會使用重新分析點來為 Azure 檔案同步的檔案系統篩選器 (StorageSync.sys) 定義儲存檔案的雲端位置。 這支援無縫存取。 使用者不需要知道正在使用 Azure 檔案同步，或是如何取得 Azure 檔案共用中檔案的存取權。 當檔案完全回收時，Azure 檔案同步就會從檔案中移除重新分析點。 |
        | O | 離線 | 指出部分或所有檔案的內容並未儲存在磁碟上。 當檔案完全回收時，Azure 檔案同步就會移除此屬性。 |

        ![檔案的 [屬性] 對話方塊，已選取 [詳細資料] 索引標籤](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        您可以在檔案總管的資料表顯示中新增 [屬性]**** 欄位，就能看見資料夾中所有檔案的屬性。 若要這樣做，以滑鼠右鍵按一下現有資料行 (例如，**大小**)，選取 [詳細]****，然後從下拉式清單中選取 [屬性]****。
        
   * **使用 `fsutil` 來檢查檔案的重新分析點。**
       如上述選項所述，已分層的檔案一律已設定重新分析點。 重新分析指標是適用於 Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 的特殊指標。 若要檢查檔案是否有重新分析點，請在提升權限的命令提示字元或 PowerShell 視窗中，執行 `fsutil` 公用程式：
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        如果檔案有重新分析點，您可以預期會看到**重新分析標記值 : 0x8000001e**。 此十六進位值是 Azure 檔案同步所擁有的重新分析點值。輸出也包含重新分析資料，代表您的檔案在 Azure 檔案共用上的路徑。

        > [!WARNING]  
        > `fsutil reparsepoint` 公用程式命令也能刪除重新分析點。 除非 Azure 檔案同步工程小組要求您，否則請勿執行此命令。 執行此命令可能導致資料遺失。 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>我想要使用的檔案已分層。 如何將檔案回收到磁碟，以便在本機使用？
將檔案回收到磁碟的最簡單方式就是開啟該檔案。 Azure 檔案同步的檔案系統篩選器 (StorageSync.sys) 會順暢地從 Azure 檔案共用下載檔案，您不必執行任何工作。 對於可以部分讀取的檔案類型 (例如，多媒體或 .zip 檔案)，開啟檔案並不會下載整個檔案。

您也可以使用 PowerShell 來強制回收檔案。 如果您想要一次回收許多檔案 (例如資料夾內的所有檔案)，便適合使用這種方法。 在 Azure 檔案同步安裝所在的伺服器節點開啟 PowerShell 工作階段，然後執行下列 PowerShell 命令：
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
選用參數：
* `-Order CloudTieringPolicy` 會先重新叫用最近修改或存取過的檔案，並由目前的階層處理原則所允許。 
    * 如果已設定磁片區可用空間原則，則會重新叫用檔案，直到達到磁片區可用空間原則設定為止。 例如，如果「磁片區可用」原則設定為20%，則當磁片區可用空間達到20% 時，就會停止召回。  
    * 如果已設定磁片區可用空間和日期原則，在達到磁片區可用空間或日期原則設定之前，將會重新叫用檔案。 例如，如果「磁片區可用」原則設定為20%，而日期原則為7天，則當磁片區可用空間達到20%，或在7天記憶體取或修改的所有檔案都是在本機時，召回將會停止。
* `-ThreadCount` 決定可以平行回收多少個檔案。
* `-PerFileRetryCount`判斷嘗試重新叫用目前封鎖之檔案的頻率。
* `-PerFileRetryDelaySeconds`判斷重試重新叫用嘗試之間的時間（以秒為單位），且應一律搭配先前的參數使用。

範例：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - 將新的伺服器端點新增至現有的同步處理群組時，也可以使用 StorageSyncFileRecall 指令程式來改善檔案下載效能。  
>- 如果裝載伺服器的本機磁碟區沒有足以重新叫用所有已分層資料的可用空間，`Invoke-StorageSyncFileRecall` Cmdlet 將會失敗。  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>使用 Azure 檔案同步之後，為什麼檔案的 [ *磁片大小* ] 屬性與 [ *大小* ] 屬性不符？ 
Windows 檔案總管會顯示兩個屬性來代表檔案的大小：**大小**和**磁碟大小**。 這些屬性的意義稍有不同。 **大小**代表檔案的完整大小。 **磁碟大小**代表儲存在磁碟上的檔案資料流大小。 這些屬性的值可能因各種不同的原因而有所不同，例如壓縮、重複使用重復資料刪除，或使用 Azure 檔案同步的雲端階層處理。如果檔案分層到 Azure 檔案共用，則磁片上的大小會是零，因為檔案串流會儲存在您的 Azure 檔案共用中，而不是儲存在磁片上。 檔案也可能部分分層 (或部分回收)。 在部分分層的檔案中，部分的檔案是在磁碟上。 當應用程式 (例如，多媒體播放程式或壓縮公用程式) 讀取部分檔案時，可能會發生這種情形。 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>如何強制讓檔案或目錄分層？

> [!NOTE]
> 當您選取要階層式目錄時，只有目前在目錄中的檔案會分層。 在該時間之後建立的任何檔案都不會自動分層。

啟用雲端分層功能時，雲端分層會自動根據上次存取和修改時間來將檔案分層，以達到雲端端點上指定的磁碟區可用空間百分比。 不過，有時候您可能會想要以手動方式強制將檔案分層。 如果您要儲存長時間不打算再次使用的大型檔案，並且想要讓磁碟區上的可用空間現在可供其他檔案和資料夾使用，便適合使用這種方法。 您可以使用下列 PowerShell 命令來強制分層：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>為什麼我的階層式檔案未在 Windows 檔案總管中顯示縮圖或預覽？
針對階層式檔案，您的伺服器端點將不會顯示縮圖和預覽。 這是預期的行為，因為 Windows 中的縮圖快取功能刻意略過讀取具有離線屬性的檔案。 啟用雲端階層處理時，透過階層式檔案讀取會導致 (重新叫用) 下載這些檔案。

這不是 Azure 檔案同步特有的行為，Windows 檔案總管會針對已設定 offline 屬性的任何檔案顯示「灰色 X」。 透過 SMB 存取檔案時，您會看到 X 圖示。 如需此行為的詳細說明，請參閱 [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>我已停用雲端階層處理，為什麼伺服器端點位置中有分層檔案？

有兩個原因：階層式檔案可能存在於伺服器端點位置：

- 將新的伺服器端點加入至現有的同步處理群組時，中繼資料會先同步處理到伺服器，然後檔案就會在背景下載至伺服器。 檔案會顯示為分層，直到在本機下載。 若要改善將新伺服器新增至同步處理群組時的檔案下載效能，請使用 [StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) Cmdlet。

- 如果已在伺服器端點上啟用雲端階層處理，然後停用，則在存取檔案之前，檔案會保持分層。


## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
