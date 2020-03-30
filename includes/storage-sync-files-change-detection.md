---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391581"
---
不會立即偵測及複寫使用 Azure 入口網站或 SMB 對 Azure 檔案共用所做的變更，和伺服器端點的變更不一樣。 Azure 檔案還沒有變更通知或日誌功能，因此無法在檔案變更時自動啟動同步工作階段。 在 Windows Server 上，Azure 檔案同步會使用 [Windows USN 日誌](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)，在檔案變更時自動啟動同步工作階段。

若要偵測 Azure 檔案共用的變更，Azure 檔案同步有個已排程的作業，稱為「變更偵測作業」**。 變更偵測作業會列舉檔案共用的每個檔案，然後比較該檔案的同步版本。 當變更偵測作業判斷檔案已有變更時，Azure 檔案同步就會起始同步工作階段。 變更偵測作業會每隔 24 小時起始。 由於變更偵測作業的運作方式是列舉 Azure 檔案共用的每個檔案，大命名空間會比小命名空間耗費更長的時間。 大命名空間判斷哪些檔案已變更所耗費的時間，可能會超過 24 小時 (每隔 24 小時執行一次偵測作業)。

要立即同步 Azure 檔共用中更改的檔，可以使用**Invoke-AzStorageSyncChange 檢測**電源 Shell Cmdlet 手動啟動檢測 Azure 檔共用中的更改。 此 Cmdlet 適用于某些類型的自動進程在 Azure 檔共用中進行更改或由管理員執行更改（例如將檔和目錄移動到共用中）的情況。 對於最終使用者更改，建議在 IaaS VM 中安裝 Azure 檔同步代理，並讓最終使用者通過 IaaS VM 訪問檔共用。 這樣，所有更改都將快速同步到其他代理，而無需使用 Invoke-AzStorageSyncChange檢測 Cmdlet。 要瞭解更多資訊，請參閱[調用-Azstorage 同步更改檢測](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)文檔。

>[!NOTE]
>使用 REST 對 Azure 檔共用所做的更改不會更新 SMB 上次修改的時間，也不會被視為按同步進行的更改。

我們發現，為 Azure 檔案新增變更偵測，類似於在 Windows 伺服器上磁碟區的 USN 變更偵測。 請至 [Azure 檔案 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)投票，協助我們決定此功能的進一步開發優先順序。
