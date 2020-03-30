---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335023"
---
| 資源 | 限制 |
| --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) VM |每個區域 25，000<sup>1。</sup> |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 總計核心 |每個區域 20<sup>1。</sup> 聯繫支援以增加限制。 |
| Azure Spot VM 每個[訂閱](../articles/billing-buy-sign-up-azure-subscription.md)的總內核數 |每個區域 20<sup>1。</sup> 聯繫支援以增加限制。 |
| 每個系列的 VM（如 Dv2 和 F）每個[訂閱](../articles/billing-buy-sign-up-azure-subscription.md)的內核 |每個區域 20<sup>1。</sup> 聯繫支援以增加限制。 |
| 每個訂閱[的可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每個區域 2，000 個。 |
| 每一可用性設定組的虛擬機器 | 200 |
| 每一訂用帳戶的憑證 |無限制<sup>2</sup> |

<sup>1</sup>預設限制因產品/服務類別類型而異，例如免費試用和即用即付，以及按系列（如 Dv2、F 和 G）更改。例如，企業協定訂閱的預設值為 350。

<sup>2</sup>使用 Azure 資源管理器，憑證存放區在 Azure 金鑰保存庫中。 訂閱的證書數是無限的。 每個部署都有 1 MB 的證書限制，其中包括單個 VM 或可用性集。

> [!NOTE]
> 虛擬機器內核具有區域總限制。 它們對於區域/尺寸系列（如 Dv2 和 F）也有限制。這些限制是單獨強制執行的。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂閱可以部署 30 個 A1 VM 或 30 個 D1 VM，或者兩者的組合不超過總共 30 個內核。 組合的一個示例是 10 個 A1 VM 和 20 個 D1 VM。  
> <!-- -->
> 
