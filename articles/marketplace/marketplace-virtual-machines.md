---
title: Azure Marketplace 上的虛擬機器供應專案發佈指南
description: 本文說明發行虛擬機器的需求，以及要從 Azure Marketplace 部署的軟體免費試用版。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 09/04/2020
ms.openlocfilehash: cc6b040731cbeb7271d7a7c0de1c32fa2d007013
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484183"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>虛擬機器供應專案發佈指南

發佈虛擬機器 (VM) 映射是將解決方案發佈至 Azure Marketplace 的主要方式之一。 您可以使用本指南來瞭解這類供應專案的需求。 

虛擬機器供應專案是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者看到的清單選項是 *立即取得*。

## <a name="free-trial"></a>免費試用 

若要安排使用者測試您的供應專案，請在使用「自備授權」 (BYOL) 計費模型時，存取有限期限的軟體授權。 

## <a name="test-drive"></a>試用產品

您可以透過基礎結構即服務 (IaaS) 或軟體即服務 (SaaS) 應用程式，部署一部或多部虛擬機器。 試用產品發佈選項的優點是，自動設定虛擬機器或整個解決方案，由合作夥伴託管的引導式導覽來 *推動* 。 試用產品可讓您的客戶評估 Vm，而不需額外付費。 客戶不必是現有的 Azure 客戶，也能與試用版體驗進行互動。 

如需試用產品的詳細資訊，請參閱 [什麼是試用產品？](what-is-test-drive.md)

|需求  |詳細資料 |
|---------|---------|
| 您有 Azure Marketplace 的應用程式   |  透過 IaaS 或 SaaS 的一或多部虛擬機器。      |

## <a name="interactive-demo"></a>互動式示範

有了這項供應專案，您就可以使用互動式示範，為客戶提供您解決方案的引導式體驗。 互動式示範發佈選項的優點是您可以提供試用版體驗，而不需要提供複雜解決方案的複雜設定。 

## <a name="virtual-machine-offer"></a>虛擬機器供應項目

當您將虛擬裝置部署到與客戶相關聯的訂用帳戶時，請使用 *虛擬機器* 供應專案類型。 使用隨用隨付或自備授權 (BYOL) 授權模型，即可完全啟用商務的 Vm。 Microsoft 會主控商務交易，並代表您向客戶收費。 您獲得的好處是，可以使用客戶與 Microsoft 之間慣用的付款關係，包括任何 Enterprise 合約。

> [!NOTE]
> 目前，與 Enterprise 合約相關聯的貨幣承諾可用於 VM 的 Azure 使用量，但不能用於您的軟體授權費用。  
> 
> [!NOTE]
> 您可以將映射和定價以私人供應專案的形式發佈，以將 VM 的探索和部署限制為一組特定的客戶。 私用供應專案可讓您為最接近的客戶建立專屬的供應專案，並提供自訂軟體和條款。 這些自訂條款可讓您突顯各式各樣的案例，包括具有特殊定價和條款的實地主導交易，以及限制版軟體的優先存取權。 私人供應專案可讓您藉由建立具有這些詳細資料的新方案，將特定定價或產品提供給一組有限的客戶。  
>
> 如需詳細資訊，請參閱 [Azure Marketplace 上的私](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)用供應專案。  

| 需求 | 詳細資料 |  
|:--- |:--- | 
| 計費和計量 | 您的 VM 必須支援 BYOL 或隨用隨付的每月計費。 |  
| Azure 相容的虛擬硬碟 (VHD) | VM 必須建置在 Windows 或 Linux 上。 如需建立 VHD 的詳細資訊，請參閱： <ul> <li>Linux[發行版本背書于 Azure](../virtual-machines/linux/endorsed-distros.md) (linux vhd) 。</li> <li>[建立與 Azure 相容的 vhd](./partner-center-portal/azure-vm-create-offer.md) (，以) Windows vhd。</li> </ul> |  

>[!Note]
>現在已可使用雲端解決方案提供者 (CSP) 合作夥伴通道加入宣告。 如需透過 Microsoft CSP 合作夥伴通路行銷供應專案的詳細資訊，請參閱 [雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell) (若您尚不了解)。

若要註冊並開始使用合作夥伴中心：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需詳細資訊，請參閱 [建立虛擬機器供應](./partner-center-portal/azure-vm-create-offer.md) 專案。
