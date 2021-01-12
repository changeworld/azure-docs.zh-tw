---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: cd3ff3fce80e66d7cd61636b4416cb2fc28f5e77
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956460"
---
| 資源 | 限制 |
| --- | --- |
| 每一[訂用帳戶](https://azure.microsoft.com/pricing/) VM |每個區域 25,000 個<sup>1</sup>。 |
| 每一[訂用帳戶](https://azure.microsoft.com/pricing/)的 VM 總計核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一[訂用帳戶](https://azure.microsoft.com/pricing/)的 Azure 現成 VM 總核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一[訂用帳戶](https://azure.microsoft.com/pricing/)的每個系列 (例如 Dv2 和 F) VM 核心 |每個區域 20 個<sup>1</sup>。 請連絡支援人員以提高限制。 |
| 每一訂用帳戶的[可用性設定組](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每個區域 2,500 個。 |
| 每一可用性設定組的虛擬機器 | 200 |
| 依[資源群組](../articles/azure-resource-manager/management/overview.md#resource-groups)的[鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) | 800 | 
| 每一可用性設定組的虛擬機器 | 199<sup>2</sup> |
| 每一訂用帳戶的憑證 |無限制<sup>3</sup> |

<sup>1</sup>預設限制會因供應項目類別類型 (例如免費試用版和隨用隨付) 以及因系列 (例如 Dv2、F 和 G) 而有所不同。例如，Enterprise 合約訂用帳戶的預設值為 350。

<sup>2</sup>屬性 (例如 SSH 公開金鑰) 也會推送為憑證，並計入此限制。 若要略過此限制，請使用[適用於 Windows 的 Azure Key Vault 延伸模組](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)，或[適用於 Linux 的 Azure Key Vault 延伸模組](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux)以安裝憑證。

<sup>3</sup>使用 Azure 資源管理員時，憑證會儲存在 Azure 金鑰保存庫內。 訂用帳戶的憑證數目不受限制。 每一部署都有 1 MB 的憑證限制，這是由單一 VM 或可用性設定組所組成。



> [!NOTE]
> 虛擬機器核心具有區域總限制。 其也有每一大小系列 (例如 Dv2 和 F) 的區域性限制。系統會分別強制執行這些限制。 例如，請考慮美國東部訂用帳戶的總計 VM 核心限制為 30、A 系列核心限制為 30，和 D 系列核心限制為 30。 此訂用帳戶可以部署 30 個 A1 VM、30 個 D1 VM，或是兩個的組合，總計不超過 30 個核心。 組合的範例為 10 個 A1 VM 和 20 個 D1 VM。  
> <!-- -->
>
