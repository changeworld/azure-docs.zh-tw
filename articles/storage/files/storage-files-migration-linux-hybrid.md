---
title: Linux 遷移到 Azure 檔同步
description: 瞭解如何使用 Azure 檔同步和 Azure 檔共用將檔從 Linux 伺服器位置遷移到混合雲部署。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: c80f43d61aeea8aba803267b7908831209812d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247713"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 檔同步從 Linux 遷移到混合雲部署

Azure 檔同步適用于具有直接附加存儲 （DAS） 的 Windows 伺服器。 它不支援與 Linux 或遠端 SMB 共用同步。
因此，將檔服務轉換為混合部署需要遷移到 Windows 伺服器。 本文將指導您完成此類遷移的規劃和執行。

## <a name="migration-goals"></a>移轉目標

目標是將 Linux Samba 伺服器上的共用移動到 Windows 伺服器。 然後利用 Azure 檔同步進行混合雲部署。 需要以保證生產資料的完整性以及在遷移期間的可用性的方式完成此遷移。 後者要求將停機時間降至最低，以便它能夠適應或僅略高於常規維護視窗。

## <a name="migration-overview"></a>移轉概觀

如 Azure 檔[遷移概述一文中](storage-files-migration-overview.md)所述，使用正確的複製工具和方法非常重要。 您的 Linux Samba 伺服器直接在本地網路上公開 SMB 共用。 RoboCopy 內置於 Windows 伺服器中，是在此遷移方案中移動檔的最佳方式。

如果您不在 Linux 伺服器上運行 Samba，而是希望將資料夾遷移到 Windows 伺服器上的混合部署，則可以使用 Linux 複製工具而不是 RoboCopy。 如果這樣做，請注意檔案複製工具中的逼真度功能。 查看遷移概述文章中的[遷移基礎知識部分](storage-files-migration-overview.md#migration-basics)，瞭解在複製工具中查找的內容。

- 第 1 階段：[確定需要多少 Azure 檔共用](#phase-1-identify-how-many-azure-file-shares-you-need)
- 第 2 階段：[在本地預配合適的 Windows 伺服器](#phase-2-provision-a-suitable-windows-server-on-premises)
- 第 3 階段：[部署 Azure 檔同步雲資源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 第 4 階段：[部署 Azure 存儲資源](#phase-4-deploy-azure-storage-resources)
- 第 5 階段：[部署 Azure 檔同步代理](#phase-5-deploy-the-azure-file-sync-agent)
- 第 6 階段：[在 Windows 伺服器上配置 Azure 檔同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 第 7 階段[：RoboCopy](#phase-7-robocopy)
- 第 8 階段：[使用者剪切](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 階段：確定需要多少 Azure 檔共用

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>第 2 階段：在本地預配合適的 Windows 伺服器

* 創建 Windows 伺服器 2019 - 至少 2012R2 - 作為虛擬機器或物理伺服器。 還支援 Windows 伺服器容錯移轉叢集。
* 預配或添加直接連接存儲（與不支援 NAS 的 NAS 相比的 DAS）。

    如果使用 Azure 檔同步[雲分層](storage-sync-cloud-tiering.md)功能，則預配的存儲量可能小於 Linux Samba 伺服器上當前使用的存儲量。
    但是，當您在稍後階段將檔從較大的 Linux Samba 伺服器空間複製到較小的 Windows Server 卷時，需要分批工作：

    1. 將一組適合磁片的檔移到磁片上。
    2. 讓檔同步和雲分層參與。
    3. 在卷上創建更多可用空間時，繼續下一批次檔。 
    
    通過在 Windows 伺服器上預配檔在 Linux Samba 伺服器上佔用的等效空間，可以避免這種批次處理方法。 請考慮在 Windows 上啟用重復資料消除。 如果不想將大量高存儲空間永久提交到 Windows 伺服器，則可以在遷移後和調整雲分層策略之前減小卷大小。 這將創建 Azure 檔共用的較小本機快取。

部署的 Windows 伺服器的資源配置（計算和 RAM）主要取決於要同步的專案（檔和資料夾）的數量。 如果您有任何疑問，我們建議您採用更高的性能配置。

[瞭解如何根據需要同步的專案（檔和資料夾）數量對 Windows 伺服器進行大小調整。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前面連結的文章提供了一個表，其中提供了伺服器記憶體 （RAM） 的範圍。 您可以定向到伺服器的較小數量，但預計初始同步可能需要更多的時間。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第 3 階段：部署 Azure 檔同步雲資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第 4 階段：部署 Azure 存儲資源

在此階段，請查閱階段 1 中的映射表，並用它來預配其中正確數量的 Azure 存儲帳戶和檔共用。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第 5 階段：部署 Azure 檔同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>第 6 階段：在 Windows 伺服器上配置 Azure 檔同步

您註冊的本地 Windows 伺服器必須已準備就緒，並連接到 Internet 進行此過程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 雲分層是 AFS 功能，它允許本機伺服器的存儲容量少於存儲在雲中的存儲容量，但具有完整的命名空間可用。 本地有趣的資料也緩存在本地，以快速訪問性能。 雲分層是每個 Azure 檔同步"伺服器終結點"的可選功能。

> [!WARNING]
> 如果 Windows 伺服器卷上的存儲空間比 Linux Samba 伺服器上使用的資料少，則必須進行雲分層。 如果不打開雲分層，伺服器將不會釋放空間來存儲所有檔。 將分層策略（臨時用於遷移）設置為 99% 的卷可用空間。 請務必在遷移完成後返回到雲分層設置，並將其設置為更長期有用的級別。

重複同步組創建和添加匹配伺服器資料夾的步驟，作為需要配置用於同步的所有 Azure 檔共用/伺服器位置的伺服器終結點。

創建所有伺服器終結點後，同步工作。 您可以創建測試檔案，並看到它從伺服器位置同步到連接的 Azure 檔共用（如同步組中的雲終結點所述）。

兩個位置、伺服器資料夾和 Azure 檔共用在任一位置都為空，正在等待任何位置的資料。 在下一步中，您將開始將檔案複製到 Azure 檔同步的 Windows 伺服器中，以將它們移動到雲中。 如果啟用了雲分層，則伺服器將開始分層檔，以防本地卷上的容量不足。

## <a name="phase-7-robocopy"></a>第 7 階段：RoboCopy

基本遷移方法是從 Linux Samba 伺服器到 Windows 伺服器的 RoboCopy 和 Azure 檔同步到 Azure 檔共用。

將第一個本機複本運行到 Windows Server 目的檔案夾：

1. 確定 Linux Samba 伺服器上的第一個位置。
1. 標識 Windows 伺服器上已配置 Azure 檔同步的匹配資料夾。
1. 使用 RoboCopy 啟動副本。

以下 RoboCopy 命令將從 Linux Samba 伺服器存儲中複製檔到 Windows 伺服器目的檔案夾。 Windows 伺服器將將其同步到 Azure 檔共用。 

如果在 Windows 伺服器上預配的存儲空間比 Linux Samba 伺服器上的檔少，則配置了雲分層。 當本地 Windows Server 卷已滿時，[雲分層](storage-sync-cloud-tiering.md)將啟動，並且已成功同步的層檔將啟動。 雲分層將生成足夠的空間來繼續從 Linux Samba 伺服器複製。 雲分層每小時檢查一次，查看已同步的內容，並釋放磁碟空間以達到 99% 的卷可用空間。
RoboCopy 移動檔的速度可能超過您同步到雲和本地層的速度，從而耗盡了本地磁碟空間。 RoboCopy 將失敗。 建議您按一個防止此情況的連續處理共用。 例如，不同時啟動所有共用的 RoboCopy 作業，或者僅移動適合 Windows 伺服器上當前可用空間量的共用，僅舉幾例。

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
      /UNILOG：\<檔案名\>
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
      允許在同一目標/目標上按順序運行此 RoboCopy 命令幾次。 它標識之前複製的內容，並省略它。 只會處理自上次運行以來發生的更改、添加和"*刪除*"。 如果之前未運行該命令，則不會省略任何內容。 對於仍在積極使用和更改的源位置 *，/MIR*標誌是一個極好的選項。
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

## <a name="phase-8-user-cut-over"></a>第 8 階段：使用者剪切

首次運行 RoboCopy 命令時，使用者和應用程式仍在訪問 Linux Samba 伺服器上的檔，並可能更改這些檔。 有可能，RoboCopy 已經處理了目錄，移動到下一個目錄，然後在源位置 （Linux） 上添加、更改或刪除檔，而該檔現在在此當前 RoboCopy 運行中不會處理。 這是預期的行為。

第一個運行是有關通過 Azure 檔同步將大部分資料移動到 Windows 伺服器並移動到雲中。此第一個副本可能需要很長時間，具體取決於：

* 您的下載頻寬。
* 上傳頻寬。
* 本地網路速度，以及 RoboCopy 執行緒數與它匹配的最佳數量。
* 需要由 RoboCopy 和 Azure 檔同步處理的專案（檔和資料夾）數。

完成初始運行後，再次運行該命令。

第二次完成得更快，因為它只需要傳輸自上次運行以來發生的更改。 在第二次運行期間，仍然會累積新的更改。

重複此過程，直到您確信為特定位置完成 RoboCopy 所需的時間在可接受的停機時間範圍內。

當您認為停機時間可以接受，並準備使 Linux 位置離線時：為了讓使用者離線訪問，您可以選擇更改共用根目錄上的 ACL，以便使用者無法再訪問該位置或獲取任何其他適當的防止內容在 Linux 伺服器上的此資料夾中更改的步驟。

運行最後一個 RoboCopy 輪。 它將接收任何可能錯過的更改。
這最後一步需要多長時間，取決於 RoboCopy 掃描的速度。 您可以通過測量上次運行所花的時間來估計時間（相當於停機時間）。

在 Windows Server 資料夾上創建共用，並可能調整 DFS-N 部署以指向該共用。 請務必設置與 Linux Samba 伺服器 SMB 共用相同的共用級別許可權。 如果在 Linux Samba 伺服器上使用了本地使用者，則需要將這些使用者重新創建為 Windows Server 本地使用者，並將現有 SIDs RoboCopy 移動到您的 Windows 伺服器到新 Windows Server 本地使用者的 SID。 如果您使用 AD 帳戶和 ACL，RoboCopy 將移動它們，無需採取進一步操作。

您已完成將共用/共用組遷移到公共根或卷。 （取決於從階段 1 的映射）

您可以嘗試並行運行其中的幾個副本。 我們建議一次處理一個 Azure 檔共用的範圍。

> [!WARNING]
> 將所有資料從 Linux Samba 伺服器移動到 Windows 伺服器後，遷移已完成：返回到 Azure 門戶中的所有***同步組***，並將雲分層卷可用空間百分比值調整為更適合緩存利用率的內容，例如 20%。 

雲分層卷可用空間策略在卷級別上運行，其中可能有多個伺服器終結點從該端點同步。 如果您忘記調整一個伺服器終結點上的可用空間，同步將繼續應用最嚴格的規則，並嘗試保留 99% 的可用磁碟空間，從而使本機快取無法按預期執行。 除非您的目標是僅具有僅包含很少訪問的卷的命名空間，否則您會將存儲空間的其餘部分保留為另一個方案。

## <a name="troubleshoot"></a>疑難排解

最有可能遇到的問題是，RoboCopy 命令在 Windows Server 端的 *"卷已滿"* 時失敗。 雲分層每小時會運行一次，以便從已同步的本地 Windows Server 磁片中疏散內容。 它的目標是達到您的99%的可用空間的音量。

讓同步進度和雲分層釋放磁碟空間。 您可以在 Windows 伺服器上的檔資源管理器中觀察到這一點。

當 Windows 伺服器具有足夠的可用容量時，重新運行該命令將解決此問題。 當你進入這種情況時，沒有什麼能打破，你可以滿懷信心地前進。 再次運行命令的不便是唯一的後果。

檢查以下部分中的連結，以排除 Azure 檔同步問題。

## <a name="next-steps"></a>後續步驟

有關 Azure 檔共用和 Azure 檔同步，還有更多要瞭解的問題。以下文章有助於瞭解高級選項、最佳實踐，並包含故障排除説明。 這些文章根據需要連結到[Azure 檔共用文件](storage-files-introduction.md)。

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
