---
title: Linux 效能工具
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 Linux 效能工具來疑難排解使用 Azure Kubernetes Service (AKS) 時的常見問題，並解決這些問題。
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982043"
---
# <a name="linux-performance-troubleshooting"></a>Linux 效能疑難排解

Linux 機器上的資源耗盡是常見的問題，並可透過各種不同的徵兆進行資訊清單。 本檔提供可協助診斷這類問題之工具的概要說明。

這些工具當中有許多都接受產生滾動輸出的間隔。 此輸出格式通常會讓找出模式變得更容易。 在接受時，範例調用會包含 `[interval]` 。

這些工具當中有許多都有大量歷程記錄和廣泛的設定選項。 此頁面只會提供簡單的調用子集，以反白顯示常見的問題。 標準的資訊來源一律是每個特定工具的參考檔。 該檔會比此處提供的還多。

## <a name="guidance"></a>指引

以您的方法進行系統調查效能問題。 有兩種常見的方法是使用 (使用率、飽和度、錯誤) 和紅色 (率、錯誤、持續時間) 。 RED 通常用於服務的內容中，以進行要求型監視。 使用通常用於監視資源：針對機器中的每個資源，監視使用率、飽和度和錯誤。 任何電腦上四種主要的資源類型為 cpu、記憶體、磁片和網路。 上述任何資源的高使用率、飽和度或錯誤率，都表示系統可能發生問題。 當有問題時，請調查根本原因：為什麼磁片 IO 延遲很高？ 磁片或虛擬機器 SKU 是否進行節流？ 哪些進程會寫入至裝置，以及哪些檔案？

診斷這些常見問題和指標的一些範例：
- IOPS 節流：使用 iostat 測量每個裝置的 IOPS。 請確定沒有任何個別磁片超過其限制，而且所有磁片的總和小於虛擬機器的限制。
- 頻寬節流設定：使用 iostat 作為 IOPS，但測量讀取/寫入輸送量。 請確定每個裝置和匯總輸送量低於頻寬限制。
- SNAT 耗盡：這可能會以 SAR 中的高使用中 (輸出) 連接資訊清單。 
- 封包遺失：這可透過 TCP 重新傳輸計數（相對於傳送/接收的計數）來測量。 `sar`和都 `netstat` 可以顯示這項資訊。

## <a name="general"></a>一般

這些是一般用途的工具，涵蓋基本的系統資訊。 它們是更好的起點，可供進一步調查。

### <a name="uptime"></a>99.99

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

執行時間會提供系統執行時間和1、5和15分鐘的負載平均值。 這些負載平均大約對應到執行工作的執行緒或等候不間斷的工作完成。 絕對這些數位可能很難解讀，但經過一段時間後，它們可以告訴我們有用的資訊：

- 平均每1分鐘 > 5 分鐘平均表示負載增加。
- 平均每1分鐘 < 5 分鐘平均表示負載正在減少。

執行時間也會照亮無法使用資訊的原因：問題可能已自行解決，或在使用者存取電腦之前重新開機。

載入的平均數目高於可用的 CPU 執行緒數目，可能表示指定的工作負載發生效能問題。

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg 傾印核心緩衝區。 OOMKill 之類的事件會將專案新增至核心緩衝區。 在 dmesg 記錄中尋找 OOMKill 或其他資源耗盡的訊息，是問題的強大指標。

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

`top` 提供目前系統狀態的廣泛總覽。 標頭會提供一些有用的匯總資訊：

- 工作狀態：正在執行、睡眠中、已停止。
- CPU 使用率，在此案例中大多顯示閒置時間。
- 總計、可用和已使用的系統記憶體。

`top` 可能會錯過存留期較短的進程;替代專案 `htop` 和會 `atop` 提供類似的介面，同時修正其中一些缺點。

## <a name="cpu"></a>CPU

這些工具提供 CPU 使用率資訊。 這特別適用于輪流輸出，其中的模式變得很容易找出。

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

`mpstat` 將類似的 CPU 資訊列印到頂端，但依 CPU 執行緒細分。 一次看到所有核心可能有助於偵測高不平衡的 CPU 使用率，例如，當單一執行緒應用程式在100% 使用率使用一個核心時。 當您在系統中的所有 Cpu 上進行匯總時，可能會更難以找出此問題。

### <a name="vmstat"></a>Vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` 提供類似的資訊 `mpstat` 以及 `top` 列舉等候 cpu 的進程數目 (r 資料行) 、記憶體統計資料，以及每個工作狀態所花費的 cpu 時間百分比。

## <a name="memory"></a>記憶體

記憶體是很重要的幸好，可讓您輕鬆追蹤資源。某些工具可以報告 CPU 和記憶體（例如） `vmstat` 。 但是像這樣 `free` 的工具可能仍適用于快速的偵錯工具。

### <a name="free"></a>free

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` 提供有關記憶體總計以及已使用和可用記憶體的基本資訊。 `vmstat` 即使是基本記憶體分析，也可能更有用，因為它可以提供輪流輸出。

## <a name="disk"></a>磁碟

這些工具會測量磁片 IOPS、等候佇列和輸送量總計。 

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

`iostat` 提供磁片使用量的深入見解。 此調用會傳遞 `-x` 擴充的統計資料， `-y` 以在開機後略過初始輸出列印系統平均，並 `1 1` 指定我們想要1秒的間隔，在一個輸出區塊之後結束。 

`iostat` 公開許多有用的統計資料：

- `r/s` 和 `w/s` 是每秒讀取數和每秒寫入數。 這些值的總和是 IOPS。
- `rkB/s` 和 `wkB/s` 是每秒讀取/寫入的 kb。 這些值的總和是輸送量。
- `await` 這是佇列要求的平均 iowait 時間（以毫秒為單位）。
- `avgqu-sz` 這是所提供間隔的平均佇列大小。

在 Azure VM 上：

- `r/s` `w/s` 個別區塊裝置的和總和可能不會超過該磁片的 SKU 限制。
- `rkB/s` `wkB/s` 個別區塊裝置的和總和可能不會超過該磁片的 SKU 限制
- `r/s` `w/s` 所有區塊裝置的和總和可能不會超過 VM SKU 的限制。
- `rkB/s`所有區塊裝置的和 ' wkB/s 總和可能不會超過 VM SKU 的限制。

請注意，OS 磁片會計算為最小 SKU 的受控磁片，其容量相對應。 例如，1024GB 作業系統磁片對應至 P30 磁片。 暫時作業系統磁片和暫存磁片沒有個別的磁片限制;它們只受到完整 VM 限制的限制。

Await 或 avgqu-sz 的非零值也是 IO 爭用的良好指標。

## <a name="network"></a>網路

這些工具會測量網路統計資料，例如輸送量、傳輸失敗和使用率。 更深入的分析可以針對擁塞和捨棄的封包公開更細緻的 TCP 統計資料。

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

`sar` 是一種功能強大的工具，可用於進行廣泛的分析。 雖然此範例使用它的能力來測量網路統計資料，但它也相當強大，可測量 CPU 和記憶體耗用量。 此範例會 `sar` 使用 `-n` 旗標來叫用，以指定 `DEV` (網路裝置) 關鍵字，並依裝置顯示網路輸送量。

- 和的總和 `rxKb/s` `txKb/s` 是指定裝置的總輸送量。 當此值超過所布建之 Azure NIC 的限制時，電腦上的工作負載將會遇到網路延遲的增加。
- `%ifutil` 測量指定裝置的使用率。 因為這個值接近100%，所以工作負載會遇到網路延遲的增加。

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

的這個調用會 `sar` 使用 `TCP,ETCP` 關鍵字來檢查 TCP 連接。 最後一個資料列的第三個數據行 "retrans" 是每秒 TCP 重新傳輸的次數。 此欄位的最高值表示不可靠的網路連線。 在第一個和第三個數據列中，「作用中」表示來自本機裝置的連線，而「遠端」表示連入連線。  Azure 上常見的問題是 SNAT 埠耗盡， `sar` 可協助偵測。 SNAT 埠耗盡會以高的「作用中」值的形式出現，因為此問題是因為輸出的高比率、本機起始的 TCP 連接。

如同 `sar` 間隔時間，它會列印輪流輸出，然後列印輸出的最後幾個資料列，其中包含調用的平均結果。

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

`netstat` 可以 introspect 各種不同的網路統計資料，這裡以摘要輸出來叫用。 視問題而定，這裡有許多有用的欄位。 TCP 區段中有一個有用的欄位是「失敗的連接嘗試」。 這可能表示發生 SNAT 埠耗盡或其他發出輸出連接的問題。 高比率的重新傳輸區段 (也在 TCP 區段下) 可能表示封包傳遞的問題。 
