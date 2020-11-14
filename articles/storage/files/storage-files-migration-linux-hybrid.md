---
title: Linux 遷移至 Azure 檔案同步
description: 瞭解如何使用 Azure 檔案同步和 Azure 檔案共用，將檔從 Linux 伺服器位置遷移至混合式雲端部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 46bcfd48d8fdfb228670b87df166c1ad8de61e52
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626345"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 檔案同步從 Linux 遷移至混合式雲端部署

Azure 檔案同步可在具有直接附加儲存體 (DAS) 的 Windows Server 實例上運作。 它不支援與 Linux 用戶端之間的同步處理，或遠端伺服器訊息區 (SMB) 共用或網路檔案系統 (NFS) 共用。

因此，將您的檔案服務轉換成混合式部署，將會需要遷移到 Windows Server。 本文將引導您完成這類遷移的規劃和執行。

## <a name="migration-goals"></a>移轉目標

目標是將您的 Linux Samba 伺服器上的共用移至 Windows Server 實例。 然後，將 Azure 檔案同步用於混合式雲端部署。 這項遷移需要以保證生產資料完整性的方式來完成，並在遷移期間提供可用性。 後者需要保持最少停機時間，使其可符合或僅稍微超過一般維護時段。

## <a name="migration-overview"></a>移轉概觀

如《 Azure 檔案儲存體 [遷移簡介](storage-files-migration-overview.md)」一文所述，使用正確的複製工具和方法是很重要的。 您的 Linux Samba 伺服器會直接在您的區域網路上公開 SMB 共用。 Windows Server 內建 Robocopy 是在此遷移案例中移動檔案的最佳方式。

如果您不是在 Linux 伺服器上執行 Samba，而想要將資料夾遷移到 Windows Server 上的混合式部署，您可以使用 Linux 複製工具，而不是 Robocopy。 如果您這樣做，請注意檔案複製工具中的精確度功能。 請參閱「遷移總覽」文章中的「 [遷移基本概念」一節](storage-files-migration-overview.md#migration-basics) ，以瞭解如何在複製工具中尋找。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>階段1：識別您需要多少個 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>第2階段：在內部部署環境中布建適合的 Windows Server 實例

* 建立 Windows Server 2019 實例作為虛擬機器或實體伺服器。 Windows Server 2012 R2 是最低需求。 也支援 Windows Server 容錯移轉叢集。
* 布建或新增直接附加儲存體 (DAS) 。 不支援網路連接儲存裝置 (NAS)。

  如果您使用 Azure 檔案同步 [雲端](storage-sync-cloud-tiering.md) 階層處理功能，您布建的儲存體數量可以小於您目前在 Linux Samba 伺服器上使用的儲存體。 不過，當您在稍後的階段中將檔案從較大的 Linux Samba 伺服器空間複製到較小的 Windows Server 磁片區時，您必須以批次方式工作：

  1. 移動一組適合磁片的檔案。
  2. 讓檔案同步和雲端階層處理的互動。
  3. 在磁片區上建立更多可用空間時，請繼續下一批次檔。 
    
  您可以在 Windows Server 實例上布建您檔案在 Linux Samba 伺服器上佔用的對等空間，以避免此批次方法。 考慮在 Windows 上啟用重復資料刪除。 如果您不想要將此大量儲存體永久認可到 Windows Server 實例，您可以在遷移後以及調整雲端階層處理原則之前，減少磁片區大小。 這會建立 Azure 檔案共用的較小內部部署快取。

您部署的 Windows Server 實例的資源設定 (計算和 RAM) ，主要取決於您要同步處理的專案 (檔案和) 資料夾的數目。 如果您有任何疑慮，建議您採用效能較高的設定。

[瞭解如何根據您需要同步的 (檔案和) 資料夾的專案數目，來調整 Windows Server 實例的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 先前連結的文章會顯示一個資料表，其中包含伺服器記憶體 (RAM) 的範圍。 您可以為伺服器提供較小的數位，但預期初始同步處理可能會花上很多時間。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>階段3：部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>階段4：部署 Azure 儲存體資源

在這個階段中，請參閱第1階段的對應資料表，然後使用它來布建正確的 Azure 儲存體帳戶和檔案共用數目。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第5階段：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>階段6：設定 Windows Server 部署上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 實例必須準備就緒，並已連線到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 雲端階層處理是 Azure 檔案同步的功能，可讓本機伺服器的儲存容量比儲存在雲端的儲存容量少，但有完整的命名空間可用。 本機上的有趣資料也會在本機快取，以提供快速存取效能。 雲端階層處理是每個 Azure 檔案同步 server 端點的選擇性功能。

> [!WARNING]
> 如果您在 Windows Server 磁片區上布建的儲存體少於 Linux Samba 伺服器上使用的資料，則必須使用雲端階層處理。 如果您未開啟雲端分層，您的伺服器將不會釋出空間來儲存所有檔案。 將您的階層處理原則暫時設定為磁片區的99% 可用空間。 在完成遷移之後，請務必返回您的雲端階層處理設定，並將原則設定為長期的較有用層級。

針對所有需要設定同步處理的 Azure 檔案共用和伺服器位置，重複執行同步群組建立以及將相符的伺服器資料夾新增為伺服器端點的步驟。

在建立所有伺服器端點之後，同步處理就會正常運作。 您可以建立測試檔案，並從您的伺服器位置將它同步到連接的 Azure 檔案共用 (如同步群組) 中的雲端端點所述。

這兩個位置、伺服器資料夾和 Azure 檔案共用都是空的，而且正在等候資料。 在下一個步驟中，您將開始將檔案複製到 Windows Server 實例中，Azure 檔案同步將它們移至雲端。 如果您已啟用雲端階層處理，則當您在本機磁片區上的容量用盡時，伺服器就會開始分層檔。

## <a name="phase-7-robocopy"></a>第7階段： Robocopy

基本的遷移方法是使用 Robocopy 來複製檔案，並使用 Azure 檔案同步來執行同步處理。

執行您的 Windows Server 目的檔案夾的第一個本機複本：

1. 識別 Linux Samba 伺服器上的第一個位置。
1. 找出 Windows Server 實例上已有 Azure 檔案同步設定的相符資料夾。
1. 使用 Robocopy 開始複製。

下列 Robocopy 命令會將檔案從 Linux Samba 伺服器的存放裝置複製到您的 Windows Server 目的檔案夾。 Windows Server 會將其同步處理至 Azure 檔案共用。 

如果您在 Windows Server 實例上布建的儲存體少於 Linux Samba 伺服器上的檔案，則您已設定雲端階層處理。 當本機 Windows Server 磁片區已滿時， [雲端](storage-sync-cloud-tiering.md) 階層處理將會啟動，並將已成功同步處理的檔案分層。 雲端階層處理會產生足夠的空間，以從 Linux Samba 伺服器繼續複製。 雲端階層處理會每小時檢查一次，以查看已同步處理的內容，以及釋出磁碟空間以達到磁片區99% 可用空間的原則。

Robocopy 可以更快地移動檔案，而不能在本機同步到雲端和層，導致您的本機磁碟空間用盡。 Robocopy 將會失敗。 建議您以防止問題的順序來處理共用。 例如，請考慮不要同時為所有共用啟動 Robocopy 作業。 或考慮移動符合 Windows Server 實例上目前可用空間量的共用。 如果您的 Robocopy 作業失敗，只要使用下列鏡像/清除選項，您就可以隨時重新執行命令：

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
      以 Unicode (覆寫現有的記錄) ，將狀態輸出至記錄檔。
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
      以備份應用程式所使用的相同模式執行 Robocopy。 它可讓 Robocopy 移動目前使用者沒有許可權的檔案。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允許在相同的目標/目的地上，依序執行此 Robocopy 命令數次。 它會識別並省略之前複製的內容。 只會處理上次執行之後發生的變更、新增和刪除。 如果之前未執行命令，則不會省略任何內容。 **/MIR** 旗標是仍在主動使用和變更之來源位置的絕佳選項。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      檔案複製的精確度 (預設值為/COPY： DAT) 。 複製旗標為： D = data、A = attributes、T = 時間戳記、S = security = NTFS Acl、O = 擁有者資訊、U = 審核資訊。
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
      目錄複本的精確度 (預設值為/DCOPY： DA) 。 複製旗標為： D = data、A = attributes、T = 時間戳記。
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>階段8：使用者剪下

當您第一次執行 Robocopy 命令時，您的使用者和應用程式仍會存取 Linux Samba 伺服器上的檔案，而且可能會變更這些檔案。 Robocopy 有可能已處理目錄並移至下一個目錄，接著 (Linux 的來源位置中的使用者) 新增、變更或刪除目前 Robocopy 執行中不會處理的檔案。 這是預期的行為。

第一次執行是關於將大量資料移至您的 Windows Server 實例，並透過 Azure 檔案同步移至雲端。此第一個複製可能需要很長的時間，視下列情況而定：

* 您的下載頻寬。
* 上傳頻寬。
* 區域網路速度，以及 Robocopy 執行緒的數目與之相符的數目。
* ) Robocopy 和 Azure 檔案同步需要處理的專案數 (檔案和資料夾。

在初始執行完成後，再次執行命令。

第二次執行的速度更快，因為它只需要傳輸自上次執行之後發生的變更。 在這段期間，執行新的變更仍會累積。

重複此程式，直到您對特定位置完成 Robocopy 作業所需的時間量達到可接受的停機時間範圍為止。

當您考慮到可接受的停機時間，而且您已準備好讓 Linux 位置離線時，您可以在共用根上變更 Acl，讓使用者無法再存取該位置。 或者，您可以採取任何其他適當的步驟，以防止內容在 Linux 伺服器上變更此資料夾。

執行最後一個 Robocopy 舍入。 它會收取可能遺漏的任何變更。 最後一個步驟所需的時間，取決於 Robocopy 掃描的速度。 您可以藉由測量上一次執行的時間長度，來估計等於停機) 的時間 (。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該共用。 請務必在您的 Linux Samba 伺服器 SMB 共用上設定相同的共用層級許可權。 如果您已在 Linux Samba 伺服器上使用本機使用者，您需要重新建立這些使用者作為 Windows Server 本機使用者。 您也需要將 Robocopy 移至您的 Windows Server 實例的現有 Sid 對應到新 Windows Server 本機使用者的 Sid。 如果您使用 Active Directory 帳戶和 Acl，Robocopy 會依原樣移動它們，而不需要採取任何進一步的動作。

您已完成將共用或共用群組移至一般的根或磁片區 (，取決於第1階段) 的對應。

您可以嘗試平行執行一些這些複本。 建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 將所有資料從 Linux Samba 伺服器移到 Windows Server 實例，並完成您的遷移之後，請回到 Azure 入口網站中的 *所有*  同步處理群組。 將雲端階層處理磁片區的可用空間百分比調整為更適合快取使用率的內容，例如20%。 

雲端階層處理磁片區中的可用空間原則會在磁片區層級上運作，而且可能會有多個伺服器端點與其進行同步處理。 如果您忘記調整偶數伺服器端點上的可用空間，同步處理將繼續套用限制最嚴格的規則，並嘗試將可用的磁碟空間維持在99%。 本機快取可能不會如預期般執行。 如果您的目標是只包含很少存取的封存資料之磁片區的命名空間，而且您要為另一個案例保留其餘的儲存空間，則效能可能會是可接受的。

## <a name="troubleshoot"></a>疑難排解

最常見的問題是，Robocopy 命令會因為 Windows Server 端的磁片區已 **滿** 而失敗。 雲端階層處理會每小時處理一次，以從已同步處理的本機 Windows Server 磁片撤除內容。 其目標是達到磁片區的可用空間（99%）。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 檔案總管中觀察到。

當您的 Windows Server 實例有足夠的可用容量時，重新執行命令將會解決此問題。 當您進入這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 重新執行命令的不便是唯一的結果。

請參閱下一節中的連結，以針對 Azure 檔案同步問題進行疑難排解。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 檔案共用及 Azure 檔案同步。下列文章包含先進的選項、最佳作法和疑難排解的協助。 這些文章會適當地連結到 [Azure 檔案共用檔](storage-files-introduction.md) 。

* [Azure 檔案同步總覽](./storage-sync-files-planning.md)
* [Azure 檔案同步部署指南](storage-files-deployment-guide.md)
* [Azure 檔案同步疑難排解](storage-sync-files-troubleshoot.md)