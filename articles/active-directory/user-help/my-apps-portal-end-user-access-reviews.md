---
title: 管理組織對應用&組的訪問 - Azure AD
description: 瞭解如何執行訪問審核，以從"我的應用"門戶管理組織的應用和組的安全存取權限。
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
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062368"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>從"我的應用"門戶執行訪問審核

您可以將工作或學校帳戶與基於 Web**的"我的應用"** 門戶一起用於應用和組的訪問審核。 訪問審核可説明您管理過時的訪問或更改訪問要求，並確保這些訪問被審查和更新。

如果您沒有 [我的應用程式]**** 入口網站的存取權，請連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於**我的應用程式**使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="manage-access-reviews"></a>管理存取權檢閱

如果管理員已授予您執行自己的訪問評論的許可權，則可以從 **"我的應用"** 門戶頁面上的 **"訪問"評論**磁貼管理組或應用存取權限。

>[!Note]
>如果您沒有看到 **"訪問審核"** 磁貼，則意味著您沒有執行訪問審核的許可權，或者您沒有任何待決審核等待您的批准。 如果您認為您應該有權訪問磁貼，請聯繫您的説明台尋求説明。

## <a name="to-perform-your-access-reviews"></a>執行訪問審核

1. 登入您的公司或學校帳戶。

2. 打開 Web 瀏覽器並轉到https://myapps.microsoft.com，或使用組織提供的連結。 例如，您可能會被定向到組織的自訂頁面，例如https://myapps.microsoft.com/contoso.com。

    將顯示 **"應用"** 頁面，顯示組織擁有並可供您使用的所有基於雲的應用。

    !["我的應用"門戶中的應用頁面](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 選擇 **"訪問審核"** 磁貼以查看等待您的批准的訪問審核清單。

    ![訪問審核頁面，包含組織的掛起訪問審核](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 選擇 **"開始審閱"** 以開始訪問審核。

5. 查看您的存取權限並確定是否仍有必要。

    ![訪問審閱頁面，顯示審核詳細資訊](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是管理員，並且允許查看組織對組和應用的存取權限，您將看到其他頁面。 有關查看組織的組或應用的詳細資訊，請參閱[在 Azure AD 訪問審核中查看對組或應用程式的存取權限](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 選擇 **"是**"可保留您的存取權限或 **"否**"以刪除您的存取權限。

    如果選擇 **"是**"，則可能需要在 **"原因"** 框中指定對齊方式。

    ![訪問審閱頁面，顯示帶有示例文本的"原因"框](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 選取 [提交]****。

    您的訪問審核已完成，您將返回到 **"我的應用"** 門戶。

    >[!Note]
    >您可以隨時更改存取權限，直到訪問審核期結束。 如果刪除了對應用或組的訪問，則不會立即刪除。 當訪問審閱期結束時或管理員關閉審核時，將發生刪除。

## <a name="next-steps"></a>後續步驟

- [訪問和使用"我的應用"門戶中的應用](my-apps-portal-end-user-access.md)
- [變更設定檔資訊](my-apps-portal-end-user-update-profile.md)
- [查看和更新與組相關的資訊](my-apps-portal-end-user-groups.md)
