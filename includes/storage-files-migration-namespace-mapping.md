---
title: 將資料夾結構對應至 Azure 檔案同步拓撲
description: 將現有的檔案和資料夾結構對應至 Azure 檔案共用，以與 Azure 檔案同步搭配使用。跨遷移檔共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425151"
---
在此步驟中，您會評估您需要多少個 Azure 檔案共用。 單一 Windows Server 實例（或叢集）最多可以同步30個 Azure 檔案共用。

您的磁片區上可能會有更多的資料夾，而您目前以 SMB 共用的形式，將其提供給您的使用者和應用程式。 最簡單的方式是預見將1:1 對應到 Azure 檔案共用的內部部署共用。 如果您有夠小的數位，低於30的單一 Windows Server 實例，則建議使用1:1 對應。

如果您的共用數超過30個，通常不需要將內部部署共用1:1 對應到 Azure 檔案共用。 請考慮下列選項。

#### <a name="share-grouping"></a>共用群組

如果您的人力資源部門（例如）總共有15個共用，您可以考慮將所有 HR 資料儲存在單一 Azure 檔案共用中。 將多個內部部署共用儲存在一個 Azure 檔案共用中，並不會讓您在本機 Windows Server 實例上建立平常的15個 SMB 共用。 這只表示您將這15個共用的根資料夾組織成一個通用資料夾下的子資料夾。 接著，您會將此通用資料夾同步至 Azure 檔案共用。 如此一來，此內部部署共用群組就只需要雲端中的單一 Azure 檔案共用。

#### <a name="volume-sync"></a>磁片區同步

Azure 檔案同步支援將磁片區的根目錄同步至 Azure 檔案共用。 如果您同步處理根資料夾，則所有子資料夾和檔案都會移至相同的 Azure 檔案共用。

同步處理磁片區的根目錄不一定是最佳的答案。 同步處理多個位置有其優點。 例如，這麼做有助於讓每個同步範圍的專案數降低。 使用較少的專案來設定 Azure 檔案同步不只對檔案同步很有説明。較少的專案也會受益于下列案例：

* 從 Azure 檔案共用快照集進行雲端端還原，可以做為備份。
* 內部部署伺服器的嚴重損壞修復可以大幅加速。
* 直接在 Azure 檔案共用（外部同步處理）中所做的變更，可以更快速地偵測及同步處理。

#### <a name="a-structured-approach-to-a-deployment-map"></a>部署對應的結構化方法

在稍後的步驟中部署雲端存放裝置之前，請務必在內部部署資料夾和 Azure 檔案共用之間建立對應。 然後，此對應會通知您要布建多少個 Azure 檔案同步*同步群組*資源。 同步群組會將 Azure 檔案共用和伺服器上的資料夾系結在一起，並建立同步連接。

若要決定您需要多少個 Azure 檔案共用，請參閱下列限制和最佳作法。 這麼做可協助您優化對應。

* 已安裝 Azure 檔案同步代理程式的伺服器可以與最多30個 Azure 檔案共用同步處理。
* Azure 檔案共用會部署在儲存體帳戶內。 這會讓儲存體帳戶成為效能數位的調整目標，例如 IOPS 和輸送量。 

  理論上，兩個標準（非 premium） Azure 檔案共用可以讓儲存體帳戶可提供的最大效能飽和。 如果您打算只將 Azure 檔案同步附加至這些檔案共用，則將數個 Azure 檔案共用分組到相同的儲存體帳戶將不會產生問題。 請參閱 Azure 檔案共用效能目標，以深入瞭解要考慮的相關計量。 

  如果您計畫將應用程式以原生方式使用 Azure 檔案共用，您的 Azure 檔案共用可能需要更多效能。 如果這是可能的，即使在未來的情況下，也最好將 Azure 檔案共用對應至自己的儲存體帳戶。
* 在單一 Azure 區域中，每個訂用帳戶的儲存體帳戶限制為250。

> [!TIP]
> 記住這項資訊之後，通常必須將磁片區上的多個最上層資料夾組成一個通用的新根目錄。 您接著會將這個新的根目錄和您分組的所有資料夾同步至單一 Azure 檔案共用。 這項技術可讓您在每個伺服器的30個 Azure 檔案共用同步處理範圍內保持不變。
>
> 此群組在通用根目錄下不會影響資料的存取。 您的 Acl 會保持不變。 您只需要調整現在已變更為通用根之伺服器資料夾上的任何共用路徑（例如 SMB 或 NFS 共用）。 沒有其他變更。

Azure 檔案同步的另一個重要層面，以及平衡的效能和經驗，就是了解 Azure 檔案同步效能的規模因素。 很明顯地，透過網際網路同步處理檔案時，較大的檔案會花更多的時間和頻寬來進行同步處理。

> [!IMPORTANT]
> Azure 檔案同步最重要的縮放向量是需要同步處理的專案（檔案和資料夾）數目。

Azure 檔案同步支援將最多100000000個專案同步至單一 Azure 檔案共用。 可以超過此限制，而且只會顯示 Azure 檔案同步小組定期測試的內容。

最佳做法是讓每個同步處理範圍的專案數降低。 這是您將資料夾對應至 Azure 檔案共用時所需考慮的重要因素。

在您的情況下，可能會有一組資料夾以邏輯方式同步處理至相同的 Azure 檔案共用（使用稍早所述的新通用根資料夾方法）。 但是重新組合資料夾可能還是會較好，因為它們會同步至兩個 Azure 檔案共用，而不是一個。 您可以使用這種方法，讓每個檔案共用的檔案和資料夾數目保持在伺服器上的平衡。

#### <a name="create-a-mapping-table"></a>建立對應表

:::row:::
    :::column:::
        [![對應資料表的範例。下載下列檔以體驗並使用此映射的內容。](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        使用先前的概念組合，以協助判斷您需要多少個 Azure 檔案共用，而現有資料的哪些部分將會在哪個 Azure 檔案共用中結束。
        
        建立記錄您想法的資料表，讓您可以在下一個步驟中參考它。 保持組織十分重要，因為當您一次布建許多 Azure 資源時，可能會很容易遺失對應計畫的詳細資料。 為了協助您建立完整的對應，您可以下載 Microsoft Excel 檔案做為範本。

[//]: # (HTML 會顯示為完成在同一行上加入具有工作影像剖析和文字/超連結的嵌套兩個數據行資料表的唯一方法。)

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
