---
title: 教學課程：以 Azure Active Directory 設定 Elium 來自動佈建使用者 | Microsoft Docs
description: 了解如何將 Azure Active Directory 設定為可對 Elium 自動佈建及取消佈建使用者帳戶。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005498"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教學課程：設定 Elium 來自動佈建使用者

本教學課程會說明如何設定 Elium 和 Azure Active Directory (Azure AD)，以將使用者和/或群組自動佈建和取消佈建至 Elium。

> [!NOTE]
> 本教學課程描述以 Azure AD 使用者佈建服務為基礎的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 自動對 SaaS 應用程式進行和取消使用者的佈建](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需有關預覽版的 Azure 功能使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您已具備下列必要條件：

* Azure AD 租用戶
* [Elium 租用戶](https://www.elium.com/pricing/)
* Elium 中具有管理員權限的使用者帳戶

## <a name="assigning-users-to-elium"></a>將使用者指派給 Elium

Azure AD 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有已指派至 Azure AD 中應用程式的使用者和群組會進行同步處理。

在設定並啟用自動佈建使用者之前，請決定 Azure AD 中有哪些使用者和群組需要存取 Elium。 然後，遵循[將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)中的步驟，將這些使用者和群組指派給 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>將使用者指派給 Elium 的重要秘訣 

建議您將單一 Azure AD 使用者指派給 Elium，以測試自動佈建使用者的設定。 稍後可以指派更多使用者和群組。

將使用者指派給 Elium 時，您必須在 [指派] 對話方塊中選取有效的應用程式特有角色 (如果有)。 具有「預設存取」角色的使用者會從佈建中排除。

## <a name="set-up-elium-for-provisioning"></a>設定 Elium 以進行佈建

使用 Azure AD 設定 Elium 來自動佈建使用者之前，您必須在 Elium 上啟用跨網域身分識別管理系統 (SCIM) 的佈建。 請遵循下列步驟：

1. 登入 Elium，並移至 **我的設定檔** > **設定**。

    ![Elium 中的 [設定] 功能表項目](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角的 **進階** 底下，選取 [安全性]。

    ![Elium 中的安全性連結](media/Elium-provisioning-tutorial/security.png)

1. 複製 **租用戶 URL** 和 **祕密權杖** 值。 您稍後會在 Azure 入口網站中 Elium 應用程式的 **佈建** 索引標籤的對應欄位中使用這些值。

    ![Elium 中的租用戶 URL 和祕密權杖欄位](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>從資源庫新增 Elium

若要將 Elium 設定為可使用 Azure AD 自動佈建使用者，您必須從 Azure AD 應用程式庫將 Elium 新增至您的受控軟體即服務 (SaaS) 應用程式清單。 請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 功能表項目](common/select-azuread.png)

1. 移至 [企業應用程式]，然後選取 [所有應用程式]。

     ![Azure AD 企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 若要新增應用程式，請選取窗格頂端的 [新增應用程式]。

    ![新增應用程式連結](common/add-new-app.png)

1. 在搜尋方塊中，輸入 **Elium**，在結果清單中選取 [Elium]，然後選取 [新增] 以新增應用程式。

    ![資源庫搜尋方塊](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>對 Elium 設定自動使用者佈建

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者和群組指派，在 Elium 中建立、更新和停用使用者和群組。

> [!TIP]
> 建議您遵循 [Elium 單一登入教學課程](Elium-tutorial.md)中的指示，選擇根據安全性聲明標記語言 (SAML) 啟用 Elium 的單一登入。 雖然單一登入和自動使用者佈建這兩個功能互相補充，您還是可以將這兩者分開設定。

若要在 Azure AD 中為 Elium 設定自動佈建使用者，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![Azure AD 企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Elium]。

    ![企業應用程式刀鋒視窗中的應用程式清單](common/all-applications.png)

1. 選取 [佈建] 索引標籤。

    ![企業應用程式刀鋒視窗中的佈建索引標籤](common/provisioning.png)

1. 將 [佈建模式] 設定為 [自動]。

    ![佈建模式的自動設定](common/provisioning-automatic.png)

1. 在 [管理員認證] 區段中，在 **租用戶 URL** 欄位中輸入 **\<tenantURL\>/scim/v2**。 (**tenantURL** 是先前從 Elium 管理主控台中取出的值。)此外，在 **祕密權杖** 欄位中輸入 Elium **祕密權杖** 值。 最後，若要確保 Azure AD 可以連線到 Elium，請選取 [測試連線]。 如果連線失敗，請確定您的 Elium 帳戶具有管理員權限並再試一次。

    ![管理員認證中的租用戶 URL 和祕密權杖欄位](common/provisioning-testconnection-tenanturltoken.png)

1. 在 [通知電子郵件] 欄位中，輸入會收到佈建錯誤通知的個人或群組電子郵件地址。 接著選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 [檔案]  。

1. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Elium]。

    ![將 Azure AD 使用者對應的連結同步處理至 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在 **屬性對應** 區段中，檢閱從 Azure AD 同步至 Elium 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Elium 中的使用者帳戶，以進行更新作業。 選取 [儲存] 認可任何變更。

    ![Azure AD 和 Elium 之間的屬性對應](media/Elium-provisioning-tutorial/userattribute.png)

1. 若要設定範圍篩選條件，請遵循[範圍篩選條件教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的指示。

1. 若要啟用 Elium 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

    ![佈建狀態切換為開啟](common/provisioning-toggle-on.png)

1. 在 **設定** 區段的 **範圍** 下拉式清單方塊中，選取您要佈建的值，以定義要佈建到 Elium 的使用者和群組。

    ![佈建範圍清單方塊](common/provisioning-scope.png)

1. 準備好要佈建時，請選取 [儲存]。

    ![佈建組態的儲存按鈕](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和群組，啟動首次同步處理。 首次同步處理所花的時間比後續同步處理更久。 如需佈建所需時間的詳細資訊，請參閱[佈建使用者需要多久時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)。

使用 [目前狀態] 區段來監視進度，並遵循連結來取得佈建活動報告。 佈建活動報告描述 Azure AD 佈建服務在 Elium 上執行的所有動作。 如需詳細資訊，請參閱[檢查使用者佈建的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 若要讀取 Azure AD 佈建記錄，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
