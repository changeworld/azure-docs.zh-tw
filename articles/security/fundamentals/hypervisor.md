---
title: Azure 車隊上的虛擬程式安全性-Azure 安全性
description: Azure 車隊上的管理程式安全性技術總覽。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696110"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Azure 車隊上的虛擬程式安全性

Azure 基礎程式系統是以 Windows Hyper-v 為基礎。 管理器系統可讓電腦系統管理員指定具有不同位址空間的來賓磁碟分割。 不同的位址空間可讓您載入作業系統和應用程式，以平行方式在電腦的根磁碟分割中執行的 (主機) 作業系統來執行。 主機作業系統 (也稱為特殊許可權根磁碟分割) 可直接存取系統 (儲存控制器、網路 adaptions) 上的所有實體裝置和週邊設備。 主機 OS 可將「虛擬裝置」公開至每個來賓分割區，讓來賓磁碟分割可以共用這些實體裝置的使用。 因此，在來賓磁碟分割中執行的作業系統可存取虛擬化的周邊裝置，這些裝置是由根磁碟分割中執行的虛擬化服務所提供。

Azure 基礎程式建基於下列安全性目標的考慮：

| 目標 | 來源 |
|--|--|
| 隔離 | 安全性原則不會要求 Vm 之間的資訊傳輸。 此條件約束需要 Virtual Machine Manager (VMM) 的功能，以及用來隔離記憶體、裝置、網路和受控資源（例如保存的資料）的硬體。 |
| VMM 完整性 | 為了達到整體系統完整性，會建立並維護個別管理程式元件的完整性。 |
| 平臺完整性 | 管理程式的完整性取決於它所依賴的硬體和軟體的完整性。 雖然基礎程式無法直接控制平臺的完整性，但 Azure 會依賴硬體和固件機制（例如 [Cerberus](project-cerberus.md) 晶片）來保護和偵測基礎平臺完整性。 如果平臺完整性遭到入侵，則無法執行 VMM 與來賓。 |
| 限制存取 | 管理功能僅供透過安全連線連接的授權系統管理員使用。 Azure 角色型存取控制會強制執行最低許可權原則， (Azure RBAC) 機制。 |
| 稽核 | Azure 可讓 audit 功能捕捉和保護系統上發生什麼狀況的資料，以便日後可以進行檢查。 |

Microsoft 的強化 Azure 虛擬程式和虛擬化子系統的方法可細分為下列三種類別。

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>由虛擬程式強制執行的強定義安全性界限

Azure 虛擬程式會在下列各項之間強制執行多個安全性界限：

- 虛擬化的「來賓」分割區和特殊許可權磁碟分割 ( 「主機」 ) 
- 多個來賓
- 本身和主機
- 本身和所有來賓

安全機制安全性界限可保證機密性、完整性和可用性。 界限會防禦各種攻擊，包括側邊通道資訊流失、阻絕服務以及權限提高。

虛擬程式安全性界限也提供租使用者之間的網路流量、虛擬裝置、儲存體、計算資源和所有其他 VM 資源的分割。

## <a name="defense-in-depth-exploit-mitigations"></a>深度防禦惡意探索緩和措施

在不太可能的情況下，安全性界限有弱點，Azure 虛擬套裝程式含多層的緩和措施，包括：

- 隔離裝載跨 VM 元件的主機型進程
- 以虛擬化為基礎的安全性 (VBS) ，可確保安全世界的使用者和核心模式元件的完整性
- 多層的惡意探索緩和措施。 緩和措施包括位址空間配置隨機載入 (ASLR) 、資料執行防止 (DEP) 、任意程式碼防護、控制流程完整性，以及防止資料損毀
- 在編譯器層級自動初始化堆疊變數
- 自動將 Hyper-v 所做的核心堆積配置初始化的核心 Api

這些緩和措施的設計目的是為了讓您得以開發跨 VM 弱點的惡意探索。

## <a name="strong-security-assurance-processes"></a>強大的安全性保證流程

與虛擬程式相關的受攻擊麵包括軟體網路功能、虛擬裝置和所有的跨 VM 表面。 攻擊面是透過自動化組建整合來追蹤，而這會觸發週期性安全性檢查。

所有 VM 攻擊面都是由我們的紅色團隊針對安全性界限違規進行的威脅模型化、程式碼檢查、採用智慧比較及測試。 Microsoft 具有 [bug 賞金方案](https://www.microsoft.com/msrc/bounty-hyper-v) ，可針對 Microsoft Hyper-V 的合格產品版本中的相關弱點支付獎勵。

> [!NOTE]
> 深入瞭解 Hyper-v 中的 [強大安全性保證流程](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) 。

## <a name="next-steps"></a>後續步驟
若要深入瞭解我們要如何驅動平臺完整性和安全性，請參閱：

- [韌體安全性](firmware.md)
- [安全開機](secure-boot.md)
- [測量開機和主機證明](measured-boot-host-attestation.md)
- [專案 Cerberus](project-cerberus.md)
- [待用加密](encryption-atrest.md)