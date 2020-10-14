---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82615980"
---
| 資源 | 限制 |
| --- | --- |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md) VM |每個區域 25,000 個<sup>1</sup>。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 總計核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的 Azure 現成 VM 總核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一[訂用帳戶](../articles/billing-buy-sign-up-azure-subscription.md)的每個系列 (例如 Dv2 和 F) VM 核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一訂用帳戶的[可用性設定組](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每個區域 2,500 個。 |
| 每一可用性設定組的虛擬機器 | 200 |
| 每一訂用帳戶的憑證 |無限制<sup>2</sup> |

<sup>1</sup>預設限制會因供應項目類別類型 (例如免費試用版和隨用隨付) 以及因系列 (例如 Dv2、F 和 G) 而有所不同。例如，Enterprise 合約訂用帳戶的預設值為 350。

<sup>2</sup>使用 Azure 資源管理員時，憑證會儲存在 Azure 金鑰保存庫內。 訂用帳戶的憑證數目不受限制。 每一部署都有 1 MB 的憑證限制，這是由單一 VM 或可用性設定組所組成。

> [!NOTE]
> 虛擬機器核心具有區域總限制。 其也有每一大小系列 (例如 Dv2 和 F) 的區域性限制。系統會分別強制執行這些限制。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可以部署 30 個 A1 VM、30 個 D1 VM，或是兩個的組合，總計不超過 30 個核心。 組合的範例為 10 個 A1 VM 和 20 個 D1 VM。  
> <!-- -->
> 
