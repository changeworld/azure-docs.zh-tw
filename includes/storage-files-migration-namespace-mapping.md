---
title: 將資料夾結構對應至 Azure 檔案同步拓撲
description: 將現有的檔案和資料夾結構對應到 Azure 檔案共用，以搭配 Azure 檔案同步使用。共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87425151"
---
在此步驟中，您會評估您需要多少個 Azure 檔案共用。 單一 Windows Server 實例 (或叢集) 可同步最多30個 Azure 檔案共用。

您的磁片區上可能會有更多資料夾，您目前在本機與使用者和應用程式的 SMB 共用共用。 最簡單的方式是想像將1:1 對應到 Azure 檔案共用的內部部署共用。 如果您有一個夠小的數位，在30以下的單一 Windows Server 實例中，則建議使用1:1 對應。

如果您有超過30個共用，通常不需要將內部部署共用1:1 對應到 Azure 檔案共用。 請考慮下列選項。

#### <a name="share-grouping"></a>共用群組

如果您的 HR 部門 (實例) 總共有15個共用，您可能會考慮將所有 HR 資料儲存在單一 Azure 檔案共用中。 將多個內部部署共用儲存在一個 Azure 檔案共用中，並無法讓您在本機 Windows Server 實例上建立一般的15個 SMB 共用。 這只表示您會將這些15個共用的根資料夾組織成共同資料夾下的子資料夾。 然後，您會將此通用資料夾同步處理至 Azure 檔案共用。 如此一來，這個內部部署共用群組就只需要一個雲端中的 Azure 檔案共用。

#### <a name="volume-sync"></a>磁片區同步

Azure 檔案同步支援將磁片區的根目錄同步至 Azure 檔案共用。 如果您同步根資料夾，則所有子資料夾和檔案都會移至相同的 Azure 檔案共用。

同步處理磁片區的根目錄不一定是最佳答案。 同步多個位置有一些優點。 例如，這麼做有助於讓每個同步範圍的專案數目保持在較低的範圍。 使用較少的專案來設定 Azure 檔案同步不只對檔案同步有説明。較少的專案也會受益于下列案例：

* 從 Azure 檔案共用快照集進行雲端端還原，可做為備份。
* 內部部署伺服器的災難復原可以大幅提高速度。
* 直接在 Azure 檔案共用中進行的變更 (外部同步) 可以更快地偵測和同步。

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署對應的結構化方法

在稍後的步驟中部署雲端儲存體之前，請務必在內部部署資料夾與 Azure 檔案共用之間建立對應。 然後，此對應會通知您將布建多少 Azure 檔案同步 *同步群組* 資源。 同步處理群組會將您伺服器上的 Azure 檔案共用和資料夾結合在一起，並建立同步連接。

若要決定您需要多少個 Azure 檔案共用，請參閱下列限制和最佳作法。 這麼做可協助您將地圖優化。

* 安裝 Azure 檔案同步代理程式的伺服器最多可以與30個 Azure 檔案共用同步。
* Azure 檔案共用會部署在儲存體帳戶內。 這會讓儲存體帳戶成為效能數位的調整目標，例如 IOPS 和輸送量。 

  這兩個標準 (不是高階) Azure 檔案共用可能會讓儲存體帳戶可傳遞的效能達到飽和。 如果您打算只將 Azure 檔案同步附加至這些檔案共用，然後將數個 Azure 檔案共用群組至相同的儲存體帳戶，將不會造成問題。 請檢查 Azure 檔案共用的效能目標，以深入瞭解要考慮的相關度量。 

  如果您打算將應用程式，以原生方式使用 Azure 檔案共用，則您的 Azure 檔案共用可能需要更多的效能。 如果這是一種可能性，即使在未來，也最好將 Azure 檔案共用對應至自己的儲存體帳戶。
* 在單一 Azure 區域中，每個訂用帳戶都有250個儲存體帳戶的限制。

> [!TIP]
> 記住這項資訊後，通常必須將磁片區上的多個最上層資料夾組成通用的新根目錄。 然後，您可以將這個新的根目錄和您群組的所有資料夾，同步處理至單一 Azure 檔案共用。 這項技術可讓您在每個伺服器的30個 Azure 檔案共用同步的限制內保持不變。
>
> 在常見的根目錄下進行此分組不會影響資料的存取。 您的 Acl 保持不變。 您只需要調整任何共用路徑 (例如 SMB 或 NFS 共用) 您可能會在現在變更為通用根目錄的伺服器資料夾上。 沒有其他變更。

Azure 檔案同步的另一個重要層面，以及平衡的效能和經驗，都是瞭解 Azure 檔案同步效能的規模因素。 很明顯地，在透過網際網路同步處理檔案時，較大的檔案需要更多時間和頻寬來進行同步處理。

> [!IMPORTANT]
> Azure 檔案同步最重要的縮放向量是需要同步處理的 (檔案和) 資料夾的專案數目。

Azure 檔案同步支援最多將100000000個專案同步至單一 Azure 檔案共用。 您可以超過此限制，而且只會顯示 Azure 檔案同步團隊定期測試的專案。

最佳做法是讓每個同步處理範圍的專案數目保持低。 這是您將資料夾對應至 Azure 檔案共用時要考慮的重要因素。

在您的情況下，您可能會使用稍早) 所述的新通用根資料夾方法，以邏輯方式將一組資料夾同步處理至相同的 Azure 檔案共用 (。 但是重新組合資料夾可能還是比較好，因為它們會同步至兩個 Azure 檔案共用，而不是一個 Azure 檔案共用。 您可以使用這種方法，在伺服器之間保持每個檔案共用的檔案和資料夾數目。

#### <a name="create-a-mapping-table"></a>建立對應表

:::row:::
    :::column:::
        [![對應資料表的範例。下載下列檔案以體驗並使用此映射的內容。](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        使用先前的概念組合來協助判斷您需要多少個 Azure 檔案共用，以及現有資料的哪些部分最後會在哪個 Azure 檔案共用中。
        
        建立記錄您想法的資料表，以便您可以在下一個步驟中參考它。 保持組織很重要，因為當您一次布建許多 Azure 資源時，可能會很容易遺失對應計畫的詳細資料。 為了協助您建立完整的對應，您可以將 Microsoft Excel 檔案下載為範本。

[//]: # (HTML 會顯示為完成新增具有工作影像剖析的嵌套雙欄資料表，以及在同一行上進行文字/超連結的唯一方法。)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">下載命名空間對應範本。</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
