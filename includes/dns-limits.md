---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 0f7187300ec96ce417866c4fb8fa02783c1da63a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86515840"
---
**公用 DNS 區域**

| 資源 | 限制 |
| --- | --- |
| 每一訂用帳戶的公用 DNS 區域 |250 <sup>1</sup> |
| 每一公用 DNS 區域的記錄集 |10,000 <sup>1</sup> |
| 公用 DNS 區域中每個記錄集的記錄 |20 |
| 單一 Azure 資源的別名記錄數目 |20|

<sup>1</sup> 如果需要增加這些限制，請連絡 Azure 支援。

**私人 DNS 區域**

| 資源 | 限制 |
| --- | --- |
| 每一訂用帳戶的公用 DNS 區域 |1000|
| 每一私人 DNS 區域的記錄集 |25000|
| 私人 DNS 區域的每個記錄集的記錄 |20|
| 每一私人 DNS 區域的虛擬網路連結 |1000|
| 已啟用自動註冊的每個私人 DNS 區域的虛擬網路連結 |100|
| 虛擬網路可以連結並已啟用自動註冊的私人 DNS 區域數目 |1|
| 虛擬網路可以連結的私人 DNS 區域數目 |1000|
| 虛擬機器每秒可傳送至 Azure DNS 解析程式的 DNS 查詢數目 |500 <sup>1</sup> |
| 每個虛擬機器的已排入佇列 (回應待決) DNS 查詢數目上限 |200 <sup>1</sup> |

<sup>1</sup>這些限制會套用至每個個別虛擬機器，而不會套用於虛擬網路層級。 超過這些限制的 DNS 查詢會遭到捨棄。
