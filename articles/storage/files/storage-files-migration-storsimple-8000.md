---
title: StorSimple 8000 系列遷移至 Azure 檔案同步
description: 瞭解如何將 StorSimple 8100 或8600應用裝置遷移至 Azure 檔案同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d04b38fac2b42d2d510902c7ba54ddebb8e3f410
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330305"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 和8600遷移至 Azure 檔案同步

StorSimple 8000 系列是由8100或8600實體、內部部署設備和其雲端服務元件所代表。 您可以將這兩個設備的資料移轉至 Azure 檔案同步環境。 本文提供成功遷移至 Azure 檔案同步所需的背景知識和遷移步驟。

## <a name="storsimple"></a>StorSimple

StorSimple 是已終止的 Microsoft 產品。 本產品和其雲端服務的延伸支援將于 31 2022 年12月到期。 請務必立即開始規劃從 StorSimple 進行遷移。

Azure 檔案同步是 StorSimple 應用裝置可以遷移到的預設和策略性長期 Azure 服務。

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 針對自訂的遷移計畫，以電子郵件 AzureFilesMigration@microsoft .com，並在遷移期間提供協助。

## <a name="azure-file-sync"></a>Azure 檔案同步

Azure 檔案同步是以兩個主要元件為基礎的 Microsoft 雲端服務：

* 檔案同步處理和雲端階層處理。
* 檔案共用是 Azure 中的原生儲存體，可透過多個通訊協定（例如 SMB 和檔案 REST）進行存取。 Azure 檔案共用相當於 Windows Server 上的檔案共用，您可以原生方式將其掛接為網路磁碟機機。 它支援屬性、許可權和時間戳記等重要的檔案精確度層面。 有了 Azure 檔案共用，應用程式或服務就不再需要解讀儲存在雲端中的檔案和資料夾。 您可以透過熟悉的通訊協定和用戶端（例如 Windows 檔案瀏覽器）以原生方式存取它們 這使得 Azure 檔案共用理想且最具彈性的方法，將一般用途的檔案伺服器資料和部分應用程式資料儲存在雲端中。

本文著重于遷移步驟。 如果您想要深入瞭解 Azure 檔案同步，建議您先閱讀下列文章：

* [Azure 檔案同步-總覽](https://aka.ms/AFS "概觀")
* [Azure 檔案同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，以及保證可用性。 後者則需要保持停機時間最少，使其符合或只會稍微超過一般的維護時段。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 系列 Azure 檔案同步的遷移路徑

需要本機 Windows 伺服器，才能執行 Azure 檔案同步代理程式。 Windows Server 最少可以是2012R2 伺服器，但最好是 Windows Server 2019。

有許多替代的遷移路徑，它會建立太長的文章來記載所有檔案，並說明為什麼它們會在我們建議的路由中，將風險或缺點放在本文的最佳作法中。

![StorSimple 8000 系列遷移階段總覽](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 系列遷移路由概述本文中進一步討論的階段。")

上圖描述對應于本文各節的階段。
我們使用雲端端遷移來避免不必要地將檔案重新叫用至您的本機 StorSimple 應用裝置。 這種方法可避免影響本機快取行為或網路頻寬使用，這可能會影響您的生產工作負載。
雲端端的遷移作業會在您資料的快照集（磁片區複製）上運作。 因此，您的生產資料會與此進程隔離，直到遷移結束為止。 從本質上來說，這是一個備份，讓遷移更安全且容易重複，萬一您遇到任何問題。

## <a name="considerations-around-existing-storsimple-backups"></a>關於現有 StorSimple 備份的考慮

StorSimple 可讓您以磁片區複本的形式來進行備份。 本文使用新的磁片區複製來遷移您的即時檔案。
除了您的即時資料以外，如果您還需要遷移備份，則本文中的所有指導方針仍然適用。 唯一的差別在於，您要從最舊的備份磁片區複本開始，而不是從新的磁片區複製開始。

順序如下：

* 決定您必須遷移的最小磁片區複製集。 建議您盡可能將此清單保留為最小值，因為您遷移的備份越多，整體遷移程式將會花費更久的時間。
* 進行遷移程式時，請從您想要遷移的最舊磁片區複製開始，並在每次後續的遷移時使用下一個最舊的。
* 當每個磁片區複製遷移完成時，您必須採用 Azure 檔案共用快照集。 [Azure 檔案共用快照](storage-snapshots-files.md)集可讓您保留 azure 檔案共用的檔案和資料夾結構的時間點備份。 在遷移完成後，您將需要這些快照集，以確保您在進行遷移的過程中，每個磁片區複本都有保留的版本。
* 請確定您為所有 Azure 檔案共用（由相同的 StorSimple 磁片區提供服務）取得 Azure 檔案共用快照集。 磁片區複本位於磁片區層級上，而 Azure 檔案共用快照集位於共用層級。 在磁片區複製完成遷移之後，您必須建立共用快照集（在每個 Azure 檔案共用上）。
* 針對磁片區複製重複執行遷移程式，並在每個磁片區複製後建立共用快照集，直到您到達即時資料的快照集為止。 遷移磁片區複製的程式會在下列各階段中說明。 

如果您不需要完全移動備份，而且可以在只遷移即時資料後於 Azure 檔案共用端啟動新的備份鏈，那麼降低遷移的複雜性和遷移所需的時間，這會很有説明。 您可以決定是否要針對 StorSimple 中的每個磁片區（而不是每個共用）移動備份及執行多少。

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

部署雲端設備是需要安全性、網路功能和一些其他考慮的程式。

> [!IMPORTANT]
> 下列指南包含一些不必要的區段。 從開頭閱讀並遵循文章，直到「步驟3」。 然後回到本文。 您目前不需要完成本指南中的「步驟3」或任何專案。

[部署 StorSimple 8020 虛擬裝置](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>判斷要使用的磁片區複製

當您準備好開始進行遷移時，第一步就是建立新的磁片區複製-就像備份一樣，它會捕獲 StorSimple 雲端儲存體的目前狀態。 針對您擁有的每個 StorSimple 磁片區進行複製。
如果您需要移動備份，則您使用的第一個磁片區複製不是新建立的複製，而是您需要遷移的最舊磁片區複製（最舊的備份）。
如需詳細指引，請參閱「[現有 StorSimple 備份的考慮](#considerations-around-existing-storsimple-backups)」一節。

> [!IMPORTANT]
> 下列指南包含一些不必要的區段。 讀取並只遵循 [連結至] 區段中所述的步驟。 然後回到本文。 您不需要遵循「後續步驟」一節。

[建立磁片區的複製](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>使用磁片區複製

第1階段的最後一個階段是讓您選擇的磁片區複製，可在 Azure 中的8020虛擬應用裝置上取得。

> [!IMPORTANT]
> 下列指南包含必要的步驟，但在結尾-用來格式化磁片區的指示。 **不要格式化磁片**區從開頭開始讀取並追蹤連結到「第7節」，直到指示：「10」。 若要格式化簡單磁片區，...」 請在遵循此步驟之前停止，並返回本文。

[在 Azure 中的8020虛擬裝置上掛接磁片區複製](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>第1階段摘要

現在您已完成第1階段，您將完成下列作業：

* 已在 Azure 中部署 StorSimple 8020 虛擬裝置。
* 決定您將開始進行遷移的磁片區複製。
* 已將您的磁片區複製（每個即時磁片區一個）掛接到 Azure 中的 StorSimple 虛擬裝置，其資料可供進一步使用。

## <a name="phase-2-cloud-vm"></a>第2階段：雲端 VM

:::row:::
    :::column:::
        ![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        在 Azure 中的 StorSimple 8020 虛擬裝置上提供初始複製之後，現在就可以布建 VM，並透過 iSCSI 向該 VM 公開磁片區複本（或多個）。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

Azure 中的 Windows Server 虛擬機器就像是 StorSimple 8020，這是只有在遷移期間才需要的一小部分基礎結構。
您部署的 VM 設定主要取決於您將同步處理的專案數（檔案和資料夾）。 如果您有任何疑慮，建議您採用較高的效能設定。

單一 Windows Server 最多可同步30個 Azure 檔案共用。
您決定的規格必須包含每個共用/路徑或 StorSimple 磁片區的根目錄，並計算專案（檔案和資料夾）。

資料的整體大小較少瓶頸-這是您需要為機器規格量身打造的專案數。

* [瞭解如何根據您需要同步的專案數目（檔案和資料夾）來調整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)
* [瞭解如何部署 Windows 伺服器 VM。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> 請確定 VM 部署在與 StorSimple 8020 虛擬裝置相同的 Azure 區域中。 如果在這項遷移過程中，您也需要從目前儲存的區域變更雲端資料的區域，您可以在稍後的步驟中，于布建 Azure 檔案共用時執行此動作。

### <a name="expose-the-storsimple-8020-volumes-to-the-vm"></a>向 VM 公開 StorSimple 8020 磁片區

在此階段中，您會將一或多個 StorSimple 磁片區從8020虛擬應用裝置連接到您已布建的 Windows Server VM。

> [!IMPORTANT]
> 針對下列文章，請只完成**雲端設備的取得私人 IP**和透過**iSCSI 連接**一節，並返回本文。

1. [取得雲端設備的私人 IP](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [透過 iSCSI 連接](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>第2階段摘要

現在您已完成第2階段，您有： 

* 已在與8020虛擬 StorSimple 應用裝置相同的區域中布建 Windows Server VM
* 透過 iSCSI 將所有適用的磁片區從8020公開至 Windows Server VM。
* 當您在掛接的磁片區上的伺服器 VM 上使用檔案瀏覽器時，您現在應該會看到檔案和資料夾內容。

當您針對需要遷移的所有磁片區完成這些步驟時，請只繼續進行第3階段。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>第3階段：設定 Azure 檔案共用並準備好開始 Azure 檔案同步

:::row:::
    :::column:::
        ![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        在此階段中，您將決定和布建數個 Azure 檔案共用、建立內部部署的 Windows Server 做為 StorSimple 應用裝置，並將該伺服器設定為 Azure 檔案同步。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>將現有的命名空間對應至 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>部署 Azure 檔案共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> 如果您需要從 StorSimple 資料所在的目前區域變更 Azure 區域，請在您想要使用的新區域中布建 Azure 檔案共用。 當您布建保存 Azure 檔案共用的儲存體帳戶時，您可以藉由選取區域來判斷。 請確定您在下方布建的 Azure 檔案同步資源都在相同的新區域中。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果您需要從 StorSimple 資料所在的目前區域變更 Azure 區域，則您已在新的區域中為您的 Azure 檔案共用布建儲存體帳戶。 當您部署此儲存體同步服務時，請確定您已選取相同的區域。

### <a name="deploy-an-on-premises-windows-server"></a>部署內部部署 Windows Server

* 建立 Windows Server 2019-最低 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 故障切換叢集。 請勿重複使用您可能已面向 StorSimple 8100 或8600的伺服器。
* 布建或新增直接連接存放裝置（與不支援的 NAS 相較之下的 DAS）。

最佳做法是讓新的 Windows Server 比您的 StorSimple 8100 或8600應用裝置在本機可供快取使用的儲存體數量相等或更大。 您將使用與 StorSimple 應用裝置相同的方式來使用 Windows 伺服器，如果它的儲存體數量與設備相同，則快取體驗應該類似（如果不相同）。
您可以從您的 Windows Server 新增或移除存放裝置。 這可讓您調整本機磁片區大小，以及可供快取使用的本機儲存體數量。

### <a name="prepare-the-windows-server-for-file-sync"></a>準備 Windows Server 以進行檔案同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>設定 Windows Server 上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 必須準備好並聯機到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必開啟雲端階層處理！** 雲端階層處理是一項 AFS 功能，可讓本機伺服器比儲存在雲端的儲存容量少，但仍可使用完整的命名空間。 本機上有趣的資料也會在本機快取，以取得快速的本機存取效能。 在此步驟開啟雲端階層處理的另一個原因是，我們不想要在這個階段同步檔案內容，此時只應移動命名空間。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>第4階段：設定 Azure VM 以進行同步處理

:::row:::
    :::column:::
        ![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        此階段會考慮您的 Azure VM 與 iSCSI 掛接的第一次磁片區複製。 在此階段中，您會透過 Azure 檔案同步來取得 VM 連線，並開始從 StorSimple 磁片區複製移動檔案的第一個迴圈。
        
    :::column-end:::
:::row-end:::

您已設定內部部署伺服器，將會取代您的 StorSimple 8100 或8600設備，以供 Azure 檔案同步。 

設定 Azure VM 幾乎是完全相同的程式，還有一個額外的步驟。 下列步驟將引導您完成整個程式。

> [!IMPORTANT]
> Azure VM 若**未設定為啟用雲端階層處理**，這點很重要！ 在整個遷移過程中，您將會以較新的磁片區複本來交換此伺服器的數量。 雲端階層處理對於您應該避免的 CPU 使用量沒有任何好處和額外負荷。

1. [部署 AFS 代理程式。（請參閱上一節）](#prepare-the-windows-server-for-file-sync)
2. [讓 VM 準備好進行 Azure 檔案同步。](#get-the-vm-ready-for-azure-file-sync)
3. [設定同步處理](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>讓 VM 準備好進行 Azure 檔案同步

Azure 檔案同步可用來將檔案從掛接的 iSCSI StorSimple 磁片區移至目標 Azure 檔案共用。
在此遷移過程中，您會在相同的磁碟機號下，將數個磁片區複本掛接至您的 VM。 Azure 檔案同步必須設定為查看您已掛接為較新版本的檔案和資料夾的下一個磁片區複製，並更新透過 Azure 檔案同步連線的 Azure 檔案共用。 

> [!IMPORTANT]
> 若要這麼做，必須先在伺服器上設定登錄機碼，然後才能設定 Azure 檔案同步。

1. 在 VM 的系統磁片磁碟機上建立新的目錄。 Azure 檔案同步資訊必須保存在該處，而不是在掛接的磁片區複製上。 例如： `“C:\syncmetadata”`
2. 開啟 regedit，並找出下列登錄區： `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. 建立 String 類型的新索引鍵，名為： ***MetadataRootPath***
4. 設定您在系統磁碟區上建立之目錄的完整路徑，例如： `C:\syncmetadata”`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>在 Azure VM 上設定 Azure 檔案同步

此步驟與上一節類似，其中討論如何在內部部署伺服器上設定 AFS。

差別在於，您不能在此伺服器上啟用雲端階層處理，而且需要確定正確的資料夾已連接到正確的 Azure 檔案共用。 否則，您的 Azure 檔案共用和資料內容的命名將不會相符，而且無法重新命名雲端資源或本機資料夾，而不需要重新設定同步。

請參閱上一[節，瞭解如何在 Windows Server 上設定 Azure 檔案同步](#configure-azure-file-sync-on-the-windows-server)。

### <a name="step-4-summary"></a>步驟4摘要

此時，您已成功地在 Azure VM 上設定 Azure 檔案同步，而您已透過 iSCSI 掛接了 StorSimple 磁片區複製。
資料現在會從 Azure VM 流向各種 Azure 檔案共用，而在您的內部部署 Windows 伺服器上會出現完全厭倦的命名空間。

> [!IMPORTANT]
> 請確認目前未進行任何變更，或已授與 Windows 伺服器的使用者存取權。

透過 Azure VM 移至 Azure 檔案共用的初始磁片區複製資料可能需要很長的時間，可能會有數周。 估計這需要花費的時間很棘手，而且取決於許多因素。 最值得注意的是，Azure VM 可以在 StorSimple 磁片區上存取檔案的速度，以及 Azure 檔案同步可以處理需要同步之檔案和資料夾的速度。 

就經驗而言，我們可以假設頻寬-因此實際的資料大小-會扮演次級角色。 此或任何後續的遷移迴圈時間，主要取決於每秒可處理的專案數。 因此，例如1具有100000檔案和資料夾的 TiB，最有可能會比只有50000個檔案和資料夾的1個 TiB 慢。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>第5階段：逐一查看多個磁片區複製

:::row:::
    :::column:::
        ![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        如前一個階段所討論，初始同步處理可能需要很長的時間。 您的使用者和應用程式仍在存取內部部署 StorSimple 8100 或8600應用裝置。 這表示變更是累積的，而且每天都會有較大的差異，而即時資料和初始磁片區複製品之間則是您目前的遷移、表單。 在本節中，您將瞭解如何使用多個磁片區複本來將停機時間降到最低，並在完成同步處理時告訴您。
    :::column-end:::
:::row-end:::

可惜的是，遷移程式並非瞬間完成。 這表示前述的即時資料差異是無法避免的結果。 好消息是，您可以重複裝載新磁片區複製的程式。 每個磁片區複製的差異將會逐漸變小。 最後，同步處理將會在您認為可接受的時間內完成，讓使用者和應用程式離線切換到您的內部部署 Windows server。

重複執行下列步驟，直到同步處理完成的時間夠快，讓使用者和應用程式離線：

1. [判斷指定磁片區複製的同步處理是否已完成。](#determine-when-sync-is-done)
2. [請建立新的磁片區複製，並將它掛接到8020虛擬裝置。](#the-next-volume-clones)
3. [判斷同步完成的時間。](#determine-when-sync-is-done)
4. [剪下策略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>下一個磁片區複製

我們已討論過本文稍早的磁片區複製。
這個階段有兩個動作：

1. [進行磁片區複製](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [掛接該磁片區複本（請參閱上文）](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>判斷同步完成的時間

完成同步處理時，您可以停止時間量測，並判斷是否需要重複執行磁片區複製並加以掛接的程式，或在最後一個磁片區複製所花費的時間同步是否夠小。

若要判斷同步處理是否已完成：

1. 開啟事件檢視器，然後流覽至 [**應用程式和服務**]
2. 導覽並開啟**Microsoft\FileSync\Agent\Telemetry**
3. 尋找最近的**事件 9102**，其對應至已完成的同步會話
4. 選取 [**詳細資料**]，並確認**SyncDirection**值為 **[上傳**]
5. 檢查**HResult**並確認它顯示**0**。 這表示同步會話已成功。 如果 HResult 為非零值，則在同步處理期間發生錯誤。如果**PerItemErrorCount**大於0，則部分檔案或資料夾未正確地進行同步處理。 HResult 為 0，但 PerItemErrorCount 大於 0，是有可能的。 此時，您不需要擔心 PerItemErrorCount。 我們稍後將會攔截這些檔案。 如果此錯誤計數很重要、數千個專案，請聯絡客戶支援，並要求連線至 Azure 檔案同步產品群組，以取得最佳、下一個階段的直接指引。
6. 檢查以查看資料列中具有 HResult 0 的多個9102事件。 這表示此磁片區複本的同步處理已完成。

### <a name="cut-over-strategy"></a>剪下策略

1. 立即判斷從磁片區複製進行同步的速度是否夠快。 （差異夠小）。
2. 讓 StorSimple 設備離線。
3. 最後的 RoboCopy。

測量時間，並判斷從最近的磁片區複製同步處理是否可在某個時間範圍內完成，您可以在系統中承受停機。

現在您可以停用使用者存取 StorSimple 應用裝置的時間。 不會再變更。 停機已開始。
您需要讓設備保持上線並聯機，但現在必須避免變更。

在第6階段中，您將會趕上上一次磁片區複製後即時資料中的任何差異。

## <a name="phase-6-a-final-robocopy"></a>第6階段：最終 RoboCopy

此時，您的內部部署 Windows Server 與 StorSimple 8100 或8600設備之間有兩個差異：

1. 可能有未同步處理的檔案（請參閱上述事件記錄檔中的**PerItemErrors** ）
2. StorSimple 應用裝置具有已填入的快取與 Windows Server，但目前不會在本機儲存任何檔案內容。

![說明先前的總覽影像部分的影像，可協助您專注于本文的這個小節。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

我們可以將 Windows Server 的快取帶入設備的狀態，並確定沒有任何檔案留下最後的 RoboCopy。

> [!CAUTION]
> 您遵循的 RoboCopy 命令是必要的，完全如下所述。 我們只想要複製本機的檔案，以及先前未透過磁片區複製 + 同步處理方法移動的檔案。 我們可以在完成遷移之後，解決其未同步處理的問題。 （請參閱[Azure 檔案同步疑難排解](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。 當您刪除檔案時，檔案名中可能不會遺漏的不可列印字元。）

RoboCopy 命令：

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景資訊：

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
      /UNILOG：<file name>
   :::column-end:::
   :::column span="1":::
      將狀態輸出到記錄檔做為 UNICODE （覆寫現有的記錄）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      輸出至主控台視窗。 用於搭配記錄檔的輸出。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許 RoboCopy 只考慮來源（StorSimple 應用裝置）與目標（Windows Server 目錄）之間的差異。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      檔案複製的精確度（預設值為/COPY： DAT），複製旗標： D = 資料，A = 屬性，T = 時間戳記，S = 安全性 = NTFS Acl，O = 擁有者資訊，U = 審核資訊
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      複製所有檔案資訊（相當於/COPY： DATSOU）
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目錄複本的精確度（預設值為/DCOPY： DA），複製旗標： D = 資料，A = 屬性，T = 時間戳記
   :::column-end:::
:::row-end:::

您應該針對 Windows 伺服器上的每個目錄執行此 RoboCopy 命令，做為目標，您已使用檔案同步處理來設定 Azure 檔案。

您可以平行執行這些命令的多個。
完成此 RoboCopy 步驟之後，您就可以讓使用者和應用程式存取 Windows Server，就像之前的 StorSimple 應用裝置一樣。

請參閱 robocopy 記錄檔，以查看是否已遺留檔案。 如果問題應該存在，在大部分的情況下，您可以在遷移完成後加以解決，而您的使用者和應用程式也會重新加入至您的 Windows Server。 如果您需要修正任何問題，請在第7階段之前進行。

您很可能需要在先前已啟用 StorSimple 資料的 Windows 伺服器上建立 SMB 共用。 您可以將此步驟前置，並在稍早執行，而不會在這裡遺失時間，但您必須確定在此時間點之前，Windows 伺服器上不會變更檔案。

如果您有一個 DFS-N 部署，您可以將 DFN 命名空間指向新的伺服器資料夾位置。 如果您沒有 DFS-N 部署，而且您使用 Windows Server 在本機前端您的 8100 8600 應用裝置，您可以將該伺服器從網域中取出，並將新的 Windows Server 加入網域，並將它與舊伺服器做為相同的伺服器名稱。和相同的共用名稱，然後針對您的使用者、群組原則或腳本，切換到新伺服器就會保持透明。

## <a name="phase-7-deprovision"></a>第7階段：取消布建

在最後一個階段中，您已逐一查看多個磁片區複本，最後能夠在將 StorSimple 設備離線之後，將使用者存取權切換到新的 Windows Server。

您現在可以開始取消布建不必要的資源。
在開始之前，最好先觀察生產環境中新的 Azure 檔案同步部署。 這可讓您選擇修正可能遇到的任何問題。

一旦您滿意併發現您的 AFS 部署至少有幾天之後，您就可以依照下列順序開始取消布建資源：

1. 關閉我們用來透過檔案同步將資料從磁片區複製移至 Azure 檔案共用的 Azure VM。
2. 移至 Azure 中的儲存體同步服務資源，並取消註冊 Azure VM。 這會將它從所有同步處理群組中移除。

    > [!WARNING]
    > **請確定您挑選的是正確的電腦。** 您已關閉雲端 VM，這表示它應該會在已註冊的伺服器清單中顯示為唯一的離線服務器。 在此步驟中，您不能挑選內部部署 Windows 伺服器，這麼做會將它取消註冊。

3. 刪除 Azure VM 及其資源。
4. 停用8020虛擬 StorSimple 應用裝置。
5. 取消布建 Azure 中的所有 StorSimple 資源。
6. 從您的資料中心拔下 StorSimple 實體設備。

您的遷移已完成。

## <a name="next-steps"></a>後續步驟

更熟悉 Azure 檔案同步。特別是雲端階層處理原則的彈性。

如果您在 Azure 入口網站或先前的事件中看到某些檔案永遠不會同步，請參閱疑難排解指南，以瞭解解決這些問題的步驟。

* [Azure 檔案同步總覽： aka.ms/AFS](https://aka.ms/AFS)
* [雲端階層處理](storage-sync-cloud-tiering.md) 
* [Azure 檔案同步疑難排解指南](storage-sync-files-troubleshoot.md)
