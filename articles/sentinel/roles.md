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
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565837"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的許可權

Azure Sentinel 使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md)來提供 [內建角色](../role-based-access-control/built-in-roles.md)   ，這些角色可指派給 azure 中的使用者、群組和服務。

使用 RBAC 來建立並指派安全性作業小組中的角色，以授與 Azure Sentinel 的適當存取權。 不同的角色可讓您更精細地控制 Azure Sentinel 的使用者可以看到及進行的動作。 您可以直接在 Azure Sentinel 工作區中指派 Azure 角色 (請參閱以下) 或工作區所屬的訂用帳戶或資源群組中的附注，Azure Sentinel 將會繼承這些角色。

## <a name="roles-for-working-in-azure-sentinel"></a>Azure Sentinel 中工作的角色

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 特定的角色

有三個專用的內建 Azure Sentinel 角色。

**所有 Azure Sentinel 內建角色都會授與您 Azure Sentinel 工作區中資料的讀取權限。**

- [Azure Sentinel 的讀者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) 可以查看資料、事件、活頁簿和其他 Azure Sentinel 資源。

- [Azure Sentinel 回應](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) 程式除了上述之外，還可以管理事件 (指派、關閉等 ) 

- [Azure Sentinel 參與者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) 除了上述之外，還可以建立和編輯活頁簿、分析規則和其他 Azure Sentinel 資源。

> [!NOTE]
>
> - 為了獲得最佳結果，您應該在包含 Azure Sentinel 工作區的 **資源群組** 上指派這些角色。 如此一來，角色就會套用至為了支援 Azure Sentinel 所部署的所有資源，因為這些資源也應放置在相同的資源群組中。
>
> - 另一個選項是直接在 Azure Sentinel **工作區** 上指派角色。 如果您這麼做，您也必須在該工作區的 SecurityInsights **解決方案資源** 上指派相同的角色。 您也可能需要在其他資源上指派它們，而且您將需要持續管理資源的角色指派。

### <a name="additional-roles-and-permissions"></a>其他角色和許可權

具有特定工作需求的使用者可能需要被指派其他角色或特定許可權，才能完成其工作。

- 使用研究人員將威脅的回應自動化

    Azure Sentinel 使用自動化威脅回應的 **手冊** 。 腳本是以 **Azure Logic Apps**為基礎，而且是個別的 Azure 資源。 您可能會想要指派給安全性作業小組的特定成員，以使用安全性協調流程、自動化和回應的 Logic Apps (SOAR) 作業。 您可以使用 [邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor) 角色或 [邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator) 角色，來指派使用手冊的明確許可權。

- 將資料來源連接到 Azure Sentinel

    若要讓使用者新增 **資料連線器**，您必須在 Azure Sentinel 工作區上指派使用者的寫入權限。 此外，請注意每個連接器所需的額外許可權，如相關的連接器頁面所列。

- 指派事件的來賓使用者

    如果來賓使用者必須能夠指派事件，則除了 Azure Sentinel 回應者角色之外，還必須將 [目錄讀取](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)者角色指派給使用者。 請注意，此角色 *不* 是 Azure RBAC 角色，而是 **Azure Active Directory** 角色，且定期 (非來賓) 使用者預設會指派此角色。 

如需並排比較，請參閱 [下表](#roles-and-allowed-actions)。

### <a name="other-roles-you-might-see-assigned"></a>您可能會看到指派的其他角色

在指派 Azure Sentinel 特定的 Azure 角色時，您可能會遇到其他可能已指派給使用者以供其他用途使用的 Azure 和 Log Analytics Azure 角色。 您應該注意，這些角色會授與一組更廣泛的許可權，包括存取您的 Azure Sentinel 工作區和其他資源：

- **Azure 角色：** [擁有](../role-based-access-control/built-in-roles.md#owner)者、 [參與者](../role-based-access-control/built-in-roles.md#contributor)和 [讀者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色會授與所有 Azure 資源的存取權，包括 Log Analytics 工作區和 Azure Sentinel 資源。

- **Log analytics 角色：** [log Analytics 參與者](../role-based-access-control/built-in-roles.md#log-analytics-contributor) 和 [log analytics 讀取](../role-based-access-control/built-in-roles.md#log-analytics-reader)者。 Log Analytics 角色會將存取權授與您的 Log Analytics 工作區。 

例如，獲指派「 **Azure Sentinel 讀者** 」角色但不是「 **Azure Sentinel 參與者** 」角色的使用者，仍然可以在已指派 Azure 層級 **參與者** 角色的 Azure Sentinel 中編輯專案。 因此，如果您只想要在 Azure Sentinel 中將許可權授與使用者，您應謹慎移除此使用者的先前許可權，以確保您不會中斷對另一個資源所需的任何存取。

## <a name="roles-and-allowed-actions"></a>角色和允許的動作

下表摘要說明 Azure Sentinel 中的角色和允許的動作。 

| 角色 | 建立和執行手冊| 建立和編輯活頁簿、分析規則和其他 Azure Sentinel 資源 | 管理事件 (關閉、指派等 )  | 查看資料、事件、活頁簿和其他 Azure Sentinel 資源 |
|---|---|---|---|---|
| Azure Sentinel 讀者 | -- | -- | -- | &#10003; |
| Azure Sentinel 回應者 | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 參與者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 參與者 + 邏輯應用程式參與者 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>自訂角色和 advanced RBAC

- 除了使用 Azure 內建角色之外，您還可以建立適用于 Azure Sentinel 的 Azure 自訂角色，而不是使用 Azure 內建角色。 Azure Sentinel 的 Azure 自訂角色會以您建立其他[自訂 AZURE RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的相同方式來建立，其根據 Azure Sentinel 和[Azure Log Analytics 資源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)的[特定許可權](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)。

- 您可以在 Azure Sentinel 工作區中的資料上使用 Log Analytics advanced 角色型存取控制。 這包括以資料型別為基礎的 RBAC 和以資源為中心的 RBAC。 如需有關 Log Analytics 角色的詳細資訊，請參閱 [Azure 監視器中的記錄管理資料和工作區](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure Sentinel 使用者的角色，以及每個角色可讓使用者執行的動作。

* [Azure Sentinel 的 Blog](https://aka.ms/azuresentinelblog)。 尋找有關 Azure 安全性與相容性的部落格文章。
