---
title: Azure 平臺完整性和安全性-Azure 安全性
description: Azure 平臺完整性和安全性的技術總覽。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557733"
---
# <a name="platform-integrity-and-security-overview"></a>平臺完整性和安全性總覽
Azure 車隊是由數百萬部伺服器組成 (主機) 每日額外加入上千個伺服器。 數以千計的主機也會透過重新開機、作業系統重新整理或修復來每天進行維護。 在主機加入車隊並開始接受客戶工作負載之前，Microsoft 會驗證主機是否處於安全且值得信任的狀態。 此驗證可確保在供應鏈或維護工作流程期間，開機順序元件上未發生惡意或意外的變更。

## <a name="securing-azure-hardware-and-firmware"></a>保護 Azure 硬體和固件
這一系列的文章說明 Microsoft 如何透過生命週期的各種階段（從製造到終止）來確保主機的完整性和安全性。 這些文章說明：
 
- [固件安全性](firmware.md)
- [UEFI 安全開機](secure-boot.md)
- [測量開機和主機證明](measured-boot-host-attestation.md)
- [專案 Cerberus](project-cerberus.md)
- [待用加密](encryption-atrest.md)
- [虛擬程式安全性](hypervisor.md)
 
## <a name="next-steps"></a>後續步驟

- 瞭解 Microsoft 如何積極地在雲端硬體生態系統內，推動持續的 [固件安全性改善](firmware.md)。

- 瞭解您 [在雲端中的共同責任](shared-responsibility.md)。