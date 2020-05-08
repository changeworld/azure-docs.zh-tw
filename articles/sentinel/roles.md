---
title: Azure Sentinel 中的許可權 |Microsoft Docs
description: 本文說明 Azure Sentinel 如何使用角色型存取控制將許可權指派給使用者，並識別每個角色允許的動作。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: 2e1b1a4786670974a40b22d44fc219c6be5d97a3
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744754"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的許可權

Azure Sentinel 使用以 [角色為基礎的存取控制（RBAC）](../role-based-access-control/role-assignments-portal.md)，以提供可在 Azure 中指派給使用者、群組和服務的 [內建角色](../role-based-access-control/built-in-roles.md) 。

使用 RBAC，您可以在安全性作業小組中使用和建立角色，以授與 Azure Sentinel 適當的存取權。 根據角色，您可以更精細地控制具有 Azure Sentinel 存取權的使用者可以看到的內容。 您可以直接將 Azure Sentinel 工作區中的 RBAC 角色指派給工作區所屬的訂用帳戶或資源群組。

有三個特定的內建 Azure Sentinel 角色。  
**所有 Azure Sentinel 內建角色都會授與讀取權限給您 Azure Sentinel 工作區中的資料。**
- [Azure Sentinel 讀取器](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel 回應程式](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 參與者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

除了 Azure Sentinel 專用的 RBAC 角色之外，還有 Azure 和 Log Analytics RBAC 角色可授與更廣泛的許可權集，包括存取您的 Azure Sentinel 工作區和其他資源：

- **Azure 角色：** [擁有](../role-based-access-control/built-in-roles.md#owner)者、[參與者](../role-based-access-control/built-in-roles.md#contributor)和[讀者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色會授與所有 Azure 資源（包括 Log Analytics 工作區和 Azure Sentinel 資源）的存取權。

-   **Log analytics 角色：** [log analytics 參與者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、 [log analytics 讀取器](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics 角色會在您所有的 Log Analytics 工作區中授與存取權。 

> [!NOTE]
> Log Analytics 角色也會授與所有 Azure 資源的讀取權限，但只會指派 Log Analytics 資源的寫入權限。


例如，使用**Azure Sentinel 讀取器**和**Azure 參與者**（而非**Azure Sentinel 參與者**）角色指派的使用者，將能夠編輯 Azure Sentinel 中的資料，不過他們只有**Sentinel 讀取器**許可權。 因此，如果您只想要在 Azure Sentinel 中授與許可權給使用者，您應該謹慎移除這位使用者的先前許可權，以確保您不會中斷另一項資源所需的任何許可權角色。

> [!NOTE]
>- Azure Sentinel 會使用自動化威脅回應的腳本。 腳本會利用 Azure Logic Apps，而且是個別的 Azure 資源。 您可能想要指派安全性作業小組的特定成員，並選擇使用 Logic Apps 進行安全性協調流程、自動化和回應（攀升情況）作業。 您可以使用[邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色或[邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator)角色來指派明確的許可權，以使用操作手冊。
>- 若要新增資料連線器，每個連接器的必要角色是每個連接器類型，而且會列在相關的 [連接器] 頁面中。 此外，若要連接任何資料來源，您必須擁有 Azure Sentinel 工作區的 [寫入] 許可權。



## <a name="roles-and-allowed-actions"></a>角色和允許的動作

下表顯示 Azure Sentinel 中的角色和允許的動作。 X 表示該角色允許的動作。

| [角色] | 建立及執行腳本| 建立和編輯儀表板、分析規則和其他 Azure Sentinel 資源 | 管理事件（關閉、指派等等） | 查看資料、事件、儀表板和其他 Azure Sentinel 資源 |
|--- |---|---|---|---|
| Azure Sentinel 讀取器 | -- | -- | -- | X |
| Azure Sentinel 回應程式|--|--| X | X |
| Azure Sentinel 參與者 | -- | X | X | X |
| Azure Sentinel 參與者 + 邏輯應用程式參與者 | X | X | X | X |


> [!NOTE]
> - 我們建議您指派所需的最寬鬆角色，以便使用者完成其工作。 例如，只將「Azure Sentinel 參與者」角色指派給需要建立規則或儀表板的使用者。
> - 建議您在資源群組範圍中設定 Azure Sentinel 的許可權，讓使用者可以存取相同資源群組中的所有 Azure Sentinel 工作區。
>
## <a name="building-custom-rbac-roles"></a>建置自訂的 RBAC 角色

除了使用內建 RBAC 角色以外，您還可以建立 Azure Sentinel 的自訂 RBAC 角色。 Azure Sentinel 的自訂 RBAC 角色的建立方式，與您建立其他[自訂 AZURE RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色時所依據的 Azure Sentinel 和[Azure Log Analytics 資源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)的[特定許可權](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)相同。

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>於 Azure Sentinel 儲存的資料具有進階 RBAC
  
您可以在 Azure Sentinel 工作區中的資料上使用 Log Analytics 先進角色型存取控制。 這包括每個資料類型的角色型存取控制，以及以資源為中心的角色型存取控制。 如需 Log Analytics 角色的詳細資訊，請參閱 [管理 Azure 監視器中的記錄資料和工作區](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何使用 Azure Sentinel 使用者的角色，以及每個角色可讓使用者執行的動作。

* [Azure Sentinel 的 Blog](https://aka.ms/azuresentinelblog)。 尋找有關 Azure 安全性與相容性的部落格文章。
