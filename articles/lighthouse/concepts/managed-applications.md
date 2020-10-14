---
title: Azure Lighthouse 和 Azure 受控應用程式
description: 瞭解 Azure Lighthouse 和 Azure 受控應用程式如何協助啟用不同的案例，以及如何搭配使用它們。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 144917716da38cc99078f663cc2f4cb8ba0199c8
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014982"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 和 Azure 受控應用程式

Azure 受控應用程式和 Azure Lighthouse 的運作方式是讓服務提供者存取位於客戶租使用者中的資源。 瞭解它們的運作方式，以及它們可協助啟用的案例，以及它們如何一起使用的差異，可能會有所説明。

> [!TIP]
> 雖然我們會在本主題中參考服務提供者和客戶，但 [管理多個](enterprise.md) 租使用者的企業可以使用相同的程式和工具。

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>比較 Azure Lighthouse 和 Azure 受控應用程式

### <a name="azure-lighthouse"></a>Azure Lighthouse

使用 [Azure Lighthouse](../overview.md)，服務提供者可以直接在客戶的訂用帳戶 (或資源群組) 上執行廣泛的管理工作。 這項存取是透過邏輯投射來達成，讓服務提供者能夠登入自己的租使用者，並存取屬於客戶租使用者的資源。 客戶可以決定要將哪些訂用帳戶或資源群組委派給服務提供者，而客戶會維持這些資源的完整存取權。 他們也可以隨時移除服務提供者的存取權。

若要使用 Azure Lighthouse，客戶可透過[部署 ARM 範本](../how-to/onboard-customer.md)或透過[Azure Marketplace 的受控服務供應](managed-services-offers.md)專案，上線[Azure 委派的資源管理](azure-delegated-resource-management.md)。 您可以 [連結您的合作夥伴識別碼](../how-to/partner-earned-credit.md)，以追蹤對客戶合作的影響。

當服務提供者持續為客戶執行管理工作時，通常會使用 Azure Lighthouse。

### <a name="azure-managed-applications"></a>Azure 受控應用程式

[Azure 受控應用程式](../../azure-resource-manager/managed-applications/overview.md) 可讓服務提供者或 ISV 提供雲端解決方案，方便客戶在自己的訂用帳戶中部署和使用。

在受控應用程式中，應用程式所使用的資源會結合在一起，並部署到由發行者管理的資源群組。 此資源群組存在於客戶的訂用帳戶中，但發行者租使用者中的身分識別有其存取權。 ISV 會繼續管理及維護受控應用程式，而客戶無法直接存取其資源群組中的工作，或對其資源的任何存取權。

受控應用程式支援自訂的 [Azure 入口網站體驗](../../azure-resource-manager/managed-applications/concepts-view-definition.md) 以及 [與自訂提供者的整合](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)。 這些選項可以用來提供更自訂和整合的體驗，讓客戶更容易執行一些管理工作。

受控應用程式可以 [發佈至 Azure Marketplace](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)，可作為特定客戶使用的私人供應專案，或作為多個客戶可購買的公用供應專案。 您也可以將 [受控應用程式發佈至您的服務類別目錄](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)，以將它們傳遞給您組織中的使用者。 您可以使用 ARM 範本部署服務類別目錄和 Marketplace 實例，其中可包含商業市場夥伴的唯一識別碼，以追蹤 [客戶使用](../../marketplace/azure-partner-customer-usage-attribution.md)方式的屬性。

Azure 受控應用程式通常用於特定客戶的需求，可透過完全由服務提供者管理的現成解決方案來達成。

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>一起使用 Azure Lighthouse 和 Azure 受控應用程式

雖然 Azure Lighthouse 和 Azure 受控應用程式會使用不同的存取機制來達成不同的目標，但在某些情況下，服務提供者可以將這兩個服務提供者與相同的客戶一起使用。

例如，客戶可能會想要透過 Azure Lighthouse 提供服務提供者所提供的受控服務，讓他們可以看到合作夥伴的動作，以及持續控制其委派的訂用帳戶。 不過，服務提供者可能不希望客戶存取將儲存在客戶租使用者中的特定資源，或允許這些資源的任何自訂動作。 為了符合這些目標，服務提供者可以將私人供應專案發佈為受控應用程式。 受控應用程式可以包含部署在客戶租使用者中的資源群組，但客戶無法直接存取該資源群組。

客戶也可能對來自多個服務提供者的受控應用程式感興趣，無論他們是否也透過 Azure Lighthouse 從任何服務提供者使用受控服務。 此外，雲端解決方案提供者 (CSP) 計畫中的合作夥伴，可以將其他 Isv 發佈的特定受控應用程式，轉銷給透過 Azure Lighthouse 所支援的客戶。 使用廣泛的選項時，服務提供者可以選擇適當的平衡以符合客戶的需求，並在適當時限制資源的存取權。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 受控應用程式](../../azure-resource-manager/managed-applications/overview.md)。
- 瞭解如何將 [訂用帳戶上架至 Azure Lighthouse](../how-to/onboard-customer.md)。