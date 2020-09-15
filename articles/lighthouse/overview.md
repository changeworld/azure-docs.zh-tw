---
title: 什麼是 Azure Lighthouse ？
description: Azure Lighthouse 能讓服務提供者以高度自動化且有效率的方式為其客戶傳遞受控服務。
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 22bec7ec8944a11ce0cfdf51776f1f193a1aedaa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488808"
---
# <a name="what-is-azure-lighthouse"></a>什麼是 Azure Lighthouse ？

Azure Lighthouse 支援跨租用戶和多租用戶管理，允許更高的自動化、可擴縮性以及跨資源和租用戶的強化治理。 透過 Azure Lighthouse，服務提供者可以使用內建於 Azure 平台、完整且強大的管理工具來傳遞受控服務。 此供應項目也能為跨多個租用戶管理資源的企業 IT 組織帶來好處。

![Azure Lighthouse 的概觀圖表](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>優點

Azure Lighthouse 可協助您以符合成本效益且有效的方式，建置並傳遞受控服務。 其優點包括：

- **大規模管理**：客戶業務開發和用來管理客戶資源的生命週期作業，已變得更加簡易且可調整。 現有的 API、管理工具和工作流程可以與委派的資源搭配使用，不論其所在區域。
- **客戶能取得更高的可見度和精確度**：客戶將能對您的動作取得更高的可見度，以及針對其所委派的管理範圍取得更精確的控制 (包括完全移除存取權)，同時將能保留您的 IP。
- **完整且整合的平台工具**：我們的工具體驗能解決重要的服務提供者案例，其中包括多個授權模型 (例如 EA、CSP 和隨用隨付)。 新的功能將能搭配現有的工具和 API、授權模型，以及如[雲端解決方案提供者計畫 (CSP)](/partner-center/csp-overview) 之類的合作夥伴計畫使用。 Azure Lighthouse 可整合到您的現有工作流程與應用程式中，而且您可以[連結合作夥伴識別碼](./how-to/partner-earned-credit.md)來追蹤對客戶業務開發的影響，並接收合作夥伴獲得的點數。

使用 Azure Lighthouse 來管理 Azure 資源並不會產生額外的費用。 任何 Azure 客戶或合作夥伴都可以使用 Azure Lighthouse。

## <a name="capabilities"></a>功能

Azure Lighthouse 包含多種方法，可協助簡化業務開發及管理：

- **Azure 委派的資源管理**：從您自己的租用戶內安全地管理客戶的 Azure 資源，而不需要切換內容和控制平面。 訂用帳戶和資源群組可以委派給管理租用戶中的指定使用者和角色，並能夠視需要移除存取權。 如需詳細資訊，請參閱 [Azure 委派的資源管理](concepts/azure-delegated-resource-management.md) \(英文\)。
- **新的 Azure 入口網站體驗**：在 Azure 入口網站的[**我的客戶**頁面](./how-to/view-manage-customers.md)中檢視跨租用戶資訊。 相對應的[**服務提供者**頁面](how-to/view-manage-service-providers.md)能讓客戶檢視及管理服務提供者存取權。
- **Azure Resource Manager 範本**：我們的範本可協助您執行跨租用戶管理工作，並將委派的客戶資源上線。 如需詳細資訊，請參閱我們的[範例存放庫](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) \(英文\)，以及[讓客戶在 Azure Lighthouse 中上線](how-to/onboard-customer.md)。
- **Azure Marketplace 中的受控服務供應項目**：透過私人或公用供應項目為客戶提供服務，並讓客戶自動上線至 Azure Lighthouse。 如需詳細資訊，請參閱 [Azure Marketplace 中的受控服務供應項目](concepts/managed-services-offers.md)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](concepts/azure-delegated-resource-management.md)。
- 探索[啟用跨租用戶管理體驗](concepts/cross-tenant-management-experience.md)。
- 請參閱如何[在企業內使用 Azure Lighthouse](concepts/enterprise.md)。
