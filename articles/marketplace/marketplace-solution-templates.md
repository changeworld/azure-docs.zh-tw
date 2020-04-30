---
title: Azure 應用程式解決方案範本供應專案發行指南 |Azure Marketplace
description: 本文說明在 Azure Marketplace 中發佈解決方案範本的需求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084850"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 應用程式：解決方案範本供應專案發佈需求

本文說明解決方案範本供應專案類型的需求，這是在 Azure Marketplace 中發佈 Azure 應用程式供應專案的一種方式。 解決方案範本供應專案類型需要[Azure Resource Manager 範本（ARM 範本）](../azure-resource-manager/templates/overview.md) ，才能自動部署您的解決方案基礎結構。

當需要下列條件時，請使用 Azure 應用程式解決方案範本供應專案類型：

- 您的解決方案需要超過單一 VM 的額外部署和設定自動化，例如 Vm、網路和儲存體資源的組合。
- 您的客戶即將管理解決方案本身。

使用者針對此供應專案類型所看到的動作呼叫為「立即取得」。

## <a name="requirements-for-solution-template-offers"></a>解決方案範本供應專案的需求

| **Requirements** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  解決方案範本供應專案不是交易供應專案，但可用於部署透過 Microsoft 商業 marketplace 計費的付費 VM 供應專案。 將會在客戶的 Azure 訂用帳戶中布建解決方案的 ARM 範本所部署的資源。 隨用隨付（PAYGO）虛擬機器將透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。<br/> 在自備授權 (BYOL) 的案例中，雖然 Microsoft 會向客戶訂用帳戶中產生的基礎結構成本收費，但您將直接向客戶收取軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱： <ul> <li>[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案（適用于 Windows vhd）</li><li>[Azure 上背書的 linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)散發套件（適用于 linux vhd）。</li></ul> |
| 客戶使用狀況屬性 | 在所有發佈至 Azure Marketplace 的解決方案範本上，都必須啟用客戶使用狀況屬性。 如需客戶使用狀況屬性及其啟用方式的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況屬性](./azure-partner-customer-usage-attribution.md)。  |
| 使用受控磁碟 | [受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS vm 的持續性磁片的預設選項。 您必須使用方案範本中的受控磁碟。 <br> <br> 1. 遵循在 Azure ARM 範本中使用受控磁碟的[指引](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[範例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)，以更新您的解決方案範本。 <br> <br> 2. 依照下列指示將受控磁碟的基礎 VHD 匯入到儲存體帳戶，以將 VHD 發佈為 Marketplace 中的映射： <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>後續步驟

- 如果您尚未這麼做，請[瞭解](https://azuremarketplace.microsoft.com/sell)Azure Marketplace。
- 登[入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以建立或完成您的供應專案。
- [建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案以取得詳細資訊。
