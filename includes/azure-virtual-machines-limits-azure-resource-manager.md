---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615980"
---
| 資源 | 限制 |
| --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) VM |每個區域 25000<sup>1</sup> 。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 總計核心 |每個區域 20<sup>1</sup>個。 請聯絡支援以增加限制。 |
| Azure 點 VM 每個[訂](../articles/billing-buy-sign-up-azure-subscription.md)用帳戶的核心總數 |每個區域 20<sup>1</sup>個。 請聯絡支援以增加限制。 |
| 每個系列的 VM，例如 Dv2 和 F，每個[訂](../articles/billing-buy-sign-up-azure-subscription.md)用帳戶的核心 |每個區域 20<sup>1</sup>個。 請聯絡支援以增加限制。 |
| 每個訂用帳戶的[可用性設定組](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |2500每個區域。 |
| 每一可用性設定組的虛擬機器 | 200 |
| 每一訂用帳戶的憑證 |無限制<sup>2</sup> |

<sup>1</sup>預設限制會因供應專案類別類型而異，例如免費試用版和隨用隨付，以及依系列（例如 Dv2、F 和 G）。例如，Enterprise 合約訂閱的預設值是350。

<sup>2</sup>使用 Azure Resource Manager，憑證會儲存在 Azure Key Vault 中。 訂用帳戶的憑證數目不受限制。 每個部署都有 1 MB 的憑證限制，這是由單一 VM 或可用性設定組所組成。

> [!NOTE]
> 虛擬機器核心具有區域總計限制。 它們也會限制每一大小的區域，例如 Dv2 和 F。系統會分別強制執行這些限制。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可以部署30個 A1 Vm 或30個 D1 Vm，或兩者的組合，而不超過總計30個核心。 結合的範例為10個 A1 Vm 和20個 D1 Vm。  
> <!-- -->
> 
