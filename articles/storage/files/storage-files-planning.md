---
title: 規劃 Azure 檔案服務部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502444"
---
# <a name="planning-for-an-azure-files-deployment"></a>規劃 Azure 檔案服務部署
[Azure 檔](storage-files-introduction.md)可通過兩種主要方式進行部署：直接安裝無伺服器 Azure 檔共用或使用 Azure 檔同步在本機快取 Azure 檔共用。您選擇的部署選項會更改在規劃部署時需要考慮的事項。 

- **Azure 檔共用的直接裝載**：由於 Azure 檔提供 SMB 訪問，因此可以使用 Windows、macOS 和 Linux 中可用的標準 SMB 用戶端在本地或雲中裝載 Azure 檔共用。 由於 Azure 檔共用是無伺服器的，因此為生產方案部署不需要管理檔案伺服器或 NAS 設備。 這意味著您不必應用軟體修補程式或交換物理磁片。 

- **使用 Azure 檔同步在本機快取 Azure 檔共用**：Azure 檔同步使您能夠在 Azure 檔中集中組織的檔共用，同時保持本地檔案伺服器的靈活性、性能和相容性。 Azure 檔同步將本地（或雲）Windows 伺服器轉換為 Azure 檔共用的快速緩存。 

本文主要討論部署由本地或雲用戶端直接裝載的 Azure 檔共用的部署注意事項。 要規劃 Azure 檔同步部署，請參閱[規劃 Azure 檔同步部署](storage-sync-files-planning.md)。

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

將 Azure 檔共用部署到存儲帳戶時，我們建議：

- 僅將 Azure 檔共用部署到具有其他 Azure 檔共用的存儲帳戶中。 儘管 GPv2 存儲帳戶允許您具有混合用途存儲帳戶，但由於存儲資源（如 Azure 檔共用和 Blob 容器）共用存儲帳戶的限制，因此將資源混合在一起可能會加大故障排除的難度稍後，性能問題。 

- 在部署 Azure 檔共用時，注意存儲帳戶的 IOPS 限制。 理想情況下，您將檔共用 1：1 映射到存儲帳戶，但由於組織與 Azure 的各種限制和限制，這並不總是可能的。 如果不可能在一個存儲帳戶中只部署一個檔共用，請考慮哪些共用將高度活躍，哪些共用將不太活躍，以確保最熱門的檔共用不會放在同一個存儲帳戶中。

- 僅在環境中找到 GPv2 和檔存儲帳戶並升級 GPv1 和經典存儲帳戶。 

## <a name="identity"></a>身分識別
要訪問 Azure 檔共用，必須對檔共用的使用者進行身份驗證並具有訪問共用的授權。 這是基於訪問檔共用的使用者的標識完成的。 Azure 檔與三個主要標識提供程式集成：
- **客戶擁有的活動目錄**（預覽）：Azure 存儲帳戶可以像 Windows 伺服器檔案伺服器或 NAS 設備一樣，加入到客戶擁有的 Windows Server 活動目錄的域。 活動目錄網域控制站可以部署在本地、Azure VM 中，甚至可以作為其他雲供應商中的 VM 進行部署;Azure 檔與 DC 的託管位置無關。 加入存儲帳戶後，最終使用者就可以將檔共用與登錄到其 PC 中的使用者帳戶一起裝載。 基於 AD 的身份驗證使用 Kerberos 身份驗證協定。
- **Azure 活動目錄域服務 （Azure AD DS）：** Azure AD DS 提供 Microsoft 管理的活動目錄網域控制站，可用於 Azure 資源。 將存儲帳戶加入 Azure AD DS 的域與將存儲帳戶加入客戶擁有的 Active Directory 的域提供了類似的好處。 此部署選項對於需要基於 AD 的許可權的應用程式升降和移位方案最有用。 由於 Azure AD DS 提供基於 AD 的身份驗證，此選項還使用 Kerberos 身份驗證協定。
- **Azure 存儲帳戶金鑰**：Azure 檔共用也可以裝載 Azure 存儲帳戶金鑰。 要以這種方式裝載檔共用，存儲帳戶名稱用作使用者名，存儲帳戶金鑰用作密碼。 使用存儲帳戶金鑰裝載 Azure 檔共用實際上是管理員操作，因為裝載的檔共用將對共用上的所有檔和資料夾具有完全許可權，即使它們具有 ACL。 當使用存儲帳戶金鑰裝載到 SMB 上時，將使用 NTLMv2 身份驗證協定。

對於從本地檔案伺服器遷移的客戶，或在 Azure 檔中創建新的檔共用，以像 Windows 檔案伺服器或 NAS 設備一樣，建議選擇將存儲帳戶加入**客戶擁有的 Active Directory 的**域。 要瞭解有關域將存儲帳戶加入客戶擁有的活動目錄的詳細資訊，請參閱[Azure 檔活動目錄概述](storage-files-active-directory-overview.md)。

如果要使用存儲帳戶金鑰訪問 Azure 檔共用，我們建議使用["網路](#networking)"部分中所述的服務終結點。

## <a name="networking"></a>網路
Azure 檔共用可通過存儲帳戶的公共終結點從任何位置訪問。 這意味著經過身份驗證的請求（如使用者登錄標識授權的請求）可以安全地源自 Azure 的內部和外部。 在許多客戶環境中，即使 Azure VM 的裝載成功，本地工作站上 Azure 檔共用的初始裝載也會失敗。 原因是許多組織和 Internet 服務提供者 （ISP） 阻止 SMB 用於通信的埠埠 445。 若要查看 ISP 是否允許從連接埠 445 進行存取的摘要，請參閱 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) \(英文\)。

要取消阻止對 Azure 檔共用的訪問，有兩個主要選項：

- 為組織的本地網路取消阻止埠 445。 Azure 檔共用只能通過公共終結點使用 Internet 安全協定（如 SMB 3.0 和 FileREST API）進行外部訪問。 這是從本地訪問 Azure 檔共用的最簡單方法，因為它除了更改組織的出站連接埠規則之外不需要高級網路設定，但是，我們建議您刪除 SMB 的舊版本和棄用版本協定，即 SMB 1.0。 要瞭解如何執行此操作，請參閱[保護 Windows/Windows 伺服器](storage-how-to-use-files-windows.md#securing-windowswindows-server)和保護[Linux](storage-how-to-use-files-linux.md#securing-linux)。

- 通過 ExpressRoute 或 VPN 連接訪問 Azure 檔共用。 通過網路隧道訪問 Azure 檔共用時，可以像本地檔共用一樣裝載 Azure 檔共用，因為 SMB 流量不會遍歷組織邊界。   

儘管從技術角度來說，通過公共終結點裝載 Azure 檔共用要容易得多，但我們預計大多數客戶將選擇通過 ExpressRoute 或 VPN 連接裝載其 Azure 檔共用。 為此，您需要為環境配置以下內容：  

- **使用 ExpressRoute、網站到網站或點到網站 VPN**的網路隧道：隧道到虛擬網路允許從本地訪問 Azure 檔共用，即使埠 445 被阻止也是如此。
- **專用終結點**：專用終結點從虛擬網路的位址空間內為您的存儲帳戶提供私人 IP 位址。 這支援網路隧道，而無需打開本地網路，最多滿足 Azure 存儲群集擁有的所有 IP 位址範圍。 
- **DNS 轉發**：配置本地 DNS 以解析存儲帳戶的名稱（即`storageaccount.file.core.windows.net`公共雲區域），以解析為專用終結點的 IP 位址。

要規劃與部署 Azure 檔共用關聯的網路，請參閱[Azure 檔網路注意事項](storage-files-networking-overview.md)。

## <a name="encryption"></a>加密
Azure 檔支援兩種不同類型的加密：傳輸中的加密（與裝載/訪問 Azure 檔共用時使用的加密相關）和靜態加密，與資料存儲在磁片上時資料的加密方式有關。 

### <a name="encryption-in-transit"></a>傳輸中加密
根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 這表示當您透過 SMB 掛接檔案共用或透過 FileREST 通訊協定 (例如，透過 Azure 入口網站、PowerShell/CLI 或 Azure SDK) 來存取檔案共用時，Azure 檔案儲存體只會在使用了 SMB 3.0 以上 (有加密功能) 或 HTTPS 來進行連線時，才會允許您建立連線。 不支援 SMB 3.0 的用戶端，或雖支援 SMB 3.0 但不支援 SMB 加密的用戶端，將無法在啟用了傳輸中加密的情況下掛接 Azure 檔案共用。 如需哪些作業系統支援 SMB 3.0 (有加密功能) 的詳細資訊，請參閱適用於 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的詳細文件。 所有目前版本的 PowerShell、CLI 和 SDK 都支援 HTTPS。  

您可以為 Azure 儲存體帳戶停用傳輸中加密。 禁用加密後，Azure 檔還將允許 SMB 2.1、SMB 3.0 不加密，以及通過 HTTP 進行未加密的檔 REST API 呼叫。 會停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件。 Azure 檔案儲存體只會在與 Azure 檔案共用相同的 Azure 區域內允許 SMB 2.1 連線；Azure 檔案共用所在的 Azure 區域外 (例如，內部部署或不同 Azure 區域) 的 SMB 2.1 用戶端，則無法存取檔案共用。

我們強烈建議確保啟用傳輸中資料的加密。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>待用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

通常，Azure 檔功能與其他服務的互通性在高級檔共用和標準檔共用之間是相同的，但是有一些重要區別：
- **計費模式**
    - 高級檔共用使用預配計費模型計費，這意味著您需要為預配的存儲量而不是實際要求多少存儲付費。 
    - 標準檔共用使用即用即付模型計費，該模型包括實際消耗的存儲量的基本存放裝置成本，然後根據您如何使用共用來支付額外的事務成本。 使用標準檔共用時，如果使用 Azure 檔共用更多內容，則帳單將增加。
- **冗余選項**
    - 高級檔共用僅適用于本地冗余 （LRS） 和區域冗余 （ZRS） 存儲。 
    - 標準檔共用可用於本地冗余、區域冗余、地理冗余 （GRS） 和地理區域冗余 （GZRS） 存儲。
- **檔共用的最大大小**
    - 高級檔共用可以預配多達 100 TiB，而無需任何其他工作。
    - 預設情況下，標準檔共用只能跨越 5 TiB，儘管通過選擇進入*大型檔共用*存儲帳戶功能標誌，共用限制可以增加到 100 TiB。 對於本地冗余或區域冗余存儲帳戶，標準檔共用可能最多隻能跨越 100 TiB。 有關增加檔共用大小的詳細資訊，請參閱[啟用和創建大型檔共用](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share)。
- **區域可用性**
    - 高級檔共用並非在每個區域都可用，區域冗余支援在較小的區域子集中可用。 要瞭解您所在地區當前是否提供高級檔共用，請參閱 Azure 按[區域可用的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)頁面。 要瞭解哪些區域支援 ZRS，請參閱[Azure 可用性區域支援區域](../../availability-zones/az-overview.md#services-support-by-region)。 為了説明我們確定新區域和高級層功能的優先順序，請填寫此[調查](https://aka.ms/pfsfeedback)。
    - 標準檔共用在每個 Azure 區域中都可用。
- Azure 庫伯奈斯服務 （AKS） 支援版本 1.13 及更高版本中的高級檔共用。

一旦檔共用創建為高級檔共用或標準檔共用，則無法自動將其轉換為其他層。 如果要切換到其他層，則必須在該層中創建新的檔共用，並手動將資料從原始共用複製到您創建的新共用。 我們建議使用`robocopy`Windows 或`rsync`macOS 和 Linux 來執行該副本。

### <a name="understanding-provisioning-for-premium-file-shares"></a>瞭解高級檔共用的預配
進階檔案共用會以固定的 GiB/IOPS/輸送量比例為基礎佈建。 對於每個佈建的 GiB，共用將會發出一個 IOPS 和 0.1 MiB/秒輸送量，直到每個共用的上限為止。 允許佈建的最小值為 100 GiB 與最小 IOPS/輸送量。

在盡可能達成的基礎上，每個佈建之儲存體 Gib 的所有共用都可高載至最多三個 IOPS，並持續 60 分鐘或更久的時間 (視共用大小而定)。 新的共用一開始有以佈建容量為基礎的完整高載額度。

必須以 1 GiB 增量預配股票。 最小尺寸為 100 GiB，下一個大小為 101 GiB 等。

> [!TIP]
> 基線 IOPS = 1 = 預配 GiB。 （最多 100，000 IOPS）。
>
> 突發限制 = 3 = 基線 IOPS。 （最多 100，000 IOPS）。
>
> 出位率 = 60 MiB/s = 0.06 = 預配 GiB
>
> 入口速率 = 40 MiB/s = 0.04 = 預配 GiB

預配的共用大小由共用配額指定。 股票配額可以隨時增加，但自上次增加後 24 小時內才能減少。 等待 24 小時後，無需增加配額，您可以根據需要多次減少份額配額，直到再次增加份額。 IOPS/輸送量規模更改將在大小更改後的幾分鐘內生效。

可以減小預配份額的大小，低於已使用的 GiB。 如果這樣做，您不會遺失資料，但您仍將按使用的大小計費，並接收預配共用的性能（基線 IOPS、輸送量和突發 IOPS），而不是使用的大小。

下表演示了預配共用大小的這些公式的幾個示例：

|容量 （GiB） | 基準 IOPS | 突發 IOPS | 出口（米B/s） | 入口（米B/s） |
|---------|---------|---------|---------|---------|
|100         | 100     | 最多 300     | 66   | 44   |
|500         | 500     | 高達 1，500   | 90   | 60   |
|1,024       | 1,024   | 高達 3，072   | 122   | 81   |
|5,120       | 5,120   | 高達 15，360  | 368   | 245   |
|10,240      | 10,240  | 高達 30，720  | 675 | 450   |
|33,792      | 33,792  | 高達 100，000 | 2,088 | 1,392   |
|51,200      | 51,200  | 高達 100，000 | 3,132 | 2,088   |
|102,400     | 100,000 | 高達 100，000 | 6,204 | 4,136   |

> [!NOTE]
> 檔共用性能受電腦網路限制、可用網路頻寬、IO 大小、並行性等諸多因素影響。 例如，基於具有 8 個 KiB 讀/寫 IO 大小的內部測試，單個 Windows 虛擬機器 *"標準F16s_v2"* 連接到 SMB 上的高級檔共用可以實現 20K 讀取 IOPS 和 15K 寫入 IOPS。 使用 512 MiB 讀取/寫入 IO 大小，相同的 VM 可實現 1.1 GiB/s 出口和 370 MiB/s 入口輸送量。 要實現最大的性能擴展，可以將負載分散到多個 VM 上。 有關一些常見的性能問題和解決方法，請參閱[故障排除指南](storage-troubleshooting-files-performance.md)。

#### <a name="bursting"></a>爆破
高級檔共用可以突發其 IOPS 最多達三倍。 爆破是自動化的，並且基於信用系統運行。 爆破工作在盡力的基礎上，突發限制不是保證，檔共用可以爆裂*到*極限。

每當檔共用的流量低於基線 IOPS 時，積分都會在突發存儲桶中累積。 例如，100 GiB 共用具有 100 個基線 IOPS。 如果共用上的實際流量為 40 IOPS，則為特定 1 秒間隔，則 60 個未使用的 IOPS 將記入突發存儲桶。 然後，當操作超過基準 IIP 時，這些積分將隨後使用。

> [!TIP]
> 突發存儲桶的大小 = 基線 IOPS = 2 = 3600。

每當共用超過基線 IOPS 並在突發存儲桶中具有積分時，它將突發。 只要剩餘積分，股票可以繼續爆發，但低於 50 TiB 的股票將僅停留在破發限制下長達一小時。 大於 50 TiB 的股票在技術上可以超過此一小時限制，最多兩小時，但是，這是基於累積的突發積分數。 超出基準 IOPS 的每個 IO 消耗一個積分，一旦用完所有積分，該份額將返回到基準 IOPS。

共用積分有三種狀態：

- 當檔共用使用小於基線 IOPS 時，應計。
- 當檔共用崩潰時，拒絕。
- 保持不變，當沒有積分或基線 IOPS 正在使用時。

新的檔共用從突發存儲桶中的全部積分開始。 如果由於伺服器限制而共用 IOPS 低於基準 IOPS，則不會累積突發積分。

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>使標準檔共用跨越多達 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>區域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>移轉
在許多情況下，您不會為組織建立淨新檔共用，而是將現有檔共用從本地檔案伺服器或 NAS 設備遷移到 Azure 檔。 有許多工具（由 Microsoft 和協力廠商提供）用於對檔共用進行遷移，但它們大致可以分為兩類：

- **維護檔案系統屬性（如 ACL 和時間戳記）的工具**：
    - **[Azure 檔同步](storage-sync-files-planning.md)**：Azure 檔同步可用作將資料引入 Azure 檔共用的方法，即使所需的結束部署不是維護本地狀態也是如此。 Azure 檔同步可以安裝在現有的 Windows 伺服器 2012 R2、Windows 伺服器 2016 和 Windows 伺服器 2019 部署上。 使用 Azure 檔同步作為引入機制的一個優點是，最終使用者可以繼續使用現有的檔共用;在後臺完成所有資料上載後，可能會將共用到 Azure 檔共用。
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**： Robocopy 是一個眾所周知的複製工具，隨 Windows 和 Windows 伺服器一起提供。 Robocopy 可在本機掛接檔案共用，然後將掛接位置作為 Robocopy 命令中的目的地使用，以將資料傳輸到 Azure 檔案服務中。

- **不維護檔案系統屬性的工具**：
    - **資料框**：資料框提供離線資料傳輸機制，將資料發送到 Azure。 此方法旨在增加輸送量和節省頻寬，但當前不支援檔案系統屬性（如時間戳記和 ACL）。
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**：AzCopy 是命令列公用程式，設計為使用最佳效能的簡單命令，將資料複製到 Azure 檔案服務與 Azure Blob 儲存體，以及從其中複製資料。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
