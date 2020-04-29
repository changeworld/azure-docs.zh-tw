---
title: Azure 應用程式受控應用程式供應專案發行指南 |Azure Marketplace
description: 本文說明在 Azure Marketplace 中發佈受控應用程式的需求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084867"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure 應用程式：受控應用程式供應專案發佈需求

本文說明受控應用程式供應專案類型的需求，這是在 Azure Marketplace 中發佈 Azure 應用程式供應專案的一種方式。 受控應用程式是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者會看到的呼籲行動是「立即取得」。

當需要下列條件時，請使用受控應用程式供應專案類型：

- 您可以使用 VM 或整個 IaaS 解決方案，為您的客戶部署訂用帳戶式解決方案。
- 您或您的客戶要求由合作夥伴管理解決方案。

>[!NOTE]
>例如，合作夥伴可能是 SI 或受控服務提供者 (MSP)。  

## <a name="managed-application-offer"></a>受控應用程式供應項目

|需求 |詳細資料  |
|---------|---------|
|已部署至客戶的 Azure 訂用帳戶 | 受管理的應用程式必須部署在客戶的訂用帳戶中，而且可以由協力廠商管理。 |
|計費和計量    |  系統會在客戶的 Azure 訂用帳戶中布建資源。 隨用隨付（PAYGO）虛擬機器將透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶（PAYGO）計費。 <br> 在自備授權的情況下，雖然 Microsoft 會向客戶訂用帳戶中產生的基礎結構成本收費，但您會直接向客戶收取軟體授權費用。        |
|Azure 相容的虛擬硬碟 (VHD)    |   VM 必須建置在 Windows 或 Linux 上。<ul> <ul> <li>如需建立 Linux VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>如需建立 Windows VHD 的詳細資訊，請參閱[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案。</li> </ul> |

>[!NOTE]
> 受控應用程式必須可透過 Marketplace 部署。 如果與客戶的交流是您的關注焦點，則在啟用潛在客戶分享之後，您應該與感興趣的客戶連絡。  

>[!Note]
>雲端解決方案提供者（CSP）合作夥伴頻道加入宣告現已推出。 如需透過 Microsoft CSP 合作夥伴管道行銷供應專案的詳細資訊，請參閱[雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

- 如果您尚未這麼做，請[瞭解](https://azuremarketplace.microsoft.com/sell)Azure Marketplace。
- 登[入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以建立或完成您的供應專案。
- [建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案以取得詳細資訊。
