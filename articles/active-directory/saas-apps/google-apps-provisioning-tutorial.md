---
title: 教學課程︰以 Azure Active Directory 設定 G Suite 來自動佈建使用者 | Microsoft Docs
description: 了解如何將使用者帳戶從 Azure AD 針對 G Suite 進行自動佈建和取消佈建。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057697"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教學課程︰設定 G Suite 來自動佈建使用者

本教程的目的是演示在 G 套件和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 G 套件預配和取消預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。

> [!NOTE]
> G Suite 連接器最近于 2019 年 10 月更新。 對 G Suite 連接器所做的更改包括：
> - 添加了對其他 G Suite 使用者和組屬性的支援。 
> - 更新了 G Suite 目標屬性名稱，以匹配[此處](https://developers.google.com/admin-sdk/directory)定義的內容。
> - 更新了預設屬性映射。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 G Suite 整合，您需要下列項目：

- Azure AD 租用戶
- [G 套房租戶](https://gsuite.google.com/pricing.html)
- 具有管理員許可權的 G 套件上的使用者帳戶。

## <a name="assign-users-to-g-suite"></a>將使用者指派給 G Suite

Azure 活動目錄使用稱為分配的概念來確定哪些使用者應接收對選定應用的存取權限。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和/或組才會同步。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 G Suite。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 G Suite：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>將使用者分配給 G 套件的重要提示

* 建議將單個 Azure AD 使用者分配給 G Suite 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 G Suite 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-g-suite-for-provisioning"></a>用於預配的設置 G 套件

在配置 G 套件以使用 Azure AD 自動預配使用者之前，需要在 G 套件上啟用 SCIM 預配。

1. 使用管理員帳戶登錄到[G Suite 管理主控台](https://admin.google.com/)，然後選擇 **"安全**"。 如果您沒有看到連結，它可能隱藏在畫面底部的 [其他控制項]**** 功能表之下。

    ![選取安全性。][10]

1. 在 [安全性]**** 頁面上，選取 [API 參考]****。

    ![選取 API 參考。][15]

1. 選取 [啟用 API 存取]****。

    ![選取 API 參考。][16]

   > [!IMPORTANT]
   > 對於要預配到 G Suite 的每個使用者，**必須在**Azure AD 中將其使用者名綁定到自訂域。 例如，G Suite 不會接受類似 bob@contoso.onmicrosoft.com 的使用者名稱。 另一方面，則接受 bob@contoso.com。 您可以按照[此處](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的說明更改現有使用者的域。

1. 使用 Azure AD 添加並驗證所需的自訂域後，必須再次使用 G Suite 驗證它們。 要驗證 G Suite 中的域，請參閱以下步驟：

    a. 在[G 套件管理主控台中](https://admin.google.com/)，選擇**域**。

    ![選取網域][20]

    b. 選取 [新增網域或網域別名]****。

    ![新增網域][21]

    c. 選取 [新增另一個網域]****，然後輸入您想要新增的網域名稱。

    ![輸入您的網域名稱][22]

    d. 選取 [繼續並驗證網域擁有權]****。 然後依照步驟以驗證您擁有網域名稱。 有關如何使用 Google 驗證您的功能變數名稱的全面說明，請參閱[驗證您的網站擁有權](https://support.google.com/webmasters/answer/35179)。

    e. 對於要添加到 G Suite 的任何其他域，請重複上述步驟。

1. 接下來，確定要使用哪個管理員帳戶來管理 G Suite 中的使用者預配。 導航到**管理員角色**。

    ![選取 Google Apps][26]

1. 對於該帳戶的**管理員角色**，編輯該角色**的許可權**。 務必啟用所有 [管理 API 權限]****，以便讓此帳戶可以用來佈建。

    ![選取 Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>從圖庫添加 G 套件

要配置 G 套件以使用 Azure AD 自動預配使用者，需要將 Azure AD 應用程式庫中的 G 套件添加到託管 SaaS 應用程式清單中。 

1. 在**[Azure 門戶](https://portal.azure.com)** 中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

1. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 要添加新應用程式，請選擇窗格頂部的 **"新建應用程式**"按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

1. 在搜索框中，在結果面板中選擇**G Suite，** 然後按一下"**添加**"按鈕以添加應用程式。 **G Suite**

    ![結果清單中的 G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>將自動使用者預配配置為 G 套件 

本節將指導您完成將 Azure AD 預配服務配置為基於 Azure AD 中的使用者和/或組分配在 G 套件中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以按照[G Suite 單一登入教程](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)中提供的說明，選擇啟用基於 SAML 的單一登入 G 套件。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

> [!NOTE]
> 要瞭解有關 G 套件的目錄 API 終結點的詳細資訊，請參閱[目錄 API](https://developers.google.com/admin-sdk/directory)。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>要在 Azure AD 中配置 G 套件的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [G Suite]****。

    ![應用程式清單中的 G Suite 連結](common/all-applications.png)

1. 選擇 **"預配"** 選項卡。

    ![預配選項卡](common/provisioning.png)

1. 將**預配模式**設置為 **"自動**"。

    ![預配選項卡](common/provisioning-automatic.png)

1. 在 [系統管理員認證]**** 區段下，選取 [授權]****。 這會在新的瀏覽器視窗中開啟 Google 授權對話方塊。

    ![G 套件授權](media/google-apps-provisioning-tutorial/authorize.png)

1. 確認要授予 Azure AD 許可權以對 G Suite 租戶進行更改。 選取 [接受]****。

    ![確認權限。][28]

1. 在 Azure 入口網站中，選取 [測試連線]**** 以確保 Azure AD 可以連線至您的應用程式。 如果連線失敗，請確定您的 G Suite 帳戶具有小組系統管理員權限。 然後再試一次**授權**步驟。

1. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 [儲存]****。

1. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 G 套件**。

    ![G 套件使用者映射](media/google-apps-provisioning-tutorial/usermappings.png)

1. 在**屬性對應**部分中查看從 Azure AD 同步到 G 套件的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 G Suite 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![G 套件使用者屬性](media/google-apps-provisioning-tutorial/userattributes.png)

1. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄組同步到 G 套件**。

    ![G 套件組映射](media/google-apps-provisioning-tutorial/groupmappings.png)

1. 在**屬性對應**部分中查看從 Azure AD 同步到 G 套件的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 G Suite 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。 UI 顯示 Azure AD 和 G 套件之間的預設屬性映射集。 您可以通過按一下"添加新映射"選擇添加其他屬性，如組織單元。

    ![G 套件組屬性](media/google-apps-provisioning-tutorial/groupattributes.png)

1. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

1. 要啟用 G 套件的 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

1. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 G Suite 的使用者和/或組。

    ![佈建範圍](common/provisioning-scope.png)

1. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並關注指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 G Suite 上執行的所有操作。

> [!NOTE]
> 如果使用者已經使用 Azure AD 使用者的電子郵件地址建立了現有的個人/消費者帳戶，則可能會導致一些問題，在執行目錄同步之前，可以使用 Google 傳輸工具解決。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>常見問題
* 當用於建立連接的帳戶不適用於 GSuite 中的管理員時，可能會發生授權失敗。 確保用於授權訪問的帳戶具有使用者需要預配**的所有域**的管理員許可權。 
* Azure AD 支援禁用 GSuite 中的使用者，以便他們無法訪問應用程式，但不會刪除 GSuite 中的使用者。

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
