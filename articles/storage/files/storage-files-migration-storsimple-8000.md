---
title: 將 StorSimple 8000 系列遷移至 Azure 檔案同步
description: 瞭解如何將 StorSimple 8100 或8600設備遷移至 Azure 檔案同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 08ed07adbfe0fc4b22d8a3d0afcfc9ab1312dba4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134342"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>將 StorSimple 8100 和8600遷移至 Azure 檔案同步

StorSimple 8000 系列以8100或8600實體、內部部署設備及其雲端服務元件表示。 您可以將其中一個設備的資料移轉至 Azure 檔案同步環境。 Azure 檔案同步是 StorSimple 設備可遷移至的預設和策略性長期 Azure 服務。

StorSimple 8000 系列將于2022年12月 [結束使用](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 。 請務必儘快開始規劃您的遷移。 本文提供成功遷移至 Azure 檔案同步所需的背景知識和遷移步驟。

## <a name="phase-1-prepare-for-migration"></a>第1階段：準備進行遷移

本節包含從 StorSimple 磁片區遷移至 Azure 檔案共用時，您應該採取的步驟。

### <a name="inventory"></a>清查

當您開始規劃您的遷移時，請先找出您需要遷移的所有 StorSimple 設備和磁片區。 完成之後，您可以為您決定最佳的遷移路徑。

* StorSimple 實體設備 (8000 系列) 使用此遷移指南。
* 虛擬裝置（ [StorSimple 1200 系列）使用不同的遷移指南](storage-files-migration-storsimple-1200.md)。

### <a name="migration-cost-summary"></a>遷移成本摘要

透過 StorSimple 資料管理員資源中的資料轉換服務工作，從 StorSimple 磁片區遷移至 Azure 檔案共用是免費的。 在遷移期間和之後可能會產生其他成本：

* **網路輸出：** 您的 StorSimple 檔案會存留在特定 Azure 區域內的儲存體帳戶中。 如果您布建 Azure 檔案共用，並將其遷移至位於相同 Azure 區域的儲存體帳戶，將不會發生輸出成本。 您可以將檔案移到不同區域中的儲存體帳戶，作為此遷移的一部分。 在這種情況下，輸出成本將適用于您。
* **Azure 檔案共用交易：** 將檔案複製到 Azure 檔案共用 (作為遷移的一部分或在某個) 之外時，交易成本會隨著檔案和中繼資料的寫入而套用。 最佳做法是在遷移期間于交易優化層上啟動 Azure 檔案共用。 完成遷移後，切換到您想要的階層。 下列階段將在適當的時間點呼叫這個。
* **變更 Azure 檔案共用層：** 變更 Azure 檔案共用的層級成本交易。 在大部分的情況下，遵循上一個點的建議將會更符合成本效益。
* **儲存體成本：** 當此遷移開始將檔案複製到 Azure 檔案共用時，就會耗用並計費 Azure 檔案儲存體儲存體。
* **StorSimple：** 在您有機會取消布建 StorSimple 裝置和儲存體帳戶之前，會繼續進行儲存體、備份和設備的 StorSimple 成本。

### <a name="direct-share-access-vs-azure-file-sync"></a>直接共用-存取與 Azure 檔案同步

Azure 檔案共用會開啟全新的商機，以結構化您的檔案服務部署。 Azure 檔案共用只是雲端中的一個 SMB 共用，您可以設定讓使用者使用熟悉的 Kerberos 驗證和現有的 NTFS 許可權直接透過 SMB 通訊協定存取， (檔案和資料夾 Acl) 以原生方式運作。 深入瞭解 [以身分識別為基礎的 Azure 檔案共用存取](storage-files-active-directory-overview.md)。

[Azure 檔案同步](./storage-sync-files-planning.md)直接存取的替代方法。Azure 檔案同步是可在內部部署環境中快取常用檔案的直接類比功能。

Azure 檔案同步是 Microsoft 雲端服務，以兩個主要元件為基礎：

* 檔案同步處理和雲端階層處理。
* 檔案共用是 Azure 中的原生儲存體，可透過多種通訊協定來存取，例如 SMB 和檔案 REST。

Azure 檔案共用會在儲存的檔案（如屬性、許可權和時間戳記）中保留重要的檔案精確度。 有了 Azure 檔案共用，應用程式或服務就不再需要解讀儲存在雲端中的檔案和資料夾。 您可以透過熟悉的通訊協定和用戶端（例如 Windows 檔案總管）以原生方式存取它們。 Azure 檔案共用可讓您將一般用途的檔案伺服器資料和應用程式資料儲存在雲端中。 Azure 檔案共用的備份是內建的功能，可透過 Azure 備份進一步增強。

本文著重于遷移步驟。 如果您想要在遷移之前深入瞭解 Azure 檔案同步，請參閱下列文章：

* [Azure 檔案同步總覽](./storage-sync-files-planning.md "概觀")
* [Azure 檔案同步部署指南](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple 服務資料加密金鑰

當您第一次設定 StorSimple 設備時，它會產生服務資料加密金鑰，並指示您安全地儲存金鑰。 此金鑰是用來加密 StorSimple 設備儲存檔案的相關 Azure 儲存體帳戶中的所有資料。

服務資料加密金鑰是成功遷移的必要項。 現在是從您的記錄中，針對清查中的每個設備取得此金鑰的好時機。

如果您在記錄中找不到金鑰，則可以從設備取得金鑰。 每個設備都有唯一的加密金鑰。 若要取出金鑰：

* 透過 Azure 入口網站提出對 Microsoft Azure 的支援要求。 要求的內容應該具有 StorSimple 裝置序號，以及取得「服務資料加密金鑰」的要求。
* StorSimple 支援工程師會與您聯繫，以取得螢幕共用會議的要求。
* 在會議開始之前，請確定您 [是透過序列主控台](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) 或透過 [遠端 PowerShell 會話](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)連線到 StorSimple 設備。

> [!CAUTION]
> 當您決定如何連線到 StorSimple 設備時，請考慮下列各項：
>
> * 透過 HTTPS 會話連接是最安全且建議的選項。
> * 直接連線至裝置序列主控台是安全的，但透過網路交換器連線至序列主控台則否。
> * HTTP 會話連接是一個選項，但 *未加密*。 除非在已關閉、受信任的網路中使用它們，否則不建議使用。

### <a name="storsimple-volume-backups"></a>StorSimple 磁片區備份

StorSimple 會在磁片區層級上提供差異備份。 Azure 檔案共用也有這種功能，稱為共用快照集。

決定是否做為遷移的一部分，也有義務移動任何備份。

> [!CAUTION]
> 如果您必須從 StorSimple 磁片區遷移備份，請停止此處。
>
> 您目前只能遷移最近的磁片區備份。 備份遷移的支援將于2020年底到達。 如果您現在開始，就無法在稍後「找不到」您的備份。 在即將推出的版本中，備份必須「播放」至 Azure 檔案共用，從最舊到最新的 Azure 檔案共用，並在其間採用 Azure 檔案共用快照集。

如果您只想要遷移即時資料，而且沒有備份的需求，您可以繼續進行本指南。 如果您有一或兩個月的短期備份保留需求，您可以決定立即繼續遷移，並在該期間之後取消布建 StorSimple 資源。 這種方法可讓您視需要在 Azure 檔案共用端建立任意數量的備份歷程記錄。 在您保持兩個系統的執行狀態時，會產生額外的費用，如果您需要短期備份保留期，則應該考慮這種方法。

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>將您現有的 StorSimple 磁片區對應至 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>儲存體帳戶數目

您的遷移可能會受益于部署多個儲存體帳戶，每個帳戶都保留較少的 Azure 檔案共用。

如果您的檔案共用 (許多使用者或應用程式所使用的高度可用或應用程式) ，兩個 Azure 檔案共用可能會達到您的儲存體帳戶的效能限制。 因此，最佳作法是遷移至多個儲存體帳戶，每個帳戶都有各自的個別檔案共用，而且每個儲存體帳戶通常不能超過兩個或三個共用。

最佳做法是部署儲存體帳戶，每個都有一個檔案共用。 如果您有封存共用，您可以將多個 Azure 檔案共用集區放在相同的儲存體帳戶中。

這些考慮適用于透過 Azure VM 或服務) 的 [直接雲端存取](#direct-share-access-vs-azure-file-sync) (，而不是 Azure 檔案同步。如果您打算只在這些共用上使用 Azure 檔案同步，請將多個群組組成單一 Azure 儲存體帳戶。 也請考慮您可能想要將應用程式隨即轉移到雲端，然後再直接存取檔案共用。 或者，您也可以開始使用 Azure 中的服務，此服務也會受益于擁有較高的 IOPS 和可用輸送量編號。

如果您已建立共用清單，請將每個共用對應至其所在的儲存體帳戶。

> [!IMPORTANT]
> 決定 Azure 區域，並確定每個儲存體帳戶和 Azure 檔案同步資源都符合您所選取的區域。

### <a name="phase-1-summary"></a>階段1摘要

在第1階段結束時：

* 您對 StorSimple 裝置和磁片區有很大的總覽。
* 資料轉換服務已準備好在雲端中存取您的 StorSimple 磁片區，因為您已針對每個 StorSimple 裝置取出服務資料加密金鑰。
* 您有需要遷移磁片區的計畫，以及如何將磁片區對應至適當的 Azure 檔案共用和儲存體帳戶數目。

> [!CAUTION]
> 如果您必須從 StorSimple 磁片區遷移備份，請在 **此停止**。
>
> 這種遷移方法依賴目前無法遷移備份的新資料轉換服務功能。 備份遷移的支援將于2020年底到達。 您目前只能遷移您的即時資料。 如果您現在開始，就無法在稍後「找不到」您的備份。 您必須使用 Azure 檔案共用快照集，將備份「播放」至 Azure 檔案共用，從最舊到最新到最新的資料到即時資料。

如果您只想要遷移即時資料，而且沒有備份的需求，您可以繼續進行本指南。

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>第2階段：部署 Azure 儲存體和遷移資源

本節討論有關部署 Azure 中所需之不同資源類型的考慮。 有些會保存您的資料移轉後，有些則只需要進行遷移。 在您完成部署計畫之前，請勿開始部署資源。 有時候，在部署 Azure 資源之後，可能會變更其特定層面。

### <a name="deploy-storage-accounts"></a>部署儲存體帳戶

您可能需要部署數個 Azure 儲存體帳戶。 根據您的部署計畫，每個檔案共用都會保留較少數目的 Azure 檔案共用，這是在本文上一節中完成的。 移至 Azure 入口網站以 [部署您規劃的儲存體帳戶](../common/storage-account-create.md#create-a-storage-account)。 針對任何新的儲存體帳戶，請考慮遵循下列基本設定。

#### <a name="subscription"></a>訂用帳戶

您可以使用您用於 StorSimple 部署的相同訂用帳戶或不同的訂用帳戶。 唯一的限制是您的訂用帳戶必須與 StorSimple 訂用帳戶位於相同的 Azure Active Directory 租使用者中。 開始進行遷移之前，請考慮將 StorSimple 訂用帳戶移至正確的租使用者。 您只能移動整個訂用帳戶。 個別的 StorSimple 資源無法移至不同的租使用者或訂用帳戶。

#### <a name="resource-group"></a>資源群組

資源群組可協助組織資源和管理員管理許可權。 [深入瞭解 Azure 中的資源群組](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)。

#### <a name="storage-account-name"></a>儲存體帳戶名稱

您的儲存體帳戶名稱將會成為 URL 的一部分，並具有某些字元限制。 在您的命名慣例中，請考慮儲存體帳戶名稱在世界中必須是唯一的，只允許小寫字母和數位，且必須介於3到24個字元之間，而且不允許使用連字號或底線等特殊字元。 如需詳細資訊，請參閱 [Azure 儲存體資源命名規則](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)。

#### <a name="location"></a>位置

儲存體帳戶的位置或 Azure 區域非常重要。 如果您使用 Azure 檔案同步，則所有的儲存體帳戶都必須位於與儲存體同步服務資源相同的區域中。 您挑選的 Azure 區域應接近或集中于您的本機伺服器和使用者。 部署資源之後，您就無法變更其區域。

您可以選擇與 StorSimple 資料 (儲存體帳戶) 目前所在的不同區域。

> [!IMPORTANT]
> 如果您從目前的 StorSimple 儲存體帳戶位置挑選不同的區域，則在遷移期間 [將會](https://azure.microsoft.com/pricing/details/bandwidth) 收取輸出費用。 資料會離開 StorSimple 區域，並輸入新的儲存體帳戶區域。 如果您停留在相同的 Azure 區域中，則不會收取任何頻寬費用。

#### <a name="performance"></a>效能

您可以選擇針對 Azure 檔案共用或標準儲存體挑選 premium 儲存體 (SSD) 。 標準儲存體包含 [數個適用于檔案共用的層級](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share)。 從 StorSimple 遷移的大部分客戶而言，標準儲存體是最適合的選擇。

仍不確定嗎？

* 如果您需要 [Premium Azure 檔案共用的效能](understanding-billing.md#provisioned-billing)，請選擇 premium 儲存體。
* 針對一般用途的檔案伺服器工作負載選擇標準儲存體，包括經常性存取資料和封存資料。 如果雲端中的共用上唯一的工作負載將會 Azure 檔案同步，也請選擇標準儲存體。

#### <a name="account-kind"></a>帳戶種類

* 針對標準儲存體，請選擇 *[StorageV2 (一般用途 v2])*。
* 針對 [premium 檔案共用]，選擇 [ *FileStorage*]。

#### <a name="replication"></a>複寫

有數個可用的複寫設定。 深入瞭解不同的複寫類型。

只選擇下列兩個選項之一：

* *本機冗余儲存體 (LRS)*。
* *區域冗余儲存體 (ZRS)*，在所有 Azure 區域中都無法使用。

> [!NOTE]
> 只有 LRS 和 ZRS 冗余類型可與大型 100-TiB 容量 Azure 檔案共用相容。

目前不支援所有變化中的地理區域冗余儲存體 (GRS) 。 您稍後可以切換您的冗余類型，並在其支援抵達 Azure 時切換至 GRS。

#### <a name="enable-100-tib-capacity-file-shares"></a>啟用 100-TiB-容量檔案共用

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="顯示建立儲存體帳戶 Azure 入口網站中 [Advanced] 索引標籤的影像。":::
    :::column-end:::
    :::column:::
        在 Azure 入口網站的 [新增儲存體帳戶] 嚮導的 [ **Advanced** ] 區段下，您可以在此儲存體帳戶中啟用 **大型檔案共用** 支援。 如果您無法使用此選項，您很可能會選取錯誤的冗余類型。 確定您只選取 LRS 或 ZRS，此選項才會變成可用。
    :::column-end:::
:::row-end:::

選擇大型的 100-TiB 容量檔案共用有幾個優點：

* 相較于較小的 5 TiB 容量檔案共用，您的效能會大幅增加 (例如，IOPS) 的10倍。
* 您的遷移將會明顯地完成。
* 確定檔案共用的容量足以容納您要遷移至其中的所有資料。
* 涵蓋未來的成長。

### <a name="azure-file-shares"></a>Azure 檔案共用

建立儲存體帳戶之後，請移至儲存體帳戶的 [檔案 **共用** ] 區段，並根據您在第1階段的遷移方案部署適當數量的 Azure 檔案共用。 針對您在 Azure 中的新檔案共用，請考慮遵循下列基本設定。

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Azure 入口網站螢幕擷取畫面，其中顯示新的檔案共用 UI。":::
    :::column-end:::
    :::column:::
        </br>**名稱**</br>支援小寫字母、數位及連字號。</br></br>**配額**</br>此處的配額相當於 Windows Server 實例上的 SMB 硬配額。 最佳做法是不要在此設定配額，因為達到配額時，您的遷移和其他服務將會失敗。</br></br>**階層**</br>選取 [針對您的新檔案共用 **優化的交易** ]。 在遷移期間，將會發生許多交易。 更符合成本效益的方式，就是將您的階層變更為最適合您工作負載的層級。
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple 資料管理員

將保留您的遷移作業的 Azure 資源稱為 **StorSimple 資料管理員**。 選取 [ **新增資源**]，並搜尋它。 然後選取 [建立]。

此暫存資源用於協調流程。 您會在遷移完成後取消布建。 它應該部署在與 StorSimple 儲存體帳戶相同的訂用帳戶、資源群組和區域中。

### <a name="azure-file-sync"></a>Azure 檔案同步

使用 Azure 檔案同步，您可以新增內部部署快取最常存取的檔案。 類似于 StorSimple 的快取功能，Azure 檔案同步雲端階層處理功能可提供本機存取延遲，並可改善 Windows Server 實例和多網站同步上可用快取容量的控制權。如果您的目標是內部部署快取，則在您的區域網路中， (實體伺服器和容錯移轉叢集的準備 Windows Server VM，) 具有足夠的直接連接儲存體容量。

> [!IMPORTANT]
> 尚未設定 Azure 檔案同步。 最好是在共用完成遷移之後設定 Azure 檔案同步。 部署 Azure 檔案同步不應該在遷移的第4階段之前啟動。

### <a name="phase-2-summary"></a>第2階段摘要

在第2階段結束時，您將會部署您的儲存體帳戶和所有 Azure 檔案共用。 您也會有 StorSimple 資料管理員資源。 當您設定遷移作業時，將會在第3階段使用後者。

## <a name="phase-3-create-and-run-a-migration-job"></a>第3階段：建立和執行遷移作業

本節說明如何設定遷移作業，並謹慎地對應 StorSimple 磁片區上的目錄，該磁片區應複製到您選取的目標 Azure 檔案共用。 若要開始使用，請移至您的 StorSimple 資料管理員，尋找功能表上的 **工作定義** ，然後選取 [ **+ 作業定義**]。 目標儲存體類型是預設的 **Azure 檔案共用**。

![StorSimple 8000 系列的遷移作業類型。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "工作定義 Azure 入口網站的螢幕擷取畫面，其中已開啟新的 [作業定義] 對話方塊，該對話方塊會要求作業類型：複製到檔案共用或 blob 容器。")

> [!IMPORTANT]
> 執行任何遷移作業之前，請先停止 StorSimple 磁片區的任何自動排程備份。

:::row:::
    :::column:::
        ![StorSimple 8000 系列的遷移作業。](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "資料轉換服務工作之新工作建立表單的螢幕擷取畫面。")
    :::column-end:::
    :::column:::
        **作業定義名稱**</br>這個名稱應該指出您要移動的檔案集合。 建議您將類似 Azure 檔案共用的名稱提供給它。 </br></br>**作業執行所在的位置**</br>選取區域時，您必須選取與 StorSimple 儲存體帳戶相同的區域，如果無法使用，則為接近該區域的區域。 </br></br><h3>來源</h3>**來源訂用帳戶**</br>選取您用來儲存 StorSimple 裝置管理員資源的訂用帳戶。 </br></br>**StorSimple 資源**</br>選取您的應用裝置所註冊的 StorSimple 裝置管理員。 </br></br>**服務資料加密金鑰**</br>如果您在記錄中找不到金鑰，請檢查本文 [之前的章節](#storsimple-service-data-encryption-key) 。 </br></br>**裝置**</br>選取保存您想要遷移之磁片區的 StorSimple 裝置。 </br></br>**磁碟區**</br>選取來源磁片區。 稍後您會決定是否要將整個磁片區或子目錄遷移至目標 Azure 檔案共用。 </br></br><h3>目標</h3>選取訂用帳戶、儲存體帳戶和 Azure 檔案共用作為此遷移作業的目標。
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 最新的磁片區備份將用來執行遷移。 請確定至少有一個磁片區備份存在，否則作業將會失敗。 此外，請確定您所擁有的最新備份很接近最新版本，以盡可能減少差異到 live share。 在執行您剛才建立的作業 *之前* ，可能值得手動觸發和完成另一種磁片區備份。

### <a name="directory-mapping"></a>目錄對應

目錄對應對您的遷移作業而言是選擇性的。 如果您將區段保留空白，StorSimple 磁片區根目錄上的 *所有* 檔案和資料夾都會移至目標 Azure 檔案共用的根目錄。 在大多數情況下，將整個磁片區的內容儲存在 Azure 檔案共用中並不是最佳的方法。 通常最好是將磁片區的內容分割成 Azure 中的多個檔案共用。 如果您尚未建立方案，請參閱先將 [StorSimple 磁片區對應至 Azure 檔案共用](#map-your-existing-storsimple-volumes-to-azure-file-shares) 。

在您的遷移計畫中，您可能已決定 StorSimple 磁片區上的資料夾必須分割至多個 Azure 檔案共用。 如果是這種情況，您可以透過下列方式來完成分割：

1. 定義多個作業，以遷移一個磁片區上的資料夾。 每個都有相同的 StorSimple 磁片區來源，但不同于目標的 Azure 檔案共用。
1. 使用工作建立表單的 **目錄對應** 區段，並遵循特定的 [對應語義](#semantic-elements)，精確地指定要將 StorSimple 磁片區中的哪些資料夾遷移到指定的檔案共用。

> [!IMPORTANT]
> 提交表單時，無法驗證此表單中的路徑和對應運算式。 如果指定的對應不正確，則工作可能會完全失敗或產生非預期的結果。 在這種情況下，通常最好是刪除 Azure 檔案共用，重新建立它，然後修正共用的新遷移作業中的對應語句。 使用固定的對應語句來執行新作業，可以修正省略的資料夾，並將它們帶入現有的共用中。 不過，只有因為路徑拼寫錯誤而省略的資料夾可以透過這種方式定址。

#### <a name="semantic-elements"></a>語義元素

對應是由左至右表示： [\source 路徑] \> [\target 路徑]。

|語義字元          | 意義  |
|:---------------------------|:---------|
| **\\**                     | 根層級指標。       |
| **\>**                     | [來源] 和 [目標對應] 運算子。     |
|**\|** 或傳回 (新行)  | 兩個資料夾對應指令的分隔。 </br>或者，您可以省略此字元並選取 **Enter 鍵** ，以在其本身的行上取得下一個對應運算式。        |

### <a name="examples"></a>範例
將資料夾 *使用者資料* 的內容移至目標檔案共用的根目錄：
``` console
\User data > \
```
將整個磁片區內容移至目標檔案共用上的新路徑：
``` console
\ > \Apps\HR tracker
```
將源資料夾內容移至目標檔案共用上的新路徑：
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
將多個來源位置排序為新的目錄結構：
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>語義規則

* 一律指定相對於根層級的資料夾路徑。
* 使用根層級指標 "" 來開始每個資料夾路徑 \\ 。
* 請勿包含磁碟機號。
* 指定多個路徑時，來源或目標路徑不能重迭：</br>
   不正確來源路徑重迭範例：</br>
    *\\folder\1 > \\ 資料夾*</br>
    *\\資料夾 \\ 1 \\ 2 > \\ folder2*</br>
   不正確目標路徑重迭範例：</br>
   *\\資料夾 > \\*</br>
   *\\folder2 > \\*</br>
* 不存在的源資料夾將會被忽略。
* 將會建立不存在於目標上的資料夾結構。
* 如同 Windows，資料夾名稱不區分大小寫，但仍會保留大小寫。

> [!NOTE]
> 轉換作業將不會複製 *\System Volume Information* 資料夾的內容以及您 StorSimple 磁片區上的 *$Recycle。*

### <a name="phase-3-summary"></a>階段3摘要

在第3階段結束時，您將從 StorSimple 磁片區將資料轉換服務作業執行至 Azure 檔案共用。 您現在可以將焦點放在共用的遷移工作完成) ，或將您的資訊工作者和應用程式的共用存取權導向至 Azure 檔案共用，以設定共用 (的 Azure 檔案同步。

## <a name="phase-4-access-your-azure-file-shares"></a>階段4：存取 Azure 檔案共用

有兩個主要策略可存取 Azure 檔案共用：

* **Azure 檔案同步**：將 [Azure 檔案同步部署](#deploy-azure-file-sync) 到內部部署 Windows Server 實例。 Azure 檔案同步具有本機快取的所有優點，就像 StorSimple 一樣。
* **直接共用-存取**： [部署直接共用存取](#deploy-direct-share-access)。 如果您在特定 Azure 檔案共用的存取案例中無法受益于本機快取，或您不再有裝載內部部署 Windows Server 實例的能力，請使用此策略。 在此，您的使用者和應用程式將會繼續透過 SMB 通訊協定存取 SMB 共用。 這些共用已不再位於內部部署伺服器上，而是直接在雲端中。

您應該已在本指南的 [第1階段](#phase-1-prepare-for-migration) 決定哪一個選項最適合您。

本節的其餘部分將著重于部署指示。

### <a name="deploy-azure-file-sync"></a>部署 Azure 檔案同步

現在就可以部署 Azure 檔案同步的一部分。

1. 建立 Azure 檔案同步雲端資源。
1. 在您的內部部署伺服器上部署 Azure 檔案同步代理程式。
1. 向雲端資源註冊伺服器。

尚未建立任何同步處理群組。 只有在 Azure 檔案共用的遷移作業完成後，才會設定與 Azure 檔案共用的同步處理。 如果您在完成遷移之前開始使用 Azure 檔案同步，就會導致您的遷移不必要，因為您無法輕易地判斷何時會起始剪下。

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果您想要在遷移完成後變更您的資料所在的 Azure 區域，請在與目標儲存體帳戶相同的區域中部署儲存體同步服務，以進行此遷移。

#### <a name="deploy-an-on-premises-windows-server-instance"></a>部署內部部署 Windows Server 實例

* 以虛擬機器或實體伺服器的最小 2012R2) 建立 Windows Server 2019 (。 也支援 Windows Server 容錯移轉叢集。 請勿重複使用 StorSimple 8100 或8600面向的伺服器。
* 布建或新增直接連接的存放裝置。 不支援網路連接存放裝置。

最佳做法是讓新的 Windows Server 實例具有相當或更大量的儲存空間，而不是您的 StorSimple 8100 或8600設備可在本機上進行快取。 您將使用與 StorSimple 設備相同的方式來使用 Windows Server 實例。 如果它具有與設備相同的儲存體數量，則快取體驗應該類似（如果不同）。 您可以從 Windows Server 實例新增或移除存放裝置。 這項功能可讓您調整本機磁片區大小，以及可供快取使用的本機儲存體數量。

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>準備 Windows Server 實例以進行檔案同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>設定 Windows Server 實例上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 實例必須準備就緒，並已連線到網際網路，才能進行此程式。

> [!IMPORTANT]
> 您的 StorSimple 將檔案和資料夾遷移至 Azure 檔案共用必須先完成，才能繼續進行。 請確定檔案共用未進行任何變更。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 請務必開啟雲端階層處理。 雲端階層處理是 Azure 檔案同步的功能，可讓本機伺服器的儲存容量比儲存在雲端的儲存容量少，但有完整的命名空間可用。 本機上的有趣資料也會在本機快取，以提供快速的本機存取效能。 在此步驟開啟雲端階層處理的另一個原因是，我們不想要在此階段同步檔案內容。 此時只應移動命名空間。

### <a name="deploy-direct-share-access"></a>部署直接共用-存取

:::row:::
    :::column:::
        [![逐步指南和示範如何將 Azure 檔案共用安全地公開給資訊工作者和應用程式-按一下即可播放！](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        這段影片示範如何以五個簡單的步驟，安全地直接將 Azure 檔案共用公開給資訊工作者和應用程式。</br>
        影片參考一些主題的專用檔：

* [身分識別概觀](storage-files-active-directory-overview.md)
* [如何將儲存體帳戶加入網域](storage-files-identity-auth-active-directory-enable.md)
* [Azure 檔案共用的網路功能總覽](storage-files-networking-overview.md)
* [如何設定公用和私人端點](storage-files-networking-endpoints.md)
* [如何設定 S2S VPN](storage-files-configure-s2s-vpn.md)
* [如何設定 Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [如何設定 Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [如何設定 DNS 轉送](storage-files-networking-dns.md)
* [設定 DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>階段4摘要

在這個階段中，您已在 StorSimple 資料管理員中建立並執行多個資料轉換服務作業。 這些作業已將您的檔案和資料夾遷移至 Azure 檔案共用。 您也已部署 Azure 檔案同步或準備您的網路和儲存體帳戶，以供直接共用存取。

## <a name="phase-5-user-cut-over"></a>第5階段：使用者剪下

這個階段就是要總結您的遷移：

* 規劃您的停機時間。
* 在第3階段中的資料轉換作業執行時，掌握在 StorSimple 端產生的使用者和應用程式的任何變更。
* 透過直接共用存取權，讓您的使用者使用 Azure 檔案同步或 Azure 檔案共用，讓您的使用者無法通過新的 Windows Server 實例。

### <a name="plan-your-downtime"></a>規劃您的停機時間

針對您的使用者和應用程式，這種遷移方法需要一些停機時間。 其目標是要將停機時間保持在最小值。 下列考慮可以提供協助：

* 執行資料轉換作業時，讓您的 StorSimple 磁片區維持可用狀態。
* 當您完成執行共用的資料移轉作業之後，就可以從 StorSimple 磁片區或共用移除使用者存取權， (至少寫入存取) 。 最終 RoboCopy 將會趕上您的 Azure 檔案共用。 然後您可以剪下您的使用者。 您執行 RoboCopy 的位置，取決於您是選擇使用 Azure 檔案同步或直接共用存取。 關於 RoboCopy 的後續章節涵蓋該主體。
* 當您完成 RoboCopy catch 之後，您就可以直接向使用者公開新的位置，方法是使用 Azure 檔案共用，或是使用 Azure 檔案同步的 Windows Server 實例上的 SMB 共用。DFS-N 部署通常可協助您快速且有效率地完成剪下。 它會將您現有的共用位址保持一致，並重新指向至包含已遷移檔案和資料夾的新位置。

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>判斷您的命名空間已完全同步至您的伺服器

當您使用 Azure 檔案共用的 Azure 檔案同步時，請務必確定您的整個命名空間在啟動任何本機 RoboCopy *之前* 已完成下載至伺服器。 下載您的命名空間所需的時間取決於 Azure 檔案共用中的專案數。 有兩種方法可以判斷您的命名空間是否已完全抵達伺服器。

#### <a name="azure-portal"></a>Azure 入口網站

您可以使用 Azure 入口網站來查看您的命名空間何時完全到達。

* 登入 Azure 入口網站，然後移至您的同步處理群組。 檢查同步處理群組和伺服器端點的同步狀態。
* 有趣的方向是下載內容。 如果伺服器端點是新布建的，則會顯示 [ **初始同步** 處理]，表示命名空間仍處於關機狀態。
在變更為 **初始同步** 處理之後，您的命名空間將會在伺服器上完整填入。 您現在可以繼續進行本機 RoboCopy。

#### <a name="windows-server-event-viewer"></a>Windows Server 事件檢視器

您也可以使用 Windows Server 實例上的事件檢視器，來判斷命名空間何時完全到達。

1. 開啟 **事件檢視器**，然後移至 [ **應用程式和服務**]。
1. 移至並開啟 **Microsoft\FileSync\Agent\Telemetry**。
1. 尋找最新的 **事件 9102**，這會對應到已完成的同步會話。
1. 選取 [ **詳細資料**]，並確認您正在查看 **SyncDirection** 值 **下載** 的事件。
1. 當您的命名空間已完成下載至伺服器時，將會有單一事件和 **案例**、值 **FullGhostedSync** 和 **HResult**  =  **0**。
1. 如果您錯過該事件，也可以使用 **SyncDirection**   =  **下載** 和 **案例**  =  **"RegularSync"** 尋找其他9102事件。 尋找其中一個事件也表示命名空間已完成下載，並同步處理至一般同步會話，不論目前是否有任何要同步處理的作業。

### <a name="a-final-robocopy"></a>最終 RoboCopy

目前，您的內部部署 Windows Server 實例與 StorSimple 8100 或8600設備之間有一些差異。

1. 在進行遷移時，您需要趕上使用者或應用程式在 StorSimple 端產生的變更。
1. 在使用 Azure 檔案同步的情況下： StorSimple 設備具有已填入的快取，而只有命名空間的 Windows Server 實例，此時不會儲存在本機的檔案內容。 最終 RoboCopy 可協助您在本機快取的檔案內容（如有提供）上拉出本機快取的檔案內容，並可容納在 Azure 檔案同步伺服器上，以協助您快速開始本機 Azure 檔案同步快取。
1. 由於不正確字元，資料轉換作業可能遺留了某些檔案。 若是如此，請將它們複製到已啟用 Azure 檔案同步的 Windows Server 實例。 稍後您可以調整它們，使它們能夠同步。如果您未針對特定的共用使用 Azure 檔案同步，最好是在 StorSimple 磁片區上以無效字元重新命名檔案。 然後直接對 Azure 檔案共用執行 RoboCopy。

> [!WARNING]
> Windows Server 2019 中的 Robocopy 目前遇到的問題，會 Azure 檔案同步導致從來源重新複製目標伺服器上的檔案，並在使用 robocopy 的/MIR 功能時重新上傳至 Azure。 在2019以外的 Windows 伺服器上，請務必使用 Robocopy。 慣用的選擇是 Windows Server 2016。 如果問題是透過 Windows Update 解決，就會更新這個注意事項。

> [!WARNING]
> 在伺服器具有完整下載的 Azure 檔案共用的命名空間之前，您 *不* 能啟動 RoboCopy。 如需詳細資訊，請參閱 [判斷您的命名空間何時完整下載到您的伺服器](#determine-when-your-namespace-has-fully-synced-to-your-server)。

 您只想要複製在上一次執行遷移作業之後變更的檔案，以及之前未移動過這些作業的檔案。 您可以解決此問題，原因是它們在完成遷移之後，為什麼不會在伺服器上移動。 如需詳細資訊，請參閱 [Azure 檔案同步疑難排解](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

RoboCopy 有數個參數。 下列範例會展示已完成的命令，以及選擇這些參數的原因清單。

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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
      /NP
   :::column-end:::
   :::column span="1":::
      略過記錄進度，讓記錄保持可讀取。
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
      /IT
   :::column-end:::
   :::column span="1":::
      確保在某些鏡像案例中會保留精確度。</br>範例：在兩個 Robocopy 之間執行時，檔案會遇到 ACL 變更和屬性更新，例如，它也標記為 *隱藏*。 如果沒有/IT，則 Robocopy 可能會遺漏 ACL 變更，因此不會傳送到目標位置。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      檔案複製的精確度 (預設值為/COPY： DAT) 、複製旗標： D = Data、A = Attributes、T = 時間戳記、S = Security = NTFS Acl、O = Owner information、U = 審核資訊。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      複製所有檔案資訊 (等同于/COPY： DATSOU) 。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目錄複本的精確度 (預設值為/DCOPY： DA) 、複製旗標： D = Data、A = Attributes、T = 時間戳記。
   :::column-end:::
:::row-end:::

當您設定 RoboCopy 命令的來源和目標位置時，請務必檢查來源和目標的結構，以確保它們相符。 如果您使用的是遷移作業的目錄對應功能，您的根目錄結構可能與 StorSimple 磁片區的結構不同。 如果是這種情況，您可能需要多個 RoboCopy 作業，每個子目錄各一個。 如果您不確定命令是否會如預期般執行，您可以使用 */l* 參數，它會模擬命令，而不會實際進行任何變更。

這個 RoboCopy 命令會使用/MIR，因此它不會移動 (階層式檔案相同的檔案，例如) 。 但是，如果您取得來源和目標路徑的錯誤，/MIR 也會在您的 Windows Server 實例或 Azure 檔案共用上，清除 StorSimple 來源路徑上不存在的目錄結構。 它們必須完全符合 RoboCopy 作業，才能達到其預定目標，以在進行遷移時進行最新的變更來更新您的已遷移內容。

請參閱 RoboCopy 記錄檔，以查看檔案是否已遺留。 如果有問題，請修正這些問題，然後重新執行 RoboCopy 命令。 在修正您在意的檔案或資料夾的未解決問題之前，請勿取消布建任何 StorSimple 資源。

如果您未使用 Azure 檔案同步快取有問題的特定 Azure 檔案共用，但改為選擇直接共用存取：

1. 將[您的 Azure 檔案共用掛接](storage-how-to-use-files-windows.md#mount-the-azure-file-share)為本機 Windows 電腦的網路磁碟機機。
1. 在 StorSimple 和掛接的 Azure 檔案共用之間執行 RoboCopy。 如果檔案未複製，請在 StorSimple 端修正其名稱以移除不正確字元。 然後重試 RoboCopy。 先前列出的 RoboCopy 命令可以執行多次，而不會造成 StorSimple 的不必要召回。

### <a name="user-cut-over"></a>使用者剪下

如果您使用 Azure 檔案同步，您可能需要在已啟用 Azure 檔案同步的 Windows Server 實例上，建立符合 StorSimple 磁片區上所含共用的 SMB 共用。 您可以事先載入此步驟，並于稍早的時間執行它，而不會遺失時間。 但是，您必須確定在這之前，沒有人可以存取來造成 Windows Server 實例的變更。

如果您有 DFS-N 部署，可以將 DFN-Namespaces 指向新的伺服器資料夾位置。 如果您沒有 DFS-N 部署，而您在本機使用 Windows Server 實例前端8100或8600設備，則可以將該伺服器從網域中取出。 然後，網域加入新的 Azure 檔案同步啟用的 Windows Server 實例。 在這個過程中，請為伺服器提供與舊伺服器相同的伺服器名稱和共用名稱，讓您的使用者、群組原則和腳本的剪取保持透明。

深入瞭解 [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)。

## <a name="deprovision"></a>取消佈建

當您取消布建資源時，將無法存取該資源及其資料的設定。 解除布建無法復原。 在確認之前，請不要繼續進行：

* 您的遷移已完成。
* 您即將取消布建的 StorSimple 檔案、資料夾或磁片區備份並無任何相依性。

開始之前，最好先在生產環境中觀察新的 Azure 檔案同步部署。 這段時間讓您有機會修正您可能遇到的任何問題。 在您觀察到您的 Azure 檔案同步部署至少幾天之後，您就可以開始以下列順序取消布建資源：

1. 透過 Azure 入口網站取消布建您的 StorSimple 資料管理員資源。 將會刪除所有的 DTS 工作。 您將無法輕鬆地取出複製記錄檔。 如果它們對您的記錄而言很重要，請在取消布建之前取出它們。
1. 確定您的 StorSimple 實體設備已遷移，然後將其取消註冊。 如果您不完全確定它們是否已遷移，請不要繼續進行。 如果您在仍需要時取消布建這些資源，您將無法復原資料或其設定。<br>（選擇性）您可以先取消布建 StorSimple 磁片區資源，這會清除設備上的資料。 這可能需要數天的時間，而 **不會** 在設備上抽絲剝繭零的資料。 如果這對您很重要，請從資源解除布建和根據您的原則，分別處理磁碟空間。
1. 如果 StorSimple 裝置管理員中沒有任何已註冊的裝置，您可以繼續移除該裝置管理員資源本身。
1. 現在您可以在 Azure 中刪除 StorSimple 儲存體帳戶。 同樣地，在您繼續之前，請停止並確認您的遷移已完成，而且沒有任何專案會相依于這項資料。
1. 從您的資料中心拔下 StorSimple 實體設備。
1. 如果您擁有 StorSimple 應用裝置，您就可以免費進行電腦回收。 如果您的裝置已租用，請通知 lessor 並視情況傳回裝置。

您的遷移已完成。

> [!NOTE]
> 仍有問題或遇到任何問題嗎？</br>
> 我們在此提供協助 AzureFilesMigration@microsoft.com 。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure 檔案同步： aka.ms/AFS](./storage-sync-files-planning.md)。
* 瞭解 [雲端分層](storage-sync-cloud-tiering.md) 原則的彈性。
* 在 Azure 檔案共用上[啟用 Azure 備份](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane)，以排程快照集並定義備份保留排程。
* 如果您在 Azure 入口網站中看到某些檔案永久未同步，請參閱 [疑難排解指南](storage-sync-files-troubleshoot.md) 以取得解決這些問題的步驟。