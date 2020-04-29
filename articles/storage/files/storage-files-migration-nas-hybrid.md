---
title: 內部部署 NAS 遷移至 Azure 檔案同步
description: 瞭解如何使用 Azure 檔案同步和 Azure 檔案共用，將檔案從內部部署網路連接存放裝置（NAS）位置遷移至混合式雲端部署。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247345"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 檔案同步從網路連接儲存裝置（NAS）遷移至混合式雲端部署

Azure 檔案同步適用于直接連接存放裝置（DAS）位置，且不支援同步到網路連接存放裝置（NAS）位置。
這項事實會遷移您需要的檔案，而這篇文章會引導您完成這類遷移的規劃和執行。

## <a name="migration-goals"></a>移轉目標

目標是將您在 NAS 應用裝置上擁有的共用移至 Windows 伺服器。 然後利用 Azure 檔案同步進行混合式雲端部署。 這項遷移必須以保證生產資料的完整性，以及在遷移期間提供可用性的方式來完成。 後者則需要保持停機時間最少，使其符合或只會稍微超過一般的維護時段。

## <a name="migration-overview"></a>移轉概觀

如 Azure 檔案儲存體[遷移總覽一文](storage-files-migration-overview.md)所述，使用正確的複製工具和方法很重要。 您的 NAS 應用裝置會直接在您的區域網路上公開 SMB 共用。 RoboCopy （內建 Windows Server）是在此遷移案例中移動檔案的最佳方式。

- 第1階段：[識別您需要多少個 Azure 檔案共用](#phase-1-identify-how-many-azure-file-shares-you-need)
- 第2階段：布[建適用于內部部署的 Windows Server](#phase-2-provision-a-suitable-windows-server-on-premises)
- 第3階段：[部署 Azure 檔案同步雲端資源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 第4階段：[部署 Azure 儲存體資源](#phase-4-deploy-azure-storage-resources)
- 第5階段：[部署 Azure 檔案同步代理程式](#phase-5-deploy-the-azure-file-sync-agent)
- 第6階段：[設定 Windows Server 上的 Azure 檔案同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 第7階段： [RoboCopy](#phase-7-robocopy)
- 第8階段：[使用者剪](#phase-8-user-cut-over)下

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第1階段：識別您需要多少個 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>第2階段：布建適用于內部部署的 Windows Server

* 建立 Windows Server 2019-最低 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 故障切換叢集。
* 布建或新增直接連接存放裝置（與不支援的 NAS 相較之下的 DAS）。

    如果您使用 Azure 檔案同步[雲端](storage-sync-cloud-tiering.md)階層處理功能，您所布建的儲存體數量可能會小於您目前在 NAS 應用裝置上使用的儲存空間。
    不過，當您在稍後的階段中，將檔案從較大的 NAS 空間複製到較小的 Windows Server 磁片區時，您必須以批次方式進行工作：

    1. 移動符合磁片的一組檔案
    2. 讓檔案同步和雲端階層處理互動
    3. 當磁片區上建立了更多的可用空間時，請繼續進行下一個批次檔。 
    
    您可以在您的檔案在 NAS 應用裝置上佔用的 Windows 伺服器上，布建對等的空間，以避免這種批次處理方法。 考慮在 NAS/Windows 上進行重復資料刪除。 如果您不想要將此大量儲存體永久認可到 Windows 伺服器，您可以在遷移之後和調整雲端階層處理原則之前，縮減磁片區大小。 這會建立較小的 Azure 檔案共用內部部署快取。

您部署之 Windows Server 的資源設定（運算和 RAM）主要取決於您要同步的專案數（檔案和資料夾）。 如果您有任何疑慮，建議您採用較高的效能設定。

[瞭解如何根據您需要同步的專案數目（檔案和資料夾）來調整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 先前連結的文章會呈現一個資料表，其中包含伺服器記憶體（RAM）的範圍。 您可以為伺服器提供較小的數目，但預期初始同步處理可能會花很多時間。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第3階段：部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第4階段：部署 Azure 儲存體資源

在此階段中，請參閱第1階段的對應資料表，並使用它來布建正確的 Azure 儲存體帳戶和其中的檔案共用數目。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第5階段：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>第6階段：設定 Windows Server 上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 必須準備好並聯機到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 雲端階層處理是一項 AFS 功能，可讓本機伺服器比儲存在雲端的儲存容量少，但仍可使用完整的命名空間。 本機上有趣的資料也會在本機快取，以快速存取效能。 雲端階層處理是每個 Azure 檔案同步「伺服器端點」的選擇性功能。

> [!WARNING]
> 如果您在 Windows server 磁片區上布建的儲存體比在 NAS 設備上使用的資料少，則雲端階層處理是強制性的。 如果您未開啟雲端階層處理，您的伺服器將無法釋放空間來儲存所有檔案。 將您的階層原則暫時用於遷移，設定為99% 磁片區可用空間。 完成遷移之後，請務必返回您的雲端階層處理設定，並將它設定為更長期有用的層級。

針對所有需要設定同步處理的 Azure 檔案共用/伺服器位置，重複執行同步群組建立和新增相符的伺服器資料夾，做為伺服器端點。

建立所有伺服器端點之後，同步處理就會正常運作。 您可以建立測試檔案，並查看它從您的伺服器位置同步到已連線的 Azure 檔案共用（如同步群組中的雲端端點所述）。

這兩個位置、伺服器資料夾和 Azure 檔案共用都是空的，而且會等待任一位置的資料。 在下一個步驟中，您會開始將檔案複製到 Windows Server，以供 Azure 檔案同步將其移至雲端。 如果您已啟用雲端階層處理，伺服器就會開始將檔案分層，但您應該在本機磁片區上的容量用盡。

## <a name="phase-7-robocopy"></a>第7階段： RoboCopy

基本的遷移方法是從您的 NAS 應用裝置到您的 Windows Server 的 RoboCopy，並 Azure 檔案同步至 Azure 檔案共用。

執行第一個本機複本至您的 Windows Server 目的檔案夾：

* 識別您的 NAS 應用裝置上的第一個位置。
* 識別 Windows Server 上已設定 Azure 檔案同步的相符資料夾。
* 使用 RoboCopy 開始複製

下列 RoboCopy 命令會將檔案從您的 NAS 存放裝置複製到 Windows Server 目的檔案夾。 Windows Server 會將它同步處理至 Azure 檔案共用。 

如果您在 Windows 伺服器上布建的儲存空間比您在 NAS 應用裝置上佔用的檔案少，則您已設定雲端階層處理。 當本機 Windows Server 磁片區已滿時，[雲端](storage-sync-cloud-tiering.md)階層處理將會開始，並將已成功同步處理的層級檔案分層。 雲端階層處理會產生足夠的空間，以從 NAS 設備繼續複製。 雲端階層會檢查一小時一次，以查看已同步處理的內容，並釋出磁碟空間以達到99% 磁片區可用空間。
該 RoboCopy 可以讓檔案的速度比您在本機上同步處理到雲端和層級更快，因此會用盡本機磁碟空間。 RoboCopy 將會失敗。 建議您以可避免此情況的順序來處理共用。 例如，同時不會同時啟動所有共用的 RoboCopy 作業，或只是移動符合 Windows 伺服器上目前可用空間量的共用時，才會提到一些。

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
      /UNILOG：\<檔案名\>
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
      /B 
   :::column-end:::
   :::column span="1":::
      在備份應用程式會使用的相同模式下執行 RoboCopy。 它允許 RoboCopy 移動目前使用者沒有許可權的檔案。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許在相同的目標/目的地上，連續執行此 RoboCopy 命令數次。 它會識別先前已複製的內容，並省略它。 只會處理自上次執行後發生的變更、新增和「*刪除*」。 如果命令未在之前執行，則不會省略任何內容。 */MIR*旗標是來源位置的絕佳選項，仍在積極使用和變更。
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

## <a name="phase-8-user-cut-over"></a>第8階段：使用者剪下

當您第一次執行 RoboCopy 命令時，您的使用者和應用程式仍會存取 NAS 上的檔案，而且可能會變更這些檔案。 該 RoboCopy 可能已經處理過目錄，接著移至下一個，然後來源位置（NAS）上的使用者新增、變更或刪除檔案，該檔案現在不會在目前的 RoboCopy 執行中處理。 這是預期的行為。

第一次執行是關於透過 Azure 檔案同步將大量資料移至您的 Windows Server 和雲端。第一次複製可能需要很長的時間，視下列情況而定：

* 您的下載頻寬
* 上傳頻寬
* 區域網路速度和 RoboCopy 執行緒數目符合的最佳程度
* 需要由 RoboCopy 處理的專案數（檔案和資料夾），並 Azure 檔案同步

完成初始執行之後，請再次執行命令。

第二次的完成速度會更快，因為它只需要傳輸自上次執行後所發生的變更。 在第二次執行期間，仍會累積新的變更。

重複此程式，直到您滿意完成特定位置的 RoboCopy 所需的時間量，才會在可接受的時間範圍內執行。

當您將停機時間視為可接受，而且您已準備好讓 NAS 位置離線：若要讓使用者離線存取，您可以選擇變更共用根目錄上的 Acl，讓使用者無法再存取該位置，或採取任何其他適當的步驟來防止在 NAS 上的此資料夾中變更內容。

執行最後一個 RoboCopy round。 它會收取可能錯過的任何變更。
這個最後步驟所花費的時間長短取決於 RoboCopy 掃描的速度。 您可以測量上一次執行所花的時間，來預估時間（等於停機）。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該檔案。 請務必在您的 NAS SMB 共用上設定與相同的共用層級許可權。 如果您有企業級加入網域的 NAS，則使用者 Sid 會在使用者存在於 Active Directory 中時自動符合，而 RoboCopy 會以完全精確的方式複製檔案和中繼資料。 如果您已在 NAS 上使用本機使用者，您需要將這些使用者重新建立為 Windows Server 本機使用者，並將現有的 Sid RoboCopy 移至您的 Windows Server，並將其對應到新的 Windows Server 本機使用者的 Sid。

您已完成將共用/群組共用遷移到通用的根或磁片區。 （視您在第1階段的對應而定）

您可以嘗試平行執行其中幾個複本。 我們建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 將所有資料從您的 NAS 移至 Windows 伺服器，並完成您的遷移作業之後：回到 Azure 入口網站中的***所有***同步處理群組，並調整 [雲端階層處理磁片區可用空間百分比] 值，使其更適合快取使用率，例如20%。 

雲端階層處理磁片區可用空間原則會在磁片區層級上運作，其中可能會有多個伺服器端點同步。 如果您忘記調整即使一個伺服器端點上的可用空間，同步處理會繼續套用最嚴格的規則，並嘗試保留99% 的可用磁碟空間，使本機快取無法如您預期般執行。 除非您的目標只包含很少存取的磁片區、封存資料，而且您在另一個案例中保留其餘的儲存空間。

## <a name="troubleshoot"></a>疑難排解

您最有可能遇到的問題是，RoboCopy 命令在 Windows 伺服器端會失敗並出現「磁片區已*滿*」。 雲端階層處理每小時會進行一次，從已同步處理的本機 Windows Server 磁片撤除內容。 其目標是要達到您磁片區上的99% 可用空間。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 的 [檔案瀏覽器] 中看到它。

當您的 Windows 伺服器擁有足夠的可用容量時，重新執行命令將會解決此問題。 當您遇到這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 重新執行命令的不便是唯一的結果。

請檢查下一節中的連結，以針對 Azure 檔案同步問題進行疑難排解。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 檔案共用和 Azure 檔案同步。下列文章可協助您瞭解先進的選項、最佳作法，並同時包含疑難排解說明。 這些文章會適當地連結至[Azure 檔案共用檔](storage-files-introduction.md)。

* [AFS 總覽](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 疑難排解](storage-sync-files-troubleshoot.md)
