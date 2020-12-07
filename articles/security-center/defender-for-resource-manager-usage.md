---
title: 如何回應 Azure Defender 的 Resource Manager 警示
description: 瞭解從 Azure Defender 針對 Resource Manager 回應警示所需的步驟
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754603"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>回應 Azure Defender 的 Resource Manager 警示

當您從 Azure Defender 針對 Resource Manager 收到警示時，建議您調查並回應警示，如下所述。 適用于 Resource Manager 的 Azure Defender 會保護所有連接的資源，因此即使您熟悉觸發警示的應用程式或使用者，還是必須確認每個警示周圍的情況。  


## <a name="step-1-contact"></a>步驟 1： Contact

1. 請聯絡資源擁有者，以判斷行為是否預期或刻意執行。
1. 如果預期活動，請關閉警示。
1. 如果活動不是預期的，請將相關的使用者帳戶、訂用帳戶和虛擬機器視為遭入侵，並緩和，如下列步驟所述。

## <a name="step-2-immediate-mitigation"></a>步驟 2： 立即緩和 

1. 修復遭盜用的使用者帳戶：
    - 如果不熟悉，請刪除它們，因為它們可能是由威脅執行者所建立
    - 如果他們很熟悉，請變更其驗證認證
    - 使用 Azure 活動記錄來檢查使用者執行的所有活動，並識別任何可疑的活動

1. 修復遭盜用的訂閱：
    - 從遭入侵的自動化帳戶中移除任何不熟悉的 Runbook
    - 查看訂用帳戶的 IAM 許可權，並移除任何不熟悉的使用者帳戶的許可權
    - 檢查訂用帳戶中的所有 Azure 資源，並刪除任何不熟悉的資源
    - 檢查並調查 Azure 資訊安全中心中訂用帳戶的任何安全性警示
    - 使用 Azure 活動記錄來檢查在訂用帳戶中執行的所有活動，並識別任何可疑的活動

1. 修復遭入侵的虛擬機器
    - 變更所有使用者的密碼
    - 在電腦上執行完整反惡意程式碼掃描
    - 從無惡意程式碼的來源重新安裝電腦映射


## <a name="next-steps"></a>後續步驟

此頁面說明從 Azure Defender 針對 Resource Manager 回應警示的流程。 如需相關資訊，請參閱下列頁面：

- [適用于 Resource Manager 的 Azure Defender 簡介](defender-for-resource-manager-introduction.md)
- [隱藏來自 Azure Defender 的警示](alerts-suppression-rules.md)
- [持續匯出資訊安全中心資料](continuous-export.md)