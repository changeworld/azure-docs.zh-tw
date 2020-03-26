---
title: 教學課程：設定 Cisco Webex 來搭配 Azure Active Directory 進行自動使用者佈建 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 將使用者帳戶自動佈建及取消佈建至 Cisco Webex。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058489"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>教學課程：設定 Cisco Webex 來自動佈建使用者

本教學課程旨在示範設定 Azure AD 以將使用者自動佈建及取消佈建至 Cisco Webex 時，在 Cisco Webex 與 Azure Active Directory (Azure AD) 中要執行的步驟。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶。
* [Cisco Webex 租用戶](https://www.webex.com/pricing/index.html)。
* Cisco Webex 中具有管理員權限的使用者帳戶。

## <a name="adding-cisco-webex-from-the-gallery"></a>從資源庫新增 Cisco Webex

設定 Cisco Webex 來搭配 Azure AD 進行自動使用者佈建之前，您必須從 Azure AD 應用程式庫將 Cisco Webex 新增至您的受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 Cisco Webex，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Cisco Webex**，從結果面板中選取 [Cisco Webex]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 Cisco Webex](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>將使用者指派給 Cisco Webex

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

設定並啟用自動使用者佈建之前，您應先決定 Azure AD 中的哪些使用者需要存取 Cisco Webex。 決定之後，即可依照這裡的指示，將這些使用者指派給 Cisco Webex︰

* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>將使用者指派給 Cisco Webex 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 Cisco Webex，以測試自動使用者佈建設定。 其他使用者可能會稍後再指派。

* 將使用者指派給 Cisco Webex 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特有角色 (如果有的話)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>設定對 Cisco Webex 的自動使用者佈建

本節將引導您逐步設定 Azure AD 佈建服務，以根據 Azure AD 中的使用者指派，在 Cisco Webex 中建立、更新及停用使用者。

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>在 Azure AD 中為 Cisco Webex 設定自動使用者佈建：

1. 登入 [Azure 入口網站](https://portal.azure.com)，依序選取 [企業應用程式]  、[所有應用程式]  及 [Cisco Webex]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Cisco Webex]  。

    ![應用程式清單中的 [Cisco Webex] 連結](common/all-applications.png)

3. 選取 [佈建]  索引標籤。

    ![Cisco Webex 佈建](common/provisioning.png)

4. 將 [佈建模式]  設定為 [自動]  。

    ![Cisco Webex 佈建](common/provisioning-automatic.png)

5. 在 [管理員認證]  區段下，輸入 Cisco Webex 帳戶的 [租用戶 URL]  和 [祕密權杖]  。

    ![Cisco Webex 佈建](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  在 [租用戶 URL]  欄位中，以 `https://api.ciscoweb.com/v1/scim/[OrgId]` 的格式輸入值。 若要取得 `[OrgId]`，請登入您的 [Cisco Webex 控制中樞](https://admin.webex.com/login)。 按一下左下方的 [組織名稱]，然後從 [組織識別碼]  複製值。 

    * 若要取得**祕密權杖**的值，請瀏覽至此 [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose)。 在出現的 [Webex 登入] 頁面中，使用您組織的完整 Cisco Webex 系統管理帳戶登入。 此時會出現錯誤頁面，指出無法連線到網站，但這是正常情況。

        ![Cisco Webex 佈建](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * 從 URL 複製產生的持有人權杖值，如下所示。 此權杖的有效期限為 365 天。
        
        ![Cisco Webex 佈建](./media/cisco-webex-provisioning-tutorial/test1.png)

7. 填入步驟 5 所示的欄位後，按一下 [測試連線]  以確定 Azure AD 可以連線到 Cisco Webex。 如果連線失敗，請確定您的 Cisco Webex 帳戶具有系統管理員權限，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)
   
8. 在 [通知電子郵件]  欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]  核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

9. 按一下 [檔案]  。

10. 在 [對應]  區段下，選取 [將 Azure Active Directory 使用者同步至 Cisco Webex]  。

    ![Cisco Webex 佈建](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. 在 [屬性對應]  區段中，檢閱從 Azure AD 同步至 Cisco Webex 的使用者屬性。 選取為 [比對]  屬性的屬性會用來比對 Cisco Webex 中的使用者帳戶以進行更新作業。 選取 [儲存]  按鈕以認可所有變更。

    ![Cisco Webex 佈建](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

13. 若要為 Cisco Webex 啟用 Azure AD 佈建服務，請在 [設定]  區段中，將 [佈建狀態]  變更為 [開啟]  。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

14. 透過在 [設定]  區段的 [範圍]  中選擇所需的值，定義要佈建到 Cisco Webex 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]  。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]  區段的 [範圍]  中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [同步處理詳細資料]  區段來監視進度，然後依循連結來佈建活動報告，此報告會描述 Azure AD 佈建服務在 Cisco Webex 上執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* Cisco Webex 目前處於 Cisco 的早期現場測試 (EFT) 階段。 如需詳細資訊，請連絡 [Cisco 的支援小組](https://www.webex.co.in/support/support-overview.html)。 
* 如需有關 Cisco Webex 設定的詳細資訊，請參閱[此處](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)的 Cisco 文件。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)