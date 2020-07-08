---
title: 將大型主機存放裝置移至 Azure 儲存體
description: 可大幅調整規模的 Azure 儲存體資源有助於以大型主機為基礎的組織遷移和現代化 IBM z14 應用程式。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76288909"
---
# <a name="move-mainframe-storage-to-azure"></a>將大型主機儲存體移至 Azure

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道您的主機功能與 Azure 的比較方式。 可大幅擴充的儲存體資源可以協助組織開始現代化，而不會放棄他們所依賴的應用程式。

Azure 提供類似大型主機的功能和儲存體容量，可與 IBM z14 為基礎的系統（此撰寫的最新模型）比較。 本文會告訴您如何在 Azure 上取得可比較的結果。

## <a name="mainframe-storage-at-a-glance"></a>大型主機存放區一覽

IBM 大型主機以兩種方式來區分儲存體。 第一個是直接存取儲存體裝置（DASD）。 第二個是順序儲存。 若要管理存放裝置，大型主機會提供資料設備存放管理子系統（DFSMS）。 它會管理對各種存放裝置的資料存取。

「 [DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) 」指的是用於次要（而非記憶體中）儲存體的個別裝置，允許使用唯一的位址來直接存取資料。 一開始會將「DASD」一詞套用到旋轉磁片、磁鼓或資料格。 不過，現在這一詞也可以套用至固態存放裝置（Ssd）、存放區域網路（San）、網路連接儲存裝置（NAS）和光學磁片磁碟機。 基於本檔的目的，「DASD」指的是磁片、San 和 Ssd 的旋轉。

相較于 DASD 存放裝置，大型主機上的順序存放區指的是磁片磁碟機，例如從起始點存取資料，然後在一行中讀取或寫入的磁帶機。

存放裝置通常是使用光纖連線（FICON）來連接，或直接使用[HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)在大型主機的 IO 匯流排上存取，這是一種 IBM 技術，可在伺服器上的磁碟分割與虛擬機器之間進行高速通訊。

大部分的大型主機系統會將儲存體分成兩種類型：

- 每日作業都需要*線上儲存體*（也稱為熱儲存）。 DASD 存放裝置通常用於此用途。 不過，順序存放裝置（例如每日磁帶備份（邏輯或實體））也可以用於此用途。

- 封存*儲存體*（也稱為冷儲存體）不保證在指定的時間裝載。 相反地，它會視需要掛接和存取。 封存儲存體通常是使用連續磁帶備份（邏輯或實體）來執行，以進行儲存。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型主機與 IO 延遲和 IOPS

大型主機通常用於需要高效能 IO 和低 IO 延遲的應用程式。 他們可以使用對 IO 裝置和 HiperSockets 的 FICON 連線來達到此目的。 當 HiperSockets 用來將應用程式和裝置直接連接到大型主機的 IO 通道時，可以達到毫秒的延遲。

## <a name="azure-storage-at-a-glance"></a>Azure 儲存體一覽

適用于儲存體的 Azure 基礎結構即服務（[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)）選項提供可比較的大型主機容量。

對於裝載于 Azure 中的應用程式，Microsoft 提供數 pb 的儲存體，而且您有數個儲存體選項。 從 SSD 儲存體的範圍，到低成本的 blob 儲存體，以供大量儲存和封存之用。 此外，Azure 會為儲存體提供資料冗余選項，這會在大型主機環境中進行更多的設定。

Azure 儲存體是以[Azure 磁片](/azure/virtual-machines/windows/managed-disks-overview)、 [Azure 檔案儲存體](/azure/storage/files/storage-files-introduction)和[azure blob](/azure/storage/blobs/storage-blobs-overview)的形式提供，如下表摘要所示。 深入瞭解[每個使用](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)時機。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>類型</th><th>Description</th><th>當您想要執行下列動作時，請使用：</th></tr>
</thead>
<tbody>
<tr><td>Azure 檔案
</td>
<td>
提供 SMB 介面、用戶端程式庫和<a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a>介面，允許從任何位置存取儲存的檔案。
</td>
<td><ul>
<li>當應用程式使用原生檔案系統 Api，在其和 Azure 中執行的其他應用程式之間共用資料時，請將應用程式隨即轉移至雲端。</li>
<li>儲存需要從許多 Vm 存取的開發和偵錯工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供用戶端程式庫和<a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a>介面，允許在區塊 blob 中大規模地儲存及存取非結構化資料。 也支援將 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> 用於企業巨量資料分析解決方案。
</td>
<td><ul>
<li>支援應用程式中的串流和隨機存取案例。</li>
<li>可以從任何地方存取應用程式資料。</li>
<li>在 Azure 上建立 enterprise data lake，並執行海量資料分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁碟
</td>
<td>提供用戶端程式庫和<a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a>介面，允許從連接的虛擬硬碟持續儲存和存取資料。
</td>
<td><ul>
<li>使用原生檔案系統 Api 來讀取和寫入資料至持續性磁片的隨即轉移應用程式。</li>
<li>儲存不需要從連接磁片的 VM 外部存取的資料。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 經常性存取（線上）和冷（封存）儲存體

指定系統的儲存體類型取決於系統的需求，包括儲存體大小、輸送量和 IOPS。 針對大型主機上的 DASD 類型儲存體，Azure 上的應用程式通常會改為使用 Azure 磁片磁碟機儲存體。 針對大型主機封存儲存體，會在 Azure 上使用 blob 儲存體。

Ssd 可在 Azure 上提供最高的儲存體效能。 以下是可用的選項（本檔的撰寫內容）：

| 類型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB 至 64 TB  | 1200至 160000 IOPS |
| 進階 SSD  | 32 GB 至 32 TB | 12到 15000 IOPS     |
| 標準 SSD | 32 GB 至 32 TB | 12到 2000 IOPS      |

Blob 儲存體提供 Azure 上的最大儲存體數量。 除了儲存體大小，Azure 還提供受控和非受控儲存體。 使用受控儲存體，Azure 會負責管理基礎儲存體帳戶。 使用非受控儲存體時，使用者需負責設定適當大小的 Azure 儲存體帳戶，以符合儲存體需求。

## <a name="next-steps"></a>後續步驟

- [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虛擬機器上的大型主機重新裝載](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型主機計算移至 Azure](mainframe-compute-Azure.md)
- [決定何時使用 Azure Blob、Azure 檔案服務或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [適用于 Azure VM 工作負載的標準 SSD 受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 資源

- [IBM Z 上的平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合設施：超越基本概念](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [建立 Db2 pureScale 功能安裝所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-建立實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型主機應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 和 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
