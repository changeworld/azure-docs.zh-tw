---
title: 管理組織對應用程式 & 群組的存取權-Azure AD
description: 瞭解如何執行存取權審查，以從我的應用程式入口網站管理組織應用程式和群組的安全性存取。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e739024686bdac497b9b7dd450c5ed46e3cf9a63
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705015"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>從我的應用程式入口網站執行存取權審查

您可以透過 [我的應用程式] Web 入口網站使用工作或學校帳戶，來檢視和啟動貴組織的許多雲端式應用程式、更新您的設定檔和帳戶資訊、查看您的**群組**資訊，以及執行您應用程式和群組的**存取權檢閱**。 如果您沒有 [我的應用程式] 入口網站的存取權，則必須連絡技術服務人員以取得權限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，可以在[應用程式管理文件](https://docs.microsoft.com/azure/active-directory/manage-apps)中找到更多關於如何設定和管理雲端式應用程式的資訊。

## <a name="manage-access-reviews"></a>管理存取權檢閱

如果您的系統管理員已授與您執行自己的存取權審查的許可權，您可以從**我的應用程式**入口網站頁面上的 [**存取審查**] 磚，管理您的群組或應用程式存取權。

>[!Note]
>如果您看不到 [**存取評論**] 磚，表示您沒有執行存取權審查的許可權，或者您沒有任何擱置中的評論正在等待您的核准。 如果您認為您應該有磚的存取權，請洽詢服務台尋求協助。

### <a name="to-perform-your-access-reviews"></a>若要執行您的存取權審查

1. 登入您的公司或學校帳戶。

2. 開啟您的網頁瀏覽器並移至 https://myapps.microsoft.com ，或使用您組織所提供的連結。 例如，您可能會被導向至您組織的自訂頁面，例如 https://myapps.microsoft.com/contoso.com 。

    [**應用程式**] 頁面隨即出現，其中顯示貴組織所擁有且可供您使用的所有雲端應用程式。

    ![我的應用程式入口網站中的 [應用程式] 頁面](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. 選取 [**存取評論**] 磚，以查看等待您核准的存取評論清單。

    ![具有組織暫止存取審查的 [存取評論] 頁面](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. 選取 [**開始審核**] 以開始存取權審查。

5. 檢查您的存取權，並判斷是否仍然需要。

    ![[存取權審查] 頁面，其中顯示審核詳細資料](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果您是系統管理員，而且允許您檢查組織對群組和應用程式的存取權，您會看到不同的頁面。 如需有關為組織審查群組或應用程式的詳細資訊，請參閱[在 Azure AD 存取審查中審查群組或應用程式的存取權](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。

6. 選取 **[是]** 以保留您的存取權，或按一下 [**否**] 以移除存取權。

    如果您選取 [**是]** ，您可能需要在 [**原因**] 方塊中指定理由。

    ![[存取權審查] 頁面，顯示 [原因] 方塊與範例文字](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 選取 [提交]。

    您的存取權檢查已完成，您會返回**我的應用程式**入口網站。

    >[!Note]
    >您可以隨時變更您的存取權，直到存取權審查期間結束為止。 如果您移除對應用程式或群組的存取權，它不會立即移除。 當存取審查期間結束或系統管理員關閉審核時，就會進行移除。

## <a name="next-steps"></a>後續步驟

- [在我的應用程式入口網站上存取和使用應用程式](my-apps-portal-end-user-access.md)。

- [變更您的設定檔資訊](my-apps-portal-end-user-update-profile.md)。

- [查看和更新您的群組相關資訊](my-apps-portal-end-user-groups.md)。
