---
title: Azure 應用程式解決方案範本提供發佈指南 |Azure 應用商店
description: 本文說明在 Azure Marketplace 中發佈解決方案範本的需求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687520"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 應用程式：解決方案範本供應項目發佈指南

解決方案範本是在 Marketplace 中發佈解決方案的其中一個主要方法。 您可以使用本指南來了解這項供應項目的需求。 

當您的解決方案除了單一 VM 之外，還需要以自動化方式進行額外的部署和設定時，請使用「Azure 應用程式：解決方案範本」供應項目類型。 您可以使用「Azure 應用程式：解決方案範本」來自動佈建一或多部 VM。 您也可以佈建網路和儲存體資源。 「Azure 應用程式：解決方案範本」供應項目類型可為單一 VM 和整個 IaaS 型解決方案提供自動化的優點。

這些解決方案範本不是事務報價,但可用於部署透過 Microsoft 商業市場計費的付費 VM 產品/服務。 使用者會看到的呼籲行動是「立即取得」。


## <a name="requirements-for-solution-templates"></a>解決方案範本的需求

| **需求** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  資源將在客戶的 Azure 訂閱中預配。 即用即付 (PAYGO) 虛擬機器將透過 Microsoft 與客戶進行交易,透過客戶的 Azure 訂閱 (PAYGO) 計費。  <br/> 在自備授權 (BYOL) 的案例中，雖然 Microsoft 會向客戶訂用帳戶中產生的基礎結構成本收費，但您將直接向客戶收取軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。  有關詳細資訊,請參閱創建[Azure 應用程式產品/服務](./partner-center-portal/create-new-azure-apps-offer.md)。 |
| 客戶使用狀況屬性 | 在所有發佈至 Azure Marketplace 的解決方案範本上，都必須啟用客戶使用狀況屬性。 如需客戶使用狀況屬性及其啟用方式的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況屬性](./azure-partner-customer-usage-attribution.md)。  |
| 使用受控磁碟 | [託管磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS VM 的持久磁碟的預設選項。 必須在解決方案範本中使用託管磁碟。 <br> <br> 1. 按照 Azure ARM 範本中使用託管磁碟[的指南](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[範例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解決方案範本。 <br> <br> 2. 按照以下說明將託管磁碟的基礎 VHD 匯入儲存帳戶,以在應用商店中將 VHD 作為映射發佈: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>後續步驟

如果您還沒有這麼做，請 

- [了解](https://azuremarketplace.microsoft.com/sell)市場。

要在合作夥伴中心註冊,請開始創建新產品/服務或處理現有產品/服務:

- [登錄合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)創建或完成您的優惠。
- 有關詳細資訊[,請參閱創建 Azure 應用程式產品/服務](./partner-center-portal/create-new-azure-apps-offer.md)。

