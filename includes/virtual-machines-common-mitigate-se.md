---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73935899"
---
**最後文檔更新**： 2019年11月12日上午10：00 PST.

揭露的[新 CPU 漏洞類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) (也稱為推測性執行旁路攻擊)，讓許多客戶不知所措。  

Microsoft 已在我們所有的雲端服務上部署防護功能。 執行 Azure 的基礎結構，以及將客戶的工作負載互相隔離這方面已受到保護。 這表示，使用相同基礎結構的潛在攻擊者不能利用這些漏洞來攻擊您的應用程式。

Azure 會盡可能使用[記憶體保留維護](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot)，將客戶的影響降到最低，以及避免重新開機。 Azure 會在對主機進行全系統更新時，繼續使用這些方法，並且保護我們的客戶。

如何將安全性整合至 Azure 各個層面的詳細資訊位於 [Azure 安全性文件](https://docs.microsoft.com/azure/security/)網站。 

> [!NOTE] 
> 自從這份文件首次發行以來，已揭露此弱點類別的多個變體。 Microsoft 持續致力於保護客戶和提供指引。 我們會持續發行進一步的修正，而此頁面也會隨之更新。 
> 
> 2019 年 11 月 12 日，[英特爾發佈了](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort)有關英特爾®事務同步擴展 （英特爾® TSX） 事務非同步中止 （TAA） 漏洞的技術諮詢，該漏洞被分配為[CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)。 此弱點會影響 Intel® Core® 處理器和 Intel® Xeon® 處理器。  Microsoft Azure 發佈了作業系統更新，並在整個車隊中部署了新的微代碼（英特爾提供微代碼）以保護我們的客戶免受這些新漏洞的影響。   Azure 正在與英特爾密切合作，在平臺上正式發佈新微碼之前對其進行測試和驗證。 
>
> **在其 VM 中運行不受信任的代碼的客戶**需要通過閱讀下文來保護這些漏洞，以獲取有關所有投機性執行側通道漏洞的其他指導（Microsoft AdvisorS ADV [180002、180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)和[190013）。](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013) [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)
>
> 其他客戶應從"深度防禦"的角度評估這些漏洞，並考慮其所選配置對安全性和性能的影響。
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>使作業系統保持最新

雖然無需更新作業系統，就能讓 Azure 上執行的應用程式與其他 Azure 客戶隔離，但最佳做法是讓軟體維持在最新狀態。 Windows 最新的安全性彙總套件包含數個推測性執行旁路弱點的防護功能。 同樣地，Linux 散發套件已發行多個更新來解決這些弱點。 以下是我們用於更新作業系統的建議操作：

| 供應項目 | 建議的動作  |
|----------|---------------------|
| Azure 雲端服務  | 啟用[自動更新](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)或確保您正在運行最新的客體作業系統。 |
| Azure Linux 虛擬機器 | 安裝來自作業系統提供者的更新。 如需詳細資訊，請參閱本文後面的 [Linux](#linux)。 |
| Azure Windows 虛擬機器  | 安裝最新的安全性彙總套件。
| 其他 Azure PaaS 服務 | 使用這些服務的客戶無需採取任何行動。 Azure 會自動將您的作業系統版本保持在最新狀態。 |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>如果您執行的是不受信任的程式碼的其他指引 

如果客戶允許不受信任的使用者執行任意程式碼，則可以建議他們在 Azure 虛擬機器或雲端服務內實作一些額外的安全性功能。 這些功能可抵禦多個推測性執行弱點描述的程序內洩漏向量。

建議使用額外安全性功能的案例：

- 您允許不受信任的程式碼在 VM 內執行。  
    - 例如，您允許其中一個客戶上傳二進位檔或指令碼，然後在您的應用程式內執行。** 
- 您允許不受信任的使用者以低權限帳戶登入 VM。   
    - 例如，您允許低權限的使用者透過遠端桌面或 SSH 登入您其中一個 VM。**  
- 允許不受信任的使用者存取透過巢狀虛擬化實作的虛擬機器。  
    - 例如，您控制 HYPER-V 主機，但將 VM 配置給不受信任的使用者。** 

如果客戶實作的案例並未包含不受信任的程式碼，則無須啟用這些額外的安全性功能。 

## <a name="enabling-additional-security"></a>啟用額外的安全性 

如果運行不受信任的代碼，則可以在 VM 或雲服務中啟用其他安全功能。 並行，確保您的作業系統是最新的，以便在 VM 或雲服務中啟用安全功能

### <a name="windows"></a>Windows 

目標作業系統必須是最新版本，才能啟用這些額外的安全性功能。 雖然許多推測性執行旁路攻擊的防護功能會預設為啟用，但此處描述的其他功能必須手動啟用，並可能會對效能造成影響。 


**第 1 步：禁用 VM 上的超執行緒 -** 在超執行緒 VM 上運行不受信任的代碼的客戶將需要禁用超執行緒或移動到非超執行緒 VM 大小。 有關超執行緒 VM 大小清單（vCPU 與 Core 的比率為 2：1），請參閱[此文檔](https://docs.microsoft.com/azure/virtual-machines/windows/acu)。 要檢查 VM 是否啟用了超執行緒，請使用 VM 中的 Windows 命令列參考以下腳本。

鍵入`wmic`以進入互動式介面。 然後鍵入以下內容以查看 VM 上的物理處理器和邏輯處理器的數量。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果邏輯處理器的數量大於物理處理器（內核），則啟用超執行緒。  如果運行超執行緒 VM，[請與 Azure 支援部門聯繫](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超執行緒。  禁用超執行緒後，**支援將需要完全重新開機 VM。** 請參閱[核心計數](#core-count)以瞭解 VM 核心計數減少的原因。


**步驟 2**： 與步驟 1 並行，按照[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)中的說明進行驗證使用[投機控制](https://aka.ms/SpeculationControlPS)PowerShell 模組啟用保護。

> [!NOTE]
> 如果您先前已下載此模組，則必須安裝最新版本。
>


PowerShell 腳本的輸出應具有以下值，以驗證針對這些漏洞的啟用保護：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

如果輸出顯示`MDS mitigation is enabled: False`，[請與 Azure 支援部門聯繫](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)，瞭解可用的緩解選項。



**步驟 3**： 要啟用內核虛擬位址陰影 （KVAS） 和分支目標注入 （BTI） 作業系統支援，請按照[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) `Session Manager`中的說明啟用使用登錄機碼的保護。 需要重新開機。


**步驟 4：** 對於使用[嵌套虛擬化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)的部署（僅限 D3 和 E3）：這些說明適用于您用作 Hyper-V 主機的 VM 內部。

1.  按照[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)中的說明啟用使用登錄機碼的保護`MinVmVersionForCpuBasedMitigations`。
2.  按照[此處](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)的說明將虛擬機器管理程式計畫程式`Core`類型設置為 。


### <a name="linux"></a>Linux

<a name="linux"></a>若要在內部啟用一組額外安全性功能，目標作業系統必須完全是最新版本。 有些防護功能會預設為啟用。 下節會描述預設為關閉的功能，以及 (或) 相依於硬體支援 (微碼) 的功能。 啟用這些功能可能會對效能造成影響。 請參閱作業系統提供者的文件，以取得進一步的指示


**第 1 步：禁用 VM 上的超執行緒 -** 在超執行緒 VM 上運行不受信任的代碼的客戶將需要禁用超執行緒或移動到非超執行緒 VM。  有關超執行緒 VM 大小清單（vCPU 與 Core 的比率為 2：1），請參閱[此文檔](https://docs.microsoft.com/azure/virtual-machines/linux/acu)。 要檢查是否運行超執行緒 VM，請在 Linux VM`lscpu`中運行該命令。 

如果`Thread(s) per core = 2`啟用了 超執行緒，則啟用了超執行緒。 

如果`Thread(s) per core = 1`，則已禁用超執行緒。 

 
啟用超執行緒的 VM 的示例輸出： 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

如果運行超執行緒 VM，[請與 Azure 支援部門聯繫](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以禁用超執行緒。  禁用超執行緒後，**支援將需要完全重新開機 VM。** 請參閱[核心計數](#core-count)以瞭解 VM 核心計數減少的原因。



**步驟 2：** 為了緩解以下任何投機性執行側通道漏洞，請參閱作業系統供應商的文檔：   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [Suse](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>核心計數

創建超執行緒 VM 時，Azure 為每個內核分配 2 個執行緒 - 這些執行緒稱為 vCPU。 禁用超執行緒時，Azure 將刪除線程並曲面單一執行緒內核（物理內核）。 vCPU 與 CPU 的比率為 2：1，因此一旦禁用超執行緒，VM 中的 CPU 計數就會減少一半。 例如，D8_v3 VM 是在 8 個 vCPU 上運行的超執行緒 VM（每個內核 2 個執行緒 x 4 個內核）。  禁用超執行緒時，CPU 將下降到 4 個物理內核，每個內核有 1 個執行緒。 

## <a name="next-steps"></a>後續步驟

本文為影響許多現代處理器的以下推測性執行側通道攻擊提供了指南：

[幽靈融化](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)：
- CVE-2017-5715 - 分支目標注射 （BTI）  
- CVE-2017-5754 - 內核頁面表隔離 （KPTI）
- CVE-2018-3639 = 投機性存儲旁路 （KPTI） 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) • Windows 內核資訊 • Spectre 變體 1 的變體
 
[L1 端子故障 （L1TF）：](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)
- CVE-2018-3615 - 英特爾軟體防護擴展（英特爾 SGX）
- CVE-2018-3620 - 作業系統 （OS） 和系統管理模式 （SMM）
- CVE-2018-3646 – 影響虛擬機器管理器 （VMM）

[微觀建築資料採樣](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013)： 
- CVE-2019-11091 - 微架構資料採樣不可緩存記憶體 （MDSUM）
- CVE-2018-12126 - 微型建築存儲緩衝區資料採樣 （MSBDS）
- CVE-2018-12127 - 微型建築負載埠資料採樣 （MLPDS）
- CVE-2018-12130 - 微型建築填充緩衝區資料採樣 （MFBDS）

事務同步擴展 （英特爾® TSX） 事務非同步中止：  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) = TSX 事務非同步中止 （TAA）








