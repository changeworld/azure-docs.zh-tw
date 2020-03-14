---
title: 了解 Azure 檔案同步雲端階層處理 | Microsoft Docs
description: 了解 Azure 檔案同步的雲端階層處理功能
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fea9cebc5199fc7c1fc5c081aa45f08044c21e44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268089"
---
# <a name="cloud-tiering-overview"></a>雲端階層處理概觀
雲端階層處理是 Azure 檔案同步的一個選用功能，其中經常存取的檔案會快取到伺服器本機上，而其他的檔案會依原則設定分層處理至 Azure 檔案服務。 當檔案被分層之後，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 會將本機檔案取代為指標或重新分析點。 重新分析點代表的是針對 Azure 檔案服務中檔案的 URL。 階層式檔案在 NTFS 中具有「離線」屬性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 屬性集，因此協力廠商應用程式可以安全地識別階層式檔案。
 
當使用者開啟階層式檔案時，Azure 檔案同步不需要知道檔案是儲存在 Azure 中，就會從 Azure 檔案儲存體順暢地重新叫用檔案資料。 
 
 > [!Important]  
 > Windows 系統磁碟區上的伺服器端點不支援雲端階層處理，且只有大小大於 64 KiB 的檔案可以分層處理到 Azure 檔案服務。
    
Azure 檔案同步不支援階層處理小於 64 KiB 的檔案，因為階層處理和重新叫用這類小型檔案的效能負擔大過所節省的空間。

 > [!Important]  
 > 若要重新叫用已階層式檔案，網路頻寬至少應為 1 Mbps。 如果網路頻寬小於 1 Mbps，檔案可能會因逾時錯誤而無法回收。

## <a name="cloud-tiering-faq"></a>雲端階層處理常見問題集

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>雲端階層處理如何運作？
Azure 檔案同步系統篩選器會在每個伺服器端點上建立您命名空間的「熱度圖」。 它會監視一段時間的存取 (讀取和寫入作業)，然後根據存取的頻率和近因，將熱度分數指派給每個檔案。 最近開啟過且經常被存取的檔案，將會被視為熱門，而很少被觸及與一段時間未被存取的檔案將被視為冷門。 當伺服器上的檔案磁碟區超過您設定的磁碟區可用空間閾值時，它會將最冷門的檔案分層處理至 Azure 檔案服務，直到符合您設定的可用空間百分比為止。

在 Azure 檔案同步代理程式 4.0 版和更新版本中，您可以另外在每個伺服器端點上指定日期原則，以對指定天數內未曾存取或修改過的檔案進行階層處理。

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
在伺服器端點上啟用雲端階層處理時，您會設定磁碟區可用空間原則。 此原則一律優先於任何其他原則，包括日期原則。 您可以選擇性地為該磁碟區上的每個伺服器端點啟用日期原則，這表示只有在此原則所描述的天數範圍內曾經存取過的檔案 (也就是，讀取或寫入)，才會保留在本機上，較舊的檔案則會進行階層處理。 請記住，磁碟區可用空間原則一律優先，而且如果磁碟區上沒有足夠的可用空間，而無法保留日期原則所描述天數的檔案數量，則 Azure 檔案同步會繼續將最不常使用的檔案進行階層處理，直到符合原則規定的磁碟區可用空間百分比。

例如，假設您有一個 60 天的日期型階層處理原則，以及 20% 的磁碟區可用空間原則。 如果在套用日期原則之後，磁碟區上的可用空間低於 20%，磁碟區可用空間原則就會開始運作，並覆寫日期原則。 這會導致有更多的檔案進行階層處理，以便讓伺服器上保存的資料量可從 60 天的資料縮減為 45 天。 相反地，即使您尚未觸達可用空間閾值，此原則仍會強制將落於時間範圍外的檔案進行階層處理 – 因此，即使磁碟區沒有資料，存在時間已有 61 天之久的檔案仍會進行階層處理。

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>如何決定適當的磁碟區可用空間？
您應在本機上保留的資料數量取決於幾項因素：您的頻寬、資料集的存取模式，和您的預算。 如果您使用低頻寬連線，您可能會想在本機上保留更多資料，以確保將使用者的延遲降到最低。 或者，您可以用指定期間內的流失率為基礎。 例如，如果您知道 1 TB 的資料集每個月約有 10% 的變更或是被積極地存取，則您可能想要保留 100 GB 在本機上，如此就不會經常重新叫用檔案。 如果您的磁碟區為 2TB，接著您想要保留 5% (或 100 GB) 在本機上，這表示其餘的 95% 是您的磁碟區可用空間百分比。 不過，建議您將緩衝區列入考量，以應付短暫的較高流失率。換句話說，先以較低的磁碟區可用空間百分比開始，稍後再視需要進行調整。 

將更多資料保留在本機代表較低的輸出成本，因為從 Azure 重新叫用的檔案會比較少，但您也需要維護更大量的內部部署儲存體，也就是要負擔其本身的成本。 一旦您部署 Azure 檔案同步執行個體後，您就可以查看儲存體帳戶的輸出，來大致衡量磁碟區可用空間設定是否適合您的使用量。 假設儲存體帳戶只包含您的 Azure 檔案同步雲端端點 (亦即您的同步共用)，則高輸出代表從雲端叫用了許多檔案，您應該考慮增加您的本機快取。

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>我已新增伺服器端點。 我在此伺服器上的檔案會於存在多久後，才進行階層處理？
在 Azure 檔案同步代理程式的4.0 版和更新版本中，一旦您的檔案上傳至 Azure 檔案共用，就會在下一次階層處理會話執行時，根據您的原則進行階層處理，這會在一小時後進行分層。 在舊版的代理程式上，最久可能需要 24 小時才會進行階層處理。

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>如何判斷檔案是否已分層？
有幾個方法可用來確認是否已將檔案分層到 Azure 檔案共用：
    
   *  **檢查檔案的檔案屬性。**
     請在檔案上按一下滑鼠右鍵，移至 [詳細資料]，然後向下捲動至 [屬性] 屬性。 分層的檔案具有下列屬性組：     
        
        | 屬性代號 | 屬性 | 定義 |
        |:----------------:|-----------|------------|
        | A | 封存 | 指出該檔案應該由備份軟體進行備份。 不論檔案已分層還是完全儲存在磁碟上，一律會設定這個屬性。 |
        | P | 疏鬆檔案 | 指出該檔案是疏鬆檔案。 疏鬆檔案是 NTFS 所提供的特殊化檔案類型，可在磁碟資料流上的檔案大多空白時有效地加以使用。 Azure 檔案同步會使用疏鬆檔案，因為檔案已完全分層或已部分回收。 在完全分層的檔案中，檔案資料流會儲存於雲端。 在部分回收的檔案中，該部分的檔案已經在磁碟上。 如果檔案已完全回收到磁碟，Azure 檔案同步便會將它從疏鬆檔案轉換為一般檔案。 此屬性只會在 Windows Server 2016 和更舊版本上設定。|
        | M | 資料存取的回想 | 表示檔案的資料未完全存在於本機儲存體上。 讀取檔案會導致至少從伺服器端點所連接的 Azure 檔案共用提取部分檔案內容。 此屬性只會在 Windows Server 2019 上設定。 |
        | L | 重新分析點 | 指出該檔案有重新分析點。 重新分析點是可供檔案系統篩選器使用的特殊指標。 Azure 檔案同步會使用重新分析點來為 Azure 檔案同步的檔案系統篩選器 (StorageSync.sys) 定義儲存檔案的雲端位置。 這支援無縫存取。 使用者不需要知道正在使用 Azure 檔案同步，或是如何取得 Azure 檔案共用中檔案的存取權。 當檔案完全回收時，Azure 檔案同步就會從檔案中移除重新分析點。 |
        | O | 離線 | 指出部分或所有檔案的內容並未儲存在磁碟上。 當檔案完全回收時，Azure 檔案同步就會移除此屬性。 |

        ![檔案的 [屬性] 對話方塊，已選取 [詳細資料] 索引標籤](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        您可以在檔案總管的資料表顯示中新增 [屬性] 欄位，就能看見資料夾中所有檔案的屬性。 若要這樣做，以滑鼠右鍵按一下現有資料行 (例如，**大小**)，選取 [詳細]，然後從下拉式清單中選取 [屬性]。
        
   * **使用 `fsutil` 來檢查檔案的重新分析點。**
       如上述選項所述，已分層的檔案一律已設定重新分析點。 重新分析指標是適用於 Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 的特殊指標。 若要檢查檔案是否有重新分析點，請在提升權限的命令提示字元或 PowerShell 視窗中，執行 `fsutil` 公用程式：
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        如果檔案有重新分析點，您可以預期會看到**重新分析標記值 : 0x8000001e**。 這個十六進位值是 Azure 檔案同步所擁有的重新分析點值。輸出中也包含重新分析資料，代表您的檔案在 Azure 檔案共用上的路徑。

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
* `-Order CloudTieringPolicy` 會先重新叫用最近修改過的檔案。  
* `-ThreadCount` 決定可平行重新叫用的檔案數目。
* `-PerFileRetryCount`會決定嘗試重新叫用目前被封鎖之檔案的頻率。
* `-PerFileRetryDelaySeconds`會決定重試重新叫用的間隔時間（以秒為單位），且應一律與先前的參數搭配使用。

> [!Note]  
> 如果裝載伺服器的本機磁碟區沒有足以重新叫用所有已分層資料的可用空間，`Invoke-StorageSyncFileRecall` Cmdlet 將會失敗。  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>使用 Azure 檔案同步之後，為什麼檔案的「磁碟大小」屬性不符合「大小」屬性？ 
Windows 檔案總管會顯示兩個屬性來代表檔案的大小：**大小**和**磁碟大小**。 這些屬性的意義稍有不同。 **大小**代表檔案的完整大小。 **磁碟大小**代表儲存在磁碟上的檔案資料流大小。 這些屬性的值可能因各種原因而有所不同，例如壓縮、使用重復資料刪除，或使用 Azure 檔案同步進行雲端階層處理。如果檔案分層至 Azure 檔案共用，則磁片上的大小為零，因為檔案資料流程是儲存在您的 Azure 檔案共用中，而不是存放在磁片上。 檔案也可能部分分層 (或部分回收)。 在部分分層的檔案中，部分的檔案是在磁碟上。 當應用程式 (例如，多媒體播放程式或壓縮公用程式) 讀取部分檔案時，可能會發生這種情形。 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>如何強制讓檔案或目錄分層？
啟用雲端分層功能時，雲端分層會自動根據上次存取和修改時間來將檔案分層，以達到雲端端點上指定的磁碟區可用空間百分比。 不過，有時候您可能會想要以手動方式強制將檔案分層。 如果您要儲存長時間不打算再次使用的大型檔案，並且想要讓磁碟區上的可用空間現在可供其他檔案和資料夾使用，便適合使用這種方法。 您可以使用下列 PowerShell 命令來強制分層：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>為什麼我的分層檔案不會在 Windows Explorer 中顯示縮圖或預覽？
若為階層式檔案，縮圖和預覽將不會顯示在您的伺服器端點上。 這是預期的行為，因為 Windows 中的縮圖快取功能會刻意略過讀取具有離線屬性的檔案。 啟用雲端階層處理時，透過階層式檔案讀取會導致下載（重新叫用）。

這不是 Azure 檔案同步特有的行為，Windows Explorer 會針對任何已設定離線屬性的檔案顯示「灰階 X」。 透過 SMB 存取檔案時，您會看到 X 圖示。 如需此行為的詳細說明，請參閱[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
