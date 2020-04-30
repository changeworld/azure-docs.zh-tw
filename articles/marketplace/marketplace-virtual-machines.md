---
title: Azure Marketplace 的虛擬機器供應項目發佈指南
description: 本文說明發佈虛擬機器的需求，以及從 Marketplace 部署免費試用軟體的需求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687505"
---
# <a name="virtual-machine-offer-publishing-guide"></a>虛擬機器供應項目發佈指南

虛擬機器映像是在 Azure Marketplace 中發佈解決方案的其中一個主要方法。 您可以使用本指南來了解這項供應項目的需求。 

這些交易供應項目是透過 Marketplace 來部署並計費的。 使用者會看到的呼籲行動是「立即取得」。

## <a name="free-trial"></a>免費試用 

您可以讓使用者在使用自備授權 (BYOL) 計費模型時，存取有限期間的軟體授權，以便測試您的供應項目。 

## <a name="test-drive"></a>試用產品

您會透過基礎結構即服務 (IaaS) 或軟體即服務 (SaaS) 應用程式部署一部或多部虛擬機器。 試用產品發佈選項的優點是，可在合作夥伴所主持的引導式導覽帶領下，以自動化方式佈建虛擬機器或整個解決方案。 試用產品可為您的客戶提供評估，而無須額外收費。 客戶不必是現有的 Azure 客戶，即可參與試用體驗。 

請經由 [amp-testdrive](mailto:amp-testdrive@microsoft.com) 與我們連絡，以開始使用。 

|需求  |詳細資料 |
|---------|---------|
| 您有 Marketplace 應用程式   |    透過 IaaS 或 SaaS 的一或多部虛擬機器。      |

## <a name="interactive-demo"></a>互動式示範

您利用互動式示範，為客戶提供引導式的解決方案體驗。 互動式示範發佈選項的優點是，無須進行複雜解決方案的複雜佈建，即可提供試用版體驗。 

## <a name="virtual-machine-offer"></a>虛擬機器供應項目

當您要將虛擬設備部署到與客戶相關的訂用帳戶時，請使用「虛擬機器」供應項目類型。 使用「隨用隨付」或「自備授權」(BYOL) 授權模型，即可啟用完整的 VM 商務功能。 Microsoft 會主控商務交易，並代表您向客戶收費。 您獲得的好處是，可以使用客戶與 Microsoft 之間慣用的付款關係，包括任何 Enterprise 合約。

> [!NOTE]
> 目前與 Enterprise 合約相關的承諾用量金額可用於您 VM 的 Azure 使用量，但不能用於您的軟體授權費用。  
> 
> [!NOTE]
> 若將映像和定價當作私人供應項目來發佈，就可以只讓一組特定的客戶探索和部署您的 VM。 私人供應項目可讓您為最親密的客戶建立專屬的供應項目，以及提供自訂軟體和條款。 這些自訂條款可讓您突顯各式各樣的案例，包括具有特殊定價和條款的實地主導交易，以及限制版軟體的優先存取權。 私人供應項目可讓您建立具有特定定價或產品詳細資料的新 SKU，為一組有限的客戶提供該特定的定價或產品。  
> *   如需有關「私人供應項目」的詳細資訊，請瀏覽「Azure Marketplace 上的私人供應項目」頁面：[azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)。  

| 需求 | 詳細資料 |  
|:--- |:--- | 
| 計費和計量 | 您的 VM 必須支援 BYOL 或每月計費「隨用隨付」。 |  
| Azure 相容的虛擬硬碟 (VHD) | VM 必須建置在 Windows 或 Linux 上。 <ul> <li>如需建立 Linux VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>如需建立 Windows VHD 的詳細資訊，請參閱[建立與 Azure 相容的 VHD](./partner-center-portal/azure-vm-create-offer.md)。</li> </ul> |  

>[!Note]
>雲端解決方案提供者（CSP）合作夥伴頻道加入宣告現已推出。  如需透過 Microsoft CSP 合作夥伴頻道行銷供應專案的詳細資訊，請參閱[雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

如果您還沒有這麼做，請 

- [瞭解](https://azuremarketplace.microsoft.com/sell)marketplace。

如果您已註冊，且要建立新供應項目或使用現有供應項目，請

- 登[入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以建立或完成您的供應專案。
- 如需詳細資訊，請參閱[建立虛擬機器供應](./partner-center-portal/azure-vm-create-offer.md)專案。
