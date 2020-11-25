---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 358e92d8e43473c168e24be9f4af504e6ffcc37a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025945"
---
*準備快取*  
設有「唯讀」主機快取的磁碟能夠提供高於磁碟限制的 IOPS。 若要從主機快取獲得這種最高的讀取效能，您必須先準備此磁碟的快取。 如此可確保效能評定工具在 CacheReads 磁碟區上推動的讀取 IO 實際上是命中快取，而非直接觸及磁碟。 快取命中會讓已啟用快取的單一磁碟產生更多 IOPS。

> [!IMPORTANT]
> 每次重新啟動 VM 時，您必須在執行效能評定之前將快取準備好。

## <a name="iometer"></a>Iometer

[下載 Iometer 工具](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) 。

### <a name="test-file"></a>測試檔案

在您執行效能評定測試的磁碟區上，Iometer 會使用此磁碟區上儲存的一個測試檔案。 它會對此測試檔案推動讀取和寫入，以測量磁碟 IOPS 和輸送量。 如果您沒有提供此測試檔案，Iometer 會建立測試檔案。 在 CacheReads 和 NoCacheWrites 磁碟區上，建立一個稱為 iobw.tst 的 200 GB 測試檔案。

### <a name="access-specifications"></a>存取規格

規格 (要求 IO 大小、% 讀取/寫入、% 隨機/循序) 是在 Iometer 中可使用 [存取規格] 索引標籤設定。 為以下說明的每個案例建立存取規格。 建立存取規格並「儲存」為適當的名稱，例如 RandomWrites\_8K、RandomReads\_8K。 執行測試案例時選取對應的規格。

以下顯示最大寫入 IOPS 案例的存取規格範例，  
    ![最大寫入 IOPS 存取規格範例](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>最大 IOPS 測試規格

 為了示範最大 IOPS，請使用較小的要求大小。 使用 8K 要求大小，並建立隨機寫入和讀取的規格。

| 存取規格 | 要求大小 | 隨機 % | 讀取 % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>最大輸送量測試規格

 為了示範最大輸送量，請使用較大的要求大小。 使用 64 K 要求大小，並建立隨機寫入和讀取的規格。

| 存取規格 | 要求大小 | 隨機 % | 讀取 % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>執行 Iometer 測試

 執行下列步驟將準備快取

1. 使用如下所示的值建立兩個存取規格

   | 名稱 | 要求大小 | 隨機 % | 讀取 % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. 執行 Iometer 測試，使用下列參數初始化快取磁碟。 對目標磁碟區使用三個背景工作執行緒，佇列深度為 128。 在 [測試安裝程式] 索引標籤上，將測試的 [執行階段] 期間設為 2 小時。

   | 狀況 | 目標磁碟區 | 名稱 | 持續時間 |
   | --- | --- | --- | --- |
   | 初始化快取磁碟 |CacheReads |RandomWrites\_1MB |2 小時 |
1. 執行 Iometer 測試，使用下列參數來準備快取。 對目標磁碟區使用三個背景工作執行緒，佇列深度為 128。 在 [測試安裝程式] 索引標籤上，將測試的 [執行階段] 期間設為 2 小時。

   | 狀況 | 目標磁碟區 | 名稱 | 持續時間 |
   | --- | --- | --- | --- |
   | 準備快取磁碟 |CacheReads |RandomReads\_1MB |2 小時 |

準備快取磁碟之後，繼續執行下列的測試案例。 若要執行 Iometer 測試，請對 **每個** 目標磁碟區使用至少三個背景工作角色執行緒。 針對每個背景工作執行緒，請選取目標磁碟區，設定佇列深度，然後選取其中一個已儲存的測試規格，如下表所示，以執行對應的測試案例。 表格也顯示執行這些測試時，預期的 IOPS 和輸送量結果。 在所有案例中，都使用較小的 IO 大小 8 KB 和較高的佇列深度 128。

| 測試案例 | 目標磁碟區 | 名稱 | 結果 |
| --- | --- | --- | --- |
| 最大 讀取 IOPS |CacheReads |RandomWrites\_8K |50,000 IOPS |
| 最大 寫入 IOPS |NoCacheWrites |RandomReads\_8K |64,000 IOPS |
| 最大 結合的 IOPS |CacheReads |RandomWrites\_8K |100,000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| 最大 讀取 MB/秒 |CacheReads |RandomWrites\_64K |524 MB/秒 |
| 最大 寫入 MB/秒 |NoCacheWrites |RandomReads\_64K |524 MB/秒 |
| 結合的 MB/秒 |CacheReads |RandomWrites\_64K |1000 MB/秒 |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

針對結合的 IOPS 和輸送量案例，以下是 Iometer 測試結果的螢幕擷取畫面。

### <a name="combined-reads-and-writes-maximum-iops"></a>結合的讀取和寫入最大 IOPS

![結合的讀取和寫入最大 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>結合的讀取和寫入最大輸送量

![結合的讀取和寫入最大輸送量](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO 是 Linux VM 上用於儲存體效能評定的一項常用工具。 它可以靈活地選取不同的 IO 大小、循序或隨機讀取和寫入。 它會繁衍背景工作執行緒或處理程序來執行指定的 I/O 作業。 您可以使用工作檔案，指定每個背景工作執行緒必須執行的 I/O 作業類型。 我們已經為下面範例所示的每個案例建立一個工作檔案。 您可以變更這些工作檔案中的規格，對進階儲存體上執行的不同工作負載進行效能評定。 在範例中，我們使用執行 **Ubuntu** 的標準 DS 14 VM。 請使用 效能評定 一節的開頭所述的相同設定，並於執行效能評定測試之前先備妥快取。

開始進行之前，請先在虛擬機器上 [下載 FIO](https://github.com/axboe/fio) 並安裝。

對 Ubuntu 執行下列命令，

```
apt-get install fio
```

我們會在磁碟上使用四個背景工作執行緒來推動讀取作業，並使用四個背景工作執行緒來推動讀取作業。 「寫入」背景工作角色會在 "nocache" 磁碟區上推動流量，此磁碟區有 10 個其快取設為「無」的磁碟。 「讀取」背景工作角色會在 "readcache" 磁碟區上推動流量，此磁碟區有一個其快取設為「唯讀」的磁碟。

### <a name="maximum-write-iops"></a>最大寫入 IOPS

 使用下列規格建立作業檔案，以產生最大寫入 IOPS。 將它命名為 "fiowrite.ini"。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

請注意以下與先前幾節所述的設計指導方針一致的重要事項。 這些規格對於達到最大 IOPS 很重要，  

* 較高的佇列深度 256。  
* 較小的區塊大小 8 KB。  
* 執行隨機寫入的多個執行緒。

執行下列命令，開始執行 FIO 測試 30 秒，  

```
sudo fio --runtime 30 fiowrite.ini
```

當測試執行時，您能夠看到 VM 和高階磁碟產生的寫入 IOPS 數目。 如下列範例所示，DS14 VM 產生最大寫入 IOPS 限制 50,000 IOPS。  
    ![傳遞的寫入 IOP VM 和 Premium 磁片數目。](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>最大讀取 IOPS

 使用下列規格建立作業檔案，以產生最大讀取 IOPS。 將它命名為 "fioread.ini"。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

請注意以下與先前幾節所述的設計指導方針一致的重要事項。 這些規格對於達到最大 IOPS 很重要，

* 較高的佇列深度 256。  
* 較小的區塊大小 8 KB。  
* 執行隨機寫入的多個執行緒。

執行下列命令，開始執行 FIO 測試 30 秒，

```
sudo fio --runtime 30 fioread.ini
```

當測試執行時，您能夠看到 VM 和高階磁碟產生的讀取 IOPS 數目。 如下列範例所示，DS14 VM 產生超過 64,000 的讀取 IOPS。 這是磁碟和快取效能的組合。  
    ![提供寫入 IOPS VM 和 Premium 磁片數目的螢幕擷取畫面。](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>最大讀取和寫入 IOPS

 使用下列規格建立作業檔案，以產生最大讀取和寫入 IOPS。 將它命名為 "fioreadwrite.ini"。

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

請注意以下與先前幾節所述的設計指導方針一致的重要事項。 這些規格對於達到最大 IOPS 很重要，

* 較高的佇列深度 128。  
* 較小的區塊大小 4 KB。  
* 執行隨機讀取和寫入的多個執行緒。

執行下列命令，開始執行 FIO 測試 30 秒，

```
sudo fio --runtime 30 fioreadwrite.ini
```

當測試執行時，您能夠看到 VM 和高階磁碟產生的結合讀取和寫入 IOPS 數目。 如下列範例所示，DS14 VM 產生超過 100,000 的結合讀取和寫入 IOPS。 這是磁碟和快取效能的組合。  
    ![結合的讀取和寫入 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>結合的最大輸送量

 若要獲得最大的結合讀取和寫入輸送量，請使用較大的區塊大小和較大佇列深度，並搭配執行讀取和寫入的多個執行緒。 您可以使用 64 KB 的區塊大小和 128 的佇列深度。
