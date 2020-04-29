---
title: Azure 區塊鏈服務限制
description: Azure 區塊鏈 Service 中的服務和功能限制總覽
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80676525"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 區塊鏈 Service 中的限制

Azure 區塊鏈 Service 具有服務和功能限制，例如成員可以擁有的節點數目、聯盟限制和儲存體數量。

## <a name="pricing-tier"></a>定價層

交易和驗證程式節點的最大限制取決於您是在基本或標準定價層布建 Azure 區塊鏈 Service。

| 定價層 | 交易節點數上限 | 驗證器節點上限 |
|:---|:---:|:---:|
| 基本 | 10 | 1 |
| Standard | 10 | 2 |

您的聯盟網路應該有至少兩個 Azure 區塊鏈 Service 標準層節點。 標準層節點包含兩個驗證程式節點。 需要四個驗證器節點，才能符合[伊斯坦布爾拜占庭容錯的共識](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)。

使用基本層是用於開發、測試和概念證明。 針對生產等級部署使用標準層。 如果您使用區塊鏈資料管理員或傳送大量的私人交易，也應該使用*標準*層。

不支援在建立成員之後變更基本和標準間的定價層。

## <a name="storage-capacity"></a>儲存體容量

每個節點可用於總帳資料和記錄的儲存體數量上限為 1.8 tb。

不支援減少總帳和記錄儲存體大小。
## <a name="consortium-limits"></a>聯盟限制

* **聯盟和成員名稱必須**與 Azure 區塊鏈服務中的其他聯盟和成員名稱是唯一的。

* **無法變更成員和聯盟名稱**

* **聯盟中的所有成員都必須位於相同的定價層**

* **參與聯盟的所有成員都必須位於相同的區域**

    聯盟中所建立的第一個成員會規定區域。 聯盟的受邀成員必須位於與第一個成員相同的區域中。 將所有成員限制在相同的區域，有助於確保網路共識不會受到負面影響。

* **聯盟必須至少有一位系統管理員**

    如果聯盟中只有一個系統管理員，他們就無法從聯盟移除自己，或刪除其成員，直到在聯盟中新增或升級另一個系統管理員為止。

* **無法再次新增從聯盟移除的成員**

    相反地，他們必須重複邀請，才能加入聯盟並建立新的成員。 不會刪除其現有的成員資源來保留歷程記錄交易。

* **聯盟中的所有成員都必須使用相同的總帳版本**

    如需有關 Azure 區塊鏈 Service 中可用修補、更新和總帳版本的詳細資訊，請參閱[修補、更新和版本](ledger-versions.md)。

## <a name="performance"></a>效能

請勿使用 eth 來進行每次提交交易的*預估*天然氣功能。 *Eth*函數會耗用大量記憶體。 多次呼叫函式會大幅減少每秒的交易數。

可能的話，請使用保守天然氣值來提交交易，並將*eth*的使用降到最低。

## <a name="next-steps"></a>後續步驟

深入瞭解有關系統修補和升級的原則-[修補、更新和版本](ledger-versions.md)。
