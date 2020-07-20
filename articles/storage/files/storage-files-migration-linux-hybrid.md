---
title: Linux 遷移至 Azure 檔案同步
description: 瞭解如何使用 Azure 檔案同步和 Azure 檔案共用，將檔案從 Linux 伺服器位置遷移至混合式雲端部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fd2e4f5c81427413e3f3f3eceaa0cc41a3b9e318
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510373"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 檔案同步從 Linux 遷移至混合式雲端部署

Azure 檔案同步在具有直接連接存放裝置（DAS）的 Windows Server 實例上運作。 它不支援從 Linux 或遠端伺服器訊息區（SMB）共用進行同步處理。

如此一來，將您的檔案服務轉換成混合式部署，就會需要遷移到 Windows Server。 本文會引導您完成這類遷移的規劃和執行。

## <a name="migration-goals"></a>移轉目標

目標是將您在 Linux Samba 伺服器上所擁有的共用移到 Windows Server 實例。 然後使用 Azure 檔案同步進行混合式雲端部署。 這項遷移必須以保證生產資料的完整性，以及在遷移期間提供可用性的方式來完成。 後者則需要保持停機時間最少，使其符合或只會稍微超過一般的維護時段。

## <a name="migration-overview"></a>移轉概觀

如 Azure 檔案儲存體[遷移總覽一文](storage-files-migration-overview.md)所述，使用正確的複製工具和方法很重要。 您的 Linux Samba 伺服器會直接在您的區域網路上公開 SMB 共用。 Robocopy （內建于 Windows Server）是在此遷移案例中移動檔案的最佳方式。

如果您不是在 Linux 伺服器上執行 Samba，而想要將資料夾遷移至 Windows Server 上的混合式部署，您可以使用 Linux 複製工具，而不是 Robocopy。 如果您這樣做，請注意檔案複製工具中的精確度功能。 請參閱遷移總覽文章中的[遷移基本概念一節](storage-files-migration-overview.md#migration-basics)，以瞭解要在複製工具中尋找的專案。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第1階段：識別您需要多少個 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>第2階段：布建適用于內部部署的 Windows Server 實例

* 建立 Windows Server 2019 實例作為虛擬機器或實體伺服器。 Windows Server 2012 R2 是最低需求。 也支援 Windows Server 容錯移轉叢集。
* 布建或新增直接連接存放裝置（DAS）。 不支援網路連接儲存裝置 (NAS)。

  如果您使用 Azure 檔案同步[雲端](storage-sync-cloud-tiering.md)階層處理功能，您所布建的儲存體數量可能會比您目前在 Linux Samba 伺服器上使用的儲存空間還要小。 不過，當您在稍後的階段中，將檔案從較大的 Linux Samba 伺服器空間複製到較小的 Windows Server 磁片區時，您必須以批次方式處理：

  1. 移動符合磁片的一組檔案。
  2. 讓檔案同步處理和雲端階層處理互動。
  3. 當磁片區上建立了更多的可用空間時，請繼續進行下一個批次檔。 
    
  您可以透過在 Linux Samba 伺服器上的檔案所占的 Windows Server 實例上布建對等的空間，來避免這種批次處理方法。 請考慮在 Windows 上啟用重復資料刪除。 如果您不想要將此大量儲存體永久認可到 Windows Server 實例，您可以在遷移之後和調整雲端階層處理原則之前，縮減磁片區大小。 這會建立較小的 Azure 檔案共用內部部署快取。

您所部署之 Windows Server 實例的資源設定（運算和 RAM）主要取決於您要同步的專案數（檔案和資料夾）。 如果您有任何疑慮，建議您採用更高效能的設定。

[瞭解如何根據您需要同步的專案（檔案和資料夾）數目來調整 Windows Server 實例的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 先前連結的文章會呈現一個資料表，其中包含伺服器記憶體（RAM）的範圍。 您可以為伺服器提供較小的數目，但預期初始同步處理可能會花費更多時間。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第3階段：部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第4階段：部署 Azure 儲存體資源

在此階段中，請參閱第1階段的對應資料表，並使用它來布建正確的 Azure 儲存體帳戶和其中的檔案共用數目。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第5階段：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>第6階段：在 Windows Server 部署上設定 Azure 檔案同步

您已註冊的內部部署 Windows Server 實例必須準備就緒並聯機到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 雲端階層處理是一項 Azure 檔案同步功能，可讓本機伺服器比儲存在雲端的儲存容量少，但擁有完整的命名空間可用。 本機上有趣的資料也會在本機快取，以快速存取效能。 雲端階層處理是每個 Azure 檔案同步伺服器端點的選用功能。

> [!WARNING]
> 如果您在 Windows Server 磁片區上布建的儲存體比 Linux Samba 伺服器上所使用的資料少，則雲端階層處理是強制性的。 如果您未開啟雲端階層處理，您的伺服器將無法釋放空間來儲存所有檔案。 將您的階層原則暫時用於遷移，將磁片區的可用空間設定為99%。 完成遷移之後，請務必返回您的雲端階層處理設定，並將原則設定為長期的更有用層級。

針對所有需要設定同步處理的 Azure 檔案共用和伺服器位置，重複建立同步群組的步驟，並將相符的伺服器資料夾新增為伺服器端點。

建立所有伺服器端點之後，同步處理就會正常運作。 您可以建立測試檔案，並查看它從您的伺服器位置同步到已連線的 Azure 檔案共用（如同步群組中的雲端端點所述）。

這兩個位置、伺服器資料夾和 Azure 檔案共用都是空的，而且會等待資料。 在下一個步驟中，您會開始將檔案複製到 Windows Server 實例中，以供 Azure 檔案同步將其移至雲端。 如果您已啟用雲端階層處理，則如果您在本機磁片區上的容量用盡，伺服器就會開始將檔案分層。

## <a name="phase-7-robocopy"></a>第7階段： Robocopy

基本的遷移方法是使用 Robocopy 來複製檔案，並使用 Azure 檔案同步來進行同步處理。

執行第一個本機複本至您的 Windows Server 目的檔案夾：

1. 識別 Linux Samba 伺服器上的第一個位置。
1. 識別已在其上設定 Azure 檔案同步的 Windows Server 實例上相符的資料夾。
1. 使用 Robocopy 開始複製。

下列 Robocopy 命令會將檔案從 Linux Samba 伺服器的存放裝置複製到您的 Windows Server 目的檔案夾。 Windows Server 會將它同步處理至 Azure 檔案共用。 

如果您在 Windows Server 實例上布建的儲存體比您在 Linux Samba 伺服器上佔用的檔案少，則您已設定雲端階層處理。 當本機 Windows Server 磁片區已滿時，[雲端](storage-sync-cloud-tiering.md)階層處理將會啟動，並將已經成功同步處理的檔案分層。 雲端階層處理會產生足夠的空間，以便從 Linux Samba 伺服器繼續複製。 雲端階層會檢查一小時一次，以查看已同步處理的內容，以及釋出磁碟空間以達到99% 可用空間的原則。

Robocopy 可能會比您在本機上同步處理到雲端和層級更快地移動檔案，因而導致您用盡本機磁碟空間。 Robocopy 就會失敗。 我們建議您以防止問題的順序來處理共用。 例如，請考慮不要同時啟動所有共用的 Robocopy 作業。 或考慮移動符合 Windows Server 實例上目前可用空間量的共用。 如果您的 Robocopy 作業失敗，只要使用下列鏡像/清除選項，您就可以隨時重新執行命令：

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

後景：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允許 Robocopy 執行多執行緒。 預設值為8，最大值為128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      將狀態輸出到記錄檔做為 Unicode （覆寫現有的記錄）。
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
      在備份應用程式會使用的相同模式下執行 Robocopy。 它允許 Robocopy 移動目前使用者沒有許可權的檔案。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許在相同的目標/目的地上，依序執行此 Robocopy 命令數次。 它會識別並省略之前複製的內容。 只會處理上次執行後所發生的變更、新增和刪除。 如果命令未在之前執行，則不會省略任何內容。 **/MIR**旗標是來源位置的絕佳選項，仍在積極使用和變更。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      檔案複製的精確度（預設值為 [/COPY： DAT]）。 複製旗標如下： D = data，A = attribute，T = 時間戳記，S = security = NTFS Acl，O = owner info，U = 審核資訊。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      複製所有檔案資訊（相當於/COPY： DATSOU）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目錄複本的精確度（預設值為/DCOPY： DA）。 複製旗標為： D = data、A = attributes、T = 時間戳記。
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>第8階段：使用者剪下

當您第一次執行 Robocopy 命令時，您的使用者和應用程式仍會存取 Linux Samba 伺服器上的檔案，而且可能會變更這些檔案。 Robocopy 可能已處理目錄，並移至下一個檔案，然後來源位置（Linux）中的使用者會新增、變更或刪除目前在此 Robocopy 執行中不會處理的檔案。 這是預期的行為。

第一次執行是關於透過 Azure 檔案同步將大量資料移至您的 Windows Server 實例和雲端。第一次複製可能需要很長的時間，視下列情況而定：

* 您的下載頻寬。
* 上傳頻寬。
* [區域網路速度]，以及 Robocopy 執行緒數目符合此數目的最佳程度。
* Robocopy 和 Azure 檔案同步需要處理的專案（檔案和資料夾）數目。

完成初始執行之後，請再次執行命令。

第二次它的完成速度較快，因為它只需要傳輸自上次執行後所發生的變更。 在這秒內，仍會累積執行新的變更。

重複執行此程式，直到您滿意為特定位置完成 Robocopy 作業所需的時間量，並在可接受的時間範圍內執行。

當您考慮可接受停機時間，而且您已準備好讓 Linux 位置離線時，您可以變更共用根目錄上的 Acl，讓使用者無法再存取該位置。 或者，您也可以採取任何其他適當的步驟，防止內容在您的 Linux 伺服器上的此資料夾中變更。

執行最後一個 Robocopy round。 它會收取可能錯過的任何變更。 此最後步驟所需的時間長短取決於 Robocopy 掃描的速度。 您可以測量上一次執行所花的時間，來預估時間（等於停機）。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該檔案。 請務必設定與 Linux Samba 伺服器 SMB 共用上相同的共用層級許可權。 如果您已在 Linux Samba 伺服器上使用本機使用者，則需要將這些使用者重新建立為 Windows Server 本機使用者。 您也需要將 Robocopy 已移至您的 Windows Server 實例的現有 Sid 對應至新 Windows Server 本機使用者的 Sid。 如果您使用 Active Directory 帳戶和 Acl，Robocopy 會依照自己的方式移動它們，而不需要進一步的動作。

您已完成將共用或共用群組遷移到通用的根或磁片區（視您在第1階段的對應而定）。

您可以嘗試平行執行其中幾個複本。 我們建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 將 Linux Samba 伺服器上的所有資料移至 Windows Server 實例，並完成您的遷移之後，請回到 Azure 入口網站中的*所有*同步處理群組。 調整雲端階層處理磁片區的可用空間百分比，使其更適合快取使用率，例如20%。 

雲端階層處理磁片區中的可用空間原則作用於磁片區層級，其中可能有多個伺服器端點同步。 如果您忘記調整即使一個伺服器端點上的可用空間，同步處理將會繼續套用最嚴格的規則，並嘗試將可用的磁碟空間保持在99%。 本機快取可能不會如您預期般執行。 如果您的目標是要讓磁片區的命名空間只包含很少存取的封存資料，而且您在另一個案例中保留其餘的儲存空間，則效能可能是可接受的。

## <a name="troubleshoot"></a>疑難排解

最常見的問題是，在 Windows Server 端，Robocopy 命令會失敗，且磁片區已**滿**。 雲端階層處理每小時會進行一次，從已同步處理的本機 Windows Server 磁片撤除內容。 其目標是要達到磁片區上99% 的可用空間。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 上的檔案瀏覽器中觀察到。

當您的 Windows Server 實例有足夠的可用容量時，重新執行命令將會解決此問題。 當您遇到這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 再次執行命令的不便是唯一的結果。

請檢查下一節中的連結，以進行 Azure 檔案同步問題的疑難排解。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 檔案共用和 Azure 檔案同步。下列文章包含先進的選項、最佳作法和疑難排解協助。 這些文章會適當地連結至[Azure 檔案共用檔](storage-files-introduction.md)。

* [Azure 檔案同步總覽](https://aka.ms/AFS)
* [Azure 檔案同步部署指南](storage-files-deployment-guide.md)
* [Azure 檔案同步疑難排解](storage-sync-files-troubleshoot.md)
