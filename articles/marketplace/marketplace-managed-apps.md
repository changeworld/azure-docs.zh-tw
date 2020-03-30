---
title: Azure 應用程式受控應用程式供應項目發佈指南
description: 本文說明在 Marketplace 中發佈受控應用程式的需求
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288405"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure 應用程式：受控應用程式供應項目發佈指南

受控應用程式是在 Marketplace 中發佈解決方案的主要方式之一。 您可以使用本指南來了解這項供應項目的需求。 

這些交易供應項目是透過 Marketplace 來部署並計費的。 使用者會看到的呼籲行動是「立即取得」。

必須符合下列條件時，請使用「Azure 應用程式：受控應用程式」供應項目類型：
- 您使用 VM 或整個 IaaS 型解決方案為客戶部署訂用帳戶型解決方案。
- 您或您的客戶要求由合作夥伴管理解決方案。

>[!NOTE]
>例如，合作夥伴可能是 SI 或受控服務提供者 (MSP)。  

## <a name="managed-application-offer"></a>受控應用程式供應項目

|需求 |詳細資料  |
|---------|---------|
|部署到客戶的 Azure 訂閱 | 託管應用必須部署在客戶的訂閱中，並且可以由協力廠商管理。 | 
|計費和計量    |  資源將在客戶的 Azure 訂閱中預配。 即用即付 （PAYGO） 虛擬機器將通過 Microsoft 與客戶進行交易，通過客戶的 Azure 訂閱 （PAYGO） 計費。 <br> 在自帶許可證的情況下，雖然 Microsoft 將向客戶訂閱中產生的基礎結構成本收費，但您將直接向客戶支付軟體許可費用。        |
|Azure 相容的虛擬硬碟 (VHD)    |   VM 必須建置在 Windows 或 Linux 上。<ul> <ul> <li>如需建立 Linux VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>如需建立 Windows VHD 的詳細資訊，請參閱[建立與 Azure 相容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。</li> </ul> |

>[!NOTE]
> 受控應用程式必須可透過 Marketplace 部署。 如果與客戶的交流是您的關注焦點，則在啟用潛在客戶分享之後，您應該與感興趣的客戶連絡。  

>[!Note]
>雲解決方案供應商 （CSP） 合作夥伴管道加入宣告現已可用。  有關通過 Microsoft CSP 合作夥伴管道行銷產品/服務的更多資訊[，請參閱雲解決方案供應商](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟
如果您還沒有這麼做，請 

- 在市集中[註冊](https://azuremarketplace.microsoft.com/sell)。

如果您已註冊，且要建立新供應項目或使用現有供應項目，請

- [登錄到雲合作夥伴門戶](https://cloudpartner.azure.com)以創建或完成您的產品/服務。
