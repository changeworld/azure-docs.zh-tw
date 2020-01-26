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
ms.openlocfilehash: d754964ac6c16c87e6f0825b4f1a955d38fa3530
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756223"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教學課程︰設定 G Suite 來自動佈建使用者

本教學課程的目的是要示範在 G Suite 中執行的步驟，並 Azure Active Directory （Azure AD）將 Azure AD 設定為自動布建和取消布建使用者和/或群組至 G Suite。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。

> [!NOTE]
> G Suite 連接器最近已于2019年10月更新。 對 G Suite 連接器所做的變更包括：
> - 已新增對其他 G Suite 使用者和群組屬性的支援。 
> - 已更新 G Suite 目標屬性名稱，以符合[這裡](https://developers.google.com/admin-sdk/directory)定義的內容。
> - 已更新預設屬性對應。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 G Suite 整合，您需要下列項目：

- Azure AD 租用戶
- [G Suite 租使用者](https://gsuite.google.com/pricing.html)
- G Suite 上具有系統管理員許可權的使用者帳戶。

## <a name="assign-users-to-g-suite"></a>將使用者指派給 G Suite

Azure Active Directory 使用稱為「指派」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 G Suite。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 G Suite：

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>將使用者指派給 G Suite 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 G Suite，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 G Suite 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-g-suite-for-provisioning"></a>設定 G Suite 以提供布建

將 G Suite 設定為使用 Azure AD 自動布建使用者之前，您必須啟用 G Suite 的 SCIM 布建。

1. 使用您的系統管理員帳戶登入[G Suite 管理主控台](https://admin.google.com/)，然後選取 [**安全性**]。 如果您沒有看到連結，它可能隱藏在畫面底部的 [其他控制項] 功能表之下。

    ![選取安全性。][10]

1. 在 [安全性] 頁面上，選取 [API 參考]。

    ![選取 API 參考。][15]

1. 選取 [啟用 API 存取]。

    ![選取 API 參考。][16]

   > [!IMPORTANT]
   > 對於您想要布建至 G Suite 的每位使用者，他們在 Azure AD 中的使用者名稱**必須**系結至自訂網域。 例如，G Suite 不會接受類似 bob@contoso.onmicrosoft.com 的使用者名稱。 另一方面，則接受 bob@contoso.com。 您可以遵循[這裡](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的指示來變更現有使用者的網域。

1. 當您使用 Azure AD 新增並驗證所需的自訂網域之後，您必須使用 G Suite 再次進行驗證。 若要驗證 G Suite 中的網域，請參閱下列步驟：

    a. 在 [ [G Suite] 管理主控台](https://admin.google.com/)中，選取 [**網域**]。

    ![選取網域][20]

    b. 選取 [新增網域或網域別名]。

    ![新增網域][21]

    c. 選取 [新增另一個網域]，然後輸入您想要新增的網域名稱。

    ![輸入您的網域名稱][22]

    d. 選取 [繼續並驗證網域擁有權]。 然後依照步驟以驗證您擁有網域名稱。 如需有關如何向 Google 驗證您的網域的完整指示，請參閱[驗證您的網站擁有權](https://support.google.com/webmasters/answer/35179)。

    e. 針對您想要新增至 G Suite 的任何其他網域重複上述步驟。

1. 接下來，判斷您想要使用哪一個系統管理員帳戶來管理 G Suite 中的使用者布建。 流覽至 [**管理員角色**]。

    ![選取 Google Apps][26]

1. 針對該帳戶的系統**管理員角色**，編輯該角色的**許可權**。 務必啟用所有 [管理 API 權限]，以便讓此帳戶可以用來佈建。

    ![選取 Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>從資源庫新增 G Suite

若要使用 Azure AD 來設定 G Suite 來自動布建使用者，您必須從 Azure AD 應用程式資源庫將 G Suite 新增至受控 SaaS 應用程式清單。 

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

1. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

1. 在搜尋方塊中，輸入**G suite**，在結果面板中選取 [ **g suite** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>設定自動使用者布建至 G Suite 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 G Suite 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 G Suite 的 SAML 型單一登入，請遵循[g Suite 單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)課程中提供的指示。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

> [!NOTE]
> 若要深入瞭解 G Suite 的目錄 API 端點，請參閱[目錄 api](https://developers.google.com/admin-sdk/directory)。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>若要在 Azure AD 中設定 G Suite 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [G Suite]。

    ![應用程式清單中的 G Suite 連結](common/all-applications.png)

1. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

1. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

1. 在 [系統管理員認證] 區段下，選取 [授權]。 這會在新的瀏覽器視窗中開啟 Google 授權對話方塊。

    ![G Suite 授權](media/google-apps-provisioning-tutorial/authorize.png)

1. 確認您想要授與 Azure AD 許可權，以便對您的 G Suite 租使用者進行變更。 選取 [接受]。

    ![確認權限。][28]

1. 在 Azure 入口網站中，選取 [測試連線] 以確保 Azure AD 可以連線至您的應用程式。 如果連線失敗，請確定您的 G Suite 帳戶具有小組系統管理員權限。 然後再試一次**授權**步驟。

1. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 [檔案]。

1. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者到 G Suite**]。

    ![G Suite 使用者對應](media/google-apps-provisioning-tutorial/usermappings.png)

1. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 G Suite 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 G Suite 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![G Suite 使用者屬性](media/google-apps-provisioning-tutorial/userattributes.png)

1. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至 G Suite**]。

    ![G Suite 群組對應](media/google-apps-provisioning-tutorial/groupmappings.png)

1. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 G Suite 的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對 G Suite 中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。 UI 會顯示 Azure AD 與 G Suite 之間的一組預設屬性對應。 您可以按一下 [新增對應]，選擇新增其他屬性，例如組織單位。

    ![G Suite 群組屬性](media/google-apps-provisioning-tutorial/groupattributes.png)

1. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

1. 若要啟用 G Suite 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

1. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 G Suite 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

1. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 G Suite 上的 Azure AD 布建服務所執行的所有動作。

> [!NOTE]
> 如果使用者已經有使用 Azure AD 使用者之電子郵件地址的現有個人/消費者帳戶，則可能會在執行目錄同步作業之前，使用 Google Transfer Tool 來解決一些問題。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>常見問題
* 當用來建立連接的帳戶不是 GSuite 的系統管理員時，可能會發生授權失敗。 確定用來授與存取權的帳戶具有使用者需要布建之**所有網域**的系統管理員許可權。 

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

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
