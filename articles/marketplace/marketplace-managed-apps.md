---
title: Azure 應用程式受控應用程式供應專案發佈指南-Azure Marketplace
description: 本文說明在 Azure Marketplace 中發行受控應用程式的需求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 5bd89e406c3067585595479dc1d8351e9ea7eea8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856110"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 受控應用程式的發佈指南

Azure*受控應用程式*供應專案是在 Azure Marketplace 中發佈 azure 應用程式的一種方式。 受控應用程式是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者所看到的動作呼叫會*立即取得*。

本文說明受控應用程式供應專案類型的需求。

在下列情況下，請使用受控應用程式供應專案類型：

- 您正在使用虛擬機器（VM）或整個基礎結構即服務（IaaS）型解決方案，為您的客戶部署訂用帳戶型解決方案。
- 您或您的客戶需要由合作夥伴管理解決方案。

>[!NOTE]
>例如，合作夥伴可以是系統整合者或受控服務提供者（MSP）。  

## <a name="managed-application-offer-requirements"></a>受控應用程式供應專案需求

|需求 |詳細資料  |
|---------|---------|
|Azure 訂用帳戶 | 受控應用程式必須部署至客戶的訂用帳戶，但可由協力廠商管理。 |
|計費和計量    |  這些資源會在客戶的 Azure 訂用帳戶中提供。 使用隨用隨付付款模型的 Vm 會透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。 <br><br> 對於自備授權 Vm，Microsoft 會針對客戶訂用帳戶中所產生的任何基礎結構成本計費，但您會直接向客戶收取軟體授權費用。        |
|Azure 相容的虛擬硬碟（VHD）    |   VM 必須建置在 Windows 或 Linux 上。<br><br>如需建立 Linux VHD 的詳細資訊，請參閱 [Azure 背書的 Linux 散發套件](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。<br><br>如需建立 Windows VHD 的詳細資訊，請參閱[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案。 |

---

> [!NOTE]
> 受控應用程式必須可透過 Azure Marketplace 進行部署。 如果需要考慮客戶溝通，請在啟用潛在客戶共用之後，觸及感興趣的客戶。  

> [!Note]
> 雲端解決方案提供者（CSP）合作夥伴頻道加入宣告現已推出。 如需透過 Microsoft CSP 合作夥伴管道行銷供應專案的詳細資訊，請參閱[雲端解決方案提供者](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請瞭解如何[使用 Azure Marketplace 來拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell)。

若要註冊並開始使用合作夥伴中心：

- 登[入合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以建立或完成您的供應專案。
- 如需詳細資訊，請參閱[建立 Azure 應用程式供應](./partner-center-portal/create-new-azure-apps-offer.md)專案。
