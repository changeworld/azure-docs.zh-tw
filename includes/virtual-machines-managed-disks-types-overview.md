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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262234"
---
Azure 受控磁片目前提供四種磁片類型，每種類型都是針對特定的客戶案例。

## <a name="disk-comparison"></a>磁碟比較

下表提供適用于受控磁片的 ultra 磁片、premium 固態硬碟（SSD）、標準 SSD 和標準硬碟（HDD）的比較，協助您決定要使用的內容。

|   | Ultra 磁片   | 進階 SSD   | 標準 SSD   | 標準 HDD   |
|---------|---------|---------|---------|---------|
|磁碟類型   |SSD   |SSD   |SSD   |HDD   |
|狀況   |IO 密集的工作負載，例如[SAP Hana](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)、最上層資料庫（例如 SQL、Oracle）和其他大量交易的工作負載。   |生產環境和重視效能的工作負載   |網頁伺服器，輕量使用的企業應用程式和開發/測試   |備份、不重要、存取不頻繁   |
|最大磁片大小   |65536 gib （GiB）    |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大輸送量   |2000 MiB/秒    |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultra 磁片

Azure ultra 磁片可為 Azure IaaS Vm 提供高輸送量、高 IOPS 以及一致的低延遲磁片儲存體。 Ultra 磁片的一些額外優點包括能夠以動態方式變更磁片的效能，以及您的工作負載，而不需要重新開機您的虛擬機器（VM）。 Ultra 磁片適用于資料密集的工作負載，例如 SAP Hana、最上層資料庫，以及高交易量的工作負載。 Ultra 磁片只能用來做為資料磁片。 建議您使用進階 SSD 作為作業系統磁碟。

### <a name="performance"></a>效能

當您佈建 Ultra 磁碟時，您可個別設定磁碟的容量和效能。 Ultra 磁片有數個固定大小，範圍從 4 GiB 到 64 TiB，並提供彈性的效能設定模型，可讓您獨立設定 IOPS 和輸送量。

Ultra 磁片的一些主要功能如下：

- 磁片容量： Ultra 磁片容量的範圍是從 4 GiB 到 64 TiB。
- 磁片 IOPS： Ultra 磁片支援 300 IOPS/GiB 的 IOPS 限制，最多可達每個磁片 160 K IOPS。 若要達到您所布建的 IOPS，請確定選取的磁片 IOPS 小於 VM IOPS 限制。 每個磁片的保證 IOPS 下限為 2 IOPS/GiB，整體基準最小值為 100 IOPS。 例如，如果您有4個 GiB 的 ultra 磁片，您最少會有 100 IOPS，而不是八個 IOPS。
- 磁片輸送量：使用 ultra 磁片時，單一磁片的輸送量限制為每個已布建 IOPS 的 256 KiB/秒，最多可達每個磁片 2000 MBps （MBps = 10 ^ 6 位元組/秒）。 針對每個布建的 IOPS，每個磁片的保證最小輸送量為 4KiB/s，整體基準最小值為 1 MBps。
- Ultra 磁片支援在執行時間調整磁片效能屬性（IOPS 和輸送量），而不需從虛擬機器卸離磁片。 向磁碟發出磁碟效能調整作業後，最多可能需要一個小時，變更才會實際生效。 24小時的時間範圍內有四種效能調整大小作業的限制。 效能調整大小作業可能會因為缺乏效能頻寬容量而失敗。

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
