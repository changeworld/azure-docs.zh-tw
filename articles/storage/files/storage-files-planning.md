---
title: 規劃 Azure 檔案服務部署 | Microsoft Docs
description: 瞭解規劃 Azure 檔案儲存體部署。 您可以直接裝載 Azure 檔案共用，或使用 Azure 檔案同步在內部部署環境中快取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 98cc72f85499481ba3841ce82fe307740d5e9fab
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842696"
---
# <a name="planning-for-an-azure-files-deployment"></a>規劃 Azure 檔案服務部署
[Azure 檔案儲存體](storage-files-introduction.md) 可以用兩種主要方式進行部署：直接裝載無伺服器的 azure 檔案共用，或使用 Azure 檔案同步快取內部部署的 azure 檔案共用。您所選擇的部署選項會變更您規劃部署時需要考慮的事項。 

- **Azure 檔案共用的直接掛接**：由於 Azure 檔案儲存體提供伺服器訊息區 (SMB) 或網路檔案系統 (NFS) 存取，因此您可以使用 OS 中可用的標準 SMB 或 NFS 用戶端，在內部部署或雲端中掛接 azure 檔案共用。 由於 Azure 檔案共用是無伺服器的，因此針對生產案例進行部署並不需要管理檔案伺服器或 NAS 裝置。 這表示您不需要套用軟體修補程式或交換實體磁碟。 

- **使用 Azure 檔案同步快取內部部署的 Azure 檔案共用**：Azure 檔案同步可讓您將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步將內部部署 (或雲端) Windows Server 轉換成 Azure SMB 檔案共用的快速快取。 

本文主要討論部署 Azure 檔案共用以直接由內部部署或雲端用戶端裝載的部署考慮。 若要規劃 Azure 檔案同步部署，請參閱 [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)。

## <a name="available-protocols"></a>可用的通訊協定

Azure 檔案儲存體提供兩種通訊協定，可在裝載檔案共用、SMB 和網路檔案系統 (NFS) 時使用。 如需這些通訊協定的詳細資訊，請參閱 [Azure 檔案共用通訊協定](storage-files-compare-protocols.md)。

> [!IMPORTANT]
> 本文的大部分內容僅適用于 SMB 共用。 適用于 NFS 共用的任何作業，都會特別指出它適用。

## <a name="management-concepts"></a>管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

將 Azure 檔案共用部署至儲存體帳戶時，建議您：

- 僅將 Azure 檔案共用部署到具有其他 Azure 檔案共用的儲存體帳戶。 雖然 GPv2 儲存體帳戶可讓您擁有混合用途的儲存體帳戶，因為 Azure 檔案共用和 blob 容器等儲存體資源會共用儲存體帳戶的限制，因此將資源混合在一起可能會讓您更難在稍後針對效能問題進行疑難排解。 

- 部署 Azure 檔案共用時，請注意儲存體帳戶的 IOPS 限制。 在理想的情況下，您會使檔案共用與儲存體帳戶 1:1 對應，不過，由於來自您組織和 Azure 的各種限制和制約，這種情況不一定始終可行。 當一個儲存體帳戶中不可能只部署一個檔案共用時，請考慮哪些共用高度活躍、哪些共用較不活躍，以確保最熱門的檔案共用不會放在同一個儲存體帳戶中。

- 當您在環境中找到 GPv2 和 FileStorage 帳戶，並升級 GPv1 和傳統儲存體帳戶時，只需部署這些帳戶。 

## <a name="identity"></a>身分識別
若要存取 Azure 檔案共用，檔案共用的使用者必須經過驗證，且擁有存取共用的授權。 這是根據存取檔案共用的使用者身分識別來完成。 Azure 檔案儲存體與三個主要身分識別提供者整合：
- 內部 **部署 Active Directory Domain Services (AD DS 或內部部署 AD DS)**： Azure 儲存體帳戶可以網域加入客戶擁有的 Active Directory Domain Services，就像 Windows Server 檔案伺服器或 NAS 裝置一樣。 您可以在內部部署、Azure VM，或甚至是另一個雲端提供者中的 VM 部署網域控制站;Azure 檔案儲存體與您的網域控制站裝載所在的位置無關。 一旦將儲存體帳戶加入網域，終端使用者就可以使用他們用來登入電腦的使用者帳戶掛接檔案共用。 以 AD 為基礎的驗證會使用 Kerberos 驗證通訊協定。
- **Azure Active Directory Domain Services (AZURE AD DS)**： Azure AD ds 提供可用於 Azure 資源的 Microsoft 管理網域控制站。 將儲存體帳戶加入至 Azure AD DS 的網域，可提供將其加入至客戶擁有的 Active Directory 的類似優點。 此部署選項最適用于需要以 AD 為基礎之許可權的應用程式隨即轉移案例。 由於 Azure AD DS 提供以 AD 為基礎的驗證，因此此選項也會使用 Kerberos 驗證通訊協定。
- **Azure 儲存體帳戶金鑰**：您也可以使用 azure 儲存體帳戶金鑰來掛接 azure 檔案共用。 若要以這種方式掛接檔案共用，則會使用儲存體帳戶名稱作為使用者名稱，並使用儲存體帳戶金鑰作為密碼。 使用儲存體帳戶金鑰來掛接 Azure 檔案共用，實際上是系統管理員作業，因為掛接的檔案共用對共用上的所有檔案和資料夾都有完整的許可權，即使它們具有 Acl 也一樣。 使用儲存體帳戶金鑰來透過 SMB 掛接時，會使用 NTLMv2 驗證通訊協定。

針對從內部部署檔案伺服器遷移，或在 Azure 檔案儲存體中建立新的檔案共用以做為 Windows 檔案伺服器或 NAS 設備的客戶，建議選項是將儲存體帳戶加入至 **客戶擁有的 Active Directory** 的網域。 若要深入了解如何使用網域將您的儲存體帳戶加入至客戶所擁有的 Active Directory，請參閱 [Azure 檔案儲存體 Active Directory 概觀](storage-files-active-directory-overview.md)。

如果您想要使用儲存體帳戶金鑰來存取 Azure 檔案共用，我們建議使用「 [網路](#networking) 」一節中所述的服務端點。

## <a name="networking"></a>網路功能
您可以透過儲存體帳戶的公用端點，從任何地方存取 Azure 檔案共用。 這表示已完成驗證的要求 (例如，由使用者的登入身分識別所授權的要求) 便可從 Azure 內部或外部安全地產生。 在許多客戶的環境中，最初將 Azure 檔案共用掛接到內部部署工作站時將會失敗，即使能從 Azure VM 掛接成功也一樣。 之所以會這樣，原因是許多組織和網際網路服務提供者 (ISP) 會將 SMB 用來通訊的連接埠 (連接埠445) 封鎖起來。 若要查看 ISP 是否允許從連接埠 445 進行存取的摘要，請參閱 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) \(英文\)。

若要解除封鎖對 Azure 檔案共用的存取，您有兩個主要選項：

- 解除封鎖您組織內部部署網路的埠445。 Azure 檔案共用只能透過公用端點從外部存取，但使用的是 SMB 3.0 和 FileREST API 等網際網路安全通訊協定。 這是從內部部署存取 Azure 檔案共用的最簡單方式，因為它不需要經過 advanced 網路設定，也不會變更您組織的輸出連接埠規則，不過，我們建議您移除舊版和已淘汰的 SMB 通訊協定版本，亦即 SMB 1.0。 若要瞭解如何進行，請參閱 [保護 windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) 和 [保護 Linux](storage-how-to-use-files-linux.md#securing-linux)的安全。

- 透過 ExpressRoute 或 VPN 連接存取 Azure 檔案共用。 當您透過網路通道存取 Azure 檔案共用時，您可以掛接 Azure 檔案共用，例如內部部署檔案共用，因為 SMB 流量不會跨越組織界限。   

從技術觀點來看，透過公用端點掛接 Azure 檔案共用會很容易，我們預期大部分的客戶會選擇透過 ExpressRoute 或 VPN 連線來掛接 Azure 檔案共用。 您可以使用 SMB 與 NFS 共用來裝載這些選項。 若要這樣做，您必須為您的環境設定下列各項：  

- **使用 ExpressRoute、站對站或點對站 VPN 的網路通道**：通道進入虛擬網路可讓您從內部部署存取 Azure 檔案共用，即使已封鎖埠445。
- **私人端點**：私人端點會在虛擬網路的位址空間內，為您的儲存體帳戶提供專用的 IP 位址。 這可啟用網路通道，而不需要開啟內部部署網路，直到 Azure 儲存體叢集擁有的所有 IP 位址範圍為止。 
- **DNS 轉送**：設定您的內部部署 DNS 以解析儲存體帳戶的名稱 (亦即， `storageaccount.file.core.windows.net` 公用雲端區域) 解析為私人端點的 IP 位址。

若要規劃與部署 Azure 檔案共用相關聯的網路功能，請參閱 [Azure 檔案儲存體網路功能考慮](storage-files-networking-overview.md)。

## <a name="encryption"></a>加密
Azure 檔案儲存體支援兩種不同的加密類型：傳輸中的加密，與掛接/存取 Azure 檔案共用時所使用的加密相關，以及待用加密（與資料儲存在磁片時的加密方式相關）。 

### <a name="encryption-in-transit"></a>傳輸中加密

> [!IMPORTANT]
> 本節會詳細說明 SMB 共用的傳輸中加密。 如需有關 NFS 共用的傳輸中加密詳細資訊，請參閱[安全性](storage-files-compare-protocols.md#security)。

根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 這表示當您透過 SMB 掛接檔案共用或透過 FileREST 通訊協定 (例如，透過 Azure 入口網站、PowerShell/CLI 或 Azure SDK) 來存取檔案共用時，Azure 檔案儲存體只會在使用了 SMB 3.0 以上 (有加密功能) 或 HTTPS 來進行連線時，才會允許您建立連線。 不支援 SMB 3.0 的用戶端，或雖支援 SMB 3.0 但不支援 SMB 加密的用戶端，將無法在啟用了傳輸中加密的情況下掛接 Azure 檔案共用。 如需哪些作業系統支援 SMB 3.0 (有加密功能) 的詳細資訊，請參閱適用於 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的詳細文件。 所有目前版本的 PowerShell、CLI 和 SDK 都支援 HTTPS。  

您可以為 Azure 儲存體帳戶停用傳輸中加密。 停用加密時，Azure 檔案儲存體也會允許 SMB 2.1、不含加密的 SMB 3.0，以及透過 HTTP 的未加密 FileREST API 呼叫。 會停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件。 Azure 檔案儲存體只會在與 Azure 檔案共用相同的 Azure 區域內允許 SMB 2.1 連線；Azure 檔案共用所在的 Azure 區域外 (例如，內部部署或不同 Azure 區域) 的 SMB 2.1 用戶端，則無法存取檔案共用。

我們強烈建議您確保傳輸中資料加密已啟用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="encryption-at-rest"></a>待用加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>資料保護
Azure 檔案儲存體具有多層式的方法，可確保您的資料已備份、可復原，並受到保護，免于遭受安全性威脅。

### <a name="soft-delete"></a>虛刪除
檔案共用的虛刪除 (preview) 是儲存體帳戶層級設定，可讓您在意外刪除檔案共用時復原檔案共用。 刪除檔案共用時，它會轉換為虛刪除狀態，而不是永久清除。 您可以設定虛刪除的資料在永久刪除之前可復原的時間量，並在此保留期間內隨時取消刪除該共用。 

建議您開啟大部分檔案共用的虛刪除。 如果您的工作流程中的共用刪除是常見且預期的，您可能會決定有非常短的保留期限，或完全未啟用虛刪除。

如需虛刪除的詳細資訊，請參閱 [防止意外刪除資料](./storage-files-prevent-file-share-deletion.md)。

### <a name="backup"></a>Backup
您可以透過 [共用快照](./storage-snapshots-files.md)集來備份 Azure 檔案共用，這是共用的唯讀、時間點複本。 快照集是累加的，這表示它們只包含自從上一個快照集以來已變更的資料量。 每個檔案共用最多可以有200個快照集，並保留最多10年的快照。 您可以透過 PowerShell 或命令列介面（ (CLI) ）手動取得這些 Azure 入口網站快照集，也可以使用 [Azure 備份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 快照集會儲存在您的檔案共用中，這表示如果您刪除檔案共用，您的快照集也會一併刪除。 若要保護您的快照集備份不會遭到意外刪除，請確定已為您的共用啟用虛刪除。

[適用于 Azure 檔案共用的 Azure 備份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 會處理快照集的排程和保留期。 它的祖父-父親 (GFS) 功能意味著您可以每日、每週、每月和每年快照集，每個快照集都有自己的相異保留期限。 Azure 備份也會協調啟用虛刪除，並在其內的任何檔案共用設定為備份時，立即在儲存體帳戶上進行刪除鎖定。 最後，Azure 備份提供某些重要的監視和警示功能，可讓客戶取得其備份資產的匯總。

您可以使用 Azure 備份在 Azure 入口網站中執行專案層級和共用層級還原。 您只需要選擇還原點 (特定的快照) 、特定的檔案或目錄（如果相關），然後再 (您想要還原的原始或替代) 位置。 備份服務會處理複製快照集資料的程式，並在入口網站中顯示您的還原進度。

如需有關備份的詳細資訊，請參閱 [關於 Azure 檔案共用備份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Azure 檔案儲存體 (preview 的 Advanced 威脅防護) 
適用于 Azure 儲存體的 Advanced 威脅防護 (ATP) 提供額外一層的安全情報，在偵測到儲存體帳戶上的異常活動時提供警示，例如，不尋常的存取儲存體帳戶的嘗試。 ATP 也會執行惡意程式碼雜湊信譽分析，並會發出已知惡意程式碼的警示。 您可以透過 Azure 資訊安全中心在訂用帳戶或儲存體帳戶層級上設定 ATP。 

如需詳細資訊，請參閱 [Azure 儲存體的 Advanced 威脅防護](../common/azure-defender-storage-configure.md)。

## <a name="storage-tiers"></a>儲存層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="understanding-provisioning-for-premium-file-shares"></a>瞭解 premium 檔案共用的布建
進階檔案共用會以固定的 GiB/IOPS/輸送量比例為基礎佈建。 所有共用大小都提供最小基準/輸送量，並允許高載。 針對每個已布建的 GiB，將會發出最小 IOPS/輸送量，以及一次 IOPS 和 0.1 MiB/秒輸送量，直到每個共用的上限為止。 最小允許的布建為 100 GiB，最少 IOPS/輸送量。 

所有 premium 共用都是以最大的努力提供免費的高載。 所有的共用大小最多可高達 4000 IOPS，或每個布建的 GiB 最多三個 IOPS，以提供更高的共用 IOPS IOPS。 所有共用都支援尖峰高載限制的最長持續時間（60分鐘）。 新的共用一開始有以佈建容量為基礎的完整高載額度。

共用必須以 1 GiB 增量布建。 大小下限為 100 GiB、下一個大小為 101 GiB 等等。

> [!TIP]
> 基準 IOPS = 400 + 1 * 布建 GiB。  (最高 100000 IOPS) 。
>
> 高載限制 = 最大 (4000，3 * 基準 IOPS) 。  (上限，最高可達 100000 IOPS) 。
>
> 輸出速率 = 60 MiB/秒 + 0.06 * 布建的 GiB
>
> 輸入速率 = 40 MiB/秒 + 0.04 * 布建的 GiB

布建的共用大小是由共用配額所指定。 您可以隨時增加共用配額，但可以在上一次增加之後24小時後減少。 等候24小時之後，如果沒有增加配額，您可以依需要減少共用配額的次數，直到再次增加為止。 IOPS/輸送量調整變更將在大小變更後的幾分鐘內生效。

您可以將布建的共用大小減少到您使用的 GiB 下。 如果您這樣做，將不會遺失資料，但您仍需支付所使用的大小，並可獲得所布建共用的效能 (基準 IOPS、輸送量和高載 IOPS) ，而不是使用的大小。

下表說明這些 homebrew 公式針對布建的共用大小的一些範例：

|容量 (GiB)  | 基準 IOPS | 高載 IOPS | 輸出 (MiB/秒)  | 輸入 (MiB/秒)  |
|---------|---------|---------|---------|---------|
|100         | 500     | 最高 4,000     | 66   | 44   |
|500         | 900     | 最高 4,000  | 90   | 60   |
|1,024       | 1424   | 最高 4,000   | 122   | 81   |
|5,120       | 5520   | 最高15360  | 368   | 245   |
|10240      | 10640  | 最高30720  | 675   | 450   |
|33792      | 34192  | 最高100000 | 2088 | 1392   |
|51200      | 51600  | 最高100000 | 3,132 | 2088   |
|102400     | 100,000 | 最高100000 | 6204 | 4136   |

要注意的是，有效的檔案共用效能受限於電腦網路限制、可用的網路頻寬、IO 大小、平行處理，還有許多其他因素。 例如，根據具有8個 KiB 讀取/寫入 IO 大小的內部測試，不啟用 SMB 多重通道的單一 Windows 虛擬機器（ *標準 F16s_v2*）會透過 smb 連線至 premium 檔案共用，以達到20K 的讀取 IOPS 和15K 寫入 iops。 使用 512 MiB 讀取/寫入 IO 大小時，相同的 VM 可以達到 1.1 GiB/s 輸出和 370 MiB/s 輸入輸送量。 如果 premium 共用上已啟用 SMB 多重通道，則相同的用戶端最多可達到 \~ 3 倍的效能。 若要達到最大效能調整，請 [啟用 SMB 多重](storage-files-enable-smb-multichannel.md) 通道，並將負載分散到多個 vm。 請參閱 [SMB 多重通道效能](storage-files-smb-multichannel-performance.md) 和 [疑難排解指南](storage-troubleshooting-files-performance.md) ，以瞭解一些常見的效能問題和因應措施。

#### <a name="bursting"></a>爆破
如果您的工作負載需要額外的效能來滿足尖峰需求，則您的共用可以使用高載點數來進行以上的共用基準 IOPS 限制，以提供符合需求所需的共用效能。 Premium 檔案共用可將其 IOPS 高載至4000或最多三個因數（以較高的值為准）。 高載是自動化的，而且會根據信用系統來運作。 高載會以最佳的方式運作，而高載限制不保證，檔案共用 *最多可以高達* 60 分鐘的最大持續時間上限。

當檔案共用的流量低於基準 IOPS 時，會在高載 bucket 中累積點數。 例如，100 GiB 共用具有500基準 IOPS。 如果共用上的實際流量是特定1秒間隔的 100 IOPS，則400未使用的 IOPS 會被計入高載值區。 同樣地，閒置的 1 TiB 共用，會在 1424 IOPS 上累算高載點數。 之後將會在作業超過基準 IOPS 時使用這些點數。

每當共用超過基準 IOPS，且在高載值區中有點數時，它會以允許的尖峰高載速率來高載。 只要剩餘信用額度（最多可達60分鐘的持續時間），共用可以持續高載，但這是以累積的高載點數數量為基礎。 除了基準 IOPS 以外的每個 IO 都會耗用一項點數，一旦取用所有信用額度之後，該共用會回到基準 IOPS。

共用信用額度有三種狀態：

- 當檔案共用使用小於基準 IOPS 時產生。
- 當檔案共用使用超過基準 IOPS，且處於高載模式時拒絕。
- 常數，抽檔案共用會使用完全相同的基準 IOPS，而不會有任何已累積或未使用的點數。

新檔案共用的開頭為其高載值區中的完整點數。 如果共用 IOPS 低於基準 IOPS （因為伺服器進行節流），將不會產生高載信用額度。

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>啟用標準檔案共用可跨越最多 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>限制
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>備援性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>遷移
在許多情況下，您將不會為您的組織建立網路新的檔案共用，而是將現有的檔案共用從內部部署檔案伺服器或 NAS 裝置遷移至 Azure 檔案儲存體。 為您的案例挑選適合的遷移策略和工具，對於您的遷移是否成功是很重要的。 

「 [遷移總覽](storage-files-migration-overview.md) 」一文簡要說明基本概念，並包含一個表格，可引導您前往可能涵蓋您案例的指南。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [查看遷移總覽文章，以找出您案例的遷移指南](storage-files-migration-overview.md)
