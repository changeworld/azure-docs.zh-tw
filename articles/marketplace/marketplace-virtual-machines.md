---
title: Azure Marketplace 上的虛擬機器供應專案發佈指南
description: 本文說明發佈虛擬機器的需求，以及要從 Azure Marketplace 部署的軟體免費試用版。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: dac67c6dae801d11866f7c4978a050a0cb2fa1dd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065646"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>虛擬機器供應專案發佈指南

發佈虛擬機器（VM）映射是將解決方案發佈至 Azure Marketplace 的主要方式之一。 使用本指南來瞭解這類供應專案的需求。 

虛擬機器供應專案是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者所看到的動作呼叫會*立即取得*。

## <a name="free-trial"></a>免費試用 

若要安排使用者測試您的供應專案，請在使用自備授權（BYOL）計費模型時存取有限期限的軟體授權。 

## <a name="test-drive"></a>試用產品

您可以透過基礎結構即服務（IaaS）或軟體即服務（SaaS）應用程式來部署一或多個虛擬機器。 *試用*產品發佈選項的優點是自動設定虛擬機器或整個解決方案，並由合作夥伴主控的導覽。 試用產品可讓您的客戶評估 Vm，而不需要額外付費。 客戶不需要是現有的 Azure 客戶，即可與試用版體驗互動。 

若要開始使用，請在[試用](mailto:amp-testdrive@microsoft.com)時透過電子郵件與我們聯繫。 

|需求  |詳細資料 |
|---------|---------|
| 您有 Azure Marketplace 應用程式   |  透過 IaaS 或 SaaS 的一或多部虛擬機器。      |

## <a name="interactive-demo"></a>互動式示範

透過這項供應專案，您可以使用互動式示範，為客戶提供您解決方案的引導式體驗。 互動式示範發佈選項的優點是，您可以提供試用版體驗，而不需要為複雜的解決方案提供複雜的設定。 

## <a name="virtual-machine-offer"></a>虛擬機器供應項目

當您將虛擬裝置部署到與客戶相關聯的訂用帳戶時，請使用*虛擬機器*供應專案類型。 Vm 具備全商務功能，使用隨用隨付或自備授權（BYOL）授權模型。 Microsoft 會主控商務交易，並代表您向客戶收費。 您獲得的好處是，可以使用客戶與 Microsoft 之間慣用的付款關係，包括任何 Enterprise 合約。

> [!NOTE]
> 此時，與 Enterprise 合約相關聯的承諾用量可用於您 VM 的 Azure 使用量，但不能用於您的軟體授權費用。  
> 
> [!NOTE]
> 您可以將映射和定價發佈為私人供應專案，以將 VM 的探索和部署限制為一組特定的客戶。 私用供應專案可讓您為最接近的客戶建立專屬的供應專案，並提供自訂的軟體和條款。 這些自訂條款可讓您突顯各式各樣的案例，包括具有特殊定價和條款的實地主導交易，以及限制版軟體的優先存取權。 私用供應專案可讓您藉由使用這些詳細資料建立新的方案，為一組有限的客戶提供特定的定價或產品。  
>
> 如需詳細資訊，請參閱[Azure Marketplace 上的私](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)用供應專案。  

| 需求 | 詳細資料 |  
|:--- |:--- | 
| 計費和計量 | 您的 VM 必須支援 BYOL 或隨用隨付每月計費。 |  
| Azure 相容的虛擬硬碟 (VHD) | VM 必須建置在 Windows 或 Linux 上。 如需建立 VHD 的詳細資訊，請參閱： <ul> <li>[Azure 上背書的 linux](../virtual-machines/linux/endorsed-distros.md)散發套件（適用于 linux vhd）。</li> <li>[建立與 Azure 相容的 vhd](./partner-center-portal/azure-vm-create-offer.md) （適用于 Windows vhd）。</li> </ul> |  

>[!Note]
>雲端解決方案提供者（CSP）合作夥伴頻道加入宣告現已推出。 如需透過 Microsoft CSP 合作夥伴頻道行銷供應專案的詳細資訊，請參閱[雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell) (若您尚不了解)。

若要註冊並開始使用合作夥伴中心：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需詳細資訊，請參閱[建立虛擬機器供應](./partner-center-portal/azure-vm-create-offer.md)專案。
