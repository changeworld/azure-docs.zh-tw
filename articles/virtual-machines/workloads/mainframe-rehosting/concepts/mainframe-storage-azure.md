---
title: 將大型機存儲移動到 Azure 存儲
description: 可大規模擴展的 Azure 存儲資源可説明基於大型機的組織遷移 IBM z14 應用程式並實現現代化。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288909"
---
# <a name="move-mainframe-storage-to-azure"></a>將大型機存儲移動到 Azure

要在 Microsoft Azure 上運行大型機工作負荷，您需要瞭解大型機的功能與 Azure 的比較情況。 大規模擴展的存儲資源可以説明組織開始現代化，而不會放棄他們依賴的應用程式。

Azure 提供類似于大型機的功能和存儲容量，可與基於 IBM z14 的系統（本文編寫時最新的模型）相媲美。 本文介紹如何在 Azure 上獲取可比較的結果。

## <a name="mainframe-storage-at-a-glance"></a>大型機存儲一覽

IBM 大型機以兩種方式描述存儲。 第一個是直接存取存放裝置 （DASD）。 第二個是順序存儲。 對於管理存儲，大型機提供資料設施存儲管理子系統 （DFSMS）。 它管理對各種存放裝置的資料訪問。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device)是指用於輔助（非記憶體中）存儲的單獨設備，允許使用唯一位址直接存取資料。 最初，術語 DASD 應用於旋轉磁片、磁桶或資料單元。 但是，現在該術語也適用于固態存放裝置 （SSD）、存放區域網路 （SAN）、網路連接存儲 （NAS） 和光碟機。 就本文檔而言，DASD 是指旋轉磁片、SAN 和 SSD。

與 DASD 存儲不同，大型機上的順序存儲是指從起點訪問資料，然後以行讀取或寫入的設備（如磁帶磁碟機）。

存放裝置通常使用光纖連接 （FICON） 連接，或者使用[HiperSocket](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)直接存取主機的 IO 匯流排，這是一種 IBM 技術，用於使用虛擬機器管理程式在伺服器上的分區之間進行高速通信。

大多數大型機系統將存儲分為兩種類型：

- *日常操作需要線上存儲*（也稱為熱存儲）。 DASD 存儲通常用於此目的。 但是，順序存儲（如每日磁帶備份（邏輯備份或物理備份）也可用於此目的。

- *不能保證存檔存儲*（也稱為冷存儲）在給定時間安裝。 相反，它會根據需要裝載和訪問。 存檔存儲通常使用順序磁帶備份（邏輯備份或物理備份）實現存儲。

## <a name="mainframe-versus-io-latency-and-iops"></a>大型機與 IO 延遲和 IOPS

大型機通常用於需要高性能 IO 和低 IO 延遲的應用程式。 他們可以使用與 IO 設備和 HiperSocket 的 FICON 連接執行此操作。 當 HiperSockets 用於將應用程式和設備直接連接到大型機的 IO 通道時，可以達到微秒的延遲。

## <a name="azure-storage-at-a-glance"></a>Azure 存儲一覽

用於存儲的 Azure 基礎結構即服務 （[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)） 選項提供了可比較的大型機容量。

Microsoft 為 Azure 中託管的應用程式提供了價值數 PB 的存儲，並且有多個存儲選項。 這些範圍從用於高性能的 SSD 存儲到用於大型存放區和歸檔的低成本 Blob 存儲。 此外，Azure 還為存儲提供了資料冗余選項，在大型機環境中需要付出更多努力。

如下表摘要，Azure 存儲可作為[Azure 磁片](/azure/virtual-machines/windows/managed-disks-overview)[、Azure 檔和](/azure/storage/files/storage-files-introduction)Azure [Blob](/azure/storage/blobs/storage-blobs-overview)提供。 詳細瞭解[何時使用每個](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>類型</th><th>描述</th><th>使用時，你想：</th></tr>
</thead>
<tbody>
<tr><td>Azure 檔案
</td>
<td>
提供 SMB 介面、用戶端庫和<a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a>介面，允許從任何位置訪問存儲的檔。
</td>
<td><ul>
<li>當應用程式使用本機檔案系統 API 在應用程式與 Azure 中運行的其他應用程式之間共用資料時，將應用程式提升到雲。</li>
<li>存儲需要從許多 VM 訪問的開發和調試工具。</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>提供用戶端庫和<a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a>介面，允許在塊 blob 中大規模存儲和訪問非結構化資料。 也支援將 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> 用於企業巨量資料分析解決方案。
</td>
<td><ul>
<li>支援應用程式中的流式處理和隨機訪問方案。</li>
<li>可隨時隨地訪問應用程式資料。</li>
<li>在 Azure 上構建企業資料湖並執行大資料分析。</li>
</ul></td>
</tr>
<tr><td>Azure 磁碟
</td>
<td>提供用戶端庫和<a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a>介面，允許持續存儲資料並從附加的虛擬硬碟訪問資料。
</td>
<td><ul>
<li>提升和轉移使用本機檔案系統 API 以將資料讀取和寫入持久磁片的應用程式。</li>
<li>存儲不需要從磁片附加到的 VM 外部訪問的資料。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 熱（連線）和冷（存檔）存儲

給定系統的存儲類型取決於系統的要求，包括存儲大小、輸送量和 IOPS。 對於大型機上的 DASD 類型存儲，Azure 上的應用程式通常使用 Azure 磁片磁碟機存儲。 對於大型機存檔存儲，Blob 存儲在 Azure 上使用。

SSD 在 Azure 上提供最高的存儲性能。 以下選項可用（截至本文檔編寫）：

| 類型         | 大小           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB 到 64 TB  | 1，200 到 160，000 IOPS |
| 進階 SSD  | 32 GB 到 32 TB | 12 到 15，000 IOPS     |
| 標準 SSD | 32 GB 到 32 TB | 12 到 2，000 IOPS      |

Blob 存儲在 Azure 上提供最大的存儲量。 除了存儲大小外，Azure 還提供託管和非託管存儲。 使用託管存儲，Azure 負責管理基礎存儲帳戶。 使用非託管存儲時，使用者負責設置適當大小的 Azure 存儲帳戶以滿足存儲要求。

## <a name="next-steps"></a>後續步驟

- [大型機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [大型機在 Azure 虛擬機器上重新託管](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [將大型機計算移動到 Azure](mainframe-compute-Azure.md)
- [決定何時使用 Azure Blob、Azure 檔案服務或 Azure 磁碟](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [適用于 Azure VM 工作負荷的標準 SSD 託管磁片](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 資源

- [IBM Z 上的並行系統](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 和耦合設施：超越基礎知識](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [為 Db2 純尺規功能安裝創建所需的使用者](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 創建實例命令](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 純標群集資料庫解決方案](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [適用于大型機應用程式的 Microsoft Azure 政府雲](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [微軟和FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>更多遷移資源

- [Azure 虛擬資料中心：原形移轉指南](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
