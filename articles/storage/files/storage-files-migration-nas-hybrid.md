---
title: 將內部部署 NAS 遷移至 Azure 檔案同步
description: 瞭解如何使用 Azure 檔案同步和 Azure 檔案共用，將檔案從內部部署網路連接的儲存體 (NAS) 位置遷移至混合式雲端部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 2d531edeeae9e0dd7e392cae66d9e4d41c68dfa2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882258"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 檔案同步從網路連接的儲存體 (NAS) 遷移至混合式雲端部署

Azure 檔案同步適用于直接連結存放裝置 (DAS) 位置，且不支援同步至網路連接存放裝置 (NAS) 位置。
這項事實會遷移您的檔案，這篇文章會引導您完成這類遷移的規劃和執行。

## <a name="migration-goals"></a>移轉目標

目標是將您的 NAS 設備上的共用移到 Windows Server。 然後利用 Azure 檔案同步進行混合式雲端部署。 這項遷移需要以保證生產資料完整性的方式來完成，並在遷移期間提供可用性。 後者需要保持最少停機時間，使其可符合或僅稍微超過一般維護時段。

## <a name="migration-overview"></a>移轉概觀

如《 Azure 檔案儲存體 [遷移簡介](storage-files-migration-overview.md)」一文所述，使用正確的複製工具和方法是很重要的。 您的 NAS 設備會直接在您的區域網路上公開 SMB 共用。 RoboCopy 內建在 Windows Server 中，是在此遷移案例中移動檔案的最佳方式。

- 階段1： [識別您需要多少個 Azure 檔案共用](#phase-1-identify-how-many-azure-file-shares-you-need)
- 階段2：布 [建適合的 Windows Server 內部部署](#phase-2-provision-a-suitable-windows-server-on-premises)
- 階段3： [部署 Azure 檔案同步雲端資源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 階段4： [部署 Azure 儲存體資源](#phase-4-deploy-azure-storage-resources)
- 第5階段： [部署 Azure 檔案同步代理程式](#phase-5-deploy-the-azure-file-sync-agent)
- 階段6： [設定 Windows Server 上的 Azure 檔案同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 第7階段： [RoboCopy](#phase-7-robocopy)
- 階段8：[使用者剪](#phase-8-user-cut-over)下

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>階段1：識別您需要多少個 Azure 檔案共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>階段2：布建適合的 Windows Server 內部部署

* 建立 Windows Server 2019-最小 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 容錯移轉叢集。
* 將直接連結的存放裝置布建或新增 (DAS （相較于 NAS）（) 不支援）。

    如果您使用 Azure 檔案同步 [雲端](storage-sync-cloud-tiering.md) 階層處理功能，您布建的儲存體數量可以小於您目前在 NAS 設備上使用的儲存體。
    但是，當您在稍後的階段中，將檔案從較大的 NAS 空間複製到較小的 Windows Server 磁片區時，您必須以批次方式工作：

    1. 移動一組適合磁片的檔案
    2. 讓檔案同步和雲端階層處理的互動
    3. 在磁片區上建立更多可用空間時，請繼續下一批次檔。 
    
    您可以在 Windows Server 上布建您檔案在 NAS 應用裝置上佔用的相同空間，以避免此批次方法。 考慮在 NAS/Windows 上重復資料刪除。 如果您不想要將此大量儲存體永久認可到 Windows Server，您可以在遷移後以及調整雲端階層處理原則之前，減少磁片區大小。 這會建立 Azure 檔案共用的較小內部部署快取。

您所部署之 Windows Server 的資源設定 (計算和 RAM) ，主要取決於您要同步處理的專案 (檔案和) 資料夾的數目。 如果您有任何疑慮，建議您採用更高的效能設定。

[瞭解如何根據您需要同步的 (檔案和) 資料夾的專案數目，來調整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 先前連結的文章會顯示一個資料表，其中包含伺服器記憶體 (RAM) 的範圍。 您可以對伺服器的較小數位進行方向，但預期初始同步處理可能會花費更多時間。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>階段3：部署 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>階段4：部署 Azure 儲存體資源

在這個階段中，請參閱第1階段的對應資料表，然後使用它來布建正確的 Azure 儲存體帳戶和檔案共用數目。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第5階段：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>階段6：設定 Windows Server 上的 Azure 檔案同步

您已註冊的內部部署 Windows Server 必須準備就緒，並已連線到網際網路，才能進行此程式。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 雲端階層處理是一種 AFS 功能，可讓本機伺服器的儲存容量比儲存在雲端的儲存容量少，但有完整的命名空間可用。 本機上的有趣資料也會在本機快取，以提供快速存取效能。 雲端階層處理是每個 Azure 檔案同步「伺服器端點」的選擇性功能。

> [!WARNING]
> 如果您在 Windows server 磁片區上布建的儲存空間 () s 與您在 NAS 設備上使用的資料較少，則雲端階層處理是強制性的。 如果您未開啟雲端階層處理，伺服器將不會釋放空間來儲存所有檔案。 將您的階層處理原則暫時設定為將遷移至99% 磁片區可用空間。 完成遷移之後，請務必返回您的雲端階層處理設定，並將它設定為更長期的有用層級。

針對所有需要設定為要同步處理的 Azure 檔案共用/伺服器位置，重複執行同步群組建立和新增相符伺服器資料夾的步驟，作為伺服器端點。

在建立所有伺服器端點之後，同步處理就會正常運作。 您可以建立測試檔案，並從您的伺服器位置將它同步到連接的 Azure 檔案共用 (如同步群組) 中的雲端端點所述。

這兩個位置、伺服器資料夾和 Azure 檔案共用都是空的，並等候任一位置的資料。 在下一個步驟中，您將開始將檔案複製到 Windows Server，以便 Azure 檔案同步將它們移至雲端。 如果您已啟用雲端階層處理，則伺服器將會開始分層檔案，但 () 的本機磁片區容量用盡。

## <a name="phase-7-robocopy"></a>第7階段： RoboCopy

基本遷移方法是從您的 NAS 設備到 Windows Server 的 RoboCopy，並 Azure 檔案同步至 Azure 檔案共用。

執行您的 Windows Server 目的檔案夾的第一個本機複本：

* 識別 NAS 設備上的第一個位置。
* 找出 Windows Server 上已有 Azure 檔案同步設定的相符資料夾。
* 使用 RoboCopy 啟動複製

下列 RoboCopy 命令會將檔案從您的 NAS 存放裝置複製到您的 Windows Server 目的檔案夾。 Windows Server 會將其同步處理至 Azure 檔案共用 (s) 。 

如果您在 Windows Server 上布建的儲存體比您在 NAS 設備上所佔用的檔案少，則您已設定雲端階層處理。 當本機 Windows Server 磁片區已滿時， [雲端](storage-sync-cloud-tiering.md) 階層處理將會啟動，並將已成功同步處理的檔案分層。 雲端階層處理會產生足夠的空間，以從 NAS 設備繼續複製。 雲端階層處理會每小時檢查一次，以查看已同步處理的內容，以及釋出磁碟空間以達到99% 磁片區可用空間。
這可能是因為 RoboCopy 將檔案的移動速度比您在本機上同步到雲端和階層的速度還要快，因此會用盡本機磁碟空間。 RoboCopy 將會失敗。 建議您以防止這種情況的順序來處理共用。 例如，請勿同時為所有共用啟動 RoboCopy 作業，或只移動符合 Windows Server 上目前可用空間量的共用，以提及一些資源。

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
      /UNILOG:\<file name\>
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
      允許在相同的目標/目的地上，依序執行此 RoboCopy 命令數次。 它會識別之前複製的內容，並將其省略。 只有在上次執行之後，才會處理變更、新增和「*刪除*」。 如果之前未執行命令，則不會省略任何內容。 */MIR* 旗標是仍在主動使用和變更之來源位置的絕佳選項。
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

## <a name="phase-8-user-cut-over"></a>階段8：使用者剪下

當您第一次執行 RoboCopy 命令時，您的使用者和應用程式仍會存取 NAS 上的檔案，而且可能會變更這些檔案。 這可能是因為 RoboCopy 已處理目錄、移至下一個，然後是來源位置上的使用者 (NAS) 新增、變更或刪除目前 RoboCopy 執行中將不會處理的檔案。 這是預期的行為。

第一次執行是關於將大量資料移至您的 Windows Server，以及透過 Azure 檔案同步將大量資料移至雲端。此第一個複製可能需要很長的時間，視下列情況而定：

* 您的下載頻寬
* 上傳頻寬
* RoboCopy 執行緒數目符合的本機網路速度和數目
* 需要由 RoboCopy 和 Azure 檔案同步處理的 (檔案和資料夾的專案數) 

初始執行完成後，再次執行命令。

第二次完成的時間會更快，因為它只需要傳輸自上次執行之後發生的變更。 在第二次執行時，您仍然可以累積新的變更。

重複此程式，直到您對特定位置完成 RoboCopy 所需的時間量達到可接受的停機時間範圍為止。

當您考慮到可接受的停機時間，且您已準備好讓 NAS 位置離線時：若要讓使用者存取離線，您可以選擇變更共用根上的 Acl，讓使用者無法再存取該位置，或採取任何其他適當的步驟來防止內容在 NAS 上的這個資料夾中變更。

執行最後一個 RoboCopy 舍入。 它會收取任何可能已錯過的變更。
最後一個步驟所需的時間，取決於 RoboCopy 掃描的速度。 您可以藉由測量上一次執行的時間長度，來估計等於停機) 的時間 (。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該共用。 請務必設定與您 NAS SMB 共用上相同的共用層級許可權。 如果您有已加入網域的企業級 NAS，則當使用者存在於 Active Directory，且 RoboCopy 複製檔案和中繼資料時，將會自動符合使用者 Sid 的精確度。 如果您已使用 NAS 上的本機使用者，則需要將這些使用者重新建立為 Windows Server 本機使用者，並將現有的 Sid RoboCopy 移至您的 Windows Server，將其移至新的 Windows Server 本機使用者的 Sid。

您已完成將共用/群組共用遷移至一般的根或磁片區。 視您在第1階段的對應而定 () 

您可以嘗試平行執行一些這些複本。 建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 當您將所有資料從 NAS 移到 Windows Server，且您的遷移完成後：請回到 Azure 入口網站中的 ***all*** 同步群組，並將 [雲端階層處理磁片區可用空間百分比] 值調整為更適合快取使用率的內容，例如20%。 

雲端階層處理磁片區可用空間原則會在磁片區層級上運作，而且可能會有多個伺服器端點與其同步。 如果您忘記調整偶數伺服器端點上的可用空間，同步處理將繼續套用最嚴格的規則，並嘗試保留99% 的可用磁碟空間，使本機快取不會如您預期般執行。 除非您的目標只有一個磁片區的命名空間只包含很少存取的封存資料，而且您要為另一個案例保留其餘的儲存空間。

## <a name="troubleshoot"></a>疑難排解

您最可能遇到的問題是，RoboCopy 命令失敗，並在 Windows Server 端出現 _ 「磁片區已滿」 *。 雲端階層處理會每小時處理一次，以從已同步處理的本機 Windows Server 磁片撤除內容。 其目標是達到磁片區上的99% 可用空間。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 上的檔案總管中觀察到。

當您的 Windows 伺服器有足夠的可用容量時，重新執行命令將會解決此問題。 當您進入這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 重新執行命令的不便是唯一的結果。

請參閱下一節中的連結，以針對 Azure 檔案同步問題進行疑難排解。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 檔案共用及 Azure 檔案同步。下列文章可協助您瞭解先進的選項、最佳作法，也包含疑難排解的協助。 這些文章會適當地連結到 [Azure 檔案共用檔](storage-files-introduction.md) 。

* [AFS 總覽](./storage-sync-files-planning.md)
* [AFS 部署指南](./storage-how-to-create-file-share.md)
* [AFS 疑難排解](storage-sync-files-troubleshoot.md)