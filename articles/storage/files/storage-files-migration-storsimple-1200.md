---
title: StorSimple 1200 遷移至 Azure 檔案同步
description: 瞭解如何將 StorSimple 1200 系列虛擬裝置遷移至 Azure 檔案同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 78c7953ef6432d37542a7a8b06f226a07f2b701f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630476"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 遷移至 Azure 檔案同步

StorSimple 1200 系列是在內部部署資料中心執行的虛擬裝置。 您可以將此設備的資料移轉至 Azure 檔案同步環境。 Azure 檔案同步是 StorSimple 設備可遷移至的預設和策略性長期 Azure 服務。

StorSimple 1200 系列將于2022年12月 [結束生命週期](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) 。  請務必儘快開始規劃您的遷移。 本文提供成功遷移至 Azure 檔案同步所需的背景知識和遷移步驟。 

## <a name="azure-file-sync"></a>Azure 檔案同步

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 AzureFilesMigration@microsoft適用于自訂遷移計畫的電子郵件，以及遷移期間的協助。

Azure 檔案同步是 Microsoft 雲端服務，以兩個主要元件為基礎：

* 檔案同步處理和雲端階層處理。
* 檔案共用是 Azure 中的原生儲存體，可透過多種通訊協定來存取，例如 SMB 和檔案 REST。 Azure 檔案共用相當於 Windows Server 上的檔案共用，您可以原生掛接為網路磁碟機機。 它支援重要的檔案精確度層面，例如屬性、許可權和時間戳記。 與 StorSimple 不同的是，不需要應用程式/服務來解讀儲存在雲端中的檔案和資料夾。 在雲端中儲存一般用途檔案伺服器資料以及一些應用程式資料的理想且最具彈性的方法。

本文著重于遷移步驟。 在遷移之前，您想要深入瞭解 Azure 檔案同步，建議您執行下列文章：

* [Azure 檔案同步-總覽](./storage-sync-files-planning.md "概觀")
* [Azure 檔案同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，以及保證可用性。 後者需要保持最少停機時間，使其可符合或僅稍微超過一般維護時段。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Azure 檔案同步的 StorSimple 1200 遷移路徑

需要本機 Windows 伺服器才能執行 Azure 檔案同步代理程式。 Windows Server 最少可以是2012R2 伺服器，但在理想情況下是 Windows Server 2019。

其中有許多替代的遷移路徑，而且可能會建立太長的文章來記錄所有的路徑，並說明為什麼在本文中，我們建議的最佳作法是有風險或缺點。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="本文後續步驟的遷移路由總覽。":::

上圖說明對應到本文各節的步驟。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>步驟1：布建您的內部部署 Windows Server 和儲存裝置

1. 建立 Windows Server 2019-最小 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 容錯移轉叢集。
2. 將直接連結的存放裝置布建或新增 (DAS （相較于 NAS）（) 不支援）。 Windows Server 存放裝置的大小必須等於或大於虛擬 StorSimple 1200 設備的可用容量大小。

### <a name="step-2-configure-your-windows-server-storage"></a>步驟2：設定您的 Windows Server 存放裝置

在此步驟中，您會將 StorSimple 儲存體結構 (磁片區和共用) 對應到您的 Windows Server 儲存結構。
如果您打算變更儲存體結構，也就是磁片區數目、資料檔案夾與磁片區的關聯，或是您目前 SMB/NFS 共用的上方或下方的子資料夾結構，則現在是將這些變更納入考慮的時間。
在設定 Azure 檔案同步之後變更您的檔案和資料夾結構是很麻煩的，應該避免。
本文假設您是對應1:1，因此當您依照本文中的步驟進行時，必須將對應變更納入考慮。

* 所有的生產資料都不會出現在 Windows Server 系統磁片區上。 系統磁碟區上不支援雲端階層處理。 不過，這項功能是遷移的必要功能，也是 StorSimple 取代的連續作業。
* 在您的 Windows Server 上布建相同數量的磁片區，就像您在 StorSimple 1200 虛擬裝置上所擁有的一樣。
* 設定任何您需要的 Windows Server 角色、功能和設定。 建議您加入宣告 Windows Server 更新，讓您的作業系統保持安全並保持最新狀態。 同樣地，建議您選擇 Microsoft Update，讓 Microsoft 應用程式保持在最新狀態，包括 Azure 檔案同步代理程式。
* 在閱讀下列步驟之前，請勿設定任何資料夾或共用。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>步驟3：部署第一個 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步驟4：符合您的本機磁片區和資料夾結構，以 Azure 檔案同步和 Azure 檔案共用資源

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>步驟5：布建 Azure 檔案共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>步驟6：設定 Windows Server 目的檔案夾

在先前的步驟中，您已考慮將決定同步拓撲元件的所有層面。 現在是時候準備伺服器來接收要上傳的檔案。

建立會將每個資料夾同步至其專屬 Azure 檔案共用的 **所有** 資料夾。
請務必遵循先前記載的資料夾結構。 比方說，如果您已決定將多個本機 SMB 共用同步處理到單一 Azure 檔案共用，則您必須將它們放在磁片區上的一般根資料夾底下。 立即在磁片區上建立這個目標根資料夾。

您已布建的 Azure 檔案共用數目應該符合您在此步驟中建立的資料夾數目 + 您將在根層級同步的磁片區數目。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>步驟7：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>步驟8：設定同步處理

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必開啟雲端階層處理！** 如果您的本機伺服器沒有足夠的空間可儲存 StorSimple 雲端儲存體中的資料大小總計，則這是必要的。 將您的階層處理原則暫時設定為將遷移至99% 磁片區可用空間。

針對所有需要設定為要同步處理的 Azure 檔案共用/伺服器位置，重複執行同步群組建立和新增相符伺服器資料夾的步驟，作為伺服器端點。

### <a name="step-9-copy-your-files"></a>步驟9：複製您的檔案

基本遷移方法是從您的 StorSimple 虛擬裝置到 Windows Server 的 RoboCopy，並 Azure 檔案同步至 Azure 檔案共用。

執行您的 Windows Server 目的檔案夾的第一個本機複本：

* 識別虛擬 StorSimple 設備上的第一個位置。
* 找出 Windows Server 上已有 Azure 檔案同步設定的相符資料夾。
* 使用 RoboCopy 啟動複製

下列 RoboCopy 命令會將您的 StorSimple Azure 儲存體中的檔案重新叫用至本機 StorSimple，然後將它們移到 Windows Server 目的檔案夾。 Windows Server 會將其同步處理至 Azure 檔案共用 (s) 。 當本機 Windows Server 磁片區已滿時，雲端階層處理將會啟動，並將已成功同步處理的檔案分層。 雲端階層處理會產生足夠的空間，以從 StorSimple 虛擬裝置繼續複製。 雲端階層處理會每小時檢查一次，以查看已同步處理的內容，以及釋出磁碟空間以達到99% 磁片區可用空間。

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
      允許在相同的目標/目的地上，依序執行此 RoboCopy 命令數次。 它會識別之前複製的內容，並將其省略。 只有在上次執行之後，才會處理變更、新增和「 *刪除* 」。 如果之前未執行命令，則不會省略任何內容。 這是仍在主動使用和變更的來源位置的絕佳選項。
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

當您第一次執行 RoboCopy 命令時，您的使用者和應用程式仍會存取 StorSimple 檔案和資料夾，而且可能會變更它。 這可能是因為 RoboCopy 已處理過目錄、移至下一個，然後再移至來源位置 (StorSimple) 新增、變更或刪除檔案，而該檔案現在將不會在此目前 RoboCopy 執行中處理。 沒關係。

第一次執行是關於將大量資料移回內部部署環境，再移至您的 Windows Server，然後透過 Azure 檔案同步將資料備份到雲端。這可能需要很長的時間，視下列情況而定：

* 您的下載頻寬
* StorSimple 雲端服務的召回速度
* 上傳頻寬
* 需要由兩個服務處理的 (檔案和資料夾的專案數) 

初始執行完成後，再次執行命令。

第二次完成的時間會更快，因為它只需要傳輸自上次執行之後發生的變更。 這些變更可能是 StorSimple 的本機變更，因為它們是最新的。 這會進一步減少時間，因為從雲端回收的需求已減少。 在第二次執行時，您仍然可以累積新的變更。

重複此程式，直到您滿意完成所需的時間量是可接受的停機時間為止。

當您考慮到可接受的停機時間，且您已準備好讓 StorSimple 位置離線時，請立即執行下列作業：例如，移除 SMB 共用，讓使用者不能存取該資料夾，或採取任何其他適當的步驟，防止內容在 StorSimple 的這個資料夾中變更。

執行最後一個 RoboCopy 舍入。 這將會收取可能遺漏的任何變更。
最後一個步驟所需的時間，取決於 RoboCopy 掃描的速度。 您可以藉由測量上一次執行的時間長度，來估計等於停機) 的時間 (。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該共用。 請務必設定與 StorSimple SMB 共用上相同的共用層級許可權。

您已完成將共用/群組共用遷移至一般的根或磁片區。  (取決於您所對應的內容，並決定要進入相同的 Azure 檔案共用。 ) 

您可以嘗試平行執行一些這些複本。 建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 當您將所有資料從 StorSimple 移到 Windows Server，且您的遷移完成後：請回到 Azure 入口網站中的 * **all** * 同步群組，並將 [雲端階層處理磁片區可用空間百分比] 值調整為更適合快取使用率的內容，例如20%。 

雲端階層處理磁片區可用空間原則會在磁片區層級上運作，而且可能會有多個伺服器端點與其同步。 如果您忘記調整偶數伺服器端點上的可用空間，同步處理將繼續套用最嚴格的規則，並嘗試保留99% 的可用磁碟空間，使本機快取不會如您預期般執行。 除非您的目標只有一個磁片區的命名空間只包含很少存取的封存資料。

## <a name="troubleshoot"></a>疑難排解

您最可能遇到的問題是，RoboCopy 命令失敗，並在 Windows Server 端出現 _ 「磁片區已滿」 *。 如果是這種情況，則您的下載速度可能比上傳速度更好。 雲端階層處理會每小時處理一次，以從已同步處理的本機 Windows Server 磁片撤除內容。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 上的檔案總管中觀察到。

當您的 Windows 伺服器有足夠的可用容量時，重新執行命令將會解決此問題。 當您進入這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 重新執行命令的不便是唯一的結果。

您也可以遇到其他 Azure 檔案同步問題。
如果發生這種情況，請查看 **Azure 檔案同步疑難排解指南的連結** 。

## <a name="relevant-links"></a>相關連結

遷移內容：

* [StorSimple 8000 系列遷移指南](storage-files-migration-storsimple-8000.md)

Azure 檔案同步內容：

* [AFS 總覽](./storage-sync-files-planning.md)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 疑難排解](storage-sync-files-troubleshoot.md)