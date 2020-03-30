---
title: 在"我的應用"門戶上更新群組資訊 - Azure AD
description: 瞭解如何查看和更新與組相關的資訊，包括查看您擁有的組、創建新組、查看您已經是成員的組以及加入您尚未加入的任何組。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022291"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>在"我的應用"門戶上更新群組資訊

您可以透過 [我的應用程式]**** Web 入口網站使用工作或學校帳戶，來檢視和啟動貴組織的許多雲端式應用程式、更新您的設定檔和帳戶資訊、查看您的**群組**資訊，以及執行您應用程式和群組的**存取權檢閱**。 如果您沒有 [我的應用程式]**** 入口網站的存取權，則必須連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="view-your-groups-information"></a>查看群組資訊

如果管理員已授予您查看 **"組"** 磁貼的許可權，則可以：

- **作為組成員。** 查看詳細資訊或離開任何組。

- **作為組擁有者。** 查看詳細資訊、創建新組、添加或刪除成員或刪除組。

### <a name="to-view-your-groups-information"></a>查看群組資訊

1. 登入您的公司或學校帳戶。

2. 打開 Web 瀏覽器並轉到https://myapps.microsoft.com，或使用組織提供的連結。 例如，您可能會被定向到組織的自訂頁面，例如https://myapps.microsoft.com/contoso.com。

    將顯示 **"應用"** 頁面，顯示組織擁有並可供您使用的所有基於雲的應用。

    !["我的應用"門戶中的應用頁面](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 選擇 **"組"** 磁貼以查看與組相關的資訊。

    ![包含擁有組和成員組的組頁面](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 根據您的許可權，您可以使用 **"組"** 頁：

    - **查看您擁有的組。** 從 **"我擁有的組**"區域查看有關您在組織內擁有的任何組的資訊。 選擇特定組名稱可提供有關組的更多詳細資訊，包括組類型、成員數、加入策略和活動成員清單。

    - **創建新組。** 創建一個新組，由您作為**我所在的組**區域的擁有者。 有關特定步驟，請參閱本文的["創建新組](#create-a-new-group)"部分。

    - **編輯現有組。** 編輯任何您自己的組的詳細資訊。 有關特定步驟，請參閱本文的["編輯現有組](#edit-an-existing-group)"部分。

    - **添加或刪除成員。** 添加或刪除您擁有的組的成員。 有關特定步驟，請參閱本文的["添加或刪除"成員](#add-or-remove-a-member)部分。

    - **續訂 Office 365 組。** 如果您的組織允許，您可以續訂 Office 365 組。 有關特定步驟，請參閱本文的["續訂 Office 365 組](#renew-an-office-365-group)"部分。 

    - **刪除組。** 刪除您擁有的任何組。 有關特定步驟，請參閱刪除本文的[組](#delete-a-group)部分。

    - **查看您參與的組。** 查看您是 **"我位於**區域的組"中成員的任何組的名稱。 選擇特定組名稱可提供有關組的更多詳細資訊，包括組類型、成員數、加入策略和活動成員清單。

    - **加入群組。** 加入現有組（您尚未成為成員）來自 **"我位於的組**"。 有關特定步驟，請參閱[加入現有組](#join-an-existing-group)。

## <a name="create-a-new-group"></a>建立新群組

1. 在 **"組"** 頁上，從 **"組"** 區域選擇 **"創建組**"。

    將顯示"**創建組**"框。

    ![創建組框](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 輸入必要資訊：

    - **群組類型：**

        - **安全。** 用來管理成員和電腦對使用者群組所共用之資源的存取權。 例如，您可以針對特定安全性原則建立安全性群組。 透過這麼做，您可以將一組權限同時授與所有成員，而不必個別為每個成員新增權限。

        - **辦公室 365.** 透過將共用信箱、行事曆、檔案、SharePoint 網站等的存取權授與成員，來提供共同作業的機會。 此選項也可讓您將群組的存取權授與組織外的人員。

    - **組名稱。** 為群組新增名稱，請使用易記且合理的名稱。

    - **組描述（可選）。** 為群組新增選擇性的描述。

    - **群組原則。** 選擇"允許所有人加入組"或僅允許組的擁有者添加成員。

3. 選取 [建立]****。

    新組由您作為擁有者創建，它將顯示在**您自己的組**清單中。 由於您是擁有者，因此此組也會顯示在 **"我位於的組"清單中**。

## <a name="edit-an-existing-group"></a>編輯現有組

創建組後，可以編輯其詳細資訊，包括更新任何現有資訊。

1. 選擇要從 **"組"** 頁上編輯的組，然後在*&lt;&gt;"group_name"* 頁上選擇 **"編輯詳細資訊**"。

    此時將顯示 **"編輯詳細資訊**"框，您可以更新最初創建組時添加的資訊。

2. 進行所有更改，然後選擇 **"更新**"。

## <a name="add-or-remove-a-member"></a>添加或刪除成員

您可以添加或刪除您擁有的任何組的成員。

1. 選擇要向其添加成員的組，然後在**+***&lt;group_name&gt;* 頁上選擇。

    ![添加具有 + 符號突出顯示的組成員](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 從"**添加成員"** 框中搜索要添加的成員，然後選擇"**添加**"。

    ![添加成員框，新成員要添加](media/my-apps-portal/my-apps-portal-add-member-page.png)

    向新成員發送邀請，以便開始訪問組織的應用。

3. 如果錯誤地添加了成員，或者成員已離開您的組織，則可以通過在*&lt;"group_name"&gt;* 頁上的成員名稱旁邊選擇 **"刪除成員"** 來刪除該成員。

    ![刪除成員，並突出顯示刪除連結](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>續訂 Office 365 組

如果您的組織允許，您可以續訂 Office 365 組，延長到期日期。

1. 選擇要續訂的 Office 365 組，然後選擇 **"續訂組**"。

    ![續訂 Office 365 組，延長到期日期](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. 按一下 **"確定"** 關閉確認訊息。

    刷新頁面後，您將看到更新**的"上次續訂"** 和"**組到期日期**"。

## <a name="delete-a-group"></a>刪除群組

您可以隨時刪除自己的任何組。 但是，如果錯誤地刪除了某個組，則需要創建該組並再次添加成員。

1. 選擇要永久刪除的組，然後在*&lt;"group_name"&gt;* 頁上選擇 **"刪除"組**。

    ![<Group_name>頁面，並突出顯示"刪除組"連結](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 在確認訊息上選擇 **"是**"。

    該組將被永久刪除。

## <a name="join-an-existing-group"></a>加入現有組

可以從 **"組"** 頁加入或離開已存在的組。

1. 在 **"組"** 頁上，從 **"我位於的組"中選擇****"加入組**"。

    將顯示"**加入組"** 頁。

    ![加入組頁面，突出顯示"加入"組按鈕](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 在 **"加入組"** 頁上，選擇要加入的組的名稱，查看關聯的組詳細資訊，然後，如果組可用，請選擇 **"加入組**"。

    如果組要求組擁有者批准成員資格，系統將要求您輸入業務理由，說明您需要加入組的原因，然後選擇 **"請求**"。 如果組不需要批准，您將立即添加為成員，並且該組將顯示在 **"我位於的組"清單中**。

3. 如果錯誤地加入了組，或者不再需要成為該組的一部分，則可以從 **"加入組"** 頁面中選擇組名稱，然後選擇 **"離開組**"。

    ![加入組頁面，突出顯示"離開"組按鈕](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>後續步驟

- [訪問和使用"我的應用"門戶上的應用](my-apps-portal-end-user-access.md)。

- [更改您的個人資料資訊](my-apps-portal-end-user-update-profile.md)。

- [執行您自己的訪問評論](my-apps-portal-end-user-access-reviews.md)。
