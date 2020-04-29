---
title: StorSimple 1200 遷移至 Azure 檔案同步
description: 瞭解如何將 StorSimple 1200 系列虛擬裝置遷移至 Azure 檔案同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502360"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 遷移至 Azure 檔案同步

StorSimple 1200 系列是在內部部署資料中心執行的虛擬裝置。 您可以將此設備的資料移轉至 Azure 檔案同步環境。 Azure 檔案同步是 StorSimple 應用裝置可以遷移到的預設和策略性長期 Azure 服務。

StorSimple 1200 系列將于12月 2022[日結束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)。  請務必儘快開始規劃您的遷移。 本文提供成功遷移至 Azure 檔案同步所需的背景知識和遷移步驟。 

## <a name="azure-file-sync"></a>Azure 檔案同步

> [!IMPORTANT]
> Microsoft 致力於協助客戶進行遷移。 適用AzureFilesMigration@microsoft于自訂遷移計畫的電子郵件 .com，以及在遷移期間的協助。

Azure 檔案同步是以兩個主要元件為基礎的 Microsoft 雲端服務：

* 檔案同步處理和雲端階層處理。
* 檔案共用是 Azure 中的原生儲存體，可透過多個通訊協定（例如 SMB 和檔案 REST）進行存取。 Azure 檔案共用相當於 Windows Server 上的檔案共用，您可以原生方式將其掛接為網路磁碟機機。 它支援屬性、許可權和時間戳記等重要的檔案精確度層面。 與 StorSimple 不同的是，不需要任何應用程式/服務來解讀儲存在雲端中的檔案和資料夾。 在雲端儲存一般用途的檔案伺服器資料以及一些應用程式資料的理想和最具彈性的方法。

本文著重于遷移步驟。 如果您想要深入瞭解 Azure 檔案同步，建議您先閱讀下列文章：

* [Azure 檔案同步-總覽](https://aka.ms/AFS "總覽")
* [Azure 檔案同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，以及保證可用性。 後者則需要保持停機時間最少，使其符合或只會稍微超過一般的維護時段。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 到 Azure 檔案同步的遷移路徑

需要本機 Windows 伺服器，才能執行 Azure 檔案同步代理程式。 Windows Server 最少可以是2012R2 伺服器，但最好是 Windows Server 2019。

有許多替代的遷移路徑，它會建立太長的文章來記載所有檔案，並說明為什麼它們會在我們建議的路由中，將風險或缺點放在本文的最佳作法中。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="這篇文章中進一步探討的步驟的遷移路由。":::

上圖描述對應于本文各節的步驟。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>步驟1：布建您的內部部署 Windows Server 和存放裝置

1. 建立 Windows Server 2019-最低 2012R2-作為虛擬機器或實體伺服器。 也支援 Windows Server 故障切換叢集。
2. 布建或新增直接連接存放裝置（與不支援的 NAS 相較之下的 DAS）。 Windows Server 儲存空間的大小必須等於或大於虛擬 StorSimple 1200 應用裝置的可用容量大小。

### <a name="step-2-configure-your-windows-server-storage"></a>步驟2：設定您的 Windows Server storage

在此步驟中，您會將 StorSimple 儲存結構（磁片區和共用）對應到您的 Windows Server 儲存結構。
如果您打算對儲存體結構進行變更，表示磁片區數目、資料檔案夾與磁片區的關聯，或是您目前 SMB/NFS 共用的上方或下方的子資料夾結構，則現在是將這些變更納入考慮的時機。
在設定 Azure 檔案同步之後變更您的檔案和資料夾結構很麻煩，而且應該避免。
本文假設您是對應1:1，因此當您依照本文中的步驟進行時，必須考慮您的對應變更。

* 您的實際執行資料都不會出現在 Windows Server 系統磁碟區上。 系統磁碟區上不支援雲端階層處理。 不過，需要這項功能才能進行遷移，以及以 StorSimple 更換的方式進行連續作業。
* 在您的 Windows Server 上布建與 StorSimple 1200 虛擬裝置上相同數目的磁片區。
* 設定您需要的任何 Windows Server 角色、功能和設定。 我們建議您選擇 Windows Server 更新，讓您的 OS 保持安全和最新狀態。 同樣地，我們建議您選擇 Microsoft Update，讓 Microsoft 應用程式保持在最新狀態，包括 Azure 檔案同步 agent。
* 在閱讀下列步驟之前，請不要設定任何資料夾或共用。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>步驟3：部署第一個 Azure 檔案同步雲端資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步驟4：比對您的本機磁片區和資料夾結構，以 Azure 檔案同步和 Azure 檔案共用資源

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>步驟5：布建 Azure 檔案共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>步驟6：設定 Windows Server 目的檔案夾

在先前的步驟中，您已考慮將決定同步拓朴元件的所有層面。 現在，您可以準備伺服器來接收要上傳的檔案。

建立**所有**資料夾，其會將每個資料夾同步至其自有的 Azure 檔案共用。
請務必遵循先前記載的資料夾結構。 比方說，如果您決定將多個本機 SMB 共用一起同步處理到單一 Azure 檔案共用，則您需要將它們放在磁片區上的一般根資料夾底下。 現在，在磁片區上建立此目標根資料夾。

您已布建的 Azure 檔案共用數目應符合您在此步驟中建立的資料夾數目，以及您將在根層級同步的磁片區數目。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>步驟7：部署 Azure 檔案同步代理程式

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>步驟8：設定同步處理

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必開啟雲端階層處理！** 如果您的本機伺服器沒有足夠的空間來儲存 StorSimple 雲端儲存體中的資料大小總計，這就是必要的。 將您的階層原則暫時用於遷移，設定為99% 磁片區可用空間。

針對所有需要設定同步處理的 Azure 檔案共用/伺服器位置，重複執行同步群組建立和新增相符的伺服器資料夾，做為伺服器端點。

### <a name="step-9-copy-your-files"></a>步驟9：複製您的檔案

基本的遷移方法是從 StorSimple 虛擬裝置到您的 Windows Server 的 RoboCopy，並 Azure 檔案同步至 Azure 檔案共用。

執行第一個本機複本至您的 Windows Server 目的檔案夾：

* 識別虛擬 StorSimple 應用裝置上的第一個位置。
* 識別 Windows Server 上已設定 Azure 檔案同步的相符資料夾。
* 使用 RoboCopy 開始複製

下列 RoboCopy 命令會將檔案從 StorSimple Azure 儲存體重新叫用至您的本機 StorSimple，然後將它們移至 Windows Server 目的檔案夾。 Windows Server 會將它同步處理至 Azure 檔案共用。 當本機 Windows Server 磁片區已滿時，雲端階層處理將會開始，並將已成功同步處理的層級檔案分層。 雲端階層處理會產生足夠的空間，以從 StorSimple 虛擬裝置繼續複製。 雲端階層會檢查一小時一次，以查看已同步處理的內容，並釋出磁碟空間以達到99% 磁片區可用空間。

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
      允許在相同的目標/目的地上，連續執行此 RoboCopy 命令數次。 它會識別先前已複製的內容，並省略它。 只會處理自上次執行後發生的變更、新增和「*刪除*」。 如果命令未在之前執行，則不會省略任何內容。 對於仍在積極使用和變更的來源位置而言，這是很好的選擇。
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

當您第一次執行 RoboCopy 命令時，您的使用者和應用程式仍會存取 StorSimple 檔案和資料夾，而且可能會變更。 這是可能的，RoboCopy 已處理目錄、移到下一個，然後來源位置（StorSimple）上的使用者新增、變更或刪除檔案，該檔案現在不會在目前的 RoboCopy 執行中處理。 沒問題。

第一次執行是關於將大量資料移回內部部署，移至您的 Windows Server，並透過 Azure 檔案同步備份到雲端。這可能需要很長的時間，視下列情況而定：

* 您的下載頻寬
* StorSimple 雲端服務的召回速度
* 上傳頻寬
* 需要由任一服務處理的專案（檔案和資料夾）數目

完成初始執行之後，請再次執行命令。

第二次的完成速度會更快，因為它只需要傳輸自上次執行後所發生的變更。 這些變更可能已經是 StorSimple 的本機，因為它們是最新的。 這會進一步縮短時間，因為需要減少雲端的召回需求。 在第二次執行期間，仍會累積新的變更。

重複此程式，直到您滿意完成所需的時間量為止，這是可接受的停機時間。

當您考慮到可接受的停機時間，而且您已準備好讓 StorSimple 位置離線時，請立即這麼做：例如，移除 SMB 共用，讓使用者不能存取該資料夾，或採取任何其他適當的步驟來防止在 StorSimple 上的此資料夾中變更內容。

執行最後一個 RoboCopy round。 這會收取可能錯過的任何變更。
這個最後步驟所花費的時間長短取決於 RoboCopy 掃描的速度。 您可以測量上一次執行所花的時間，來預估時間（等於停機）。

在 Windows Server 資料夾上建立共用，而且可能會調整您的 DFS-N 部署以指向該檔案。 請務必在您的 StorSimple SMB 共用上設定與相同的共用層級許可權。

您已完成將共用/群組共用遷移到通用的根或磁片區。 （取決於您所對應的內容，並決定要進入相同的 Azure 檔案共用）。

您可以嘗試平行執行其中幾個複本。 我們建議您一次處理一個 Azure 檔案共用的範圍。

> [!WARNING]
> 將所有資料從 StorSimple 移至 Windows Server，並完成您的遷移作業之後：回到 Azure 入口網站中的***所有***同步處理群組，並調整 [雲端階層處理磁片區可用空間百分比] 值，使其更適合用於快取使用率，例如20%。 

雲端階層處理磁片區可用空間原則會在磁片區層級上運作，其中可能會有多個伺服器端點同步。 如果您忘記調整即使一個伺服器端點上的可用空間，同步處理會繼續套用最嚴格的規則，並嘗試保留99% 的可用磁碟空間，使本機快取無法如您預期般執行。 除非您的目標是只包含很少存取的磁片區（封存資料）的命名空間。

## <a name="troubleshoot"></a>疑難排解

您最有可能遇到的問題是，RoboCopy 命令在 Windows 伺服器端會失敗並出現「磁片區已*滿*」。 如果是這種情況，則您的下載速度可能比您的上傳速度來得好。 雲端階層處理每小時會進行一次，從已同步處理的本機 Windows Server 磁片撤除內容。

讓同步進度和雲端階層處理釋放磁碟空間。 您可以在 Windows Server 的 [檔案瀏覽器] 中看到它。

當您的 Windows 伺服器擁有足夠的可用容量時，重新執行命令將會解決此問題。 當您遇到這種情況時，不會有任何中斷，而且您可以放心地繼續進行。 重新執行命令的不便是唯一的結果。

您也可以遇到其他 Azure 檔案同步問題。
不太可能發生這種情況，請查看**Azure 檔案同步疑難排解指南的連結**。

## <a name="relevant-links"></a>相關連結

遷移內容：

* [StorSimple 8000 系列遷移指南](storage-files-migration-storsimple-8000.md)

Azure 檔案同步內容：

* [AFS 總覽](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 疑難排解](storage-sync-files-troubleshoot.md)
