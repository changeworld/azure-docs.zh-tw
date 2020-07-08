---
title: Azure 機密運算常見問題集
description: Azure 機密運算常見問題集的解答。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: bb821d00a168e3b8f0636b93696376dc8b5d492e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772893"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 機密運算的常見問題集

本文提供關於[在 Azure 上執行機密運算工作負載](overview.md)的一些最常見問題解答。

若本文中未提及您的 Azure 問題，請前往 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇中張貼您的問題，或將問題貼到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 您也可以提交 Azure 支援要求。 若要提交支援要求，請在 [Azure 支援頁面](https://azure.microsoft.com/support/options/)上，選取 [取得支援]。

## <a name="confidential-computing-virtual-machines"></a>機密運算虛擬機器 <a id="vm-faq"></a>

**如何在 Azure 上部署 DCsv2 系列 VM？**

以下是您可以用來部署 DCsv2 VM 的一些方法：
   - 使用 [Azure Resource Manager 範本](../virtual-machines/windows/template-description.md)
   - 從 [Azure 入口網站](https://portal.azure.com/#create/hub)
   - 在 [Azure 機密運算 (虛擬機器)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) 市集解決方案範本中。 市集解決方案範本可協助將客戶限制為支援的案例 (區域、映像、可用性、磁碟加密)。 

**所有作業系統映像是否都會使用 Azure 機密運算？**

否。 虛擬機器只能在具有 Ubuntu Server 18.04、Ubuntu Server 16.04、Windows Server 2019 Datacenter 和 Windows Server 2016 Datacenter 的第 2 代運作電腦上部署。 深入了解 [Linux](../virtual-machines/linux/generation-2.md) 和 [Windows](../virtual-machines/windows/generation-2.md)上的第 2 代 VM

**DCsv2 虛擬機器在入口網站中呈現灰色，我無法選取其中一個**

根據 VM 旁的資訊泡泡，需要採取不同的動作：
   -    **UnsupportedGeneration**：將虛擬機器映像的世代變更為 “Gen2”。
   -    **NotAvailableForSubscription**：您的訂用帳戶尚無法使用此區域。 選取可用的區域。
   -    **InsufficientQuota**：[建立支援要求以提高您的配額](../azure-portal/supportability/per-vm-quota-requests.md)。 免費試用訂用帳戶沒有機密運算 VM 的配額。 

**當我嘗試在入口網站大小選取器中搜尋時，未顯示 DCsv2 虛擬機器**

請確定您已選取[可用的區域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。 此外，也請確定您在大小選取器中選取 [清除所有篩選]。 

**我收到 Azure Resource Manager 範本部署失敗錯誤：「無法完成作業，因為作業導致超過已核准的標準 DcsV2 系列核心配額」**

[建立支援要求以提高您的配額](../azure-portal/supportability/per-vm-quota-requests.md)。 免費試用訂用帳戶沒有機密運算 VM 的配額。 

**DCsv2 系列和 DC 系列 VM 之間有何差異？**

DC 系列 VM 是在較舊的 6 核心 Intel 處理器 (具備 Intel SGX) 上執行，具有較少的總記憶體、較少的記憶體保護區頁面快取 (EPC) 記憶體，而且僅適用於兩個區域 (美國東部和西歐，大小為 Standard_DC2 和 Standard_DC4)。 沒有任何方案可讓這些 VM 正式運作，不建議用於實際執行環境。 若要部署這些 VM，請使用[機密運算 DC 系列 VM [預覽]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace 執行個體。

**DCsv2 虛擬機器是否可在全域使用？**

否。 目前這些虛擬機器僅適用於選取的區域。 如需最新的可用區域，請參閱[依區域的產品頁面](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。 

**如何在 DCsv2 虛擬機器上安裝開啟記憶體保護區 SDK？**
   
如需有關如何在 Azure 或內部部署電腦上安裝 OE SDK 的指示，請遵循[開啟記憶體保護區 SDK GitHub](https://github.com/openenclave/openenclave) 上的指示。
     
您也可以查看開啟記憶體保護區 SDK GitHub 以取得作業系統特定的安裝指示：
   - [在 Windows 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [在 Ubuntu 18.04 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [在 Ubuntu 16.04 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
