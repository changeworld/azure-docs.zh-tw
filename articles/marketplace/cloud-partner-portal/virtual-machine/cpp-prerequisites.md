---
title: 微軟 Azure 的虛擬機器先決條件 |Azure 應用商店
description: 若要發佈 VM 供應項目至 Microsoft Azure Marketplace，此為先決條件清單。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277611"
---
# <a name="virtual-machine-prerequisites"></a>虛擬機器先決條件

本文列出了在將 VM 產品/服務發佈到[Azure 應用商店](https://azuremarketplace.microsoft.com/marketplace/)之前必須滿足的技術要求和業務需求。  如果您尚未這樣做，請查看[虛擬機器產品供應發佈指南](../../marketplace-virtual-machines.md)。


## <a name="technical-requirements"></a>技術需求

要發佈一個虛擬機器 (VM) 解決方案的技術先決條件相當清楚直接：

- 您必須擁有使用中的 Azure 帳戶。 如果沒有帳戶，您可以在[Microsoft Azure 網站](https://azure.microsoft.com)註冊申請。  
- 您必須設定好支援 Windows 或 Linux 虛擬機器的開發環境。  如需詳細資訊，請參考相關 VM 文件網站：
    - [Linux VM 文件](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows VM 文件](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>商業需求

商業需求包括程序、 契約，和法律責任： 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- 您必須是已註冊的雲端 Marketplace 發行者。  如果您尚未註冊，請遵循下列文章中的步驟：[成為雲端 Marketplace 發行者](https://docs.microsoft.com/azure/marketplace/become-publisher)。

    > [!NOTE]
    > 您需要使用與註冊 Microsoft 開發人員中心相同的帳戶來登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com)。
    > 您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。 帳戶不應僅供單一服務或供應項目使用。
    
- 您的公司（或其子公司）必須位於 Azure 應用商店支援的從國家/地區銷售。  有關這些國家/地區的當前清單，請參閱 Microsoft [Azure 應用商店參與策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。
- 您的產品授權，必須與 Azure Marketplace 支援的計費模式相容。  有關詳細資訊，請參閱[Azure 應用商店中的計費選項](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace)。 
- 您必須以合乎商業行為的方式，負責為客戶提供技術支援。 此支援可以免費、收費或透過社群提供。
- 您必須負責為您的軟體和任何第三方廠商相依性進行授權。
- 為了使您的供應項目可列於 Microsoft Azure Marketplace 和 Microsoft Azure 入口網站中，您提供的內容必須符合標準。 <!-- TD: Meaning/links? -->
- 您必須同意[Microsoft Azure 應用商店參與策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)和發行者協定的條款。
- 您必須遵守[微軟 Azure 網站使用條款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[微軟隱私權聲明](https://privacy.microsoft.com/privacystatement)和[微軟 Azure 認證計畫協定](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。


## <a name="next-steps"></a>後續步驟

滿足這些先決條件後，可以創建 VM[產品/服務](./cpp-create-offer.md)。
