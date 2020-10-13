---
title: Azure 應用程式受控應用程式供應專案發佈指南-Azure Marketplace
description: 本文說明在 Azure Marketplace 中發佈受控應用程式的需求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 10ca7602dd3cf5a80e371ceda845977bcd8f218a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484234"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 受控應用程式發佈指南

Azure *受控應用程式* 供應專案是在 Azure Marketplace 中發佈 azure 應用程式的一種方式。 受控應用程式是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者看到的清單選項是 *立即取得*。

本文說明受控應用程式供應專案類型的需求。

在下列情況下，請使用受控應用程式供應專案類型：

- 您要為客戶部署以訂用帳戶為基礎的解決方案，方法是使用虛擬機器 (VM) 或整個基礎結構即服務 (IaaS) 型解決方案。
- 您或您的客戶需要由合作夥伴管理解決方案。

>[!NOTE]
>例如，夥伴可以是系統整合者或受控服務提供者 (MSP) 。  

## <a name="managed-application-offer-requirements"></a>受控應用程式供應專案需求

|需求 |詳細資料  |
|---------|---------|
|Azure 訂用帳戶 | 受控應用程式必須部署至客戶的訂用帳戶，但可由協力廠商管理。 |
|計費和計量    |  這些資源會在客戶的 Azure 訂用帳戶中提供。 使用隨用隨付付款模型的 Vm 會透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。 <br><br> 針對自備授權 Vm，Microsoft 會針對客戶訂用帳戶中所產生的任何基礎結構費用進行計費，但您會向客戶收取軟體授權費用。        |
|Azure 相容的虛擬硬碟 (VHD)     |   VM 必須建置在 Windows 或 Linux 上。<br><br>如需建立 Linux VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](../virtual-machines/linux/endorsed-distros.md)。<br><br>如需建立 Windows VHD 的詳細資訊，請參閱 [建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案。 |

---

> [!NOTE]
> 受控應用程式必須可透過 Azure Marketplace 來部署。 如果客戶通訊有問題，請在您啟用潛在客戶共用之後，觸及感興趣的客戶。  

> [!Note]
> 現在已可使用雲端解決方案提供者 (CSP) 合作夥伴通道加入宣告。 如需透過 Microsoft CSP 合作夥伴通路行銷供應專案的詳細資訊，請參閱 [雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell) (若您尚不了解)。

若要註冊並開始使用合作夥伴中心：

- [登入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立或完成您的供應項目。
- 如需詳細資訊，請參閱 [建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md) 專案。
