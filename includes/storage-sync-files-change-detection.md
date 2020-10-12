---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "72391581"
---
不會立即偵測及複寫使用 Azure 入口網站或 SMB 對 Azure 檔案共用所做的變更，和伺服器端點的變更不一樣。 Azure 檔案還沒有變更通知或日誌功能，因此無法在檔案變更時自動啟動同步工作階段。 在 Windows Server 上，Azure 檔案同步會使用 [Windows USN 日誌](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)，在檔案變更時自動啟動同步工作階段。

若要偵測 Azure 檔案共用的變更，Azure 檔案同步有個已排程的作業，稱為「變更偵測作業」**。 變更偵測作業會列舉檔案共用的每個檔案，然後比較該檔案的同步版本。 當變更偵測作業判斷檔案已有變更時，Azure 檔案同步就會起始同步工作階段。 變更偵測作業會每隔 24 小時起始。 由於變更偵測作業的運作方式是列舉 Azure 檔案共用的每個檔案，大命名空間會比小命名空間耗費更長的時間。 大命名空間判斷哪些檔案已變更所耗費的時間，可能會超過 24 小時 (每隔 24 小時執行一次偵測作業)。

若要立即同步處理 Azure 檔案共用中變更的檔案，您可以使用 **Invoke-AzStorageSyncChangeDetection** PowerShell Cmdlet，手動在 Azure 檔案共用中起始變更偵測。 此 Cmdlet 是用於在 Azure 檔案共用中以自動化程序進行變更，或是由系統管理員完成變更的案例 (例如將檔案和目錄移到共用中)。 針對使用者變更，建議您在 IaaS VM 中安裝 Azure 檔案同步代理程式，並讓終端使用者透過 IaaS VM 存取檔案共用。 如此一來，所有變更都會快速同步處理至其他代理程式，而不需要使用 Invoke-AzStorageSyncChangeDetection Cmdlet。 若要深入瞭解，請參閱 [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 文件。

>[!NOTE]
>使用 REST 對 Azure 檔案共用所做的變更不會更新 SMB 上次修改時間，而且將不會被同步視為變更。

我們發現，為 Azure 檔案新增變更偵測，類似於在 Windows 伺服器上磁碟區的 USN 變更偵測。 請至 [Azure 檔案 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)投票，協助我們決定此功能的進一步開發優先順序。
