---
title: 設定適用于 Azure API for FHIR 的 Azure 角色型存取控制（Azure RBAC）
description: 本文說明如何為 Azure API for FHIR 資料平面設定 Azure RBAC
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: a3cdb5a640e09c0095db79f5e3a43b455a1a5705
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447157"
---
# <a name="configure-azure-rbac-for-fhir"></a>設定適用于 FHIR 的 Azure RBAC 

在本文中，您將瞭解如何使用[azure 角色型存取控制（AZURE RBAC）](https://docs.microsoft.com/azure/role-based-access-control/)來指派 Azure API for FHIR 資料平面的存取權。 在與您的 Azure 訂用帳戶相關聯的 Azure Active Directory 租使用者中管理資料平面的使用者時，Azure RBAC 是指派資料平面存取權的慣用方法。 如果您使用外部 Azure Active Directory 租使用者，請參閱[本機 RBAC 指派參考](configure-local-rbac.md)。

## <a name="confirm-azure-rbac-mode"></a>確認 Azure RBAC 模式

若要使用 Azure RBAC，您的 Azure API for FHIR 必須設定為使用您的 Azure 訂用帳戶租使用者來進行資料平面，而且不應該有指派的身分識別物件識別碼。 您可以藉由檢查 Azure API for FHIR 的 [**驗證**] 分頁來驗證您的設定：

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="確認 Azure RBAC 模式":::

**授權**單位應設定為與您的訂用帳戶相關聯的 Azure Active directory 租使用者，且方塊中應該沒有標示為 [**允許的物件識別碼**] 的 guid。 您也會注意到方塊已停用，且標籤指出應該使用 Azure RBAC 來指派資料平面角色。

## <a name="assign-roles"></a>指派角色

若要授與使用者、服務主體或群組對 FHIR 資料平面的存取權，請按一下 [**存取控制（IAM）**]，然後按一下 [**角色指派**]，再按一下 [ **+ 新增**]：

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="新增 Azure RBAC 角色指派":::

在 [**角色**選取] 中，搜尋 FHIR 資料平面的其中一個內建角色：

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="內建的 FHIR 資料角色":::

您可以選擇：

* FHIR 資料讀取器：可以讀取（和搜尋） FHIR 資料。
* FHIR 資料寫入器外掛程式：可以讀取、寫入和虛刪除 FHIR 資料。
* FHIR 資料匯出工具：可以讀取和匯出（ `$export` operator）資料。
* FHIR 資料參與者：可以執行所有資料平面作業。

如果這些角色不足以滿足您的需求，您也可以[建立自訂角色](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)。

在 [**選取**] 方塊中，搜尋您想要指派角色的使用者、服務主體或群組。

## <a name="caching-behavior"></a>快取行為

Azure API for FHIR 將會快取最多5分鐘的決策。 如果您授與使用者 FHIR 伺服器的存取權，方法是將它們新增至允許的物件識別碼清單，或從清單中將其移除，您應該預期它需要最多五分鐘的時間才能傳播許可權變更。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何為 FHIR 資料平面指派 Azure RBAC 角色。 接下來，請瞭解 Azure API for FHIR 的其他設定：
 
>[!div class="nextstepaction"]
>[其他設定 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
