---
title: 教學課程：使用 Azure Active Directory 設定閃爍以自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 自動布建和取消布建使用者帳戶以使其閃爍。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 9ebcbf4a-0cf9-41c3-96af-d8ab6ab11639
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 314445275f99898913bd91eb8abc2a5acdab098a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381031"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的閃爍

本教學課程的目的是要示範要在閃爍和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者以使其閃爍。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先決條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [閃爍租使用者](https://joinblink.com/pricing)
* 中的使用者帳戶會以系統管理員許可權閃爍。

## <a name="assigning-users-to-blink"></a>將使用者指派給閃爍

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組成員會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組成員需要有閃爍的存取權。 一旦決定後，您可以依照此處的指示，將這些使用者和/或群組指派為閃爍：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>將使用者指派給閃爍的重要秘訣

* 建議將單一 Azure AD 使用者指派為閃爍，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給閃爍時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-blink-for-provisioning"></a>布建的設定閃爍

1. 記錄[支援案例](https://support.joinblink.com)或電子郵件**閃爍支援** support@joinblink.com以要求 SCIM token。 .

2.  複製**SCIM 驗證權杖**。 此值會在 Azure 入口網站中閃爍應用程式的 [布建] 索引標籤的 [密碼權杖] 欄位中輸入。

## <a name="add-blink-from-the-gallery"></a>從資源庫新增閃爍

在使用 Azure AD 來設定自動使用者布建的閃爍之前，您需要從 Azure AD 應用程式資源庫將閃爍新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式資源庫新增閃爍，請執行下列步驟：**

1. 在**[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**閃爍**，在結果面板中選取 [**閃爍**]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的閃爍](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>設定自動使用者布建以閃爍 

本節會引導您完成設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派來建立、更新和停用使用者的步驟。

> [!TIP]
> 您也可以選擇啟用以 SAML 為基礎的單一登入來進行閃爍，請遵循[閃爍單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)課程中提供的指示。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此的補充

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>若要在 Azure AD 中設定自動使用者布建以進行閃爍：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Blink]****。

    ![應用程式清單中的閃爍連結](common/all-applications.png)

3. 選取 [**布**建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將布建模式設定為 [**自動** **]** 。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下`https://api.joinblink.com/scim` ，輸入 [**租使用者 URL**]。 輸入稍早在**秘密權杖**中所取得的**SCIM Authentication Token**值。 按一下 [**測試連接**]，以確保 Azure AD 可以連接至閃爍。 如果連線失敗，請確定您的閃爍帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 **[儲存]** 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者以使其閃爍**]。

    ![使用者對應閃爍](media/blink-provisioning-tutorial/User_mappings.png)

9. 檢查從 Azure AD 同步處理的使用者屬性，以在 [**屬性對應**] 區段中閃爍。 選取為 [比對] 屬性**的屬性會**用來比對閃爍的使用者帳戶，以進行更新作業。 選取 [儲存]**** 按鈕以認可所有變更。

    ![使用者屬性閃爍](media/blink-provisioning-tutorial/user-attributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Azure AD 布建服務進行閃爍，請將 [**設定**] 區段中的 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建的使用者。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

這項作業會啟動 [**設定**] 區段的 [**範圍**] 中定義之所有使用者的首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述在閃爍時，Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶布建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

