---
title: Azure 應用程式的發佈指南解決方案範本提供-Azure Marketplace
description: 本文說明在 Azure Marketplace 上發佈解決方案範本的需求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 4d395aa68c579b8e1271f4225ba57fa5f9932246
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963779"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 應用程式的發佈指南解決方案範本供應專案

本文說明發佈解決方案範本供應專案的需求，這是在 Azure Marketplace 中發佈 Azure 應用程式供應專案的一種方式。 解決方案範本供應專案類型需要[Azure Resource Manager 範本（ARM 範本）](../azure-resource-manager/templates/overview.md) ，才能自動部署您的解決方案基礎結構。

在下列情況下，請使用 Azure 應用程式*解決方案範本*供應專案類型：

- 您的解決方案需要除了單一虛擬機器（VM）之外的額外部署和設定自動化，例如 Vm、網路和儲存體資源的組合。
- 您的客戶即將管理解決方案本身。

客戶針對此供應專案類型所看到的動作呼叫現在會*立即取得*。

## <a name="requirements-for-solution-template-offers"></a>解決方案範本供應專案的需求

| **需求** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  解決方案範本供應專案不是交易供應專案，但可用來部署透過 Microsoft 商業 marketplace 計費的付費 VM 供應專案。 解決方案的 ARM 範本部署的資源會在客戶的 Azure 訂用帳戶中設定。 隨用隨付虛擬機器是透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。<br/> 對於自備授權（BYOL）計費，雖然 Microsoft 會收取客戶訂用帳戶中所產生的基礎結構成本，但您會直接向客戶交軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱： <ul> <li>[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案（適用于 Windows vhd）。</li><li>[Azure 上背書的 linux](../virtual-machines/linux/endorsed-distros.md)散發套件（適用于 linux vhd）。</li></ul> |
| 客戶使用狀況屬性 | 在 Azure Marketplace 上發佈的所有解決方案範本上，都必須啟用客戶使用方式屬性。 如需客戶使用狀況屬性和如何啟用的詳細資訊，請參閱[Azure 合作夥伴客戶使用狀況](./azure-partner-customer-usage-attribution.md)屬性。  |
| 使用受控磁碟 | [受控磁片](../virtual-machines/windows/managed-disks-overview.md)是 Azure 中基礎結構即服務（IaaS） vm 的持續性磁片的預設選項。 您必須在解決方案範本中使用受控磁片。 <ul><li>若要更新您的解決方案範本，請遵循在[Azure Resource Manager 範本中使用受控磁片](../virtual-machines/windows/using-managed-disks-template-deployments.md)中的指導方針，並使用提供的[範例](https://github.com/Azure/azure-quickstart-templates)。<br><br> </li><li>若要在 Azure Marketplace 中將 VHD 發佈為映射，請使用下列其中一種方法，將受控磁片的基礎 VHD 匯入至儲存體帳戶：<ul><li>[Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md) </li> <li> [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md) </li> </ul></ul> |

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell) (若您尚不了解)。

若要註冊並開始使用合作夥伴中心：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需詳細資訊，請參閱[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案。
