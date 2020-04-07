---
title: Azure 應用商店中的託管服務產品
description: 託管服務提供允許服務提供者向 Azure 應用商店中的客戶提供資源管理產品。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672432"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure 應用商店中的託管服務產品

本文介紹[Azure 應用商店](https://azuremarketplace.microsoft.com)中的**託管服務**產品/服務類型。 託管服務提供允許您通過使用[Azure 委派的資源管理](azure-delegated-resource-management.md)為客戶提供資源管理服務。 您可以向所有潛在客戶或僅向一個或多個特定客戶提供這些優惠。 由於您會直接向客戶收取與這些受控服務相關的費用，因此 Microsoft 不會向您收取任何費用。

## <a name="understand-managed-service-offers"></a>瞭解託管服務提供

託管服務提供簡化為 Azure 委派資源管理的入職客戶流程。 當客戶在 Azure Marketplace 中購買供應項目時，他們將能夠指定應上架的訂用帳戶和 (或) 資源群組。

之後,根據您在[雲合作夥伴門戶](https://cloudpartner.azure.com/)中創建產品/服務時定義的訪問許可權,組織中的使用者將能夠從組織的租戶中處理這些資源。 這是透過資訊清單完成，該資訊清單可指定能使用 Azure 委派資源管理來存取客戶資源的 Azure AD 使用者、群組及服務主體，以及能定義其存取層級的角色。 透過將權限指派給 Azure AD 群組，而非一系列個別使用者或應用程式帳戶，您便可以在需要變更存取權時個別地加入或移除使用者。

## <a name="public-and-private-offers"></a>公用和私人供應項目

每個受控服務供應項目都包含一或多個方案。 計劃可以是私有的,也可以是公開的。

如果您想要將供應項目限制為僅提供特定客戶使用，則可以發佈私人方案。 當您這麼做時，該方案只能針對由您提供的特定訂用帳戶識別碼購買。 如需詳細資訊，請參閱[私人供應項目](../../marketplace/private-offers.md) \(部分機器翻譯\)。

公用方案能讓您將服務推廣給新的客戶。 當您僅需要客戶租用戶的有限存取權時，這通常是較為適合的選擇。 當您與客戶建立關聯性之後，如果他們決定將額外存取權授與您的組織，您可以僅為該客戶發佈新的私人方案，或是[使用 Azure Resource Manager 範本將他們上線以取得進一步存取](../how-to/onboard-customer.md)。

如有需要，您可以在相同的供應項目中同時包含公用與私人方案。

> [!IMPORTANT]
> 計劃一旦公開發佈,您就無法將其更改為私有。 要控制哪些客戶可以接受您的報價並委派資源,請使用私人計劃。 使用公共計畫時,您無法將可用性限制為特定客戶,甚至限制特定數量的客戶(儘管如果您選擇這樣做,您可以完全停止銷售計劃)。 只有在發佈產品/服務時,在**角色定義**中包含「**授權」** 設定為[「託管服務註冊分配刪除角色」時](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role),才能在客戶接受產品/服務後取消對[委派的存取權限](../how-to/onboard-customer.md#remove-access-to-a-delegation)。 您也可以聯絡客戶,要求他們[移除您的存取權限](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>發佈受控服務供應項目

若要了解如何發佈受控服務供應項目，請參閱[將受控服務供應項目發佈到 Azure Marketplace](../how-to/publish-managed-services-offers.md)。 有關使用雲合作夥伴門戶發佈到 Azure 應用商店的一般資訊,請參閱[Azure 應用商店和 AppSource 發布指南](../../marketplace/marketplace-publishers-guide.md)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](azure-delegated-resource-management.md)與[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- 向 Azure 應用商店[發佈託管服務產品/](../how-to/publish-managed-services-offers.md)服務。
