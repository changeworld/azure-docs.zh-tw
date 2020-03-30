---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262234"
---
Azure 託管磁片當前提供四種磁片類型，每種類型都針對特定的客戶方案。

## <a name="disk-comparison"></a>磁碟比較

下表提供了超級磁片、高級固態硬碟 （SSD）、標準 SSD 和託管磁片的標準硬碟磁碟機 （HDD） 的比較，以説明您決定使用什麼。

|   | Ultra 磁碟   | 進階 SSD   | 標準 SSD   | 標準 HDD   |
|---------|---------|---------|---------|---------|
|磁碟類型   |SSD   |SSD   |SSD   |HDD   |
|狀況   |IO 密集型工作負載，如[SAP HANA、](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)頂級資料庫（例如 SQL、Oracle）和其他事務密集型工作負載。   |生產環境和重視效能的工作負載   |網頁伺服器，輕量使用的企業應用程式和開發/測試   |備份、不重要、存取不頻繁   |
|最大磁碟大小   |65,536 GiB    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大輸送量   |2,000 MiB/秒    |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra 磁碟

Azure Ultra 磁碟可為 Azure IaaS VM 提供高輸送量、高 IOPS 以及一致的低延遲磁碟儲存體。 超磁片的一些其他好處包括能夠動態更改磁片的性能以及工作負載，而無需重新開機虛擬機器 （VM）。 Ultra 磁碟適用於處理大量資料的工作負載 (例如 SAP Hana)、最上層資料庫，以及高交易量的工作負載。 Ultra 磁碟只可用來作為資料磁碟。 建議您使用進階 SSD 作為作業系統磁碟。

### <a name="performance"></a>效能

當您佈建 Ultra 磁碟時，您可個別設定磁碟的容量和效能。 Ultra 磁片有多種固定尺寸，從 4 GiB 到 64 TiB，具有靈活的性能配置模型，允許您獨立配置 IOPS 和輸送量。

超磁片的一些關鍵功能包括：

- 磁片容量：超磁片容量範圍從 4 GiB 到 64 TiB。
- 磁片 IOPS：超級磁片支援 IOPS 限制 300 IOPS/GiB，每個磁片最多 160 K IOPS。 要實現預配的 IOPS，請確保所選磁片 IOPS 小於 VM IOPS 限制。 每個磁片的最小保證 IOPS 為 2 IOPS/GiB，總基線最小為 100 IOPS。 例如，如果您有一個 4 GiB 超磁片，您將擁有至少 100 個 IOPS，而不是 8 個 IOPS。
- 磁片輸送量：對於超磁片，每個預配 IOPS 單個磁片的輸送量限制為 256 KiB/s，每個磁片最多為 2000 MBps（其中 MBps = 10⁄6 位元組/秒）。 每個預配 IOPS 的每個磁片的最小保證輸送量為 4KiB/s，總體基線最小為 1 MBps。
- Ultra 磁片支援在運行時調整磁片性能屬性（IOPS 和輸送量），而無需從虛擬機器分離磁片。 向磁碟發出磁碟效能調整作業後，最多可能需要一個小時，變更才會實際生效。 在 24 小時視窗內，有四個性能調整操作的限制。 由於缺少性能頻寬容量，性能調整操作可能會失敗。

### <a name="disk-size"></a>磁碟大小

|磁碟大小 (GiB)  |IOPS 上限  |輸送量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536 (此範圍中的大小會以 1 TiB 為單位遞增)     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>GA 範圍和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
