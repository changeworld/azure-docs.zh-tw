---
title: StorSimple 1200 遷移到 Azure 檔同步
description: 瞭解如何將 StorSimple 1200 系列虛擬裝置遷移到 Azure 檔同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502360"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 遷移到 Azure 檔同步

StorSimple 1200 系列是在本地資料中心運行的虛擬裝置。 可以將資料從此設備遷移到 Azure 檔同步環境。 Azure 檔同步是 StorSimple 設備可以遷移到的預設且戰略性的長期 Azure 服務。

StorSimple 1200 系列將于 2022 年 12 月實現[生命週期結束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)。  儘快開始規劃遷移非常重要。 本文提供了成功遷移到 Azure 檔同步的必要背景知識和遷移步驟。 

## <a name="azure-file-sync"></a>Azure 檔案同步

> [!IMPORTANT]
> Microsoft 致力於協助客戶遷移。 Email AzureFilesMigration@microsoft .com，用於自訂遷移計畫以及在遷移期間提供説明。

Azure 檔同步是一種 Microsoft 雲服務，基於兩個主要元件：

* 檔同步和雲分層。
* 檔共用作為 Azure 中的本機存儲，可通過多個協定（如 SMB 和檔 REST）進行訪問。 Azure 檔共用可與 Windows 伺服器上的檔共用相媲美，您可以將其作為網路磁碟機本機裝載。 它支援重要的檔逼真度方面，如屬性、許可權和時間戳記。 與 StorSimple 不同，無需應用程式/服務即可解釋存儲在雲中的檔和資料夾。 在雲中存儲通用檔案伺服器資料以及某些應用程式資料的理想且最靈活的方法。

本文重點介紹遷移步驟。 如果要在遷移之前瞭解有關 Azure 檔同步的更多內容，我們建議您提供以下文章：

* [Azure 檔同步 - 概述](https://aka.ms/AFS "總覽")
* [Azure 檔同步 - 部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移轉目標

目標是保證生產資料的完整性，並保證可用性。 後者要求將停機時間降至最低，以便它能夠適應或僅略高於常規維護視窗。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 遷移路徑到 Azure 檔同步

運行 Azure 檔同步代理需要本地 Windows 伺服器。 Windows 伺服器可以至少位於 2012R2 伺服器，但理想情況下是 Windows 伺服器 2019。

有許多替代遷移路徑，它會創建太長的文章來記錄所有這些路徑，並說明它們為什麼在本文中作為最佳實踐建議的路徑上承擔風險或缺點。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="遷移路線概述本文中進一步的步驟。":::

上圖描述了與本文中各節相對應的步驟。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>第 1 步：預配本地 Windows 伺服器和存儲

1. 創建 Windows 伺服器 2019 - 至少 2012R2 - 作為虛擬機器或物理伺服器。 還支援 Windows 伺服器容錯移轉叢集。
2. 預配或添加直接連接存儲（與不支援 NAS 的 NAS 相比的 DAS）。 Windows Server 存儲的大小必須等於或大於虛擬 StorSimple 1200 設備的可用容量大小。

### <a name="step-2-configure-your-windows-server-storage"></a>第 2 步：配置 Windows 伺服器存儲

在此步驟中，您將 StorSimple 存儲結構（卷和共用）映射到 Windows Server 存儲結構。
如果計畫更改存儲結構，即卷數、資料檔案夾與卷的關聯，或當前 SMB/NFS 共用上方或下方的子資料夾結構，則現在是考慮這些更改的時候了。
在配置 Azure 檔同步後更改檔和資料夾結構是繁瑣的，應避免。
本文假定您正在映射 1：1，因此在按照本文中的步驟操作時，必須考慮映射更改。

* 任何生產資料都不應最終位於 Windows Server 系統磁碟區上。 系統磁碟區不支援雲分層。 但是，遷移和連續操作作為 StorSimple 替換是必需的。
* 在 Windows 伺服器上預配與 StorSimple 1200 虛擬裝置上的卷數相同。
* 配置所需的任何 Windows 伺服器角色、功能和設置。 我們建議您加入宣告 Windows 伺服器更新，以確保作業系統的安全和最新。 同樣，我們建議加入宣告 Microsoft 更新，使 Microsoft 應用程式保持最新，包括 Azure 檔同步代理。
* 在閱讀以下步驟之前，不要配置任何資料夾或共用。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>第 3 步：部署第一個 Azure 檔同步雲資源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步驟 4：將本地卷和資料夾結構與 Azure 檔同步和 Azure 檔共用資源匹配

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>第 5 步：預配 Azure 檔共用

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>步驟 6：配置 Windows 伺服器目的檔案夾

在前面的步驟中，您已經考慮了將確定同步拓撲元件的所有方面。 現在是時候準備伺服器接收檔進行上傳了。

創建**所有**資料夾，這些資料夾將每個資料夾同步到其自己的 Azure 檔共用。
請務必遵循前面記錄的資料夾結構。 例如，如果您決定將多個本地 SMB 共用同步到單個 Azure 檔共用中，則需要將它們放在卷上的公用根資料夾下。 立即在卷上創建此目標根資料夾。

預配的 Azure 檔共用數應與在此步驟中創建的資料夾數匹配 - 將在根級別同步的卷數。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>第 7 步：部署 Azure 檔同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>第 8 步：配置同步

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **請務必打開雲分層！** 如果本機伺服器沒有足夠的空間將資料的總大小存儲在 StorSimple 雲存儲中，則需要這樣做。 將分層策略（臨時用於遷移）設置為 99% 的卷可用空間。

重複同步組創建和添加匹配伺服器資料夾的步驟，作為需要配置用於同步的所有 Azure 檔共用/伺服器位置的伺服器終結點。

### <a name="step-9-copy-your-files"></a>第 9 步：複製檔

基本遷移方法是從 StorSimple 虛擬裝置到 Windows 伺服器的 RoboCopy，以及 Azure 檔同步到 Azure 檔共用。

將第一個本機複本運行到 Windows Server 目的檔案夾：

* 確定虛擬 StorSimple 設備上的第一個位置。
* 標識 Windows 伺服器上已配置 Azure 檔同步的匹配資料夾。
* 使用 RoboCopy 啟動副本

以下 RoboCopy 命令將從 StorSimple Azure 存儲中撤回到本地 StorSimple 檔，然後將這些檔移動到 Windows Server 目的檔案夾。 Windows 伺服器將將其同步到 Azure 檔共用。 當本地 Windows Server 卷已滿時，雲分層將啟動，並且已成功同步的層檔將啟動。 雲分層將生成足夠的空間來繼續從 StorSimple 虛擬裝置複製。 雲分層每小時檢查一次，查看已同步的內容，並釋放磁碟空間以達到 99% 的卷可用空間。

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
      /UNILOG：<file name>
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
      允許在同一目標/目標上按順序運行此 RoboCopy 命令幾次。 它標識之前複製的內容，並省略它。 只會處理自上次運行以來發生的更改、添加和"*刪除*"。 如果之前未運行該命令，則不會省略任何內容。 對於仍在積極使用和更改的源位置，這是一個極好的選項。
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

首次運行 RoboCopy 命令時，使用者和應用程式仍在訪問 StorSimple 檔和資料夾，並可能更改它。 有可能，RoboCopy 已經處理了目錄，移動到下一個目錄，然後在源位置（StorSimple）上添加、更改或刪除檔，而該檔現在在此當前 RoboCopy 運行中不會處理。 沒關係

第一個運行是將大部分資料移回本地，轉移到 Windows 伺服器並通過 Azure 檔同步備份到雲中。這可能需要很長時間，具體取決於：

* 您的下載頻寬
* StorSimple 雲服務的召回速度
* 上傳頻寬
* 需要由任一服務處理的專案（檔和資料夾）的數量

完成初始運行後，再次運行該命令。

第二次完成得更快，因為它只需要傳輸自上次運行以來發生的更改。 這些變化可能是 StorSimple 的本地更改，因為它們是最新的。 這進一步縮短了時間，因為從雲中調用的需求減少了。 在第二次運行期間，仍然會累積新的更改。

重複此過程，直到您確信完成所需的時間是可接受的停機時間。

當您認為停機時間可以接受，並準備使 StorSimple 位置離線時，請立即執行此操作：例如，刪除 SMB 共用，以便任何使用者都無法訪問該資料夾或採取任何其他阻止內容在此中更改的相應步驟資料夾在Stor簡單。

運行最後一個 RoboCopy 輪。 這將選取任何可能錯過的更改。
這最後一步需要多長時間，取決於 RoboCopy 掃描的速度。 您可以通過測量上次運行所花的時間來估計時間（相當於停機時間）。

在 Windows Server 資料夾上創建共用，並可能調整 DFS-N 部署以指向該共用。 請務必設置與 StorSimple SMB 共用相同的共用級別許可權。

您已完成將共用/共用組遷移到公共根或卷。 （取決於您映射的內容，並決定需要進入相同的 Azure 檔共用。

您可以嘗試並行運行其中的幾個副本。 我們建議一次處理一個 Azure 檔共用的範圍。

> [!WARNING]
> 將所有資料從 StorSimple 移動到 Windows Server 後，遷移已完成：返回到 Azure 門戶中的所有***同步組***，並將雲分層卷可用空間百分比值調整為更適合緩存利用率的內容，例如 20%。 

雲分層卷可用空間策略在卷級別上運行，其中可能有多個伺服器終結點從該端點同步。 如果您忘記調整一個伺服器終結點上的可用空間，同步將繼續應用最嚴格的規則，並嘗試保留 99% 的可用磁碟空間，從而使本機快取無法按預期執行。 除非您的目標是僅具有僅包含很少訪問的存檔資料的卷的命名空間。

## <a name="troubleshoot"></a>疑難排解

最有可能遇到的問題是，RoboCopy 命令在 Windows Server 端的 *"卷已滿"* 時失敗。 如果是這樣的話，那麼你的下載速度可能比你的上傳速度好。 雲分層每小時會運行一次，以便從已同步的本地 Windows Server 磁片中疏散內容。

讓同步進度和雲分層釋放磁碟空間。 您可以在 Windows 伺服器上的檔資源管理器中觀察到這一點。

當 Windows 伺服器具有足夠的可用容量時，重新運行該命令將解決此問題。 當你進入這種情況時，沒有什麼能打破，你可以滿懷信心地前進。 再次運行命令的不便是唯一的後果。

您還可以遇到其他 Azure 檔同步問題。
如果發生這種情況，請看**LINK Azure 檔同步故障排除指南**。

## <a name="relevant-links"></a>相關連結

遷移內容：

* [StorSimple 8000 系列遷移指南](storage-files-migration-storsimple-8000.md)

Azure 檔同步內容：

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
