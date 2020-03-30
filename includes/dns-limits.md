---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335070"
---
**公共 DNS 區域**

| 資源 | 限制 |
| --- | --- |
| 每個訂閱的公共 DNS 區域 |250 <sup>1</sup> |
| 每個公共 DNS 區域的記錄集 |10，000 <sup>1</sup> |
| 公共 DNS 區域中設置的記錄 |20 |
| 單個 Azure 資源的別名記錄數 |20|
| 每個訂閱的專用 DNS 區域 |1000|
| 每個專用 DNS 區域的記錄集 |25000|
| 為專用 DNS 地區設定的記錄 |20|
| 每個專用 DNS 區域的虛擬網路連結 |1000|
| 啟用自動註冊的專用 DNS 區域的虛擬網路連結 |100|
| 虛擬網路可以通過啟用自動註冊連接到的專用 DNS 區域數 |1|
| 虛擬網路可以連結的專用 DNS 區域數 |1000|
| 虛擬機器每秒可以發送到 Azure DNS 解析器的 DNS 查詢數 |500 <sup>2</sup> |
| 每個虛擬機器排隊（掛起回應）的最大 DNS 查詢數 |200 <sup>2</sup> |

<sup>1</sup>如果需要增加這些限制，請與 Azure 支援部門聯繫。

<sup>2</sup>這些限制應用於每個單獨的虛擬機器，而不是在虛擬網路級別。 將刪除超出這些限制的 DNS 查詢。