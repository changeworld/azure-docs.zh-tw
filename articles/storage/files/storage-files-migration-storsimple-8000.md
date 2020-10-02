---
title: 將 StorSimple 8000 系列遷移至 Azure 檔案同步
description: 瞭解如何將 StorSimple 8100 或8600設備遷移至 Azure 檔案同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: be61a6e75c4aa9b5714ffbf3b4f19656b347c493
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653242"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>將 StorSimple 8100 和8600遷移至 Azure 檔案同步

StorSimple 8000 系列以8100或8600實體、內部部署設備和其雲端服務元件表示。 您可以將其中一個設備的資料移轉至 Azure 檔案同步環境。 Azure 檔案同步是 StorSimple 設備可遷移至的預設和策略性長期 Azure 服務。

StorSimple 8000 系列將于2022年12月 [結束生命週期](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 。 請務必儘快開始規劃您的遷移。 本文提供成功遷移至 Azure 檔案同步所需的背景知識和遷移步驟。 

## <a name="azure-file-sync"></a>Azure 檔案同步

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 AzureFilesMigration@microsoft適用于自訂遷移計畫的電子郵件，以及遷移期間的協助。

Azure 檔案同步是 Microsoft 雲端服務，以兩個主要元件為基礎：

* 檔案同步處理和雲端階層處理。
* 檔案共用是 Azure 中的原生儲存體，可透過多種通訊協定來存取，例如 SMB 和檔案 REST。 Azure 檔案共用相當於 Windows Server 上的檔案共用，您可以原生掛接為網路磁碟機機。 它支援重要的檔案精確度層面，例如屬性、許可權和時間戳記。 有了 Azure 檔案共用，應用程式或服務就不再需要解讀儲存在雲端中的檔案和資料夾。 您可以透過熟悉的通訊協定和用戶端（例如 Windows 檔案總管）以原生方式存取它們。 這讓 Azure 檔案共用在雲端中儲存一般用途檔案伺服器資料以及一些應用程式資料的理想且最有彈性的方法。

本文著重于遷移步驟。 在遷移之前，您想要深入瞭解 Azure 檔案同步，建議您執行下列文章：

* [Azure 檔案同步-總覽](https://aka.ms/AFS "概觀")
* [Azure 檔案同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，以及保證可用性。 後者需要保持最少停機時間，使其可符合或僅稍微超過一般維護時段。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Azure 檔案同步的 StorSimple 8000 系列遷移路徑

需要本機 Windows 伺服器才能執行 Azure 檔案同步代理程式。 Windows Server 最少可以是2012R2 伺服器，但在理想情況下是 Windows Server 2019。

其中有許多替代的遷移路徑，而且可能會建立太長的文章來記錄所有的路徑，並說明為什麼在本文中，我們建議的最佳作法是有風險或缺點。

![StorSimple 8000 系列遷移階段總覽](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 系列遷移路由概述本文後續的階段。")

上圖描述對應至本文各節的階段。
我們會使用雲端端遷移來避免不必要的檔案在您的本機 StorSimple 設備上重新叫用。 這種方法可避免影響本機快取行為或使用的網路頻寬，可能會影響您的生產工作負載。
雲端端遷移會在快照集上運作， (資料的磁片區複製) 。 如此一來，您的實際執行資料就會與此進程隔離，直到遷移結束時剪下。 從本質上來看，如果您遇到任何問題，就會使遷移安全且容易重複。

## <a name="considerations-around-existing-storsimple-backups"></a>關於現有 StorSimple 備份的考慮

StorSimple 可讓您以磁片區複製的形式進行備份。 本文使用新的磁片區複製來遷移您的即時檔案。
如果您還需要遷移您的即時資料以外的備份，則本文中的所有指引仍適用。 唯一的不同之處在于，您會從需要遷移的最舊備份磁片區複本開始，而不是從新的磁片區複製開始。

順序如下：

* 決定您必須遷移的最小磁片區複製集合。 建議您盡可能將這份清單保持最少，因為您遷移的備份越多，整體遷移程式將會花費更久的時間。
* 進行遷移程式時，請從您想要遷移的最舊磁片區複本開始，然後在每個後續的遷移中使用下一個最舊的磁片區。
* 當每個磁片區複製遷移完成時，您必須取得 Azure 檔案共用快照集。 [Azure 檔案共用快照](storage-snapshots-files.md) 集是您針對 Azure 檔案共用的檔案和資料夾結構保留時間點備份的方式。 完成遷移之後，您將需要這些快照集，以確保您在進行遷移時，每個磁片區複製品都有保留的版本。
* 確定您為所有 Azure 檔案共用（由相同的 StorSimple 磁片區提供）採用 Azure 檔案共用快照集。 磁片區複製位於磁片區層級，Azure 檔案共用快照集位於共用層級。 您必須在磁片區複製作業完成之後，在每個 Azure 檔案共用上 (共用快照集) 。
* 針對磁片區複製重複執行遷移程式，並在每個磁片區複製之後拍攝共用快照集，直到您到達即時資料的快照為止。 下列階段將說明遷移磁片區複製的程式。 

如果您完全不需要移動備份，而且可以在只遷移即時資料完成之後，在 Azure 檔案共用端上啟動新的備份鏈，那麼在遷移時降低複雜性以及遷移將會花費的時間量。 您可以決定是否要移動備份，以及每個磁片區的數量 (不是您在 StorSimple 中) 的每個共用。

## <a name="phase-1-get-ready"></a>第1階段：準備就緒

:::row:::
    :::column:::
        ![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        遷移的基礎是磁片區複製和虛擬雲端設備，稱為 StorSimple 8020。
此階段著重于在 Azure 中部署這些資源。
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>部署 StorSimple 8020 虛擬裝置

部署雲端設備是需要安全性、網路功能和一些其他考慮的流程。

> [!IMPORTANT]
> 下列指南包含一些不必要的部分。 閱讀並遵循一開始的文章，直到「步驟3」為止。 然後返回此文章。 您目前不需要完成該指南中的「步驟3」或其以外的任何工作。

[StorSimple 8020 虛擬裝置的部署](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>判斷要使用的磁片區複製

當您準備好開始進行遷移時，第一個步驟是建立新的磁片區複製，就像備份一樣，它會捕捉 StorSimple 雲端儲存體的目前狀態。 針對您擁有的每個 StorSimple 磁片區進行複製。
如果您需要移動備份，您所使用的第一個磁片區複製不是新建立的複本，而是最舊的磁片區複製 (您需要遷移的最舊備份) 。
如需詳細指引，請參閱「 [現有 StorSimple 備份的考慮](#considerations-around-existing-storsimple-backups) 」一節。

> [!IMPORTANT]
> 下列指南包含一些不必要的部分。 請閱讀並遵循連結至章節中所述的步驟。 然後返回此文章。 您不需要遵循「後續步驟」一節。

[建立磁碟區複製](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>使用磁片區複製

第1階段的最後一個階段是建立您所選擇的磁片區複製，可在 Azure 中的8020虛擬裝置上取得。

> [!IMPORTANT]
> 下列指南包含必要的步驟，但也包含將磁片區格式化的結尾指令。 **不要格式化磁片** 區從開始，讀取並追蹤連結至「第7節」，直到指示：「10。 若要將簡單磁片區格式化 ...」 請在遵循此步驟之前停止，並返回此文章。

[在 Azure 中的8020虛擬裝置上掛接磁片區複製](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>階段1摘要

現在您已完成第1階段，您將完成下列步驟：

* 在 Azure 中部署 StorSimple 8020 虛擬應用裝置。
* 決定您將開始進行遷移的磁片區複製。
* 已將您的磁片區複製 (s)  (每個動態磁碟區) 到 Azure 中的 StorSimple 虛擬裝置，而且其資料可供進一步使用。

## <a name="phase-2-cloud-vm"></a>第2階段：雲端 VM

:::row:::
    :::column:::
        ![圖例顯示現在可以布建 VM，並透過 iSCSI 將磁片區複製 (或多個) 公開給該 VM。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        在 Azure 中的 StorSimple 8020 虛擬裝置上提供您的初始複製品之後，現在就可以布建 VM，並透過 iSCSI 將磁片區複製 (或多個) 公開給該 VM。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

Azure 中的 Windows Server 虛擬機器就像 StorSimple 8020 一樣，這是一種只在遷移期間才需要的暫存基礎結構。
您部署的 VM 設定主要取決於您要同步的 (檔案和) 資料夾的專案數目。 如果您有任何疑慮，建議您採用更高的效能設定。

單一 Windows Server 最多可以同步30個 Azure 檔案共用。
您所決定的規格必須包含每個共用/路徑或 StorSimple 磁片區的根目錄，並計算 (檔案和資料夾) 的專案數目。

資料的整體大小不是瓶頸，而是您需要量身訂做機器規格的專案數目。

* [瞭解如何根據您需要同步的 (檔案和) 資料夾的專案數目，來調整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

    **請注意：** 先前連結的文章會顯示一個資料表，其中包含伺服器記憶體 (RAM) 的範圍。 指向 Azure VM 的大型數位。 您可以為您的內部部署電腦提供較小的數位。

* [瞭解如何部署 Windows server VM。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> 請確定 VM 部署在與 StorSimple 8020 虛擬裝置相同的 Azure 區域中。 如果在這種情況下，您也需要從目前儲存的區域變更雲端資料的區域，您可以在稍後的步驟中布建 Azure 檔案共用。

> [!IMPORTANT]
> 通常會使用內部部署 Windows Server 來前端您的內部部署 StorSimple 設備。 在這種設定中，您可以在該 Windows Server 上啟用「[重復資料刪除](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)」功能。 **如果您將重復資料刪除與 StorSimple 資料搭配使用，請務必在此 Azure VM 上啟用重復資料刪除。** 請勿將此檔案層級重復資料刪除與 StorSimples 內建的區塊層級重復資料刪除混淆，而不需要採取任何動作。

> [!IMPORTANT]
> 若要優化效能，請為您的雲端 VM 部署 **快速的 OS 磁片** 。 您將會在作業系統磁片上儲存所有資料磁片區的同步處理資料庫。 此外，請確定您建立的是 **大型作業系統磁片**。 根據 StorSimple 磁片區上)  (檔案和資料夾的專案數目，OS 磁片可能需要 **數百 GiB** 的空間來容納同步資料庫。

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>將 StorSimple 8020 磁片區公開給 Azure VM

在這個階段中，您會透過 iSCSI 將一或多個 StorSimple 磁片區從8020虛擬裝置連接到您已布建的 Windows Server VM。

> [!IMPORTANT]
> 針對下列文章，請只完成 **雲端設備的 Get 私人 IP** 並透過 **iSCSI** 區段進行連線，並返回此文章。

1. [取得雲端設備的私人 IP](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [透過 iSCSI 連接](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>第2階段摘要

現在您已完成第2階段，您有： 

* 在與8020虛擬 StorSimple 設備相同的區域中布建 Windows Server VM
* 透過 iSCSI 將所有適用的磁片區從8020公開至 Windows Server VM。
* 當您在裝載的磁片區上使用伺服器 VM 上的檔案總管時，現在應該會看到檔案和資料夾內容。

當您針對所有需要遷移的磁片區完成這些步驟時，請只繼續進行第3階段。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>第3階段：設定 Azure 檔案共用，並準備好開始 Azure 檔案同步

:::row:::
    :::column:::
        ![圖例顯示需要判斷及布建一些 Azure 檔案共用，並在內部部署環境中建立 Windows Server 來取代 StorSimple 設備。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        在這個階段中，您將會決定和布建數個 Azure 檔案共用，建立內部部署的 Windows Server 做為 StorSimple 設備的取代，並將該伺服器設定為 Azure 檔案同步。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>將現有的命名空間對應至 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>部署 Azure 檔案共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> 如果您需要從 StorSimple 資料所在的目前區域變更 Azure 區域，請在您想要使用的新區域中布建 Azure 檔案共用。 您可以在布建保存 Azure 檔案共用的儲存體帳戶時，藉由選取區域來判斷區域。 請確定您在下方布建的 Azure 檔案同步資源位於相同的新區域中。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果您需要從 StorSimple 資料所在的目前區域變更 Azure 區域，則您已在新區域中布建 Azure 檔案共用的儲存體帳戶。 當您部署此儲存體同步服務時，請確定您已選取相同的區域。

### <a name="deploy-an-on-premises-windows-server"></a>部署內部部署 Windows Server

* 建立 Windows Server 2019-最小 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 容錯移轉叢集。 請勿重複使用您可能已面向 StorSimple 8100 或8600的伺服器。
* 將直接連結的存放裝置布建或新增 (DAS （相較于 NAS）（) 不支援）。

最佳做法是讓新的 Windows Server 擁有等於或大於您的 StorSimple 8100 或8600設備的儲存體數量，而不是在本機提供快取。 您將使用與 StorSimple 設備相同的方式來使用 Windows Server，如果它具有與設備相同的儲存體數量，則快取體驗應該類似（如果不同）。
您可以從 Windows Server 隨時新增或移除存放裝置。 這可讓您調整本機磁片區大小，以及可供快取使用的本機儲存體數量。

### <a name="prepare-the-windows-server-for-file-sync"></a>準備 Windows Server 以進行檔案同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>設定 Windows Server 上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 必須準備就緒，並已連線到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必開啟雲端階層處理！** 雲端階層處理是一種 AFS 功能，可讓本機伺服器的儲存容量比儲存在雲端的儲存容量少，但有完整的命名空間可用。 本機上的有趣資料也會在本機快取，以提供快速的本機存取效能。 在此步驟開啟雲端階層處理的另一個原因是，我們不想要在此階段同步處理檔案內容，此時只應移動命名空間。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>階段4：設定 Azure VM 進行同步處理

:::row:::
    :::column:::
        ![說明如何透過 Azure 檔案同步連接 VM，以及開始從 StorSimple 磁片區複製 (s) 開始移動檔案的第一輪。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        這個階段會考慮使用 iSCSI 掛接的 Azure VM、第一個磁片區複製 (s) 。 在這個階段中，您將會透過 Azure 檔案同步取得 VM 連線，並開始從 StorSimple 磁片區複製 (s) 開始移動檔案的第一輪。
        
    :::column-end:::
:::row-end:::

您已設定您的內部部署伺服器，將會取代您的 StorSimple 8100 或8600設備，以供 Azure 檔案同步。 

設定 Azure VM 是幾乎相同的程式，還有一個額外的步驟。 下列步驟將引導您完成此程式。

> [!IMPORTANT]
> 重要的是，Azure VM 未 **設定啟用雲端階層處理！** 您將會在整個遷移期間，將此伺服器的磁片區與較新的磁片區複製交換。 雲端階層處理對於 CPU 使用率沒有任何好處，您應該避免此情況。

1. [部署 AFS 代理程式。 (參閱上一節) ](#prepare-the-windows-server-for-file-sync)
2. [正在準備 VM 以進行 Azure 檔案同步。](#get-the-vm-ready-for-azure-file-sync)
3. [設定同步處理](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>讓 VM 準備好 Azure 檔案同步

Azure 檔案同步是用來將檔案從掛接的 iSCSI StorSimple 磁片區移至目標 Azure 檔案共用。
在此遷移程式中，您會在相同的磁碟機號底下，將數個磁片區複製掛接到您的 VM。 Azure 檔案同步必須設定為可查看您已裝載為較新版本之檔案和資料夾的下一個磁片區複製，並更新透過 Azure 檔案同步連接的 Azure 檔案共用。 

> [!IMPORTANT]
> 若要這樣做，必須先在伺服器上設定登錄機碼，才能設定 Azure 檔案同步。

1. 在 VM 的系統磁片磁碟機上建立新的目錄。 Azure 檔案同步的資訊將必須保存在那裡，而不是在載入的磁片區複製上。 例如：`"C:\syncmetadata"`
2. 開啟 regedit，並找出下列登錄 hive： `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. 建立字串類型的新索引鍵，名為： ***MetadataRootPath***
4. 將完整路徑設定為您在系統磁碟區上建立的目錄，例如： `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>設定 Azure VM 上的 Azure 檔案同步

此步驟類似于上一節，討論您如何在內部部署伺服器上設定 AFS。

差別在於，您必須在此伺服器上啟用雲端階層處理，且必須確定正確的資料夾已連線到正確的 Azure 檔案共用。 否則，您的 Azure 檔案共用和資料內容的命名將不相符，且沒有任何方法可重新命名雲端資源或本機資料夾，而不需要重新設定同步。

請參閱上一 [節，以瞭解如何在 Windows Server 上設定 Azure 檔案同步](#configure-azure-file-sync-on-the-windows-server)。

### <a name="step-4-summary"></a>步驟4摘要

至此，您已成功在 Azure VM 上設定 Azure 檔案同步，您已將 StorSimple 磁片區複製 () via iSCSI。
資料現在會從 Azure VM 流向各種 Azure 檔案共用，而且您的內部部署 Windows Server 上會出現完全厭倦的命名空間。

> [!IMPORTANT]
> 請確定目前沒有任何變更或授與 Windows Server 的使用者存取權。

透過 Azure VM 移至 Azure 檔案共用的初始磁片區複製資料可能需要很長的時間，可能需要數周的時間。 估計所需的時間很棘手，取決於許多因素。 最值得注意的是，Azure VM 可以存取 StorSimple 磁片區上的檔案，以及快速 Azure 檔案同步可以處理需要同步處理的檔案和資料夾的速度。 

從經驗來說，我們可以假設頻寬（因此實際的資料大小）扮演次級角色。 這個或任何後續的遷移迴圈將花費的時間大多取決於每秒可處理的專案數。 因此，例如 1 TiB 含100000的檔案和資料夾，最可能會比只有50000檔案和資料夾的 1 TiB 完成。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>第5階段：逐一查看多個磁片區複製

:::row:::
    :::column:::
        ![說明如何使用多個磁片區複製來將停機時間降到最低，並告訴完成同步處理的方式。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        如先前的階段所討論，初始同步處理可能需要很長的時間。 您的使用者和應用程式仍在存取內部部署 StorSimple 8100 或8600設備。 這表示變更會累積，而且每一天都會有更大的即時資料和初始磁片區複製之間的差異，而您目前正在進行遷移、表單。 在本節中，您將瞭解如何使用多個磁片區複製來將停機時間降到最低，並告訴完成同步處理的時間。
    :::column-end:::
:::row-end:::

可惜的是，遷移流程不是瞬間的。 這表示上述的即時資料差異是不可避免的結果。 好消息是，您可以重複掛接新磁片區複製的程式。 每個磁片區複製的差異將會逐漸減少。 最後，同步處理將會在您視為可接受的時間內完成，讓使用者和應用程式離線以切換至您的內部部署 Windows server。

重複執行下列步驟，直到同步處理完成的時間夠短，讓使用者和應用程式離線：

1. [判斷指定磁片區複製的同步是否完成。](#determine-when-sync-is-done)
2. [將新的磁片區複製 (的) ，並將它掛接至8020虛擬裝置。](#the-next-volume-clones)
3. [判斷同步處理的完成時間。](#determine-when-sync-is-done)
4. [剪下策略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>下一個磁片區複製 (s) 

我們已討論過將磁片區複製 (的) 本文稍早所述。
這個階段有兩個動作：

1. [進行磁片區複製](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [裝載該磁片區複製 (查看上述) ](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>判斷同步處理的完成時間

當同步處理完成時，您可以停止您的時間測量，並判斷是否需要重複執行磁片區複製並掛接的程式，或者，如果最近一次磁片區複製所花費的時間同步夠小，

為了判斷同步已完成：

1. 開啟事件檢視器，然後流覽至 [**應用程式和服務**]
2. 流覽並開啟 **Microsoft\FileSync\Agent\Telemetry**
3. 尋找最新的 **事件 9102**，其對應至已完成的同步會話
4. 選取 [**詳細資料**]，並確認**SyncDirection**值為 [**上傳**]
5. 檢查 **HResult** 並確認它顯示 **0**。 這表示同步會話已成功。 如果 HResult 為非零值，則同步期間發生錯誤。如果 **PerItemErrorCount** 大於0，表示某些檔案或資料夾未正確地進行同步處理。 HResult 為 0，但 PerItemErrorCount 大於 0，是有可能的。 到目前為止，您不需要擔心 PerItemErrorCount。 稍後我們將會攔截這些檔案。 如果此錯誤計數很重要，有上千個專案，請與客戶支援人員聯繫，並要求連接到 Azure 檔案同步產品群組，以取得最佳、下個階段的指導方針。
6. 查看在資料列中使用 HResult 0 的多個9102事件。 這表示這個磁片區複製的同步已完成。

### <a name="cut-over-strategy"></a>剪下策略

1. 判斷從磁片區複製同步處理的速度是否夠快。  (Delta 夠小。 ) 
2. 讓 StorSimple 設備離線。
3. 最終 RoboCopy。

測量時間，並判斷從最近的磁片區複製同步處理是否可以在時間範圍內完成，而您可以在系統中承受停機時間。

現在可停用 StorSimple 設備的使用者存取。 不再有任何變更。 停機時間已開始。
您必須讓設備保持上線且已連線，但現在必須防止其變更。

在第6階段中，您會在上次磁片區複製之後趕上即時資料中的任何差異。

## <a name="phase-6-a-final-robocopy"></a>第6階段：最終 RoboCopy

到目前為止，您的內部部署 Windows Server 與 StorSimple 8100 或8600設備之間有兩種差異：

1. 可能有檔案尚未同步 (請參閱上述事件記錄檔中的 **PerItemErrors**) 
2. StorSimple 設備具有已填入的快取與 Windows Server，這是目前不會儲存在本機的檔案內容的命名空間。

![圖例顯示 Windows Server 的快取如何進入設備的狀態，並確保沒有任何檔案留下最後 RoboCopy。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

我們可以將 Windows Server 的快取帶到設備的狀態，並確保沒有任何檔案留下最後一個 RoboCopy。

> [!CAUTION]
> 您遵循的 RoboCopy 命令很重要，如下所述。 我們只想要複製本機的檔案，以及之前未透過磁片區複製 + 同步處理方法移動的檔案。 我們可以在完成遷移之後，解決這些問題不會在稍後同步的問題。  (查看 [Azure 檔案同步疑難排解](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。 在刪除時，您不會錯過的檔案名中最有可能無法列印的字元。 ) 

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
      允許 RoboCopy 執行多執行緒。 預設值為8，最大值為128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      以 UNICODE (覆寫現有的記錄) ，以將狀態輸出至記錄檔。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      輸出至主控台視窗。 搭配使用於記錄檔的輸出。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B 
   :::column-end:::
   :::column span="1":::
      以備份應用程式所使用的相同模式來執行 RoboCopy。 它可讓 RoboCopy 移動目前使用者沒有許可權的檔案。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許 RoboCopy 只考慮來源 (StorSimple 設備之間的差異) 和目標 (Windows Server 目錄) 。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      檔案複製的精確度 (預設值為/COPY： DAT) 、複製旗標： D = Data、A = Attributes、T = 時間戳記、S = Security = NTFS Acl、O = Owner info、U = 審核資訊
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      複製所有檔案資訊 (等同于/COPY： DATSOU) 
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目錄複本的精確度 (預設值為/DCOPY： DA) 、複製旗標： D = Data、A = Attributes、T = 時間戳記
   :::column-end:::
:::row-end:::

您應該針對 Windows 伺服器上的每個目錄，針對您已使用檔案同步處理至 Azure 檔案的目標，執行此 RoboCopy 命令。

您可以平行執行這些命令的多個。
完成此 RoboCopy 步驟之後，您就可以讓使用者和應用程式存取 Windows Server，就像之前的 StorSimple 設備一樣。

請參閱 robocopy 記錄檔 (s) ，以查看是否已遺留檔案。 如果問題應該存在，在大部分情況下，您可以在完成遷移之後解決這些問題，並將您的使用者和應用程式重新發佈到您的 Windows Server。 如果您需要修正任何問題，請在第7階段之前這麼做。

您可能需要在 Windows Server 上建立您之前在 StorSimple 資料上的 SMB 共用。 您可以事先載入此步驟，並于稍後執行它，而不會遺失時間，但您必須確保在此時間點之前，不會對 Windows server 上的檔案進行任何變更。

如果您有 DFS-N 部署，可以將 DFN 命名空間指向新的伺服器資料夾位置。 如果您沒有 DFS-N 部署，而您在本機使用 Windows Server 前端了 8100 8600 設備，則可以將該伺服器從網域中移出，並將新的 Windows Server 加入網域，並將新的 Windows Server 加入至網域，提供與舊伺服器相同的伺服器名稱，以及相同的共用名稱，然後讓您的使用者可以看到新伺服器的剪下、群組原則或腳本。

## <a name="phase-7-deprovision"></a>第7階段：取消布建

在最後一個階段中，您已逐一查看多個磁片區複製，最後能夠在您將 StorSimple 設備離線之後，將使用者存取權切換至新的 Windows Server。

您現在可以開始取消布建不必要的資源。
開始之前，最好先在生產環境中觀察新的 Azure 檔案同步部署。 這可提供選項來修正您可能遇到的任何問題。

一旦您滿意並觀察到您的 AFS 部署至少有幾天，您就可以依照下列順序開始取消布建資源：

1. 關閉我們用來透過檔案同步將資料從磁片區複製到 Azure 檔案共用的 Azure VM。
2. 移至 Azure 中的儲存體同步服務資源，並取消註冊 Azure VM。 這會將它從所有同步群組中移除。

    > [!WARNING]
    > **請確定您挑選的是正確的電腦。** 您已關閉雲端 VM，這表示它應該會在已註冊的伺服器清單中顯示為唯一的離線服務器。 在此步驟中，您不能選擇內部部署 Windows Server，這麼做會將其取消註冊。

3. 刪除 Azure VM 及其資源。
4. 停用8020虛擬 StorSimple 設備。
5. 取消布建 Azure 中的所有 StorSimple 資源。
6. 從您的資料中心拔下 StorSimple 實體設備。

您的遷移已完成。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 檔案同步。尤其是雲端階層處理原則的彈性。

如果您在 Azure 入口網站中看到，或從先前的事件中看到某些檔案永久未同步，請參閱疑難排解指南以取得解決這些問題的步驟。

* [Azure 檔案同步總覽： aka.ms/AFS](https://aka.ms/AFS)
* [雲端階層處理](storage-sync-cloud-tiering.md) 
* [Azure 檔案同步疑難排解指南](storage-sync-files-troubleshoot.md)
