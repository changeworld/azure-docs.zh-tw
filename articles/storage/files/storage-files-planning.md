---
title: 規劃 Azure 檔案服務部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 88c35b7b1420b5d89f9215f7da3ccf24870024e9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597777"
---
# <a name="planning-for-an-azure-files-deployment"></a>規劃 Azure 檔案服務部署
[Azure 檔案儲存體](storage-files-introduction.md)可以透過兩種主要方式來部署：直接裝載無伺服器 Azure 檔案共用，或使用 Azure 檔案同步快取內部部署的 Azure 檔案共用。您所選擇的部署選項會變更規劃部署時所需考慮的事項。 

- **直接掛接 Azure 檔案共用**：因為 Azure 檔案儲存體會提供 SMB 存取，所以您可以使用 Windows、MacOS 和 Linux 中提供的標準 SMB 用戶端，在內部部署或雲端中掛接 azure 檔案共用。 由於 Azure 檔案共用是無伺服器的，因此針對生產案例進行部署並不需要管理檔案伺服器或 NAS 裝置。 這表示您不需要套用軟體修補程式或交換實體磁片。 

- **使用 Azure 檔案同步**來快取內部部署的 Azure 檔案共用： Azure 檔案同步可讓您將組織的檔案共用集中在 Azure 檔案儲存體，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步將內部部署（或雲端） Windows Server 轉換成 Azure 檔案共用的快速快取。 

本文主要針對部署 Azure 檔案共用以直接由內部部署或雲端用戶端掛接的部署考慮。 若要規劃 Azure 檔案同步部署，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)。

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

將 Azure 檔案共用部署到儲存體帳戶時，我們建議：

- 僅將 Azure 檔案共用部署到具有其他 Azure 檔案共用的儲存體帳戶。 雖然 GPv2 儲存體帳戶可讓您擁有混合用途的儲存體帳戶，但因為 Azure 檔案共用和 blob 容器等儲存體資源會共用儲存體帳戶的限制，所以將資源混合在一起可能會使其更難以進行疑難排解稍後的效能問題。 

- 部署 Azure 檔案共用時，請注意儲存體帳戶的 IOPS 限制。 在理想的情況下，您會使用儲存體帳戶對應檔案共用1:1，不過，這可能不一定是因為您的組織和 Azure 有各種限制和限制。 當一個儲存體帳戶中不可能只部署一個檔案共用時，請考慮哪些共用會高度使用，哪些共用會較不活躍，以確保最熱門的檔案共用不會放在同一個儲存體帳戶中。

- 只有當您在環境中找到 GPv2 和 FileStorage 帳戶，並升級 GPv1 和傳統儲存體帳戶時，才可將其部署。 

## <a name="identity"></a>身分識別
若要存取 Azure 檔案共用，檔案共用的使用者必須經過驗證，並具有存取共用的授權。 這是根據存取檔案共用之使用者的身分識別來完成。 Azure 檔案儲存體整合了三個主要身分識別提供者：
- **客戶擁有的 Active Directory** （預覽）： Azure 儲存體帳戶可以加入客戶擁有的 windows server Active Directory，就像 windows server 檔案伺服器或 NAS 裝置一樣。 您的 Active Directory 網網域控制站可以部署于內部部署、Azure VM，或甚至是另一個雲端提供者中的 VM;Azure 檔案儲存體與裝載 DC 的位置無關。 一旦儲存體帳戶加入網域之後，使用者就可以使用登入其電腦的使用者帳戶來掛接檔案共用。 以 AD 為基礎的驗證會使用 Kerberos 驗證通訊協定。
- **Azure Active Directory Domain Services （AZURE AD ds）** ： Azure AD DS 提供可用於 Azure 資源的 Microsoft 管理的 Active Directory 網網域控制站。 將您的儲存體帳戶加入至 Azure AD DS 的網域，可提供將其加入至客戶所擁有之 Active Directory 的類似優點。 此部署選項最適合需要以 AD 為基礎之許可權的應用程式隨即轉移案例。 由於 Azure AD DS 提供以 AD 為基礎的驗證，因此此選項也會使用 Kerberos 驗證通訊協定。
- **Azure 儲存體帳戶金鑰**： azure 檔案共用可能也會使用 azure 儲存體帳戶金鑰來裝載。 為了以這種方式掛接檔案共用，會使用儲存體帳戶名稱做為使用者名稱，並使用儲存體帳戶金鑰作為密碼。 使用儲存體帳戶金鑰來裝載 Azure 檔案共用實際上是系統管理員作業，因為掛接的檔案共用將擁有共用上所有檔案和資料夾的完整許可權，即使他們有 Acl 也一樣。 使用儲存體帳戶金鑰透過 SMB 掛接時，會使用 NTLMv2 驗證通訊協定。

針對從內部部署檔案伺服器進行遷移，或在 Azure 檔案儲存體中建立新的檔案共用，以作為 Windows 檔案伺服器或 NAS 應用裝置的行為，建議您將儲存體帳戶加入至**客戶擁有的 Active Directory**網域。 若要深入瞭解將您的儲存體帳戶加入至客戶擁有之 Active Directory 的網域，請參閱[Azure 檔案儲存體 Active Directory 總覽](storage-files-active-directory-overview.md)。

如果您想要使用儲存體帳戶金鑰來存取 Azure 檔案共用，建議使用「[網路](#networking)功能」一節中所述的服務端點。

## <a name="networking"></a>網路功能
您可以透過儲存體帳戶的公用端點，從任何地方存取 Azure 檔案共用。 這表示已驗證的要求（例如使用者登入身分識別所授權的要求）可以從 Azure 內部或外部安全地產生。 在許多客戶環境中，Azure 檔案共用在內部部署工作站上的初始掛接將會失敗，即使 Azure Vm 的裝載成功也一樣。 這是因為許多組織和網際網路服務提供者（Isp）會封鎖 SMB 用來通訊的埠（埠445）。 

若要解除封鎖 Azure 檔案共用的存取，您有兩個主要選項：

- 為組織的內部部署網路解除封鎖埠445。 Azure 檔案共用只能透過公用端點，使用 SMB 3.0 和 FileREST API 等網際網路安全通訊協定從外部存取。 這是從內部部署存取 Azure 檔案共用的最簡單方式，因為它不需要額外的網路設定，而只是變更您組織的輸出連接埠規則，不過，我們建議您移除舊版和過時的 SMB 版本通訊協定，亦即 SMB 1.0。 若要瞭解如何執行此操作，請參閱[保護 Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)和[保護 Linux](storage-how-to-use-files-linux.md#securing-linux)。

- 透過 ExpressRoute 或 VPN 連線存取 Azure 檔案共用。 當您透過網路通道存取 Azure 檔案共用時，您可以掛接 Azure 檔案共用，例如內部部署檔案共用，因為 SMB 流量不會跨越您的組織界限。   

雖然從技術觀點來看，透過公用端點掛接 Azure 檔案共用會變得更容易，我們預期大部分的客戶會選擇透過 ExpressRoute 或 VPN 連線來掛接其 Azure 檔案共用。 若要這樣做，您必須為您的環境設定下列各項：  

- **使用 ExpressRoute、站對站或點對站 VPN 的網路通道**：在虛擬網路中的通道可讓您從內部部署存取 Azure 檔案共用，即使已封鎖埠445也一樣。
- **私人端點**：私人端點會從虛擬網路的位址空間中，為您的儲存體帳戶提供專用的 IP 位址。 這會啟用網路通道，而不需要開啟 Azure 儲存體叢集所擁有的所有 IP 位址範圍的內部部署網路。 
- **DNS 轉送**：設定您的內部部署 DNS 來解析儲存體帳戶的名稱（也就是公用雲端區域的 `storageaccount.file.core.windows.net`），以解析為私人端點的 IP 位址。

若要規劃與部署 Azure 檔案共用相關聯的網路功能，請參閱[Azure 檔案儲存體的網路功能考慮](storage-files-networking-overview.md)。

## <a name="encryption"></a>加密
Azure 檔案儲存體支援兩種不同的加密類型：傳輸中的加密，與掛接/存取 Azure 檔案共用時所使用的加密相關，以及待用加密（與資料儲存在磁片上時的加密方式相關）。 

### <a name="encryption-in-transit"></a>傳輸中加密
根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 這表示當您透過 SMB 掛接檔案共用或透過 FileREST 通訊協定 (例如，透過 Azure 入口網站、PowerShell/CLI 或 Azure SDK) 來存取檔案共用時，Azure 檔案儲存體只會在使用了 SMB 3.0 以上 (有加密功能) 或 HTTPS 來進行連線時，才會允許您建立連線。 不支援 SMB 3.0 的用戶端，或雖支援 SMB 3.0 但不支援 SMB 加密的用戶端，將無法在啟用了傳輸中加密的情況下掛接 Azure 檔案共用。 如需哪些作業系統支援 SMB 3.0 (有加密功能) 的詳細資訊，請參閱適用於 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的詳細文件。 所有目前版本的 PowerShell、CLI 和 SDK 都支援 HTTPS。  

您可以為 Azure 儲存體帳戶停用傳輸中加密。 停用加密時，Azure 檔案儲存體也會允許 SMB 2.1、沒有加密的 SMB 3.0，以及透過 HTTP 的未加密 FileREST API 呼叫。 會停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件。 Azure 檔案儲存體只會在與 Azure 檔案共用相同的 Azure 區域內允許 SMB 2.1 連線；Azure 檔案共用所在的 Azure 區域外 (例如，內部部署或不同 Azure 區域) 的 SMB 2.1 用戶端，則無法存取檔案共用。

我們強烈建議您確保在傳輸中的資料加密已啟用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>待用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

一般來說，高階檔案共用和標準檔案共用之間 Azure 檔案儲存體功能和與其他服務的互通性，不過有幾個重要的差異：
- **計費模型**
    - Premium 檔案共用會使用已布建的計費模型來計費，這表示您需支付所布建的儲存體數量，而不是您實際要求的儲存體數量。 
    - 標準檔案共用會使用隨用隨付模型來計費，其中包括您實際取用多少儲存體的基本儲存體成本，還有額外的交易成本（根據您使用該共用的方式而定）。 使用標準檔案共用時，如果您使用（讀取/寫入/掛接） Azure 檔案共用，您的帳單將會增加。
- **冗余選項**
    - Premium 檔案共用僅適用于本機多餘（LRS）和區域冗余（ZRS）儲存體。 
    - 標準檔案共用適用于本機多餘、區域冗余、異地冗余（GRS）和異地區域冗余（切換）儲存體。
- **檔案共用的大小上限**
    - Premium 檔案共用最多可以布建到 100 TiB，而不需要任何額外的工作。
    - 根據預設，標準檔案共用最多隻能跨越5個 TiB，不過，藉由選擇 [*大型檔案共用*儲存體帳戶] 功能旗標，可以將共用限制增加到 100 TiB。 標準檔案共用最多隻能跨越 100 TiB，用於本機多餘或區域的多餘儲存體帳戶。 如需增加的詳細資訊  
- **區域可用性**
    - Premium 檔案共用無法在每個區域中使用，且區域的多餘支援可在較小的區域子集中使用。 若要找出您的區域目前是否有 premium 檔案共用，請參閱 Azure 的[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)頁面。 若要瞭解哪些區域支援 ZRS，請參閱[依區域的 Azure 可用性區域支援](../../availability-zones/az-overview.md#services-support-by-region)。 為協助我們設定新區域和進階層功能的優先順序，請填寫這[份問卷](https://aka.ms/pfsfeedback)。
    - 每個 Azure 區域中都有提供標準檔案共用。
- Azure Kubernetes Service （AKS）支援1.13 版和更新版本中的 premium 檔案共用。

一旦將檔案共用建立為 premium 或 standard 檔案共用，您就無法自動將它轉換成另一層。 如果您想要切換至另一層，您必須在該階層中建立新的檔案共用，並手動將資料從原始共用複製到您建立的新共用。 建議使用適用于 Windows 的 `robocopy` 或適用于 macOS 和 Linux 的 `rsync` 來執行該複本。

### <a name="understanding-provisioning-for-premium-file-shares"></a>瞭解 premium 檔案共用的布建
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

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>啟用標準檔案共用以跨越最多 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>區域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>移轉
在許多情況下，您將不會為您的組織建立網路新的檔案共用，而是改為將現有的檔案共用從內部部署檔案伺服器或 NAS 裝置遷移到 Azure 檔案儲存體。 Microsoft 和協力廠商都有提供許多工具，可進行檔案共用的遷移，但大致上可以分成兩個類別：

- **維護檔案系統屬性的工具，例如 acl 和時間戳記**：
    - **[Azure 檔案同步](storage-sync-files-planning.md)** ： Azure 檔案同步可用來做為將資料內嵌至 Azure 檔案共用的方法，即使所需的結束部署不會維持內部部署的狀態也一樣。 Azure 檔案同步可以就地安裝在現有的 Windows Server 2012 R2、Windows Server 2016 和 Windows Server 2019 部署上。 使用 Azure 檔案同步做為內嵌機制的優點是，使用者可以繼續就地使用現有的檔案共用;當所有資料都在背景上傳完成後，就可以切換到 Azure 檔案共用。
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** ： robocopy 是隨附于 Windows 和 windows Server 的知名複製工具。 Robocopy 可在本機掛接檔案共用，然後將掛接位置作為 Robocopy 命令中的目的地使用，以將資料傳輸到 Azure 檔案服務中。

- **不會維護檔案系統屬性的工具**：
    - **資料箱**：資料箱提供離線資料傳輸機制，讓實體將資料傳送到 Azure。 這個方法是設計來增加輸送量並節省頻寬，但目前不支援時間戳記和 Acl 等檔案系統屬性。
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** ：AzCopy 是命令列公用程式，設計為使用最佳效能的簡單命令，將資料複製到 Azure 檔案服務與 Azure Blob 儲存體，以及從其中複製資料。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)