---
title: 管理組織對應用程式與群組的存取權 - Azure AD
description: 了解如何執行存取權檢閱，以從我的應用程式入口網站管理組織應用程式和群組的安全性存取。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 3b529c8112683281148751091ee93dd12ae73b4a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741934"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>從我的應用程式入口網站執行存取權檢閱

您可以使用公司或學校帳戶搭配 Web 型**我的應用程式**入口網站，來針對您的應用程式和群組執行存取權檢閱。 存取審查可協助您管理過期的存取權，或變更存取需求，並確保其經過檢閱和更新。

如果您沒有 [我的應用程式] 入口網站的存取權，請連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於**我的應用程式**使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="manage-access-reviews"></a>管理存取權檢閱

如果您的系統管理員已授與您自行執行存取權檢閱的權限，您可以從**我的應用程式**入口網站頁面上的 [存取權檢閱] 圖格，管理您的群組或應用程式存取權。

>[!Note]
>如果您沒有看到 [存取權檢閱] 圖格，表示您沒有執行存取檢閱的權限，或者您沒有任何擱置中的評論正在等待您的核准。 如果您認為您應該有該圖格的存取權，請連絡您的技術服務人員以取得協助。

## <a name="to-perform-your-access-reviews"></a>若要執行您的存取權檢閱

1. 登入您的公司或學校帳戶。

2. 開啟您的網頁瀏覽器並移至 https://myapps.microsoft.com ，或使用您組織所提供的連結。 例如，系統可能將您導向至組織的自訂頁面，例如 https://myapps.microsoft.com/contoso.com 。

    [應用程式] 頁面隨即出現，其中顯示您組織所擁有且可供您使用的所有雲端式應用程式。

    ![我的應用程式入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 選取 [存取權檢閱] 圖格來查看等待您核准的存取權檢閱清單。

    ![有組織擱置中存取權檢閱的 [存取權檢閱] 頁面](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 選取 [開始檢閱] 來開始存取權檢閱。

5. 檢閱您的存取權，並判斷其是否仍為必要。

    ![[存取權檢閱] 頁面，其中顯示檢閱詳細資料](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是系統管理員，而且允許您檢閱組織對群組和應用程式的存取權，您會看到不同的頁面。 如需有關為組織檢閱群組或應用程式的詳細資訊，請參閱 [Azure AD 存取權檢閱中的群組或應用程式存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review) \(部分機器翻譯\)。

6. 選取 [是] 以保留您的存取權，或 [否] 以移除您的存取權。

    如果選取 [是]，您可能需要在 [原因] 方塊中指定理由。

    ![[存取權檢閱] 頁面，顯示包含範例文字的 [原因] 方塊](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 選取 [提交]。

    您的存取權檢閱已完成，您將會返回**我的應用程式**入口網站。

    >[!Note]
    >您可以隨時變更您的存取權，直到存取權檢閱期間結束為止。 如果您移除對應用程式或群組的存取權，其不會立即移除。 當存取檢閱期間結束或系統管理員關閉檢閱時，就會進行移除。

## <a name="next-steps"></a>後續步驟

- [在我的應用程式入口網站上存取和使用應用程式](my-apps-portal-end-user-access.md)
- [變更設定檔資訊](my-apps-portal-end-user-update-profile.md)
- [檢視和更新您的群組相關資訊](my-apps-portal-end-user-groups.md)
