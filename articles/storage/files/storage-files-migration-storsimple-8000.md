---
title: StorSimple 8000 系列遷移到 Azure 檔同步
description: 瞭解如何將 StorSimple 8100 或 8600 設備遷移到 Azure 檔同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528620"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 和 8600 遷移到 Azure 檔同步

StorSimple 8000 系列由 8100 或 8600 物理本地設備及其雲服務元件表示。 可以將資料從其中任一設備遷移到 Azure 檔同步環境。 Azure 檔同步是 StorSimple 設備可以遷移到的預設且戰略性的長期 Azure 服務。

StorSimple 8000 系列將于 2022 年 12 月實現[生命週期結束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series)。 儘快開始規劃遷移非常重要。 本文提供了成功遷移到 Azure 檔同步的必要背景知識和遷移步驟。 

## <a name="azure-file-sync"></a>Azure 檔案同步

> [!IMPORTANT]
> Microsoft 致力於協助客戶遷移。 Email AzureFilesMigration@microsoft .com，用於自訂遷移計畫以及在遷移期間提供説明。

Azure 檔同步是一種 Microsoft 雲服務，基於兩個主要元件：

* 檔同步和雲分層。
* 檔共用作為 Azure 中的本機存儲，可通過多個協定（如 SMB 和檔 REST）進行訪問。 Azure 檔共用可與 Windows 伺服器上的檔共用相媲美，您可以將其作為網路磁碟機本機裝載。 它支援重要的檔逼真度方面，如屬性、許可權和時間戳記。 使用 Azure 檔共用時，不再需要應用程式或服務來解釋存儲在雲中的檔和資料夾。 您可以通過熟悉的協定和用戶端（如 Windows 檔資源管理器）以本機方式訪問它們。 這使得 Azure 檔共用成為在雲中存儲通用檔案伺服器資料以及某些應用程式資料的理想和最靈活的方法。

本文重點介紹遷移步驟。 如果要在遷移之前瞭解有關 Azure 檔同步的更多內容，我們建議您提供以下文章：

* [Azure 檔同步 - 概述](https://aka.ms/AFS "總覽")
* [Azure 檔同步 - 部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，並保證可用性。 後者要求將停機時間降至最低，以便它能夠適應或僅略高於常規維護視窗。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 系列遷移路徑到 Azure 檔同步

運行 Azure 檔同步代理需要本地 Windows 伺服器。 Windows 伺服器可以至少位於 2012R2 伺服器，但理想情況下是 Windows 伺服器 2019。

有許多替代遷移路徑，它會創建太長的文章來記錄所有這些路徑，並說明它們為什麼在本文中作為最佳實踐建議的路徑上承擔風險或缺點。

![StorSimple 8000 系列遷移階段概述](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 系列遷移路線概述本文中進一步介紹了以下階段。")

前面的圖像描述了與本文中各節相對應的階段。
我們使用雲端遷移來避免不必要地將檔撤回到您當地的 StorSimple 設備。 此方法可避免影響本機快取行為或網路頻寬使用，其中任一都可能影響生產工作負載。
雲端遷移正在資料快照（卷克隆）上運行。 因此，您的生產資料與此過程隔離， 直到遷移結束時進行翻越。 處理本質上是備份，使遷移安全且易於重複，如果遇到任何困難。

## <a name="considerations-around-existing-storsimple-backups"></a>有關現有 StorSimple 備份的注意事項

StorSimple 允許您以卷克隆的形式進行備份。 本文使用新的卷克隆來遷移即時檔。
如果您需要遷移備份以及即時資料，則本文中的所有指南仍適用。 唯一的區別是，您不是從新的卷克隆開始，而是從需要遷移的最舊的備份卷克隆開始。

順序如下：

* 確定必須遷移的最小卷克隆集。 如果可能，我們建議將此清單保持在最低水準，因為遷移的備份越多，整個遷移過程所花的時間就越長。
* 完成遷移過程時，從要遷移的最早卷克隆開始，然後在每個後續遷移中，使用下一個最舊的卷克隆。
* 完成每個卷克隆遷移後，必須拍攝 Azure 檔共用快照。 [Azure 檔共用快照](storage-snapshots-files.md)是 Azure 檔共用的檔和資料夾結構的時間點備份方式。 遷移完成後將需要這些快照，以確保在遷移中完成遷移時保留每個卷克隆的版本。
* 確保為同一 StorSimple 卷提供的所有 Azure 檔共用快照獲取。 卷克隆位於卷級別，Azure 檔共用快照位於共用級別。 卷克隆的遷移完成後，必須拍攝共用快照（每個 Azure 檔共用）。
* 重複卷克隆的遷移過程，並在每次卷克隆後拍攝共用快照，直到您趕上即時資料的快照。 遷移卷克隆的過程在以下階段仲介紹。 

如果根本不需要移動備份，並且在僅遷移即時資料後可以在 Azure 檔共用端啟動新的備份鏈，則這有利於降低遷移的複雜性和遷移所花費的時間。 您可以決定是否移動備份，以及 StorSimple 中每個卷（不是每個共用）有多少。

## <a name="phase-1-get-ready"></a>第 1 階段：做好準備

:::row:::
    :::column:::
        ![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        遷移的基礎是卷克隆和虛擬雲設備，稱為 StorSimple 8020。
此階段側重于在 Azure 中部署這些資源。
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>部署 StorSimple 8020 虛擬裝置

部署雲設備是一個需要安全性、網路和一些其他注意事項的過程。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 閱讀並遵循文章從開始到"步驟 3"。 然後返回到本文。 此時，您無需完成本指南中超出該指南的"步驟 3"或任何其他內容。

[部署 StorSimple 8020 虛擬裝置](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>確定要使用的卷克隆

準備好開始遷移後，第一步是採用新的卷克隆（就像備份一樣）來捕獲 StorSimple 雲存儲的目前狀態。 獲取每個 StorSimple 卷的克隆。
如果需要移動備份，則使用的第一個卷克隆不是新創建的克隆，而是需要遷移的最舊的卷克隆（最舊備份）。
有關詳細指導，請參閱["有關現有 StorSimple 備份的注意事項"](#considerations-around-existing-storsimple-backups)一節。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 只閱讀並遵循連結到部分中概述的步驟。 然後返回到本文。 您無需遵循"後續步驟"部分。

[建立磁碟區複製](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>使用卷克隆

階段 1 的最後階段是使您選擇的卷克隆在 Azure 中的 8020 虛擬裝置上可用。

> [!IMPORTANT]
> 以下指南包含必要的步驟，但最後還包含設置卷格式的說明。 **不格式化卷**閱讀並遵循連結到"第 7 節"從開始到指令："10。 要格式化一個簡單的卷，..." 在按照此步驟之前停止，然後返回到本文。

[在 Azure 中的 8020 虛擬裝置上安裝卷克隆](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>第 1 階段摘要

現在已完成第 1 階段，您將完成以下操作：

* 在 Azure 中部署了 StorSimple 8020 虛擬裝置。
* 確定您將從哪個卷克隆開始遷移。
* 將卷克隆（每個即時卷一個）裝載到 Azure 中的 StorSimple 虛擬裝置，其資料可供進一步使用。

## <a name="phase-2-cloud-vm"></a>第 2 階段：雲 VM

:::row:::
    :::column:::
        ![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        初始克隆在 Azure 中的 StorSimple 8020 虛擬裝置上可用後，現在是時候預配 VM 並在 iSCSI 上向該 VM 公開卷克隆（或多個）。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

Azure 中的 Windows Server 虛擬機器與 StorSimple 8020 類似，這是一個臨時基礎結構，僅在遷移期間才是必需的。
部署的 VM 的配置主要取決於要同步的專案（檔和資料夾）數。 如果您有任何疑問，我們建議您採用更高的性能配置。

單個 Windows 伺服器最多可以同步 30 個 Azure 檔共用。
您決定需要包含每個共用/路徑或 StorSimple 卷的根並計算專案（檔和資料夾）的規範。

資料的總體大小不是瓶頸 - 它是需要定制機器規格的專案數量。

* [瞭解如何根據需要同步的專案（檔和資料夾）數量對 Windows 伺服器進行大小調整。](storage-sync-files-planning.md#recommended-system-resources)

    **請注意：** 前面連結的文章提供了一個表，其中提供了伺服器記憶體 （RAM） 的範圍。 定向到 Azure VM 的大數。 您可以定向到本地機器的較小數位。

* [瞭解如何部署 Windows 塞弗 VM。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> 確保 VM 部署在同一 Azure 區域中，與 StorSimple 8020 虛擬裝置相同。 如果作為此遷移的一部分，還需要從今天存儲的區域更改雲資料的區域，則可以在預配 Azure 檔共用時在後續步驟中執行此操作。

> [!IMPORTANT]
> 要優化性能，請為雲 VM 部署**非常快速**的 OS 磁片。 您將將同步資料庫存儲在作業系統磁片上，用於所有資料卷。 此外，請確保創建**大型 OS 磁片**。 根據 StorSimple 卷上的專案（檔和資料夾）數，作業系統磁片可能需要**幾百 GiB**的空間來容納同步資料庫。

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>將 StorSimple 8020 卷公開到 Azure VM

在此階段，您正在通過 iSCSI 將一個或多個 StorSimple 卷從 8020 虛擬裝置連接到已預配的 Windows Server VM。

> [!IMPORTANT]
> 對於以下文章，請僅完成**雲設備的獲取專用 IP，****然後通過 iSCSI 部分進行連接**，然後返回到本文。

1. [取得雲端設備的私人 IP](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [通過 iSCSI 連接](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>第 2 階段摘要

現在，您已完成第 2 階段，您有： 

* 在與 8020 虛擬 StorSimple 設備相同的區域中預配 Windows 伺服器 VM
* 通過 iSCSI 將 8020 到 Windows 伺服器 VM 的所有適用卷公開。
* 現在，當您在裝載的卷上的伺服器 VM 上使用檔資源管理器時，您應該會看到檔和資料夾內容。

僅當已完成所有需要遷移的卷的這些步驟時，才能繼續階段 3。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>第 3 階段：設置 Azure 檔共用並為 Azure 檔同步做好準備

:::row:::
    :::column:::
        ![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        在此階段，您將確定和預配許多 Azure 檔共用，在本地創建 Windows 伺服器作為 StorSimple 設備替換，並為 Azure 檔同步配置該伺服器。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>將現有命名空間映射到 Azure 檔共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>部署 Azure 檔共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> 如果需要從 StorSimple 資料所在的目前範圍更改 Azure 區域，則在要使用的新區域中預配 Azure 檔共用。 在預配保存 Azure 檔共用的存儲帳戶時，通過選擇區域來確定該區域。 確保將在下面預配的 Azure 檔同步資源也位於同一新區域中。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 檔同步雲資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果需要從 StorSimple 資料所在的目前範圍更改 Azure 區域，則已為新區域中的 Azure 檔共用預配了存儲帳戶。 請確保在部署此存儲同步服務時選擇了同一區域。

### <a name="deploy-an-on-premises-windows-server"></a>部署本地 Windows 伺服器

* 創建 Windows 伺服器 2019 - 至少 2012R2 - 作為虛擬機器或物理伺服器。 還支援 Windows 伺服器容錯移轉叢集。 不要重複使用 StorSimple 8100 或 8600 前面可能具有的伺服器。
* 預配或添加直接連接存儲（與不支援 NAS 的 NAS 相比的 DAS）。

最佳做法是，為新的 Windows Server 提供與 StorSimple 8100 或 8600 設備在本地可用於緩存的同等或更大的存儲空間。 如果 Windows 伺服器的存儲量與設備相同，則緩存體驗應該類似（如果不是相同的話）。
您可以自此添加或刪除 Windows 伺服器中的存儲。 這使您能夠擴展本地卷大小和可用於緩存的本機存放區量。

### <a name="prepare-the-windows-server-for-file-sync"></a>準備 Windows 伺服器進行檔同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>在 Windows 伺服器上配置 Azure 檔同步

您註冊的本地 Windows 伺服器必須已準備就緒，並連接到 Internet 進行此過程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必打開雲分層！** 雲分層是 AFS 功能，它允許本機伺服器的存儲容量少於存儲在雲中的存儲容量，但具有完整的命名空間可用。 本地有趣的資料也緩存在本地，以快速的本地訪問性能。 在此步驟中打開雲階層式另一個原因是，我們不希望在此階段同步檔內容，此時僅應移動命名空間。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>第 4 階段：配置 Azure VM 進行同步

:::row:::
    :::column:::
        ![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        此階段涉及 Azure VM，並安裝了 iSCSI，這是第一個卷克隆。 在此階段，您將通過 Azure 檔同步連接 VM，並開始從 StorSimple 卷克隆中移動檔的第一輪移動。
        
    :::column-end:::
:::row-end:::

您已經配置了本機伺服器，該伺服器將替換 StorSimple 8100 或 8600 設備，用於 Azure 檔同步。 

配置 Azure VM 是一個幾乎相同的過程，還有一個步驟。 以下步驟將指導您完成此過程。

> [!IMPORTANT]
> 未**啟用雲分層後配置**Azure VM 非常重要！ 在整個遷移過程中，您將使用此伺服器的卷與較新的卷克隆交換該卷。 雲分層對 CPU 使用率沒有好處和開銷，您應該避免。

1. [部署 AFS 代理。（見上一節）](#prepare-the-windows-server-for-file-sync)
2. [為 Azure 檔同步準備 VM。](#get-the-vm-ready-for-azure-file-sync)
3. [配置同步](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>為 Azure 檔同步準備好 VM

Azure 檔同步用於將檔從裝載的 iSCSI StorSimple 卷移動到目標 Azure 檔共用。
在此遷移過程中，您將在同一磁碟機號下將多個卷克隆裝載到 VM。 必須將 Azure 檔同步配置為查看已裝載為檔和資料夾的較新版本的下一個卷克隆，並更新通過 Azure 檔同步連接的 Azure 檔共用。 

> [!IMPORTANT]
> 為此，必須在配置 Azure 檔同步之前在伺服器上設置登錄機碼。

1. 在 VM 的系統磁碟機上創建新目錄。 Azure 檔同步資訊需要保留在那裡，而不是在裝載的卷克隆上。 例如： `"C:\syncmetadata"`
2. 打開註冊並查找以下註冊表配置單元：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. 創建新的字串類型鍵，名稱為：***中繼資料根路徑***
4. 設置在系統磁碟區上創建的目錄的完整路徑，例如：`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>在 Azure VM 上配置 Azure 檔同步

此步驟與上一節類似，該部分討論了如何在本機伺服器上配置 AFS。

區別在於，不得在此伺服器上啟用雲分層，並且需要確保正確的資料夾已連接到正確的 Azure 檔共用。 否則，對 Azure 檔共用和資料內容的命名將不匹配，並且無法在不重新配置同步的情況下重命名雲資源或本地資料夾。

請參閱上一[節關於如何在 Windows 伺服器上配置 Azure 檔同步](#configure-azure-file-sync-on-the-windows-server)。

### <a name="step-4-summary"></a>步驟 4 摘要

此時，您將在通過 iSCSI 裝載 StorSimple 卷克隆的 Azure VM 上成功配置 Azure 檔同步。
資料現在從 Azure VM 流向各種 Azure 檔共用，並且從該共用中會出現完全疲憊的命名空間。

> [!IMPORTANT]
> 確保此時未對 Windows 伺服器進行任何更改或授予使用者存取權限。

通過 Azure VM 移動到 Azure 檔共用的初始卷克隆資料可能需要很長時間，可能需要數周時間。 估計這需要時間是棘手的，取決於許多因素。 最顯著的是 Azure VM 訪問 StorSimple 卷上檔的速度，以及 Azure 檔同步處理需要同步的檔和資料夾的速度。 

根據經驗，我們可以假設頻寬（因此實際資料大小）起著從屬作用。 此或任何後續遷移輪採用的時間主要取決於每秒可處理的專案數。 因此，例如，具有 100，000 個檔和資料夾的 1 TiB 很可能完成得慢于只有 50，000 個檔和資料夾的 1 TiB。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>第 5 階段：通過多個卷克隆反覆運算

:::row:::
    :::column:::
        ![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        如前一階段所述，初始同步可能需要很長時間。 您的使用者和應用程式仍在訪問本地 StorSimple 8100 或 8600 設備。 這意味著更改正在累積，並且隨著即時資料與初始卷克隆之間的更大增量，您當前正在遷移表單。 在本節中，您將學習如何通過使用多個卷克隆並告知何時完成同步來最大程度地減少停機時間。
    :::column-end:::
:::row-end:::

遺憾的是，遷移過程不是即時的。 這意味著上述增量對即時資料是一個不可避免的後果。 好消息是，您可以重複安裝新卷克隆的過程。 每個卷克隆的增量將逐漸變小。 因此，最終，同步將在您認為可以接受的使用者和應用程式離線以剪切到本地 Windows 伺服器的持續時間內完成。

重複以下步驟，直到同步完成足夠快，讓您放心地使使用者和應用程式離線：

1. [確定給定卷克隆的同步已完成。](#determine-when-sync-is-done)
2. [獲取新的卷克隆並將其裝載到 8020 虛擬裝置。](#the-next-volume-clones)
3. [確定同步何時完成。](#determine-when-sync-is-done)
4. [截止策略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>下一個卷克隆

本文前面討論了使用卷克隆。
此階段有兩個操作：

1. [獲取卷克隆](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [安裝該卷克隆（見上文）](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>確定同步何時完成

同步完成後，您可以停止時間測量，並確定是否需要重複獲取卷克隆並安裝它的過程，或者與上次卷克隆的同步是否足夠小。

為了確定同步是否完成：

1. 打開事件檢視器並導航到**應用程式和服務**
2. 導航並打開**Microsoft_檔同步\代理\遙測**
3. 查找與已完成的同步會話對應的最新**事件 9102**
4. 選擇**詳細資訊**並確認**同步方向**值為 **"上載"**
5. 檢查**HResult**並確認它顯示**0**。 這意味著同步會話成功。 如果 HResult 是非零值，則同步期間出錯。如果**PerItemErrorCount**大於 0，則某些檔或資料夾未正確同步。 HResult 為 0，但 PerItemErrorCount 大於 0，是有可能的。 此時，您無需擔心 PerItemErrorCount。 稍後我們將捕獲這些檔。 如果此錯誤計數很大，則數千個專案將與客戶支援聯繫，並要求連接到 Azure 檔同步產品組，以便直接指導最佳的下一階段。
6. 檢查以查看多個 9102 事件，並連續出現 HResult 0。 這表示此卷克隆的同步已完成。

### <a name="cut-over-strategy"></a>截止策略

1. 確定來自卷克隆的同步現在是否足夠快。 （增量足夠小。
2. 使 StorSimple 設備離線。
3. 最後的RoboCopy。

測量時間並確定最近卷克隆的同步是否可以在足夠小的時間視窗內完成，這樣您才能承受系統中的停機時間。

現在是時候禁用使用者訪問 StorSimple 設備了。 不再更改。 停機時間已經開始。
您需要使產品保持連線並連接，但現在必須防止對產品進行更改。

在第 6 階段中，您將趕上自上次卷克隆以來的即時資料中的任何增量。

## <a name="phase-6-a-final-robocopy"></a>第 6 階段：最終 RoboCopy

此時，本地 Windows 伺服器與 StorSimple 8100 或 8600 設備之間存在兩種差異：

1. 可能有一些檔尚未同步（請參閱上面的事件日誌中的**PerItem 錯誤**）
2. StorSimple 設備具有填充緩存，而 Windows 伺服器只是一個命名空間，此時本地沒有存儲任何檔內容。

![一種圖像，用於說明早期概述圖像的一部分，可説明集中文章的本小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

我們可以將 Windows Server 的緩存帶到設備的狀態，並確保沒有檔留下最終的 RoboCopy。

> [!CAUTION]
> 您遵循的 RoboCopy 命令必須完全按照下文所述。 我們只想複製本地檔和以前未通過卷克隆同步方法移動的檔。 我們可以解決他們以後在遷移完成後沒有同步的問題。 （請參閱[Azure 檔同步故障排除](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。 檔案名中很可能無法列印的字元，刪除時不會錯過這些字元。

RoboCopy 命令：

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

後景：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允許 RoboCopy 運行多執行緒。 預設值為 8，最大值為 128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG：<file name>
   :::column-end:::
   :::column span="1":::
      將日誌檔輸出為 UNICODE（覆蓋現有日誌）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      輸出到主控台視窗。 與日誌檔的輸出一起使用。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B 
   :::column-end:::
   :::column span="1":::
      以備份應用程式將使用的相同模式運行 RoboCopy。 它允許 RoboCopy 移動當前使用者無權訪問的檔。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許 RoboCopy 只考慮源（StorSimple 設備）和目標（Windows Server 目錄）之間的增量。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY：複製標誌*
   :::column-end:::
   :::column span="1":::
      檔副本的逼真度（預設為 /COPY：DAT），複製標誌：D=資料、A=屬性、T=時間戳記、S=安全=NTFS ACL、O=擁有者資訊、U_auditing資訊
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /複製
   :::column-end:::
   :::column span="1":::
      複製所有檔資訊（等效于 /COPY：DATSOU）
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY：複製標誌*
   :::column-end:::
   :::column span="1":::
      目錄副本的逼真度（預設值為 /DCOPY：DA），複製標誌：D=資料、A=屬性、T=時間戳記
   :::column-end:::
:::row-end:::

您應該將此 RoboCopy 命令作為 Windows Server 上每個目錄的目標運行，該目錄已配置到 Azure 檔的檔同步。

您可以並行運行這些命令中的多個。
完成此 RoboCopy 步驟後，您可以允許使用者和應用程式像之前使用 StorSimple 設備一樣訪問 Windows 伺服器。

請參閱 robocopy 日誌檔，查看檔是否已留有。 如果問題仍然存在，在大多數情況下，您可以在遷移完成後解決這些問題，並且您的使用者和應用已重新駐留在您的 Windows 伺服器。 如果需要修復任何問題，在階段 7 之前執行此操作。

可能需要在 Windows 伺服器上創建之前在 StorSimple 資料上擁有的 SMB 共用。 您可以預先載入此步驟，並提前執行此操作，以免在此處浪費時間，但必須確保在這一點之前，Windows 伺服器上不會發生對檔的更改。

如果您有 DFS-N 部署，則可以將 DFN 命名空間指向新的伺服器資料夾位置。 如果您沒有 DFS-N 部署，並且在本地使用 Windows 伺服器面對 8100 8600 設備，則可以將該伺服器從域中取下，並將新的 Windows 伺服器與 AFS 聯接到域中，使其具有與舊伺服器相同的伺服器名稱和相同的共用名稱稱，然後新伺服器的截止時間對使用者、群組原則或腳本保持透明。

## <a name="phase-7-deprovision"></a>第 7 階段：取消預配

在最後階段，您通過多個卷克隆進行了反覆運算，並最終在使 StorSimple 設備離線後，能夠減少使用者對新 Windows 伺服器的訪問。

您現在可以開始取消預配不必要的資源。
在開始之前，最佳做法是觀察生產中的新 Azure 檔同步部署。 這為您提供了修復可能遇到的任何問題的選項。

滿足並觀察 AFS 部署至少幾天後，可以按此順序開始取消預配資源：

1. 關閉用於通過檔同步將資料從卷克隆移動到 Azure 檔共用的 Azure VM。
2. 轉到 Azure 中的存儲同步服務資源，然後取消註冊 Azure VM。 這將從所有同步組中刪除它。

    > [!WARNING]
    > **確保選擇正確的機器。** 您已關閉雲 VM，這意味著它應顯示為註冊伺服器清單中的唯一離線服務器。 在此步驟中，您不得選擇本地 Windows 伺服器，這樣做將取消註冊它。

3. 刪除 Azure VM 及其資源。
4. 禁用 8020 虛擬 StorSimple 設備。
5. 取消預配 Azure 中的所有 StorSimple 資源。
6. 從資料中心拔下 StorSimple 物理設備。

您的遷移已完成。

## <a name="next-steps"></a>後續步驟

更加熟悉 Azure 檔同步。特別是具有雲分層策略的靈活性。

如果在 Azure 門戶或早期事件中看到某些檔永久未同步，請查看故障排除指南，瞭解解決這些問題的步驟。

* [Azure 檔同步概述：aka.ms/AFS](https://aka.ms/AFS)
* [雲分層](storage-sync-cloud-tiering.md) 
* [Azure 檔同步故障排除指南](storage-sync-files-troubleshoot.md)
