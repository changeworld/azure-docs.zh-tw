---
title: Azure 檔案服務簡介 | Microsoft Docs
description: Azure 檔案服務的概觀，此服務可讓您使用業界標準 SMB 通訊協定在雲端建立和使用網路檔案共用。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070128"
---
# <a name="what-is-azure-files"></a>什麼是 Azure 檔案服務？
Azure 檔案儲存體提供雲端中完全受控的檔案共用，可透過業界標準[伺服器訊息區 (SMB) 通訊協定](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)或[網路檔案系統 (NFS) 通訊協定](https://en.wikipedia.org/wiki/Network_File_System)來存取。 雲端部署或內部部署可同時掛接 Azure 檔案共用。 您可以從 Windows、Linux 和 macOS 用戶端存取 Azure 檔案儲存體 SMB 檔案共用。 也可以從 Linux 或 macOS 用戶端存取 Azure 檔案儲存體 NFS 檔案共用。 此外，透過 Azure 檔案儲存體 SMB 檔案共用可以在 Windows Server 上快取 Azure 檔案同步，以便在資料的使用位置附近快速存取。

## <a name="videos"></a>影片
| Azure 檔案同步簡介 | 同步的 Azure 檔案儲存體 (Ignite 2019)  |
|-|-|
| [![介紹 Azure 檔案同步影片的螢幕錄製影片 - 按一下以播放！](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Azure 檔案同步簡報的螢幕錄製影片 - 按一下以播放！](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

以下是一些 Azure 檔案儲存體常見的使用案例影片：
* [以無伺服器 Azure 檔案共用取代您的檔案伺服器](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [在使用 AD 驗證的 Windows 虛擬桌面中 Azure 檔案儲存體上開始使用 FSLogix 設定檔容器](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Azure 檔案服務為何很實用
Azure 檔案共用可以用來：

* **取代或補充內部部署檔案伺服器**：  
    Azure 檔案服務可用來完全取代或補充傳統內部部署檔案伺服器或 NAS 裝置。 無論身在何處，熱門作業系統 (例如 Windows、macOS 和 Linux) 都可以直接掛接 Azure 檔案共用。 透過 Azure 檔案 SMB 檔案共用，也可以將 Azure 檔案同步複寫到 Windows Server (在內部部署環境或雲端)，從而在資料的使用位置進行高效能和分散式快取。 使用最新版本的 [Azure 檔案儲存體 AD 驗證](storage-files-active-directory-overview.md)，Azure 檔案 SMB 檔案共用可以繼續使用已裝載 AD 的內部部署進行存取控制。 

* **「原形移轉」應用程式**：  
    Azure 檔案服務可讓您輕易將預期檔案共用會儲存檔案應用程式或使用者資料的應用程式「原形移轉」到雲端。 Azure 檔案服務可支援「傳統」原形移轉案例 (其中的應用程式及其資料會移至 Azure)，和「混合式」原形移轉案例 (其中的應用程式資料會移至 Azure 檔案服務，而應用程式會繼續在內部部署環境執行)。 

* **簡化雲端開發**：  
    Azure 檔案服務也可透過數種使用方式來簡化新的雲端開發專案。 例如：
    * **共用的應用程式設定**：  
        分散式應用程式的常見模式是將組態檔放在一個集中的位置，這些應用程式可從許多應用程式執行個體進行存取。 應用程式執行個體可透過 File REST API 載入其組態，而使用者可藉由在本機掛接 SMB 共用來視需要進行存取。

    * **診斷共用**：  
        Azure 檔案共用是便於雲端應用程式寫入其記錄、計量和損毀傾印的地方。 應用程式執行個體可以透過 File REST API 寫入記錄，而開發人員可藉由在其本機電腦上掛接檔案共用來存取記錄。 這可提供更多的彈性，因為開發人員可以採用雲端開發，而不必放棄任何其熟悉且喜愛的現有工具。

    * **開發/測試/偵錯**：  
        當開發人員或系統管理員在雲端的 VM 上執行作業時，他們通常需要一組工具或公用程式。 將這類公用程式和工具複製到每個 VM，可能是費時的練習。 開發人員和系統管理員可藉由在 VM 本機掛接 Azure 檔案共用，快速存取其工具和公用程式 (不需要複製)。
* **容器化**：  
    Azure 檔案共用可以用作具狀態容器的永久性磁碟區。 容器提供「組建一次，隨處執行」功能，可讓開發人員加速創新。 對於在每次啟動時存取原始資料的容器，需有共用檔案系統，才能讓這些容器存取檔案系統，不論其執行所在的執行個體為何。

## <a name="key-benefits"></a>主要權益
* **共用存取**。 Azure 檔案共用支援業界標準 SMB 及 NFS 通訊協定，這表示您可以順暢地使用 Azure 檔案共用取代內部檔案共用，而不需擔心應用程式相容性。 能夠跨多部電腦和應用程式/執行個體共用檔案系統，是 Azure 檔案服務的重大優勢，尤其是針對需要共用能力的應用程式。 
* **完全受控**。 不需要管理硬體或作業系統就可以建立 Azure 檔案共用。 這表示您不必透過重大安全性升級或替換故障硬碟來處理修補伺服器作業系統。
* **指令碼和工具**。 管理 Azure 應用程式時，可以使用 PowerShell Cmdlet 和 Azure CLI 來建立、掛接和管理 Azure 檔案共用。 您可以使用 Azure 入口網站和 Azure 儲存體總管來建立和管理 Azure 檔案共用。 
* **復原功能**。 Azure 檔案服務已從頭建置，可讓您隨時使用。 使用 Azure 檔案服務取代內部部署檔案共用，表示您不再需要被吵醒去處理本機電源中斷或網路問題。 
* **熟悉的可程式性**。 Azure 中執行的應用程式可透過檔案[系統 I/O API](/dotnet/api/system.io.file) 來存取共用中的資料。 因此，開發人員可利用現有的程式碼和技能來移轉現有的應用程式。 除了系統 IO API，您也可以使用 [Azure 儲存體用戶端程式庫](/previous-versions/azure/dn261237(v=azure.100))或 [Azure 儲存體的 REST API](/rest/api/storageservices/file-service-rest-api)。

## <a name="next-steps"></a>後續步驟
* [了解可用的檔案共用通訊協定](storage-files-compare-protocols.md)
* [建立 Azure 檔案共用](storage-how-to-create-file-share.md)
* [在 Windows 上連線並掛接 SMB 共用](storage-how-to-use-files-windows.md)
* [在 Linux 上連線並掛接 SMB 共用](storage-how-to-use-files-linux.md)
* [在 macOS 上連線並掛接 SMB 共用](storage-how-to-use-files-mac.md)
* [如何建立 NFS 磁碟區](storage-files-how-to-create-nfs-shares.md)
