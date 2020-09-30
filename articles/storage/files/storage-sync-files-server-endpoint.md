---
title: 新增/移除 Azure 檔案同步伺服器端點 | Microsoft Docs
description: 瞭解如何使用 Azure 檔案同步來新增或移除伺服器端點。伺服器端點是已註冊伺服器上的特定位置，例如伺服器磁片區上的資料夾。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541487"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>新增/移除 Azure 檔案同步伺服器端點
Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 它會將您的 Windows Server 轉換成 Azure 檔案共用的快速快取來達到這個目的。 您可以使用 Windows Server 上可用的任何通訊協定來存取本機資料 (包括 SMB、NFS 和 FTPS)，並且可以在世界各地擁有任何所需數量的快取。

*伺服器端點*代表*已註冊的伺服器*上的特定位置，例如伺服器磁片區上的資料夾或磁片區的根目錄。 如果多個伺服器端點的命名空間不重迭 (（例如 F:\sync1 和 F:\sync2) ，而且每個端點正在同步處理至唯一的同步處理群組），則相同磁片區中可以存在多個伺服器端點。 您可以為每個伺服器端點個別設定雲端階層原則。 如果您將內含一組現有檔案的伺服器位置當作伺服器端點新增至同步群組，那些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

如需如何從頭到尾部署 Azure 檔案同步的資訊，請參閱[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>必要條件
若要建立伺服器端點，您必須先確定已符合下列準則： 
- 伺服器已安裝 Azure 檔案同步代理程式且已註冊。 如需安裝 Azure 檔案同步代理程式的指示，請參閱[向 Azure 檔案同步註冊/取消註冊伺服器](storage-sync-files-server-registration.md)文章。 
- 確定已部署儲存體同步服務。 如需如何部署儲存體同步服務的詳細資訊，請參閱[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。 
- 確定已部署同步群組。 了解如何[建立同步群組](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)。
- 確定伺服器已連線到網際網路，而且 Azure 可供存取。 針對伺服器和我們服務之間的所有通訊，我們使用連接埠 443。

## <a name="add-a-server-endpoint"></a>新增伺服器端點
若要新增伺服器端點，請瀏覽至所需的同步群組，並選取 [新增伺服器端點]。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

[新增伺服器端點]**** 底下需要下列資訊：

- **已註冊的伺服器**：要在其上建立伺服器端點的伺服器或叢集名稱。
- **路徑**：Windows Server 上要隨著同步群組同步的路徑。
- **雲端階層處理**：啟用或停用雲端階層處理的開關。 啟用時，雲端階層會將檔案分層** 到 Azure 檔案共用。 這會將內部部署的檔案共用轉換成快取，而不是一份完整的資料集，協助您管理伺服器上的空間效率。
- **磁碟區可用空間**：伺服器端點所在磁碟區要保留的可用空間量。 例如，如果在具有單一伺服器端點的磁碟區上，將磁碟區可用空間設定為 50%，則大約有一半的資料量會分層至 Azure 檔案服務。 無論雲端階層處理是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

選取 [建立]**** 以新增伺服器端點。 同步群組命名空間中的檔案現在會保持同步。 

## <a name="remove-a-server-endpoint"></a>移除伺服器端點
如果您想要停止對指定的伺服器端點使用 Azure 檔案同步，便可以移除該伺服器端點。 

> [!Warning]  
> 除非 Microsoft 工程師明確指示，否則請勿將伺服器端點移除後再重新建立，嘗試對同步、雲端階層或任何其他方面的 Azure 檔案同步問題進行疑難排解。 移除伺服器端點是破壞性作業，而且重新建立伺服器端點之後，伺服器端點內的階層式檔案並不會「重新連接」至其在 Azure 檔案共用的位置，進而導致同步錯誤。 另請注意，位在伺服器端點命名空間外部的階層式檔案可能會永久遺失。 即使從未啟用雲端階層，伺服器端點仍可能有階層式檔案存在。

若要確保在移除伺服器端點之前會回收所有階層式檔案，請停用伺服器端點上的雲端階層，然後執行下列 PowerShell Cmdlet 來回收的伺服器端點命名空間內的所有階層式檔案：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
指定 `-Order CloudTieringPolicy` 將會先重新叫用最近修改過的檔案。
其他選擇性但可考慮的實用參數如下：
* `-ThreadCount` 決定可平行回收的檔案數目。
* `-PerFileRetryCount`判斷嘗試重新叫用目前封鎖之檔案的頻率。
* `-PerFileRetryDelaySeconds`判斷重試重新叫用嘗試之間的時間（以秒為單位），且應一律搭配先前的參數使用。

> [!Note]  
> 如果裝載伺服器的本機磁碟區沒有足以重新叫用所有已分層資料的可用空間，`Invoke-StorageSyncFileRecall` Cmdlet 將會失敗。  

移除伺服器端點：

1. 瀏覽至您的伺服器註冊所在的儲存體同步服務。
2. 瀏覽至需要的同步群組。
3. 在儲存體同步服務中，移除同步群組中所要移除的伺服器端點。 這可以透過以滑鼠右鍵按一下 [同步群組] 窗格中的相關伺服器端點來完成。

    ![從同步群組移除伺服器端點](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>後續步驟
- [向 Azure 檔案同步註冊/取消註冊伺服器](storage-sync-files-server-registration.md)
- [針對 Azure 檔案同步部署進行規劃](storage-sync-files-planning.md) \(部分機器翻譯\)
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
