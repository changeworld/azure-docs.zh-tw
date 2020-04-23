---
title: Azure 應用程式解決方案範本提供發佈指南 |Azure 應用商店
description: 本文說明在 Azure Marketplace 中發佈解決方案範本的需求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084850"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 應用程式:解決方案範本提供發佈要求

本文介紹解決方案範本產品提供類型的要求,這是在 Azure 應用商店中發佈 Azure 應用程式產品/服務的一種方式。 解決方案範本提供類型需要[Azure 資源管理員範本 (ARM 範本)](../azure-resource-manager/templates/overview.md)才能自動部署解決方案基礎結構。

當需要以下條件時,請使用 Azure 應用程式解決方案樣本產品類型:

- 您的解決方案需要單個 VM 以外的其他部署和配置自動化,例如 VM、網路和儲存資源的組合。
- 您的客戶將自行管理解決方案。

使用者為此產品/服務類型看到的操作調用是"立即獲取」。

## <a name="requirements-for-solution-template-offers"></a>解決方案範本提供要求

| **需求** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  解決方案範本產品/服務不是交易優惠,但可用於部署透過Microsoft商業市場計費的付費VM產品/服務。 解決方案的 ARM 範本部署的資源將在客戶的 Azure 訂閱中預配。 即用即付 (PAYGO) 虛擬機器將透過 Microsoft 與客戶進行交易,透過客戶的 Azure 訂閱計費。<br/> 在自備授權 (BYOL) 的案例中，雖然 Microsoft 會向客戶訂用帳戶中產生的基礎結構成本收費，但您將直接向客戶收取軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱 <ul> <li>[建立 Azure 應用程式產品/服務](./partner-center-portal/create-new-azure-apps-offer.md)(適用於 Windows VHD)</li><li>[在 Azure 上認可的 Linux 發行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)(對於 Linux VHD)。</li></ul> |
| 客戶使用狀況屬性 | 在所有發佈至 Azure Marketplace 的解決方案範本上，都必須啟用客戶使用狀況屬性。 如需客戶使用狀況屬性及其啟用方式的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況屬性](./azure-partner-customer-usage-attribution.md)。  |
| 使用受控磁碟 | [託管磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS VM 的持久磁碟的預設選項。 必須在解決方案範本中使用託管磁碟。 <br> <br> 1. 按照 Azure ARM 範本中使用託管磁碟[的指南](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[範例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解決方案範本。 <br> <br> 2. 按照以下說明將託管磁碟的基礎 VHD 匯入儲存帳戶,以在應用商店中將 VHD 作為映射發佈: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>後續步驟

- 如果尚未這樣做,[則瞭解](https://azuremarketplace.microsoft.com/sell)Azure 應用商店。
- [登錄合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)創建或完成您的優惠。
- [有關詳細資訊,請創建 Azure 應用程式產品/](./partner-center-portal/create-new-azure-apps-offer.md)服務。
