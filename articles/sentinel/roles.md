---
title: Azure Sentinel 中的許可權 |Microsoft Docs
description: 本文說明 Azure Sentinel 如何使用角色型存取控制將許可權指派給使用者，並識別每個角色允許的動作。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b907fead145847d54b8b1de196586b65101b22ed
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534000"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的許可權

Azure Sentinel 使用以 [角色為基礎的存取控制（RBAC）](../role-based-access-control/role-assignments-portal.md)來提供可指派給 Azure 中的使用者、群組和服務的 [內建角色](../role-based-access-control/built-in-roles.md)   。

使用 RBAC 來建立及指派安全性作業小組中的角色，以授與 Azure Sentinel 適當的存取權。 不同的角色可讓您更精細地控制 Azure Sentinel 的使用者可以看到和執行哪些動作。 您可以直接在 Azure Sentinel 工作區中指派 Azure 角色（請參閱下面的附注），或在工作區所屬的訂用帳戶或資源群組中，Azure Sentinel 將會繼承。

## <a name="roles-for-working-in-azure-sentinel"></a>Azure Sentinel 中工作的角色

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 特定的角色

有三個專用的內建 Azure Sentinel 角色。

**所有 Azure Sentinel 內建角色都會授與讀取權限給您 Azure Sentinel 工作區中的資料。**

- [Azure Sentinel 讀取器](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)可以查看資料、事件、活頁簿和其他 Azure Sentinel 資源。

- [Azure Sentinel 回應](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)程式除了上述之外，也可以管理事件（指派、解除等）

- 除了上述， [Azure Sentinel 參與者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)可以建立和編輯活頁簿、分析規則和其他 Azure Sentinel 資源。

> [!NOTE]
>
> - 為了獲得最佳結果，應該在包含 [Azure Sentinel] 工作區的**資源群組**上指派這些角色。 如此一來，角色就會套用至部署支援 Azure Sentinel 的所有資源，因為這些資源也應該放在相同的資源群組中。
>
> - 另一個選項是直接在 Azure Sentinel**工作區**本身指派角色。 如果您這樣做，您也必須在該工作區的 SecurityInsights**解決方案資源**上指派相同的角色。 您可能也需要將它們指派給其他資源，而且您需要持續管理資源的角色指派。

### <a name="additional-roles-and-permissions"></a>其他角色和許可權

具有特定作業需求的使用者可能需要獲指派其他角色或特定許可權，才能完成其工作。

- 使用操作手冊將威脅的回應自動化

    Azure Sentinel 會**使用自動化**威脅回應的腳本。 腳本是以**Azure Logic Apps**為基礎，而且是個別的 Azure 資源。 您可能想要指派給安全性作業小組的特定成員，讓他們能夠使用 Logic Apps 進行安全性協調流程、自動化和回應（攀升情況）作業。 您可以使用[邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色或[邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator)角色來指派明確的許可權，以使用操作手冊。

- 將資料來源連接到 Azure Sentinel

    若要讓使用者新增**資料連線器**，您必須在 Azure Sentinel 工作區上指派使用者寫入權限。 此外，請注意每個連接器所需的額外許可權，如相關的連接器頁面所列。

如需並存比較，請參閱[下表](#roles-and-allowed-actions)。

### <a name="other-roles-you-might-see-assigned"></a>您可能會看到指派的其他角色

在指派 Azure Sentinel 特定的 Azure 角色時，您可能會遇到其他可能指派給使用者的其他 Azure 和 Log Analytics Azure 角色，以供其他用途使用。 請注意，這些角色會授與更多的許可權集，包括 Azure Sentinel 工作區和其他資源的存取權：

- **Azure 角色：** [擁有](../role-based-access-control/built-in-roles.md#owner)者、[參與者](../role-based-access-control/built-in-roles.md#contributor)和[讀者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色會授與所有 Azure 資源（包括 Log Analytics 工作區和 Azure Sentinel 資源）的存取權。

- **Log analytics 角色：** [log Analytics 參與者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)和[log analytics 讀取器](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics 角色會授與 Log Analytics 工作區的存取權。 

例如，獲指派 [ **Azure Sentinel 讀取**者] 角色但不屬於 [ **Azure Sentinel 參與者**] 角色的使用者，仍然可以在 Azure Sentinel 中，如果指派了 Azure 層級的**參與者**角色，就能夠編輯專案。 因此，如果您只想要在 Azure Sentinel 中授與許可權給使用者，您應該謹慎移除這位使用者的先前許可權，以確保您不會中斷其他資源的任何必要存取權。

## <a name="roles-and-allowed-actions"></a>角色和允許的動作

下表摘要說明 Azure Sentinel 中的角色和允許的動作。 

| 角色 | 建立及執行腳本| 建立和編輯活頁簿、分析規則和其他 Azure Sentinel 資源 | 管理事件（關閉、指派等等） | 查看資料、事件、活頁簿和其他 Azure Sentinel 資源 |
|---|---|---|---|---|
| Azure Sentinel 讀者 | -- | -- | -- | &#10003; |
| Azure Sentinel 回應者 | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 參與者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 參與者 + 邏輯應用程式參與者 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>自訂角色和 advanced RBAC

- 除了使用 Azure 內建角色以外，您還可以建立適用于 Azure Sentinel 的 Azure 自訂角色。 根據 Azure Sentinel 和[Azure Log Analytics 資源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)的[特定許可權](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)，建立適用于 Azure Sentinel 的 Azure 自訂角色與建立其他[自訂 Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的方式相同。

- 您可以在 Azure Sentinel 工作區中的資料上使用 Log Analytics 先進角色型存取控制。 這包括以資料類型為基礎的 RBAC 和以資源為中心的 RBAC。 如需 Log Analytics 角色的詳細資訊，請參閱 [管理 Azure 監視器中的記錄資料和工作區](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure Sentinel 使用者的角色，以及每個角色可讓使用者執行的動作。

* [Azure Sentinel 的 Blog](https://aka.ms/azuresentinelblog)。 尋找有關 Azure 安全性與相容性的部落格文章。
