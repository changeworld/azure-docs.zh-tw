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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73935899"
---
**上次檔更新**：12年11月 2019 10:00 AM PST。

揭露的[新 CPU 漏洞類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) (也稱為推測性執行旁路攻擊)，讓許多客戶不知所措。  

Microsoft 已在我們所有的雲端服務上部署防護功能。 執行 Azure 的基礎結構，以及將客戶的工作負載互相隔離這方面已受到保護。 這表示，使用相同基礎結構的潛在攻擊者不能利用這些漏洞來攻擊您的應用程式。

Azure 會盡可能使用[記憶體保留維護](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot)，將客戶的影響降到最低，以及避免重新開機。 Azure 會在對主機進行全系統更新時，繼續使用這些方法，並且保護我們的客戶。

如何將安全性整合至 Azure 各個層面的詳細資訊位於 [Azure 安全性文件](https://docs.microsoft.com/azure/security/)網站。 

> [!NOTE] 
> 自從這份文件首次發行以來，已揭露此弱點類別的多個變體。 Microsoft 持續致力於保護客戶和提供指引。 我們會持續發行進一步的修正，而此頁面也會隨之更新。 
> 
> 在2019年11月12日， [intel 發佈](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort)了 Intel®交易式同步處理延伸模組（INTEL® TSX）交易非同步中止（TAA）弱點的技術諮詢，其已指派[CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)。 此弱點會影響 Intel® Core® 處理器和 Intel® Xeon® 處理器。  Microsoft Azure 已發行作業系統更新，並部署新的微碼，這是由 Intel 提供，可讓我們的客戶抵禦這些新的弱點。   Azure 會與 Intel 密切合作，在其正式發行之前，先測試並驗證新的微碼。 
>
> 在**其 VM 中執行未受信任程式碼的客戶**，必須採取動作來防範這些弱點，方法是閱讀下列內容，以取得所有理論式執行端通道弱點的其他指引（MICROSOFT 諮詢進階[180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)、 [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)和[190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)）。
>
> 其他客戶應從深層防禦的觀點來評估這些弱點，並考慮其所選設定的安全性和效能含意。
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>讓您的作業系統保持在最新狀態

雖然無需更新作業系統，就能讓 Azure 上執行的應用程式與其他 Azure 客戶隔離，但最佳做法是讓軟體維持在最新狀態。 Windows 最新的安全性彙總套件包含數個推測性執行旁路弱點的防護功能。 同樣地，Linux 散發套件已發行多個更新來解決這些弱點。 以下是我們建議的動作，以更新您的作業系統：

| 供應項目 | 建議的動作  |
|----------|---------------------|
| Azure 雲端服務  | 啟用[自動更新](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)，或確定您正在執行最新的虛擬作業系統。 |
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

如果您執行的是不受信任的程式碼，您可以在 VM 或雲端服務內啟用其他安全性功能。 平行，確保您的作業系統處於最新狀態，以啟用 VM 或雲端服務內的安全性功能

### <a name="windows"></a>Windows 

目標作業系統必須是最新版本，才能啟用這些額外的安全性功能。 雖然許多推測性執行旁路攻擊的防護功能會預設為啟用，但此處描述的其他功能必須手動啟用，並可能會對效能造成影響。 


**步驟1：停用 VM 上的超執行緒**-在超執行緒 vm 上執行不受信任程式碼的客戶，必須停用超執行緒，或移至非超執行緒 vm 大小。 如需超執行緒 VM 大小的清單，請參閱[此](https://docs.microsoft.com/azure/virtual-machines/windows/acu)檔（VCPU 與 Core 的比率為2:1）。 若要檢查您的 VM 是否已啟用超執行緒，請從 VM 內使用 Windows 命令列參閱下列腳本。

鍵入`wmic`以輸入互動式介面。 然後輸入下列資訊，以查看 VM 上的實體和邏輯處理器數量。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

如果邏輯處理器的數目大於實體處理器（核心），則會啟用超執行緒。  如果您正在執行超執行緒 VM，請[聯絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以停用超執行緒。  停用超執行緒之後，**支援將需要完整的 VM 重新開機**。 請參閱[核心計數](#core-count)，以瞭解 VM 核心計數降低的原因。


**步驟 2**：平行處理步驟1，遵循[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)中的指示，使用[SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 模組來確認是否已啟用保護。

> [!NOTE]
> 如果您先前已下載此模組，則必須安裝最新版本。
>


PowerShell 腳本的輸出應該具有下列值，以根據下列弱點來驗證已啟用的保護：

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

如果輸出顯示`MDS mitigation is enabled: False`，請[聯絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以取得可用的緩和選項。



**步驟 3**：若要啟用核心虛擬位址陰影（KVAS）和分支目標插入（BTI） OS 支援，請遵循[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)中的指示，使用`Session Manager`登錄機碼來啟用保護。 需要重新開機。


**步驟 4**：針對使用「[嵌套虛擬化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)」的部署（僅限 D3 和 E3）：這些指示適用于您用來做為 hyper-v 主機的 VM 中。

1.  遵循[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)中的指示，使用`MinVmVersionForCpuBasedMitigations`登錄機碼來啟用保護。
2.  依照此處的指示，將`Core` [程式管理器[here](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)排程器] 類型設定為。


### <a name="linux"></a>Linux

<a name="linux"></a>若要在內部啟用一組額外安全性功能，目標作業系統必須完全是最新版本。 有些防護功能會預設為啟用。 下節會描述預設為關閉的功能，以及 (或) 相依於硬體支援 (微碼) 的功能。 啟用這些功能可能會對效能造成影響。 請參閱作業系統提供者的文件，以取得進一步的指示


**步驟1：停用 vm 上的超執行緒**-在超執行緒 vm 上執行不受信任程式碼的客戶，必須停用超執行緒，或移至非超執行緒 vm。  如需超執行緒 VM 大小的清單，請參閱[此](https://docs.microsoft.com/azure/virtual-machines/linux/acu)檔（VCPU 與 Core 的比率為2:1）。 若要檢查您是否正在執行超執行緒 VM，請在 Linux `lscpu` VM 中執行命令。 

如果`Thread(s) per core = 2`為，則已啟用超執行緒。 

如果`Thread(s) per core = 1`為，則會停用超執行緒。 

 
已啟用超執行緒之 VM 的範例輸出： 

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

如果您正在執行超執行緒 VM，請[聯絡 Azure 支援](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)以停用超執行緒。  停用超執行緒之後，**支援將需要完整的 VM 重新開機**。 請參閱[核心計數](#core-count)，以瞭解 VM 核心計數降低的原因。



**步驟 2**：若要減輕下列任何推測性執行端通道弱點的風險，請參閱作業系統提供者的檔：   
 
- [Redhat 和 CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>核心計數

建立超執行緒 VM 時，Azure 會為每個核心配置2個執行緒-這些是稱為個 vcpu。 停用超執行緒時，Azure 會移除執行緒，並表面化單一執行緒核心（實體核心）。 VCPU 與 CPU 的比率為2:1，因此一旦停用超執行緒，VM 中的 CPU 計數就會顯示為已減少一半。 例如，D8_v3 VM 是在8個 vcpu （每個核心2個執行緒 x 4 核心）上執行的超執行緒 VM。  停用超執行緒時，Cpu 會降至4個實體核心，每個核心1個執行緒。 

## <a name="next-steps"></a>後續步驟

本文提供適用于下列會影響許多新式處理器之理論式執行端通道攻擊的指引：

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002)：
- CVE-2017-5715-分支目標插入（BTI）  
- CVE-2017-5754-核心分頁表隔離（KPTI）
- CVE-2018-3639-推測式存放區略過（KPTI） 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows 核心資訊– Spectre variant 1 的變異
 
[L1 終端機錯誤（L1TF）](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018)：
- CVE-2018-3615-Intel 軟體防護延伸模組（Intel SGX）
- CVE-2018-3620-作業系統（OS）和系統管理模式（SMM）
- CVE-2018-3646 –影響 Virtual Machine Manager （VMM）

[Microarchitectural 資料取樣](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013)： 
- CVE-2019-11091-Microarchitectural 資料取樣 Uncacheable 記憶體（MDSUM）
- CVE-2018-12126-Microarchitectural 存放區緩衝區資料取樣（MSBDS）
- CVE-2018-12127-Microarchitectural 載入埠資料取樣（MLPDS）
- CVE-2018-12130-Microarchitectural 填滿緩衝區資料取樣（MFBDS）

交易式同步處理延伸模組（Intel® TSX）交易非同步中止：  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX 交易非同步中止（TAA）








