---
title: 如何回應 Azure Defender 的 DNS 警示
description: 瞭解從 Azure Defender for DNS 回應警示所需的步驟
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754612"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>回應 Azure Defender 的 DNS 警示

當您收到來自 Azure Defender for DNS 的警示時，建議您調查並回應警示，如下所述。 適用于 DNS 的 Azure Defender 會保護所有連線的資源，因此即使您熟悉觸發警示的應用程式或使用者，還是必須確認每個警示周圍的情況。  


## <a name="step-1-contact"></a>步驟 1： Contact

1. 請聯絡資源擁有者，以判斷行為是否預期或刻意執行。
1. 如果預期活動，請關閉警示。
1. 如果活動是未預期的，請將資源視為可能遭到入侵，並因下一個步驟中所述而緩和。

## <a name="step-2-immediate-mitigation"></a>步驟 2： 立即緩和 

1. 將資源與網路隔離，以防止橫向移動。
1. 在資源上執行完整的反惡意程式碼掃描，並遵循任何產生的補救建議。
1. 複習資源上已安裝並執行的軟體，移除任何未知或不必要的套件。
1. 將電腦還原為已知的良好狀態，並視需要重新安裝作業系統，並從已驗證的無惡意程式碼來源還原軟體。
1. 解決電腦的任何 Azure 資訊安全中心建議，修復反白顯示的安全性問題，以防止未來的缺口。


## <a name="next-steps"></a>後續步驟

此頁面說明從 Azure Defender for DNS 回應警示的流程。 如需相關資訊，請參閱下列頁面：

- [適用于 DNS 的 Azure Defender 簡介](defender-for-dns-introduction.md)
- [隱藏來自 Azure Defender 的警示](alerts-suppression-rules.md)
- [持續匯出資訊安全中心資料](continuous-export.md)