---
title: Azure 機密運算常見問題
description: Azure 機密運算常見問題的解答。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189440"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 機密計算的常見問題

本文提供有關[在 Azure 上執行機密運算工作負載](overview.md)的一些常見問題的解答。

若本文中未提及您的 Azure 問題，請造訪 MSDN 上的 Azure 論壇[和 Stack Overflow](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇中張貼您的問題，或張貼到[ @AzureSupport Twitter 上的](https://twitter.com/AzureSupport)。 您也可以提交 Azure 支援要求。 若要提交支援要求，請在 [ [Azure 支援] 頁面](https://azure.microsoft.com/support/options/)上，選取 [取得支援]。

## <a name="confidential-computing-virtual-machines"></a>機密計算虛擬機器<a id="vm-faq"></a>

1. **如何開始部署 DCsv2 系列 Vm？**

   以下是一些您可以部署 DCsv2 VM 的方法：
   - 使用[Azure Resource Manager 範本](../virtual-machines/windows/template-description.md)
   - 從[Azure 入口網站](https://portal.azure.com/#create/hub)
   - 在[Azure 機密計算（虛擬機器）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) marketplace 解決方案範本中。 Marketplace 解決方案範本可協助將客戶限制為支援的案例（區域、映射、可用性、磁片加密）。 

1. **所有 OS 映射都會使用 Azure 機密運算嗎？**

   不可以。 虛擬機器只能部署在第2代虛擬機器上。 我們為 Ubuntu Server 18.04、Ubuntu Server 16.04 和 Windows Server 2016 Datacenter 提供第2代支援。 深入瞭解[Linux](../virtual-machines/linux/generation-2.md)和[Windows](../virtual-machines/windows/generation-2.md)上的 Gen 2 vm

1. **DCsv2 虛擬機器在入口網站中呈現灰色，我無法選取其中一個**

   根據 VM 旁的資訊反升，需要採取不同的動作：
    -   **UnsupportedGeneration**：將虛擬機器映射的世代變更為 "Gen2"。
    -   **NotAvailableForSubscription** ：您的訂用帳戶尚無法使用此區域。 選取可用的區域。
    -   **InsufficientQuota**：[建立支援要求以增加您的配額](../azure-portal/supportability/per-vm-quota-requests.md)。 免費試用訂用帳戶沒有機密計算 Vm 的配額。 

1. **當我嘗試在入口網站大小選取器中搜尋時，不會顯示 DCsv2 虛擬機器**

   請確定您已選取可用的區域。 此外，也請確定您在 [大小] 選取器中選取 [清除所有篩選準則]。 

1. **DCsv2 系列和 DC 系列 Vm 之間有何差異？**

   DC 系列 Vm 會在具有 Intel SGX 的舊版6核心 Intel 處理器上執行。 這些記憶體的總計較少、較少的 EPC （記憶體保護區頁面主機殼）記憶體，且可在較少的區域中使用。 這些 Vm 僅適用于美國東部和西歐提供兩種大小： Standard_DC2s 和 Standard_DC4s。 它們不會正式推出，而且只能部署在[機密計算 DC 系列 VM [預覽]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace 實例

1. **DCsv2 的虛擬機器是否可全域使用？**

   否，這些虛擬機器僅適用于選取的區域。 查看 [[依區域的產品] 頁面](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)，以取得最新的可用區域。 

1. **如何? 安裝 Open 記憶體保護區 SDK？**
   
   如需有關如何在 Azure 或內部部署電腦上安裝 OE SDK 的指示，請遵循[Open 記憶體保護區 SDK GitHub](https://github.com/openenclave/openenclave)上的指示。
     
   您也可以前往 Open 記憶體保護區 SDK GitHub 以取得作業系統特定的安裝指示：
     - [在 Windows 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [在 Ubuntu 18.04 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [在 Ubuntu 16.04 上安裝 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
