---
title: 創建或編輯動態組並獲取狀態 - Azure AD |微軟文檔
description: 如何在 Azure 門戶中創建或更新組成員身份規則，並檢查其處理狀態。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266373"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>在 Azure 活動目錄中創建或更新動態組

在 Azure 活動目錄 （Azure AD） 中，可以使用規則根據使用者或裝置屬性確定組成員身份。 本文介紹如何在 Azure 門戶中為動態組設置規則。
安全性群組或 Office 365 組支援動態成員資格。 應用組成員身份規則時，將計算使用者和裝置屬性與成員資格規則的匹配。 當使用者或設備的屬性發生更改時，將處理組織中的所有動態組規則以進行成員身份更改。 如果使用者和設備滿足組的條件，則添加或刪除它們。 安全性群組可用於設備或使用者，但 Office 365 組只能是使用者組。

## <a name="rule-builder-in-the-azure-portal"></a>Azure 門戶中的規則產生器

Azure AD 提供了一個規則產生器，用於更快地創建和更新重要規則。 規則產生器最多支援構造五個運算式。 規則產生器使使用幾個簡單運算式形成規則變得更加容易，但是，它不能用於複製每個規則。 如果規則產生器不支援要創建的規則，則可以使用文字方塊。

下面是一些高級規則或語法的示例，我們建議您為此使用文字方塊構造：

- 具有五個以上運算式的規則
- 直接下屬規則
- 設置[運算子優先順序](groups-dynamic-membership.md#operator-precedence)
- [具有複雜運算式的規則](groups-dynamic-membership.md#rules-with-complex-expressions);例如`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 規則產生器可能無法顯示在文字方塊中構造的某些規則。 當規則產生器無法顯示規則時，您可能會看到一條消息。 規則產生器不會以任何方式更改支援的動態組規則的語法、驗證或處理。

![添加動態組的成員資格規則](./media/groups-create-rule/update-dynamic-group-rule.png)

有關成員資格規則的語法、受支援屬性、運算子和值的示例，請參閱 Azure[活動目錄中的組的動態成員資格規則](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>建立群組成員資格規則

1. 使用租戶中的全域管理員、Intune 管理員或使用者管理員角色中的帳戶登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 搜索並選擇**組**。
1. 選取 [所有群組]****，然後選取 [新增群組]****。

   ![選擇要添加新組的命令](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. 在 **"組"** 頁上，輸入新組的名稱和說明。 為使用者或設備選擇**成員資格類型**，然後選擇 **"添加動態查詢**"。 規則產生器最多支援五個運算式。 要添加五個以上運算式，必須使用文字方塊。

   ![添加動態組的成員資格規則](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 要查看可用於成員資格查詢的自訂擴充屬性：
   1. 選擇 **"獲取自訂擴充屬性**
   1. 輸入應用程式 ID，然後選擇 **"刷新屬性**"。
1. 創建規則後，選擇 **"保存**"。
1. 在 **"新建"組**頁上選擇 **"創建**"以創建組。

如果輸入的規則無效，則門戶中的 Azure 通知中將顯示無法處理規則的原因的說明。 仔細閱讀，瞭解如何修復規則。

## <a name="to-update-an-existing-rule"></a>更新現有規則

1. 使用租戶中的全域管理員、組管理員、Intune 管理員或使用者管理員角色中的帳戶登錄到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 選擇 **"所有組組** > **All groups**組"。
1. 選擇要打開其設定檔的組。
1. 在組的設定檔頁面上，選擇**動態成員資格規則**。 規則產生器最多支援五個運算式。 要添加五個以上運算式，必須使用文字方塊。

   ![添加動態組的成員資格規則](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 要查看可用於成員資格規則的自訂擴充屬性：
   1. 選擇 **"獲取自訂擴充屬性**
   1. 輸入應用程式 ID，然後選擇 **"刷新屬性**"。
1. 更新規則後，選擇 **"保存**"。

## <a name="turn-on-or-off-welcome-email"></a>開啟或關閉歡迎電子郵件

創建新的 Office 365 組時，將發送歡迎電子郵件通知，通知添加到該組的使用者。 稍後，如果使用者或設備的任何屬性發生更改，則處理組織中的所有動態組規則以進行成員身份更改。 新增的使用者隨後也會收到歡迎通知。 您可以在[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)中關閉此行為。

## <a name="check-processing-status-for-a-rule"></a>檢查規則的處理狀態

您可以在群組的 [概觀]**** 頁面上看到成員資格處理狀態和上次更新日期。
  
  ![顯示動態組狀態](./media/groups-create-rule/group-status.png)

可能會針對**成員資格處理**狀態顯示下列狀態訊息：

- **評估中**：已收到群組變更，而且正在評估更新。
- **處理中**：正在處理更新。
- **更新完成**：處理已完成，並已建立所有適用的更新。
- **處理錯誤**：由於評估成員資格規則的錯誤，無法完成處理。
- **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：

- &lt;**日期和時間**&gt;：上次更新成員資格的時間。
- **進行中**：目前正在更新。
- **未知**：無法檢索上次更新時間。 該組可能是新的。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀]**** 頁面頂端顯示警示。 如果無法為租戶中的所有組處理超過 24 小時的掛起的動態成員身份更新，則**所有組**的頂部都會顯示警報。

![處理錯誤訊息警報](./media/groups-create-rule/processing-error.png)

這些文章提供有關 Azure Active Directory 中群組的其他資訊。

- [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
- [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-dynamic-membership.md)
