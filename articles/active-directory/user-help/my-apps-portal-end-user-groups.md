---
title: 在我的應用程式入口網站上更新您的群組資訊-Azure AD
description: 瞭解如何查看和更新您的群組相關資訊，包括觀看您擁有的群組、建立新群組、查看您已經是成員的群組，以及加入您不在其中的任何群組。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77022291"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>在我的應用程式入口網站上更新您的群組資訊

您可以透過 [我的應用程式]**** Web 入口網站使用工作或學校帳戶，來檢視和啟動貴組織的許多雲端式應用程式、更新您的設定檔和帳戶資訊、查看您的**群組**資訊，以及執行您應用程式和群組的**存取權檢閱**。 如果您沒有 [我的應用程式]**** 入口網站的存取權，則必須連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="view-your-groups-information"></a>查看您的群組資訊

如果您的系統管理員已授與您查看 [**群組**] 磚的許可權，您可以：

- **做為群組成員。** [查看詳細資料] 或 [離開任何群組]。

- **身為群組擁有者。** 查看詳細資料、建立新的群組、新增或移除成員，或刪除您的群組。

### <a name="to-view-your-groups-information"></a>若要查看您的群組資訊

1. 登入您的公司或學校帳戶。

2. 開啟您的網頁瀏覽器並https://myapps.microsoft.com移至，或使用您組織所提供的連結。 例如，您可能會被導向至您組織的自訂頁面，例如https://myapps.microsoft.com/contoso.com。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有且可供您使用的所有雲端應用程式。

    ![我的應用程式入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 選取 [**群組**] 圖格，以查看群組相關資訊。

    ![具有擁有和成員群組的群組頁面](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 根據您的許可權，您可以使用 [**群組**] 頁面來執行下列動作：

    - **檢查您擁有的群組。** 從 [**我自己的群組**] 區域中，查看您在組織內所擁有之任何群組的相關資訊。 選取特定的組名可提供更多關於群組的詳細資料，包括群組類型、成員數目、聯結原則和作用中成員清單。

    - **建立新群組。** 建立新的群組，並將您當做 [**我自己的群組**] 區域的擁有者。 如需特定步驟，請參閱本文的[建立新的群組](#create-a-new-group)一節。

    - **編輯現有的群組。** 編輯任何您自己群組的詳細資料。 如需特定步驟，請參閱本文的[編輯現有的群組](#edit-an-existing-group)一節。

    - **新增或移除成員。** 新增或移除您所擁有之群組的成員。 如需特定步驟，請參閱本文的[新增或移除成員](#add-or-remove-a-member)一節。

    - **更新 Office 365 群組。** 如果您的組織允許，您可以續訂 Office 365 群組。 如需特定步驟，請參閱本文的[續訂 Office 365 群組](#renew-an-office-365-group)一節。 

    - **刪除群組。** 刪除您擁有的任何群組。 如需特定步驟，請參閱本文的[刪除群組](#delete-a-group)一節。

    - **檢查您所隸屬的群組。** 從 [**我所在的群組**] 區域中，查看您是其成員的任何群組名稱。 選取特定的組名可提供更多關於群組的詳細資料，包括群組類型、成員數目、聯結原則和作用中成員清單。

    - **加入群組。** 從 [我所在的群組] 區域**中**，加入您還不是其成員的現有群組。 如需特定步驟，請參閱[加入現有的群組](#join-an-existing-group)。

## <a name="create-a-new-group"></a>建立新群組

1. 在 [**群組**] 頁面上，從 [**我擁有的群組**] 區域中選取 [**建立群組**]。

    [**建立群組**] 方塊隨即出現。

    ![建立群組方塊](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 輸入必要資訊：

    - **群組類型：**

        - **安全級.** 用來管理成員和電腦對使用者群組所共用之資源的存取權。 例如，您可以針對特定安全性原則建立安全性群組。 透過這麼做，您可以將一組權限同時授與所有成員，而不必個別為每個成員新增權限。

        - **Office 365。** 透過將共用信箱、行事曆、檔案、SharePoint 網站等的存取權授與成員，來提供共同作業的機會。 此選項也可讓您將群組的存取權授與組織外的人員。

    - **組名。** 為群組新增名稱，請使用易記且合理的名稱。

    - **群組描述（選擇性）。** 為群組新增選擇性的描述。

    - **群組原則。** 選擇 [允許每個人加入群組] 或 [僅允許群組的擁有者新增成員]。

3. 選取 [建立]  。

    新群組會以您的擁有者身分建立，並出現在 [**我擁有的群組**] 清單中。 因為您是擁有者，所以此群組也會出現在 [**我所在的群組**] 清單中。

## <a name="edit-an-existing-group"></a>編輯現有的群組

建立群組之後，您可以編輯其詳細資料，包括更新任何現有的資訊。

1. 從 [**群組**] 頁面中選取您想要編輯的群組，然後選取 [ * &lt;group_name&gt; * ] 頁面上的 [**編輯詳細資料**]。

    [**編輯詳細資料**] 方塊隨即出現，您可以更新最初建立群組時所新增的資訊。

2. 進行所有變更，然後選取 [**更新**]。

## <a name="add-or-remove-a-member"></a>新增或移除成員

您可以為您擁有的任何群組新增或移除成員。

1. 選取您想要新增成員的群組，然後在 [ **+** * &lt;group_name&gt; * ] 頁面上選取。

    ![新增群組成員，並反白顯示 + 符號](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 在 [**新增成員**] 方塊中搜尋您想要新增的成員，然後選取 [**新增**]。

    ![[新增成員] 方塊，包含要加入的新成員](media/my-apps-portal/my-apps-portal-add-member-page.png)

    邀請會傳送給新的成員，以開始存取組織的應用程式。

3. 如果您不小心新增成員，或成員已離開您的組織，您可以在 [ * &lt; &gt; group_name* ] 頁面上，選取成員名稱旁邊的 [**移除成員**] 來移除成員。

    ![移除成員，並反白顯示移除連結](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>更新 Office 365 群組

如果您的組織允許，您可以續訂 Office 365 群組，延長您的到期日。

1. 選取您想要更新的 Office 365 群組，然後選取 [**更新群組**]。

    ![續訂 Office 365 群組，延長到期日](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. 按一下 **[確定]** 以關閉確認訊息。

    重新整理頁面之後，您會看到最近更新的**上次**更新和**群組到期**日。

## <a name="delete-a-group"></a>刪除群組

您可以隨時刪除任何您自己的群組。 不過，如果您不小心刪除了某個群組，就必須建立它並再次新增成員。

1. 選取您要永久刪除的群組，然後選取 [ * &lt; &gt; group_name* ] 頁面上的 [**刪除群組**]。

    ![已反白顯示刪除群組連結的 <Group_name> 頁面](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 在確認訊息上選取 **[是]** 。

    群組會永久刪除。

## <a name="join-an-existing-group"></a>加入現有的群組

您可以從 [**群組**] 頁面加入或離開已存在的群組。

1. 在 [**群組**] 頁面上，從 [**我所在的群組**] 區域中選取 [**加入群組**]。

    [**加入群組**] 頁面隨即出現。

    ![[加入群組] 頁面，反白顯示 [加入群組] 按鈕](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 在 [**加入群組**] 頁面上，選取您想要加入的組名、查看相關聯的群組詳細資料，然後如果群組可供使用，請選取 [**加入群組**]。

    如果群組需要群組擁有者核准成員資格，系統會要求您輸入商業理由，以瞭解您需要加入群組的原因，然後選取 [**要求**]。 如果群組不需要核准，您就會立即新增為成員，而群組會出現在 [**我所在的群組**] 清單中。

3. 如果您不小心加入群組，或是您不再需要它的一部分，可以從 [**加入群組**] 頁面中選取組名，然後選取 [**離開群組**]。

    ![[加入群組] 頁面，反白顯示 [保留群組] 按鈕](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>後續步驟

- [在我的應用程式入口網站上存取和使用應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [執行您自己的存取權審查](my-apps-portal-end-user-access-reviews.md)。
