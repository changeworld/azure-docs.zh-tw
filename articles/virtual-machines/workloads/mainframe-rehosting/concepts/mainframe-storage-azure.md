---
title: 將大型主機儲存體移至 Azure 儲存體
description: 可大幅調整的 Azure 儲存體資源可協助以大型主機為基礎的組織遷移和現代化 IBM z14 應用程式。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 8f57210ff6b65ee3a893fb344a48629466e90004
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095350"
---
# <a name="move-mainframe-storage-to-azure"></a>將大型主機儲存體移至 Azure

若要在 Microsoft Azure 上執行大型主機工作負載，您需要知道您的大型主機功能與 Azure 的比較方式。 可大幅擴充的儲存體資源可協助組織開始現代化，而不放棄所依賴的應用程式。

Azure 提供類似于 IBM z14 型系統的大型大型主機功能和儲存體容量， (此撰寫) 的最新模型。 本文將告訴您如何在 Azure 上取得可比較的結果。

## <a name="mainframe-storage-at-a-glance"></a>大型主機存放裝置

IBM 大型主機的儲存方式有兩種。 第一個是 (DASD) 的直接存取儲存體裝置。 第二個是連續的儲存體。 針對管理存放裝置，大型主機會提供資料設施存放裝置管理子系統 (DFSMS) 。 它會管理各種存放裝置的資料存取。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) 指的是不同的裝置，適用于次要 (不是記憶體內部) 儲存體，可讓您使用唯一位址來直接存取資料。 一開始，會將 DASD 詞彙套用至旋轉磁片、磁性鼓或資料格。 不過，現在該詞彙也可以套用到固態存儲裝置， (Ssd) 、儲存區域網路 (San) 、網路連接存放裝置 (NAS) 和光碟機。 基於本檔的目的，DASD 指的是旋轉磁片、San 和 Ssd。

相較于 DASD 存放裝置，大型主機上的連續存放裝置指的是可從起點存取資料，然後讀取或寫入一行的裝置（例如磁帶機）。

儲存體裝置通常是使用光纖連線 (FICON) 來連接，或使用 [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)，直接在大型主機的 IO 匯流排上進行存取，這是一種 IBM 技術，可在伺服器上的磁碟分割上，使用虛擬程式來進行高速通訊。

大部分的大型主機系統會將儲存體分成兩種類型：

- *線上儲存體* (也知道每日作業需要經常性儲存體) 。 DASD 儲存體通常用於此用途。 不過，順序儲存（例如 (邏輯或實體) 的每日磁帶備份）也可用於此用途。

- 封存 *儲存體* (也稱為冷儲存體) 不保證會在指定的時間掛接。 相反地，它會視需要掛接和存取。 封存儲存體通常會使用順序磁帶備份來執行， (邏輯或實體) 以進行儲存。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型主機與 IO 延遲和 IOPS

大型主機通常用於需要高效能 IO 和低 IO 延遲的應用程式。 他們可以使用對 IO 裝置和 HiperSockets 的 FICON 連線來完成此動作。 當使用 HiperSockets 直接將應用程式和裝置連接到大型主機的 IO 通道時，可以達到毫秒的延遲。

## <a name="azure-storage-at-a-glance"></a>Azure 儲存體一覽

適用于儲存體的 Azure 基礎結構即服務 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 選項提供可比較的大型主機容量。

Microsoft 為裝載于 Azure 中的應用程式提供數 pb 的儲存體，且您有數個儲存體選項。 這些範圍從 SSD 儲存體，到低成本的 blob 儲存體，以進行大量儲存和封存。 此外，Azure 也為儲存體提供資料重複選項，這是在大型主機環境中設定所需的工作。

Azure 儲存體以 [Azure 磁片](../../../managed-disks-overview.md)、 [Azure 檔案儲存體](../../../../storage/files/storage-files-introduction.md)和 [azure blob](../../../../storage/blobs/storage-blobs-overview.md) 的形式提供，如下表摘要所示。 深入瞭解 [每個使用](../../../../storage/common/storage-introduction.md)時機。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>類型</th><th>描述</th><th>當您想要：</th></tr>
</thead>
<tbody>
<tr><td>Azure 檔案
</td>
<td>
提供 SMB 介面、用戶端程式庫和 <a href="/rest/api/storageservices/file-service-rest-api">REST</a> 介面，允許從任何位置存取儲存的檔案。
</td>
<td><ul>
<li>當應用程式使用原生檔案系統 Api 在 Azure 中執行的應用程式與其他應用程式之間共用資料時，隨即將應用程式隨即轉移至雲端。</li>
<li>儲存需要從許多 Vm 存取的開發和偵錯工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供用戶端程式庫和 <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> 介面，可讓您在區塊 blob 中大規模地儲存和存取非結構化資料。 也支援將 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> 用於企業巨量資料分析解決方案。
</td>
<td><ul>
<li>支援應用程式中的串流和隨機存取案例。</li>
<li>可從任何地方存取應用程式資料。</li>
<li>在 Azure 上建立企業 data lake，並執行 big data analytics。</li>
</ul></td>
</tr>
<tr><td>Azure 磁碟
</td>
<td>提供用戶端程式庫和 <a href="/rest/api/compute/disks">REST</a> 介面，允許從連接的虛擬硬碟持續儲存及存取資料。
</td>
<td><ul>
<li>使用原生檔案系統 Api 來讀取和寫入資料至持續性磁片的應用程式隨即轉移。</li>
<li>儲存不需要從磁片所連接的 VM 外部存取的資料。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 熱 (線上) 和冷 (封存) 儲存體

指定系統的儲存體類型取決於系統的需求，包括儲存體大小、輸送量和 IOPS。 針對大型主機上的 DASD 類型儲存體，Azure 上的應用程式通常會改用 Azure 磁片磁碟機儲存體。 針對大型主機封存儲存體，blob 儲存體會在 Azure 上使用。

Ssd 可在 Azure 上提供最高的儲存體效能。 以下是本檔撰寫)  (的可用選項：

| 類型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB 至 64 TB  | 1200至 160000 IOPS |
| 進階 SSD  | 32 GB 至 32 TB | 12到 15000 IOPS     |
| 標準 SSD | 32 GB 至 32 TB | 12到 2000 IOPS      |

Blob 儲存體會在 Azure 上提供最大量的儲存體。 除了儲存體大小，Azure 還提供受控和非受控儲存體。 使用受控儲存體時，Azure 會負責管理基礎儲存體帳戶。 使用非受控儲存體時，使用者需負責設定適當大小的 Azure 儲存體帳戶，以符合儲存體需求。

## <a name="next-steps"></a>後續步驟

- [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 虛擬機器上的大型主機重新裝載](../overview.md)
- [將大型主機計算移至 Azure](mainframe-compute-Azure.md)
- [決定何時使用 Azure Blob、Azure 檔案服務或 Azure 磁碟](../../../../storage/common/storage-introduction.md)
- [適用于 Azure VM 工作負載的標準 SSD 受控磁碟](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM 資源

- [在 IBM Z 平行 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和結合功能：超越基本概念](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [建立 Db2 pureScale 功能安裝所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-建立實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 叢集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型主機應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 與 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多的遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)