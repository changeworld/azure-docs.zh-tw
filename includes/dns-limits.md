---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805946"
---
**公用 DNS 區域**

| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶的公用 DNS 區域 |250 <sup>1</sup> |
| 每個公用 DNS 區域的記錄集 |10000 <sup>1</sup> |
| 公用 DNS 區域中每個記錄集的記錄 |20 |
| 單一 Azure 資源的別名記錄數目 |20|
| 每個訂用帳戶的私人 DNS 區域 |1000|
| 每個私人 DNS 區域的記錄集 |25000|
| 私人 DNS 區域的每個記錄集記錄 |20|
| 每個私人 DNS 區域的虛擬網路連結 |1000|
| 已啟用自動註冊的每個私人 DNS 區域的虛擬網路連結 |100|
| 已啟用自動註冊的虛擬網路可連結的私人 DNS 區域數目 |1|
| 虛擬網路可以連結的私人 DNS 區域數目 |1000|
| 虛擬機器可傳送至 Azure DNS 解析程式的 DNS 查詢數（每秒） |500 <sup>2</sup> |
| 每個虛擬機器的 DNS 查詢佇列（擱置回應）數目上限 |200 <sup>2</sup> |

<sup>1</sup> 如果需要增加這些限制，請連絡 Azure 支援。

<sup>2</sup>這些限制會套用至每個個別的虛擬機器，而不會套用至虛擬網路層級。 超過這些限制的 DNS 查詢會被捨棄。
