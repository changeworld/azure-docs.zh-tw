---
title: 規劃 Azure 檔案服務部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 98965a50037558f512401e09915021234790840d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526473"
---
# <a name="planning-for-an-azure-files-deployment"></a>規劃 Azure 檔案服務部署

[Azure 檔案服務](storage-files-introduction.md)可提供在雲端中完全受控的檔案共用，可透過業界標準 SMB 通訊協定加以存取。 因為 Azure 檔案服務受到完整管理，所以部署於生產環境案例遠易於部署及管理檔案伺服器或 NAS 裝置。 針對在組織中部署生產環境使用的 Azure 檔案共用，本文說明應考慮的主題。

## <a name="management-concepts"></a>管理概念

 下圖說明 Azure 檔案服務管理建構：

![檔案結構](./media/storage-files-introduction/files-concepts.png)

* **儲存體帳戶**：一律透過儲存體帳戶來存取 Azure 儲存體。 如需儲存體帳戶容量的詳細資訊，請參閱[標準儲存體帳戶的擴充性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **共用**：檔案儲存體共用是 Azure 中的 SMB 檔案共用。 所有的目錄和檔案必須在上層共用中建立。 帳戶可以包含不限數目的共用，而共用可以儲存無限數量的檔案，最多可達檔案共用的總容量。 Premium 和標準檔案共用的總容量為 100 TiB。

* **目錄**：選擇性的目錄階層。

* **檔案**：共用中的檔案。 檔案的大小可高達 1 TiB。

* **URL 格式**：若是透過檔案 REST 通訊協定以進行 Azure 檔案共用的要求，可以使用下列 URL 格式來定址檔案：

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>資料存取方法

Azure 檔案服務提供兩種方便的內建資料存取方法，您可以個別或結合使用來存取資料：

1. **雲端直接存取**：利用業界標準伺服器訊息區 (SMB) 通訊協定或透過檔案 REST API，[Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 及/或 [Linux](storage-how-to-use-files-linux.md) 可掛接任何 Azure 檔案共用。 共用上的檔案藉由 SMB 讀取和寫入時，會直接在 Azure 中的檔案共用上進行。 若要由 Azure 中的 VM 掛接，作業系統中的 SMB 用戶端必須至少支援 SMB 2.1。 若要掛接在內部 (例如使用者的工作站上)，工作站所支援的 SMB 用戶端必須至少支援 SMB 3.0 (含加密)。 除了 SMB 之外，新的應用程式或服務可以透過檔案 REST 直接存取檔案共用，為軟體開發提供了方便且可擴充的應用程式開發介面。
2. **Azure 檔案同步**：藉由 Azure 檔案同步，共用可以複寫到內部部署或 Azure 中的 Windows Server。 您的使用者可透過 Windows Server 存取檔案共用，像是透過 SMB 或 NFS 共用。 這適用於從遠離 Azure 資料中心的位置 (例如分公司) 存取和修改資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。 最後，資料可能會分層至 Azure 檔案服務，因此透過伺服器仍然可以存取所有資料，但伺服器並沒有資料的完整複本。 資料實際上是在使用者開啟時順暢地取回。

下表說明使用者和應用程式如何存取您的 Azure 檔案共用：

| | 雲端直接存取 | Azure 檔案同步 |
|------------------------|------------|-----------------|
| 您需要使用哪些通訊協定？ | Azure 檔案服務支援 SMB 2.1、SMB 3.0 和檔案 REST API。 | 透過 Windows Server 上任何支援的通訊協定 (SMB、NFS、FTPS 等) 存取您的 Azure 檔案共用 |  
| 您會在何處執行工作負載？ | **Azure 中**：Azure 檔案服務提供對資料的直接存取。 | **低速網路的內部部署**：Windows、Linux 及 macOS 用戶端可掛接區域內部部署的 Windows 檔案共用，作為 Azure 檔案共用的快速快取。 |
| 您需要何種 ACL 層級？ | 共用和檔案層級。 | 共用、檔案和使用者層級。 |

## <a name="data-security"></a>資料安全性

Azure 檔案服務具有數個內建的選項，可用於確保資料安全性：

* 支援兩種網路通訊協定的加密：SMB 3.0 加密和經由 HTTPS 的檔案 REST。 依照預設： 
    * 支援 SMB 3.0 加密的用戶端會透過加密的通道傳送和接收資料。
    * 不支援 SMB 3.0 與加密的用戶端可以在資料中心內透過 SMB 2.1 或 SMB 3.0 進行通訊，而不需要加密。 不允許 SMB 用戶端透過 SMB 2.1 或 SMB 3.0 進行無加密的資料中心之間通訊。
    * 用戶端可以藉由 HTTP 或 HTTPS 透過檔案 REST 進行通訊。
* 待用加密 ([Azure 儲存體服務加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json))：為所有儲存體帳戶啟用「儲存體服務加密」(SSE)。 靜止資料是使用完全受控金鑰加密。 待用加密不會增加儲存成本或降低效能。 
* 傳輸中加密資料的選擇性需求：選取時，Azure 檔案儲存體拒絕透過未加密的通道存取資料。 具體來說，只允許具有加密連線的 HTTPS 和 SMB 3.0。

    > [!Important]  
    > 要求資料安全傳輸將導致舊版 SMB 用戶端通訊失敗，因為它無法與加密的 SMB 3.0 通訊。 如需詳細資訊，請參閱[掛接在 Windows 上](storage-how-to-use-files-windows.md)、[掛接在 Linux 上](storage-how-to-use-files-linux.md)、[掛接在 macOS 上](storage-how-to-use-files-mac.md)。

為了有最高安全性，當您使用新式用戶端來存取資料時，強烈建議您一律啟用加密待用資料與加密傳輸資料。 例如，如果您需要在僅支援 SMB 2.1 的 Windows Server 2008 R2 VM 上掛接共用，因為 SMB 2.1 不支持加密，因此儲存體帳戶必須允許未加密的流量。

如果您使用 Azure 檔案同步來存取 Azure 檔案共用，則無論是否需要對待用資料進行加密，我們一律會使用加密的 HTTPS 和 SMB 3.0 將資料同步處理至 Windows Server。

## <a name="file-share-performance-tiers"></a>檔案共用效能層級

Azure 檔案儲存體提供兩個效能層級： standard 和 premium。

### <a name="standard-file-shares"></a>標準檔案共用

標準檔案共用是由硬碟（Hdd）所支援。 標準檔案共用可針對效能變化較不敏感的 IO 工作負載提供可靠的效能，例如一般用途的檔案共用和開發/測試環境。 標準檔案共用僅適用於隨用隨付計費模型。

> [!IMPORTANT]
> 如果您想要使用大於 5 TiB 的檔案共用，請參閱上架[至較大的檔案共用（標準層）](#onboard-to-larger-file-shares-standard-tier)一節，以取得上架的步驟，以及區域可用性和限制。

### <a name="premium-file-shares"></a>Premium 檔案共用

Premium 檔案共用是由固態硬碟（Ssd）支援。 高階檔案共用可針對 IO 密集型工作負載，在大部分 IO 作業的單一位數毫秒內，提供一致的高效能和低延遲。 這讓它們適用于各種不同的工作負載，例如資料庫、網站裝載和開發環境。 進階檔案共用僅適用於佈建計費模型。 Premium 檔案共用會使用與標準檔案共用分開的部署模型。

Azure 備份適用于 premium 檔案共用，Azure Kubernetes Service 支援1.13 版和更新版本中的高階檔案共用。

如果您想要瞭解如何建立 premium 檔案共用，請參閱主題的文章：[如何建立 Azure premium 檔案儲存體帳戶](storage-how-to-create-premium-fileshare.md)。

目前，您無法直接在標準檔案共用和 premium 檔案共用之間轉換。 如果您想要切換至任一層，您必須在該階層中建立新的檔案共用，並手動將資料從原始共用複製到您建立的新共用。 您可以使用任何 Azure 檔案儲存體支援的複製工具（例如 Robocopy 或 AzCopy）來執行這項操作。

> [!IMPORTANT]
> 在大部分區域中，提供儲存體帳戶和 ZRS 的高階檔案共用在較小的區域子集中，可供 LRS 使用。 若要找出您的區域目前是否有 premium 檔案共用，請參閱 Azure 的[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)頁面。 如需有關支援 ZRS 之區域的詳細資訊，請參閱[Azure 儲存體冗余](../common/storage-redundancy.md)。
>
> 為協助我們設定新區域和進階層功能的優先順序，請填寫這[份問卷](https://aka.ms/pfsfeedback)。

#### <a name="provisioned-shares"></a>佈建共用

進階檔案共用會以固定的 GiB/IOPS/輸送量比例為基礎佈建。 對於每個佈建的 GiB，共用將會發出一個 IOPS 和 0.1 MiB/秒輸送量，直到每個共用的上限為止。 允許佈建的最小值為 100 GiB 與最小 IOPS/輸送量。

在盡可能達成的基礎上，每個佈建之儲存體 Gib 的所有共用都可高載至最多三個 IOPS，並持續 60 分鐘或更久的時間 (視共用大小而定)。 新的共用一開始有以佈建容量為基礎的完整高載額度。

共用必須以 1 GiB 遞增的方式布建。 大小下限為 100 GiB，下一個大小為 101 GiB 等等。

> [!TIP]
> 基準 IOPS = 1 * 已布建的 GiB。 （最多 100000 IOPS）。
>
> 高載限制 = 3 * 基準 IOPS。 （最多 100000 IOPS）。
>
> 輸出速率 = 60 MiB/秒 + 0.06 * 已布建的 GiB
>
> 輸入速率 = 40 MiB/秒 + 0.04 * 已布建的 GiB

布建的共用大小是由共用配額指定。 共用配額可以隨時增加，但只能在上一次增加後的24小時內減少。 等待24小時後，如果沒有增加配額，您可以視需要多次減少共用配額，直到您再次增加為止。 IOPS/輸送量調整變更將于大小變更後的幾分鐘內生效。

您可以將布建的共用大小減少到您使用的 GiB 下方。 如果您這樣做，您將不會遺失資料，但仍會向您收取所用大小的費用，並接收已布建共用的效能（基準 IOPS、輸送量和高載 IOPS），而不是使用的大小。

下表說明這些 homebrew 公式針對布建共用大小所提供的幾個範例：

|容量（GiB） | 基準 IOPS | 高載 IOPS | 輸出（MiB/秒） | 輸入（MiB/秒） |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | 最多1500   | 90   | 60   |
|1,024       | 1,024   | 最多3072   | 122   | 81   |
|5,120       | 5,120   | 最多15360  | 368   | 245   |
|10240      | 10240  | 最多30720  | 675 | 450   |
|33792      | 33792  | 最多100000 | 2088 | 1392   |
|51200      | 51200  | 最多100000 | 3,132 | 2088   |
|102400     | 100,000 | 最多100000 | 6204 | 4136   |

> [!NOTE]
> 檔案共用效能受限於機器網路限制、可用的網路頻寬、IO 大小、平行處理，還有許多其他因素。 例如，根據具有8個 KiB 讀取/寫入 IO 大小的內部測試，透過 SMB 連線到 premium 檔案共用的單一 Windows 虛擬機器*標準 F16s_v2*，可以達到20K 的讀取 IOPS 和15K 寫入 iops。 使用 512 MiB 讀取/寫入 IO 大小，相同的 VM 可以達到 1.1 GiB/s 輸出，以及 370 MiB/s 輸入輸送量。 若要達到最大效能等級，請將負載分散到多個 Vm。 請參閱[疑難排解指南](storage-troubleshooting-files-performance.md)，以瞭解一些常見的效能問題和因應措施。

#### <a name="bursting"></a>負載平衡

高階檔案共用可以將其 IOPS 高載至三倍。 高載會自動化，並根據信用系統運作。 高載會以最大的方式運作，而且高載限制並不保證，檔案共用*最多可達*限制。

當您檔案共用的流量低於基準 IOPS 時，點數會累積在高載值區中。 例如，100 GiB 共用具有100基準 IOPS。 如果共用上的實際流量為特定1秒間隔的 40 IOPS，則會將60未使用的 IOPS 貸到高載值區。 之後，當作業會超過基準 IOPs 時，就會使用這些信用額度。

> [!TIP]
> 高載 bucket 的大小 = 基準 IOPS * 2 * 3600。

每當共用超過基準 IOPS，而且在高載 bucket 中有信用額度時，就會進行高載。 只要剩餘信用額度，共用就可以繼續高載，但是小於 50 TiB 的共用則只會保持高載限制達一小時。 在技術上，大於 50 TiB 的共用可能會超過此一小時的限制，最多兩個小時，但這是根據所累積的高載點數數目。 除了基準 IOPS 以外的每個 IO 都會取用一個點數，一旦取用所有信用額度，該共用就會回到基準 IOPS。

共用信用額度有三種狀態：

- 當檔案共用使用低於基準 IOPS 時產生。
- 在檔案共用進行高載時拒絕。
- 當沒有任何點數或基準 IOPS 正在使用時，剩餘的常數。

新的檔案共用會從其高載值區中的完整點數開始。 如果由於伺服器進行節流，所以共用 IOPS 低於基準 IOPS 時，將不會產生高載點數。

## <a name="file-share-redundancy"></a>檔案共用備援

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

如果您選擇讀取權限異地多餘儲存體（RA-GRS），您應該知道 Azure 檔案目前不支援任何區域中的讀取權限異地多餘儲存體（RA-GRS）。 GRS 儲存體帳戶中的檔案共用的工作方式與在 GRS 帳戶中一樣，而且會以 GRS 價格收費。

> [!Warning]  
> 若您使用 Azure 檔案共用作為 GRS 儲存體帳戶中的雲端端點，您不應該啟動儲存體帳戶的容錯移轉。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。 若發生 Azure 區域遺失，Microsoft 將會觸發與 Azure 檔案同步相容的儲存體帳戶容錯移轉。

Azure 檔案儲存體 premium 共用同時支援 LRS 和 ZRS，但 ZRS 目前可用於較小的區域子集。

## <a name="onboard-to-larger-file-shares-standard-tier"></a>上架至較大的檔案共用（標準層）

本節僅適用于標準檔案共用。 所有 premium 檔案共用都有 100 TiB 容量可供使用。

### <a name="restrictions"></a>限制

- 對於已啟用大型檔案共用的任何儲存體帳戶，將無法使用 LRS/ZRS to GRS/切換帳戶轉換。

### <a name="regional-availability"></a>區域可用性

具有 100 TiB 容量限制的標準檔案共用，可在所有 Azure 區域中全域取得-

- LRS：除了南非北部、南非西部、德國中西部和德國北部以外的所有區域。
- ZRS：除了日本東部、北歐、南非北部以外的所有區域。
- GRS/切換：不支援。

### <a name="enable-and-create-larger-file-shares"></a>啟用和建立較大的檔案共用

若要開始使用較大的檔案共用，請參閱[如何啟用和建立大型檔案共用](storage-files-how-to-create-large-file-share.md)一文。

## <a name="data-growth-pattern"></a>資料成長模式

目前，Azure 檔案共用的大小上限為 100 TiB。 由於目前的此一限制，當您在部署 Azure 檔案共用時，必須考量預期的資料成長。

您可以使用 Azure 檔案同步，將多個 Azure 檔案共用同步處理到單一 Windows 檔案伺服器。這可讓您確保內部部署中較舊的大型檔案共用可以帶入 Azure 檔案同步。如需詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-files-planning.md)。

## <a name="data-transfer-method"></a>資料傳輸方法

有許多簡單的選項可從現有檔案共用 (例如內部部署檔案共用) 大量傳輸資料到 Azure 檔案服務。 部分常用方法包括 (非完整清單)：

* **[Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** ：在 Azure 檔案共用（「雲端端點」）和 Windows 目錄命名空間（「伺服器端點」）之間的第一次同步處理過程中，Azure 檔案同步會從現有的檔案共用將所有資料複寫至 Azure 檔案儲存體。
* **[Azure 匯入/匯出](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：Azure 匯入/匯出服務可讓您將硬碟寄送到 Azure 資料中心，以便將大量資料安全地傳入 Azure 檔案共用。 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ：Robocopy 是知名的複製工具，隨附於 Windows 和 Windows Server。 Robocopy 可在本機掛接檔案共用，然後將掛接位置作為 Robocopy 命令中的目的地使用，以將資料傳輸到 Azure 檔案服務中。
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：AzCopy 是命令列公用程式，設計為使用最佳效能的簡單命令，將資料複製到 Azure 檔案服務與 Azure Blob 儲存體，以及從其中複製資料。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
