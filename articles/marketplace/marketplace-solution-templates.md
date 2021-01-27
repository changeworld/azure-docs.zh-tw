---
title: Azure 應用程式解決方案範本供應專案-Azure Marketplace 的發佈指南
description: 本文說明在 Azure Marketplace 上發佈解決方案範本的需求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879338"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 應用程式解決方案範本供應專案發佈指南

本文說明發佈解決方案範本供應專案的需求，這是在 Azure Marketplace 中發佈 Azure 應用程式供應專案的一種方式。 解決方案範本供應專案類型需要 [Azure Resource Manager 範本 (ARM 範本) ](../azure-resource-manager/templates/overview.md) ，以自動部署您的解決方案基礎結構。

在下列情況下，請使用 Azure 應用程式 *解決方案範本* 供應專案類型：

- 您的解決方案需要在單一虛擬機器 (VM) （例如 Vm、網路和儲存體資源的組合）之外進行額外的部署和設定自動化。
- 您的客戶即將自行管理解決方案。

客戶看到的這項供應專案類型清單選項是 *立即取得*。

## <a name="requirements-for-solution-template-offers"></a>解決方案範本供應專案的需求

| **Requirements** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  解決方案範本供應專案並非交易供應專案，但是可以用來部署透過 Microsoft 商業 marketplace 計費的付費 VM 供應專案。 解決方案的 ARM 範本部署的資源會在客戶的 Azure 訂用帳戶中設定。 隨用隨付的虛擬機器會透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。<br/> 針對自備授權 (BYOL) 帳單，雖然 Microsoft 會收取客戶訂用帳戶所產生的基礎結構成本，但您會直接向客戶收取軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱 <ul> <li>建立適用于 Windows Vhd) 的[Azure 應用程式供應](./create-new-azure-apps-offer.md)專案 (。</li><li>Linux[發行版本背書于 Azure](../virtual-machines/linux/endorsed-distros.md) (linux vhd) 。</li></ul> |
| 客戶使用狀況屬性 | 在 Azure Marketplace 上發行的所有解決方案範本都需要啟用客戶使用狀況屬性。 如需客戶使用方式屬性和如何啟用的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況](./azure-partner-customer-usage-attribution.md)屬性。  |
| 使用受控磁碟 | [受控磁片](../virtual-machines/managed-disks-overview.md) 是 Azure 中的基礎結構即服務 (IaaS) vm 之保存磁片的預設選項。 您必須在解決方案範本中使用受控磁片。 <ul><li>若要更新您的解決方案範本，請遵循在 [Azure Resource Manager 範本中使用受控磁片](../virtual-machines/using-managed-disks-template-deployments.md)的指導方針，並使用提供的 [範例](https://github.com/Azure/azure-quickstart-templates)。<br><br> </li><li>若要將 VHD 發佈為 Azure Marketplace 中的映射，請使用下列其中一種方法，將受控磁片的基礎 VHD 匯入至儲存體帳戶：<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell) (若您尚不了解)。

若要註冊並開始使用合作夥伴中心：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需詳細資訊，請參閱 [建立 Azure 應用程式供應](./create-new-azure-apps-offer.md) 專案。