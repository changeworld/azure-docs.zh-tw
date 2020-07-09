---
title: Azure Lighthouse 和 Azure 受控應用程式
description: Azure 燈塔和 Azure 受控應用程式 .。。
ms.date: 05/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3e1477de18b24cf5099cd4479a82169e3ecada26
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121600"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 和 Azure 受控應用程式

Azure 受控應用程式和 Azure 燈塔的工作方式，是讓服務提供者存取位於客戶租使用者中的資源。 瞭解其運作方式的差異，以及它們有助於啟用的案例，以及如何搭配使用它們，可能會很有説明。

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>比較 Azure 燈塔和 Azure 受控應用程式

### <a name="azure-lighthouse"></a>Azure Lighthouse

透過[Azure 燈塔](../overview.md)，服務提供者可直接在客戶的訂用帳戶（或資源群組）上執行各種管理工作。 這項存取是透過邏輯投射來達成，讓服務提供者能夠登入自己的租使用者，並存取屬於客戶租使用者的資源。 客戶可以判斷要委派給服務提供者的訂用帳戶或資源群組，而客戶會維護這些資源的完整存取權。 他們也可以隨時移除服務提供者的存取權。

若要使用 Azure 燈塔，您可以透過[部署 ARM 範本](../how-to/onboard-customer.md)或[Azure Marketplace 中的受控服務供應](managed-services-offers.md)專案，上架[azure 委派資源管理](azure-delegated-resource-management.md)的客戶。 您可以藉由[連結合作夥伴識別碼](../../cost-management-billing/manage/link-partner-id.md)來追蹤對客戶業務的影響。

當服務提供者會持續為客戶執行管理工作時，通常會使用 Azure 燈塔。

### <a name="azure-managed-applications"></a>Azure 受控應用程式

[Azure 受控應用程式](../../azure-resource-manager/managed-applications/overview.md)可讓服務提供者或 ISV 提供雲端解決方案，讓客戶能夠輕鬆地在自己的訂用帳戶中部署和使用。

在受控應用程式中，應用程式所使用的資源會配套在一起，並部署到由發行者管理的資源群組。 此資源群組存在於客戶的訂用帳戶中，但發行者租使用者中的身分識別有其存取權。 ISV 會繼續管理和維護受控應用程式，而客戶無法直接存取其資源群組中的工作，或存取其資源。

受控應用程式支援自訂的[Azure 入口網站體驗](../../azure-resource-manager/managed-applications/concepts-view-definition.md)，以及[與自訂提供者的整合](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)。 這些選項可用來提供更自訂和整合的體驗，讓客戶能夠更輕鬆地自行執行一些管理工作。

受控應用程式可以[發佈到 Azure Marketplace](../../azure-resource-manager/managed-applications/publish-marketplace-app.md)，做為特定客戶使用的私用供應專案，或做為多個客戶可購買的公用優惠。 您也可以將[受控應用程式發佈至您的服務類別目錄](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)，以將其傳遞給組織內的使用者。 您可以使用 ARM 範本部署服務類別目錄和 Marketplace 實例，其中可包含商業 Marketplace 合作夥伴的唯一識別碼，以追蹤[客戶使用狀況](../../marketplace/azure-partner-customer-usage-attribution.md)的屬性。

Azure 受控應用程式通常用於特定客戶需求，可透過完全由服務提供者管理的全包式解決方案來達成。

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>同時使用 Azure 燈塔和 Azure 受控應用程式

雖然 Azure 燈塔和 Azure 受控應用程式會使用不同的存取機制來達到不同的目標，但在某些情況下，服務提供者可以將這兩者與相同的客戶搭配使用。

例如，客戶可能想要透過 Azure 燈塔提供給服務提供者的受控服務，讓他們能夠看到合作夥伴的動作，並持續控制其委派的訂用帳戶。 不過，服務提供者可能不想讓客戶存取將儲存在客戶租使用者中的特定資源，或允許對這些資源執行任何自訂動作。 為了符合這些目標，服務提供者可以將私用供應專案發佈為受控應用程式。 受控應用程式可以包含部署在客戶租使用者中的資源群組，但客戶無法直接存取。

客戶可能也會對來自多個服務提供者的受控應用程式感興趣，無論他們是否也會透過 Azure 燈塔，從任何這些服務提供者使用受控服務。 此外，雲端解決方案提供者（CSP）方案中的合作夥伴可以將其他 Isv 發佈的特定受控應用程式轉售給他們透過 Azure 燈塔支援的客戶。 透過各種不同的選項，服務提供者可以選擇適當的平衡來符合客戶的需求，並在適當時限制資源的存取權。

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure 受控應用程式](../../azure-resource-manager/managed-applications/overview.md)。
- 瞭解如何將[訂用帳戶上架到 Azure 燈塔](../how-to/onboard-customer.md)。
