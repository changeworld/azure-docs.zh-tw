---
title: Azure 哨兵中的許可權 |微軟文檔
description: 本文介紹 Azure Sentinel 如何使用基於角色的訪問控制項向使用者分配許可權，並確定每個角色的允許操作。
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
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587170"
---
# <a name="permissions-in-azure-sentinel"></a>Azure 哨兵中的許可權

Azure Sentinel 使用 [基於角色的存取控制 （RBAC）](../role-based-access-control/role-assignments-portal.md)來提供可分配給 Azure 中的使用者、組和服務的 [內置角色](../role-based-access-control/built-in-roles.md) 。

使用 RBAC，您可以在安全操作團隊中使用和創建角色，以授予對 Azure Sentinel 的適當存取權限。 根據角色，您可以細細微性地控制有權訪問 Azure Sentinel 的使用者可以看到的內容。 可以直接在 Azure Sentinel 工作區中分配 RBAC 角色，也可以將 RBAC 角色指派給工作區所屬的訂閱或資源組。

有三個特定的內置 Azure Sentinel 角色。  
**所有 Azure Sentinel 內置角色都授予對 Azure Sentinel 工作區中資料的讀取存取許可權。**
- [Azure 哨兵讀取器](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure 哨兵回應器](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure 哨兵參與者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

除了 Azure Sentinel 專用 RBAC 角色外，還有 Azure 和日誌分析 RBAC 角色可以授予更廣泛的許可權集，其中包括對 Azure Sentinel 工作區和其他資源的存取權限：

- **Azure 角色：**[擁有者](../role-based-access-control/built-in-roles.md#owner)、[參與者](../role-based-access-control/built-in-roles.md#contributor)和[讀取器](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色授予跨所有 Azure 資源（包括日誌分析工作區和 Azure Sentinel 資源）的訪問。

-   **日誌分析角色：**[日誌分析參與者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)，[日誌分析閱讀器](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 日誌分析角色授予跨所有日誌分析工作區的存取權限。 

> [!NOTE]
> 日誌分析角色還授予所有 Azure 資源的讀取存取許可權，但只會將寫入權限分配給日誌分析資源。


例如，使用**Azure Sentinel 讀取器讀取器**和**Azure 參與者**（不是 Azure **Sentinel 參與者**）角色指派的使用者將能夠編輯 Azure Sentinel 中的資料，儘管他們只有**Sentinel 讀取器**許可權。 因此，如果要僅向 Azure Sentinel 中授予 的 許可權，則應仔細刪除此使用者的先前許可權，以確保不會破壞其他資源所需的任何許可權角色。

> [!NOTE]
>- Azure Sentinel 使用行動手冊進行自動威脅回應。 行動手冊利用 Azure 邏輯應用，是單獨的 Azure 資源。 您可能希望分配安全操作團隊的特定成員，並選擇使用邏輯應用進行安全編排、自動化和回應 （SOAR） 操作。 您可以使用[邏輯應用參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色或[邏輯應用運算子](../role-based-access-control/built-in-roles.md#logic-app-operator)角色指派使用操作手冊的明確權限。
>- 要添加資料連線器，每個連接器的必要角色是每個連接器類型，並列在相關的連接器頁中。 此外，為了連接任何資料來源，必須在 Azure Sentinel 工作區上具有寫入權限。



## <a name="roles-and-allowed-actions"></a>角色和允許的動作

下表在 Azure Sentinel 中顯示角色和允許的操作。 X 表示該角色允許的動作。

| 角色 | 創建和運行行動手冊| 創建和編輯儀表板、分析規則和其他 Azure 哨兵資源 | 管理事件（解雇、分配等） | 查看資料、事件、儀表板和其他 Azure 哨兵資源 |
|--- |---|---|---|---|
| Azure 哨兵讀取器 | -- | -- | -- | X |
| Azure 哨兵回應器|--|--| X | X |
| Azure 哨兵參與者 | -- | X | X | X |
| Azure 哨兵參與者 + 邏輯應用參與者 | X | X | X | X |


> [!NOTE]
> - 我們建議您指派所需的最寬鬆角色，以便使用者完成其工作。 例如，將 Azure Sentinel 參與者角色僅分配給需要創建規則或儀表板的使用者。
> - 我們建議您在資源組作用域中設置 Azure Sentinel 的許可權，以便使用者可以訪問同一資源組中的所有 Azure Sentinel 工作區。
>
## <a name="building-custom-rbac-roles"></a>建置自訂的 RBAC 角色

除了使用內置 RBAC 角色外，還可以為 Azure Sentinel 創建自訂 RBAC 角色。 Azure Sentinel 的自訂 RBAC 角色的創建方式與創建其他[自訂 Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的方式相同，具體取決於對 Azure Sentinel 和[Azure 日誌分析資源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)[的特定許可權](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)。

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>於 Azure Sentinel 儲存的資料具有進階 RBAC
  
您可以在 Azure Sentinel 工作區中的資料使用日誌分析高級基於角色的存取控制。 這包括每個資料類型基於角色的存取控制以及以資源為中心的基於角色的存取控制。 有關日誌分析角色的詳細資訊，請參閱在 [Azure 監視器 中管理日誌資料和工作區](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>後續步驟
在本文中，您學習了如何使用 Azure Sentinel 使用者的角色，以及每個角色使使用者能夠做什麼。

* [Azure 哨兵博客](https://aka.ms/azuresentinelblog). 尋找有關 Azure 安全性與相容性的部落格文章。
