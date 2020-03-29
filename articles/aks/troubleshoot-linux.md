---
title: Linux 性能工具
titleSuffix: Azure Kubernetes Service
description: 了解在使用 Azure Kubernetes Service (AKS) 時，如何針對常見問題進行疑難排解並加以解決
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925601"
---
# <a name="linux-performance-troubleshooting"></a>Linux 性能故障排除

Linux 機器上的資源耗盡是一個常見的問題，可以通過各種症狀表現出來。 本文檔提供了可用於説明診斷此類問題的工具的高級概述。

其中許多工具接受生成滾動輸出的間隔。 這種輸出格式通常使發現模式更容易。 如果接受，示例調用將包括`[interval]`。

其中許多工具具有廣泛的歷史記錄和廣泛的配置選項組。 此頁面僅提供一個簡單的調用子集，以突出顯示常見問題。 規範的資訊源始終是每個特定工具的參考文檔。 該檔將比這裡提供的檔更徹底。

## <a name="guidance"></a>指引

系統化調查性能問題的方法。 兩種常見方法是 USE（利用率、飽和度、錯誤）和紅色（速率、錯誤、持續時間）。 RED 通常用於基於請求的監視的服務上下文。 USE 通常用於監視資源：對於電腦中的每個資源，監視利用率、飽和度和錯誤。 任何電腦上的四種主要資源是 cpu、記憶體、磁片和網路。 這些資源的高利用率、飽和度或錯誤率表示系統可能存在問題。 當出現問題時，請調查根本原因：為什麼磁片 IO 延遲很高？ 磁片或虛擬機器是否限制？ 哪些進程正在寫入設備以及哪些檔？

一些常見問題和指標的例子來診斷它們：
- IOPS 限制：使用 iostat 測量每個設備 IOPS。 確保沒有單個磁片超過其限制，並且所有磁片的總和都小於虛擬機器的限制。
- 頻寬限制：使用 iostat 作為 IOPS，但測量讀/寫輸送量。 確保每台設備和聚合輸送量都低於頻寬限制。
- SNAT 耗盡：這可以表現為 SAR 中的高活動（出站）連接。 
- 資料包丟失：這可以通過代理通過 TCP 重新傳輸計數相對於已發送/接收計數進行測量。 和`sar``netstat`都可以顯示此資訊。

## <a name="general"></a>一般

這些工具是通用的，涵蓋基本系統資訊。 它們是進一步調查的良好起點。

### <a name="uptime"></a>超時時間

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime 提供系統 UPtime 和 1、5 和 15 分鐘負載平均值。 這些負載平均值大致對應于執行工作的執行緒或等待不間斷工作的執行緒完成。 在絕對這些數位可能難以解釋，但隨著時間的推移，他們可以告訴我們有用的資訊：

- 1 分鐘平均> 5 分鐘平均值意味著負載正在增加。
- 1 分鐘平均< 5 分鐘平均值意味著負載正在減少。

uptime 還可以闡明資訊不可用的原因：問題可能自行解決，或者在使用者訪問電腦之前重新開機。

負載平均值高於可用 CPU 執行緒數可能表示給定工作負載存在性能問題。

### <a name="dmesg"></a>德梅斯格

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg 轉儲內核緩衝區。 像 OOMKill 這樣的事件向內核緩衝區添加一個條目。 在 dmesg 日誌中查找 OOMKill 或其他資源耗盡消息是問題的有力指標。

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`提供了當前系統狀態的廣泛概述。 標頭提供一些有用的聚合資訊：

- 任務狀態：運行、睡眠、停止。
- CPU 利用率，在這種情況下，主要顯示閒置時間。
- 總計、空閒和已使用的系統記憶體。

`top`可能錯過短期過程;替代方案喜歡`htop`並提供`atop`類似的介面，同時修復其中一些缺點。

## <a name="cpu"></a>CPU

這些工具提供 CPU 利用率資訊。 這對於滾動輸出特別有用，因為滾動輸出模式變得容易發現。

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`將類似的 CPU 資訊列印到頂部，但按 CPU 執行緒細分。 同時查看所有內核可用於檢測高度不平衡的 CPU 使用率，例如，當單一執行緒應用程式以 100% 的利用率使用一個內核時。 聚合到系統中的所有 CPU 時，此問題可能更難發現。

### <a name="vmstat"></a>Vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`提供類似的資訊和`mpstat``top`，枚舉等待 CPU （r 列）、記憶體統計資訊和在每個工作狀態中花費的 CPU 時間百分比的進程數。

## <a name="memory"></a>記憶體

記憶是一個非常重要的，而且幸好是容易跟蹤的資源。某些工具可以同時報告 CPU 和記憶體`vmstat`，例如 。 但是，像`free`這樣的工具對於快速調試可能仍然很有用。

### <a name="free"></a>任意

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`提供有關總記憶體以及使用和空閒記憶體的基本資訊。 `vmstat`由於其提供滾動輸出的能力，即使對於基本記憶體分析也更有用。

## <a name="disk"></a>磁碟

這些工具測量磁片 IOPS、等待佇列和總輸送量。 

### <a name="iostat"></a>Iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`深入瞭解磁片利用率。 此調用通過`-x`擴展統計資訊，`-y`跳過自啟動以來的初始輸出列印系統平均值，並`1 1`指定我們想要的 1 秒間隔，在一個輸出塊後結束。 

`iostat`公開了許多有用的統計資訊：

- `r/s`每秒`w/s`讀取和每秒寫入。 這些值的總和是 IOPS。
- `rkB/s`並且`wkB/s`是每秒讀/寫入的千位元組。 這些值的總和是輸送量。
- `await`是排隊請求的平均 iowait 時間（以毫秒為單位）。
- `avgqu-sz`是提供間隔內的平均佇列大小。

在 Azure VM 上：

- 單個塊設備`r/s`的總和`w/s`和不能超過該磁片的 SKU 限制。
- 單個塊設備`rkB/s`的總和`wkB/s`和不能超過該磁片的 SKU 限制
- 和`w/s`的所有`r/s`塊設備的總和和不能超過 VM SKU 的限制。
- 所有塊設備的`rkB/s`和 'wkB/s 的總和不得超過 VM SKU 的限制。

請注意，OS 磁片計為與其容量對應的最小 SKU 的託管磁片。 例如，1024GB OS 磁片對應于 P30 磁片。 臨時 OS 磁片和臨時磁片沒有單獨的磁片限制;它們僅受完整的 VM 限制的限制。

await 或 avgqu-sz 的非零值也是 IO 爭用的良好指標。

## <a name="network"></a>網路

這些工具測量網路統計資訊，如輸送量、傳輸故障和利用率。 更深入的分析可以公開有關擁塞和丟棄資料包的細細微性 TCP 統計資訊。

### <a name="sar"></a>特區

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`是進行廣泛分析的強大工具。 雖然此示例使用其測量網路統計資訊的能力，但它在測量 CPU 和記憶體消耗方面同樣強大。 此示例`sar`使用`-n`標誌調用以指定`DEV`（網路設備）關鍵字，按設備顯示網路輸送量。

- 和`txKb/s`的總`rxKb/s`輸送量是給定設備的總輸送量。 當此值超過預配 Azure NIC 的限制時，電腦上的工作負荷將體驗到更高的網路延遲。
- `%ifutil`測量給定設備的利用率。 當此值接近 100% 時，工作負載將經歷更高的網路延遲。

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

此調用`sar`使用`TCP,ETCP`關鍵字檢查 TCP 連接。 最後一行的第三列"retrans"是每秒重新傳輸 TCP 的數量。 此欄位的較高值表示網路連接不可靠。 在第一行和第三行中，"活動"表示來自本地設備的連接，而"遠端"表示傳入連接。  Azure 上的一個常見問題是 SNAT 埠`sar`耗盡，這可以説明檢測。 SNAT 埠耗盡將表現為高"活動"值，因為問題是由於出站率高、本地啟動的 TCP 連接。

按`sar`間隔，它列印滾動輸出，然後列印包含調用的平均結果的最終輸出行。

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`可以介紹各種網路統計資訊，此處調用摘要輸出。 根據問題，這裡有許多有用的欄位。 TCP 部分中的一個有用欄位是"連接嘗試失敗"。 這可能表示 SNAT 埠耗盡或進行出站連接的其他問題。 高重新傳輸段率（也屬於 TCP 部分）可能表示資料包傳遞出現問題。 
