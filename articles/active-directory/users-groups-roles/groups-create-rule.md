---
title: 建立或編輯動態群組並取得狀態-Azure AD |Microsoft Docs
description: 如何在 Azure 入口網站中建立或更新群組成員資格規則，並檢查其處理狀態。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94d1a4fa717bbee9974f7218e704ba06dad530d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91531525"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>在 Azure Active Directory 中建立或更新動態群組

在 Azure Active Directory (Azure AD) ，您可以使用規則來根據使用者或裝置屬性來決定群組成員資格。 本文說明如何在 Azure 入口網站中設定動態群組的規則。
安全性群組或 Microsoft 365 群組支援動態成員資格。 套用群組成員資格規則時，會評估使用者和裝置屬性是否符合成員資格規則。 當使用者或裝置的屬性變更時，組織中的所有動態群組規則都會處理成員資格變更。 如果使用者和裝置符合群組的條件，則會加以新增或移除。 安全性群組可用於裝置或使用者，但 Microsoft 365 群組只能是使用者群組。

## <a name="rule-builder-in-the-azure-portal"></a>Azure 入口網站中的規則建立器

Azure AD 提供規則建立器，可讓您更快速建立和更新重要的規則。 規則建立器支援建構最多五個運算式。 規則建立器可讓您更輕鬆以幾個簡單的運算式來建立規則，但不可能重現每一個規則。 如果規則建立器不支援您想建立的規則，您可以使用文字方塊。

以下是進階規則或語法的一些範例，建議您使用文字方塊來建構：

- 具有五個以上運算式的規則
- 直屬員工規則
- 設定[運算子優先順序](groups-dynamic-membership.md#operator-precedence)
- [具有複雜運算式的規則](groups-dynamic-membership.md#rules-with-complex-expressions)，例如 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 規則建立器可能無法顯示文字方塊中建構的某些規則。 當規則建立器無法顯示規則時，您可能會看到訊息。 規則建立器完全不會變更動態群組規則支援的語法、驗證或處理。

![顯示 [動態成員資格規則] 頁面的螢幕擷取畫面，其中已選取 [設定規則] 索引標籤上的 [新增運算式] 動作。](./media/groups-create-rule/update-dynamic-group-rule.png)

如需語法、支援的屬性、運算子和成員資格規則值的範例，請參閱 [Azure Active Directory 中群組的動態成員資格規則](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>建立群組成員資格規則

1. 使用 Azure AD 組織的全域系統管理員、Intune 管理員或使用者系統管理員角色的帳戶登入 [Azure AD admin center](https://aad.portal.azure.com) 。
1. 搜尋並選取 **群組**。
1. 選取 [所有群組]****，然後選取 [新增群組]****。

   ![選取命令以新增群組](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. 在 [ **群組** ] 頁面上，輸入新群組的名稱和描述。 選取 [使用者] 或 [裝置] 的 **成員資格類型** ，然後選取 [ **新增動態查詢**]。 規則產生器最多可支援五個運算式。 若要加入五個以上的運算式，您必須使用 [] 文字方塊。

   ![顯示 [所有群組] 頁面的螢幕擷取畫面，其中已選取 [新增群組] 動作。](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 若要查看您的成員資格查詢可用的自訂延伸模組屬性：
   1. 選取 **取得自訂延伸模組屬性**
   1. 輸入應用程式識別碼，然後選取 [重新整理 **屬性**]。
1. 建立規則之後，請選取 [ **儲存**]。
1. 選取 [**新增群組**] 頁面上的 [**建立**] 以建立群組。

如果您輸入的規則無效，說明為何無法處理規則的說明會顯示在入口網站的 Azure 通知中。 請仔細閱讀以瞭解如何修正規則。

## <a name="to-update-an-existing-rule"></a>若要更新現有的規則

1. 使用全域管理員、群組管理員、Intune 管理員或 Azure AD 組織中的使用者系統管理員角色的帳戶登入 [Azure AD 系統管理中心](https://aad.portal.azure.com) 。
1. 選取 [**群組**  >  **所有群組**]。
1. 選取群組以開啟其設定檔。
1. 在群組的 [設定檔] 頁面上，選取 [ **動態成員資格規則**]。 規則產生器最多可支援五個運算式。 若要加入五個以上的運算式，您必須使用 [] 文字方塊。

   ![新增動態群組的成員資格規則](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 若要查看適用于成員資格規則的自訂延伸模組屬性：
   1. 選取 **取得自訂延伸模組屬性**
   1. 輸入應用程式識別碼，然後選取 [重新整理 **屬性**]。
1. 更新規則之後，請選取 [ **儲存**]。

## <a name="turn-on-or-off-welcome-email"></a>開啟或關閉歡迎電子郵件

建立新的 Microsoft 365 群組時，會將新增至群組的使用者傳送給歡迎電子郵件通知。 稍後，如果使用者或裝置的任何屬性變更，則組織中的所有動態群組規則都會處理成員資格變更。 新增的使用者隨後也會收到歡迎通知。 您可以在 [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)中關閉此行為。

## <a name="check-processing-status-for-a-rule"></a>檢查規則的處理狀態

您可以在群組的 [概觀]**** 頁面上看到成員資格處理狀態和上次更新日期。
  
  ![顯示動態群組狀態](./media/groups-create-rule/group-status.png)

可能會針對**成員資格處理**狀態顯示下列狀態訊息：

- **評估中**：已收到群組變更，而且正在評估更新。
- **處理中**：正在處理更新。
- **更新完成**：處理已完成，並已建立所有適用的更新。
- **處理錯誤**：因為評估成員資格規則時發生錯誤，所以無法完成處理。
- **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：

- &lt;**日期和時間** &gt; ：上次更新成員資格的時間。
- **進行中**：目前正在更新。
- **未知**：無法抓取上次更新時間。 群組可能是新的。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀]**** 頁面頂端顯示警示。 如果無法針對組織內的所有群組處理任何擱置中的動態成員資格更新超過24小時，則會在 **所有群組**的頂端顯示警示。

![處理錯誤訊息警示](./media/groups-create-rule/processing-error.png)

這些文章提供有關 Azure Active Directory 中群組的其他資訊。

- [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
- [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-dynamic-membership.md)