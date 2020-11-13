---
title: 固件安全性-Azure 安全性
description: 瞭解 Microsoft 如何保護 Azure 硬體和固件。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557721"
---
# <a name="firmware-security"></a>固件安全性
本文說明 Microsoft 如何保護雲端硬體生態系統和供應鏈。

## <a name="securing-the-cloud-hardware-ecosystem"></a>保護雲端硬體生態系統
Microsoft 積極地在雲端硬體生態系統內的夥伴，利用下列方式來推動持續的安全性改進：

- 與 Azure 硬體和固件合作夥伴 (（例如元件製造商和系統整合者）合作，) 以符合 Azure 硬體和固件的安全性需求。

- 讓合作夥伴使用 Microsoft 定義的需求，在下列領域中執行持續的評量和改進其產品的安全性狀況：

  - 固件安全開機
  - 固件安全修復
  - 固件安全更新
  - 固件密碼編譯
  - 鎖定的硬體
  - 精細的 debug 遙測
  - TPM 2.0 硬體的系統支援，可啟用測量開機

- 透過開發規格，參與並參與 [開放式計算專案 (OCP) ](https://www.opencompute.org/wiki/Security) 安全性專案。 規格可提升安全設計與架構在生態系統中的一致性和清晰度。

   > [!NOTE]
   > 關於 OCP 安全性專案的貢獻範例是 [硬體安全開機](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) 規格。

## <a name="securing-hardware-and-firmware-supply-chains"></a>保護硬體和固件供應鏈
雲端硬體供應商和廠商（Azure）也必須遵守 Microsoft 所開發的供應鏈安全性流程和需求。 需要硬體和固件開發和部署程式，才能遵循 Microsoft [安全性開發生命週期](https://www.microsoft.com/securityengineering/sdl) (SDL) 程式，例如：

- 威脅分析模型
- 安全的設計審核
- 固件審核和滲透測試
- 保護組建和測試環境
- 安全性弱點管理和事件回應

## <a name="next-steps"></a>後續步驟
若要深入瞭解我們要如何驅動平臺完整性和安全性，請參閱：

- [安全開機](secure-boot.md)
- [測量開機和主機證明](measured-boot-host-attestation.md)
- [專案 Cerberus](project-cerberus.md)
- [待用加密](encryption-atrest.md)
- [虛擬程式安全性](hypervisor.md)