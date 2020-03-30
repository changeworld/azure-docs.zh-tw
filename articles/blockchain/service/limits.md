---
title: Azure 區塊鏈服務限制
description: Azure 區塊鏈服務中的服務和功能限制概述
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455653"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 區塊鏈服務中的限制

Azure 區塊鏈服務具有服務和功能限制，例如成員可以擁有的節點數、聯合體限制和存儲量。

## <a name="pricing-tier"></a>定價層

事務和驗證器節點的最大限制取決於是在基本定價層還是標準定價層提供 Azure 區塊鏈服務。

| 定價層 | 最大事務節點 | 最大驗證器節點 |
|:---|:---:|:---:|
| 基本 | 10 | 1 |
| 標準 | 10 | 2 |

不支援在成員創建後更改基本和標準之間的定價層。

## <a name="storage-capacity"></a>儲存體容量

每個節點可用於分類帳資料和日誌的最大存儲量為 1.8 TB。

不支援減小分類帳和日誌存儲大小。

## <a name="consortium-limits"></a>聯盟限制

* **聯盟和成員名稱必須與**Azure 區塊鏈服務中的其他聯合體和成員名稱是唯一的。

* **不能更改成員和聯合體名稱**

* **聯合體中的所有成員必須位於同一定價層中**

* **參加聯合體的所有成員必須居住在同一區域**

    在聯合體中創建的第一個成員指示該區域。 受邀加入聯合體的成員必須居住在第一個成員所在的同一區域。 將所有成員限制在同一區域有助於確保網路共識不會受到負面影響。

* **聯合體必須至少有一個管理員**

    如果聯合體中只有一個管理員，則在聯合體中添加或提升另一個管理員之前，他們無法將自己從聯合體中刪除或刪除其成員。

* **無法再次添加從聯合體中刪除的成員**

    相反，必須重新邀請他們加入聯合體，並創建一個新成員。 不會刪除其現有成員資源以保留歷史事務。

* **聯合體中的所有成員必須使用相同的分類帳版本**

    有關 Azure 區塊鏈服務中提供的修補、更新和分類帳版本的詳細資訊，請參閱[修補、更新和版本](ledger-versions.md)。

## <a name="next-steps"></a>後續步驟

瞭解有關系統修補和升級的策略的詳細資訊 -[修補、更新和版本](ledger-versions.md)。
