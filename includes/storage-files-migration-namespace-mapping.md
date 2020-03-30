---
title: 將資料夾結構映射到 Azure 檔同步拓撲
description: 將現有檔和資料夾結構映射到 Azure 檔共用，以便與 Azure 檔同步一起使用。在遷移文檔之間共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124711"
---
在此步驟中，您將評估所需的 Azure 檔共用數。 單個 Windows 伺服器（或群集）最多可以同步 30 個 Azure 檔共用。

卷上可能有更多的資料夾，您當前以 SMB 共用方式在本地共用給使用者和應用。 最簡單的方法是設想本地共用將 1：1 映射到 Azure 檔共用。 如果單個 Windows 伺服器的數位足夠小，低於 30，則建議使用 1：1 映射。

如果共用數超過 30，則通常不需要將本地共用 1：1 映射到 Azure 檔共用。
請考慮下列選項：

#### <a name="share-grouping"></a>共用分組

例如，如果人力資源部門共有 15 個共用，則可以考慮將所有 HR 資料存儲在單個 Azure 檔共用中。 在一個 Azure 檔共用中存儲多個本地共用不會阻止您在本地 Windows 伺服器上創建通常的 15 個 SMB 共用。 這僅意味著您將這 15 個共用的根資料夾組織為公共資料夾下的子資料夾。 然後，將此公用資料夾同步到 Azure 檔共用。 這樣，此一組本地共用只需要雲中的單個 Azure 檔共用。

#### <a name="volume-sync"></a>卷同步

Azure 檔同步支援將卷的根部同步到 Azure 檔共用。
如果同步根資料夾，則所有子資料夾和檔將最終位於同一 Azure 檔共用中。

合成卷的根並不總是最好的答案。 同步多個位置的好處是，這樣做有助於降低每個同步作用域的專案數。 使用少量專案設置 Azure 檔同步不僅有利於檔同步。專案數量減少還有利於其他方案，如：

* 從作為備份拍攝的 Azure 檔共用快照的雲端還原
* 本機伺服器的災害復原可以顯著加快速度
* 可以直接在 Azure 檔共用（同步之外）進行的更改可以更快地檢測和同步

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署映射的結構化方法

在後續步驟中部署雲存儲之前，在本地資料夾和 Azure 檔共用之間創建映射非常重要。 然後，此映射將通知將預配的 Azure 檔同步"同步組"資源數和哪些資源。 同步組將 Azure 檔共用和伺服器上的資料夾連接在一起，並建立同步連接。

要決定需要多少 Azure 檔共用，請查看以下限制和最佳實踐。 這樣做將説明您優化地圖：

* 安裝了 Azure 檔同步代理的伺服器最多可與 30 個 Azure 檔共用同步。
* Azure 檔共用部署在存儲帳戶中。 這使得存儲帳戶成為性能數位（如 IOPS 和輸送量）的規模目標。 從理論上講，兩個標準（非高級）Azure 檔共用可以飽和存儲帳戶所能提供的最大性能。 如果計畫僅將 Azure 檔同步附加到這些檔共用，則將多個 Azure 檔共用分組到同一存儲帳戶不會造成問題。 查看 Azure 檔共用性能目標，深入瞭解要考慮的相關指標。 如果計畫將應用提升到 Azure，該應用將本機使用 Azure 檔共用，則可能需要 Azure 檔共用的更多性能。 如果這是一種可能性，即使將來也是如此，那麼將 Azure 檔共用映射到其自己的存儲帳戶是最佳的。
* 單個 Azure 區域中每個訂閱有 250 個存儲帳戶的限制。

> [!TIP]
> 考慮到此資訊，通常需要將卷上的多個頂級資料夾分組到一個常見的新根目錄中。 然後，您將此新根目錄和分組到該目錄中的所有資料夾同步到單個 Azure 檔共用。                                                    

此技術允許您保持在每個伺服器的 30 個 Azure 檔共用同步限制內。
公共根項下的此分組不會影響對資料的訪問。 您的 ACL 保持不變，您只需調整伺服器資料夾中可能具有的任何共用路徑（如 SMB 或 NFS 共用），現在更改為公共根目錄。 沒有其他改變。

Azure 檔同步的另一個重要方面以及平衡的性能和體驗是瞭解 Azure 檔同步性能的規模因素。 顯然，當檔在互聯網上同步時，較大的檔需要更多的時間和頻寬來同步。

> [!IMPORTANT]
> Azure 檔同步最重要的縮放向量是需要同步的專案（檔和資料夾）數。

Azure 檔同步支援將多達 100，000 個專案同步到單個 Azure 檔共用。 可以超出此限制，並且僅描述 Azure 檔同步團隊定期測試的內容。

最佳做法是保持每個同步作用域的項數較低。 在將資料夾映射到 Azure 檔共用時，需要考慮這一方面。

即使在您的情況下，一組資料夾可以在邏輯上同步到同一個 Azure 檔共用（使用上面新的通用根資料夾方法），最好還是重新組合資料夾，以便它們同步到兩個而不是一個 Azure 檔共用。 此方法可用於保持伺服器中每個檔共用的檔和資料夾數的平衡。

#### <a name="create-a-mapping-table"></a>創建映射表

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        使用前面概念的組合來説明確定需要多少 Azure 檔共用，以及現有資料的哪些部分最終將位於 Azure 檔共用的哪個部分。
        
        創建一個表來記錄您的想法，以便您可以在下一步中引用它。 保持井然有序很重要，因為在同時預配許多 Azure 資源時，很容易丟失映射計畫的詳細資訊。 為了説明您創建完整的映射，您可以將 Microsoft Excel 檔下載為範本。

[//]: # (HTML 似乎是在同一行上添加具有工作圖像解析和文本/超連結的嵌套兩清單的唯一方法。)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">下載命名空間映射範本。</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
