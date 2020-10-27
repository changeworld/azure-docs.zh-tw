---
title: 什麼是 Azure Lighthouse ？
description: Azure Lighthouse 能讓服務提供者以高度自動化且有效率的方式為其客戶傳遞受控服務。
ms.date: 10/19/2020
ms.topic: overview
ms.openlocfilehash: a76606ff48a09c0c31584882e3d2aa164ec97325
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203784"
---
# <a name="what-is-azure-lighthouse"></a>什麼是 Azure Lighthouse ？

Azure Lighthouse 支援跨租用戶和多租用戶管理，允許更高的自動化、可擴縮性以及跨資源和租用戶的強化治理。

透過 Azure Lighthouse，服務提供者可以使用內建於 Azure 平台、完整且強大的管理工具來傳遞受控服務。 客戶可以控制誰能存取其租用戶、其可以存取哪些資源，以及可以採取哪些動作。 此供應項目也能為跨多個租用戶管理資源的[企業 IT 組織](concepts/enterprise.md)帶來好處。

![Azure Lighthouse 的概觀圖表](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>優點

Azure Lighthouse 可協助服務提供者有效率地建置並傳遞受控服務。 好處包括：

- **大規模管理** ：客戶業務開發和用來管理客戶資源的生命週期作業，已變得更加簡易且可調整。 現有的 API、管理工具和工作流程可以與委派的資源搭配使用 (包括裝載於 Azure 之外的機器)，不論其所在區域為何。
- **提升客戶的可見度和控制力** ：客戶可以精確控制其所委派的管理範圍和允許的權限。 如有需要，其可以稽核服務提供者的動作並完全移除存取權。
- **完整且整合的平台工具** ：我們的工具體驗能解決重要的服務提供者案例，其中包括多個授權模型 (例如 EA、CSP 和隨用隨付)。 Azure Lighthouse 可與現有工具和 API、授權模式、[Azure 受控應用程式](concepts/managed-applications.md)和合作夥伴方案 (例如[雲端解決方案提供者方案 (CSP)](/partner-center/csp-overview)) 搭配運作。 您可以將 Azure Lighthouse 整合到現有工作流程與應用程式中，並透過[連結您的合作夥伴識別碼](./how-to/partner-earned-credit.md)來追蹤您對客戶業務開發的影響。

使用 Azure Lighthouse 來管理 Azure 資源並不會產生額外的費用。 任何 Azure 客戶或合作夥伴都可以使用 Azure Lighthouse。

## <a name="capabilities"></a>功能

Azure Lighthouse 包含多種方法，可協助簡化業務開發及管理：

- **Azure 委派的資源管理** ：[從您自己的租用戶內安全地管理客戶的 Azure 資源](concepts/azure-delegated-resource-management.md)，而不需要切換內容和控制平面。 客戶的訂用帳戶和資源群組可以委派給管理租用戶中的指定使用者和角色，並能夠視需要移除存取權。
- **新的 Azure 入口網站體驗** ：在 Azure 入口網站的 [**我的客戶** 頁面](how-to/view-manage-customers.md)中檢視跨租用戶資訊。 相對應的 [**服務提供者** 頁面](how-to/view-manage-service-providers.md)能讓客戶檢視及管理服務提供者存取權。
- **Azure Resource Manager 範本** ：使用 ARM 範本可[將委派的客戶資源上線](how-to/onboard-customer.md)並[執行跨租用戶管理工作](samples/index.md)。
- **Azure Marketplace 中的受控服務供應項目** ：透過私人或公用供應項目[為客戶提供服務](concepts/managed-services-offers.md)，並自動將其上線至 Azure Lighthouse。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](concepts/azure-delegated-resource-management.md)。
- 探索[啟用跨租用戶管理體驗](concepts/cross-tenant-management-experience.md)。
- 請參閱如何[在企業內使用 Azure Lighthouse](concepts/enterprise.md)。
