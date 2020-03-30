---
title: 教程：使用 Azure 活動目錄配置 Zscaler 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Zscaler 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062705"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Zscaler

本教程的目的是演示在 Zscaler 和 Azure 活動目錄 （Azure AD） 中執行的步驟，以將 Azure AD 配置為自動預配和取消向 Zscaler 預配使用者和/或組。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../active-directory-saas-app-provisioning.md)。
>

## <a name="prerequisites"></a>Prerequisites

本教學課程中說明的案例假設您已經具有下列項目：

* Azure AD 租用戶。
* 茲卡爾瓦勒租戶。
* Zscaler 中的具有管理員許可權的使用者帳戶。

> [!NOTE]
> Azure AD 預配集成依賴于 Zscaler SCIM API，對於具有企業包的帳戶，Zscaler 開發人員可以使用該 API。

## <a name="adding-zscaler-from-the-gallery"></a>從資源庫新增 Zscaler

在配置 Zscaler 以使用 Azure AD 進行自動使用者預配之前，需要將 Azure AD 應用程式庫中的 Zscaler 添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫添加 Zscaler，請執行以下步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]**** 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]****，然後選取 [所有應用程式]**** 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]**** 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **Zscaler**，並從結果面板中選取 [Zscaler]****，然後按一下 [新增]**** 按鈕以新增應用程式。

    ![結果清單中的 Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>將使用者分配給 Zscaler

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和/或群組會進行同步處理。

在配置和啟用自動使用者預配之前，應決定 Azure AD 中的哪些使用者和/或組需要訪問 Zscaler。 一旦確定，您可以按照此處的說明將這些使用者和/或組分配給 Zscaler：

* [將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>將使用者分配給 Zscaler 的重要提示

* 建議將單個 Azure AD 使用者分配給 Zscaler 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Zscaler 時，必須在分配對話方塊中選擇任何有效的特定于應用程式的角色（如果可用）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>將自動使用者預配配置配置到 Zscaler

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和/或組分配在 Zscaler 中創建、更新和禁用使用者和/或組的步驟。

> [!TIP]
> 您也可以根據[Zscaler 單點登錄教程](zscaler-tutorial.md)中提供的說明，選擇為 Zscaler 啟用基於 SAML 的單一登入。 雖然自動使用者佈建和單一登入這兩個功能互相補充，您還是可以將它們分開設定。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>要在 Azure AD 中配置 Zscaler 的自動使用者預配：

1. 登錄到 Azure[門戶](https://portal.azure.com)並選擇**企業應用程式**，選擇**所有應用程式**，然後選擇**Zscaler**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Zscaler]****。

    ![應用程式清單中的 Zscaler 連結](common/all-applications.png)

3. 選擇 **"預配"** 選項卡。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. 將**預配模式**設置為 **"自動**"。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. 在 **"管理認證"** 部分下，輸入 Zscaler 帳戶的**租戶 URL**和**金鑰權杖**，如步驟 6 所述。

6. 要獲取**租戶 URL**和**金鑰權杖**，請在 Zscaler 門戶使用者介面中導航到**管理>身份驗證設置**，然後按一下 **"身份驗證類型**"下的**SAML。**

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    按一下 **"配置 SAML"** 以打開**配置 SAML**選項。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    選擇**啟用基於 SCIM 的預配**以檢索**基本 URL**和**承載權杖**，然後保存設置。 將**基本 URL**複製到**租戶 URL**，並將**權杖複製到**Azure 門戶中**的秘密權杖**。

7. 填充步驟 5 中所示的欄位後，按一下 **"測試連接**"以確保 Azure AD 可以連接到 Zscaler。 如果連接失敗，請確保您的 Zscaler 帳戶具有管理員許可權，然後重試。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/test-connection.png)

8. 在 [通知電子郵件]**** 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/notification.png)

9. 按一下 [儲存]****。

10. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄使用者同步到 Zscaler**。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. 在**屬性對應**部分中查看從 Azure AD 同步到 Zscaler 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zscaler 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. 在 **"映射"** 部分下，選擇**將 Azure 活動目錄組同步到 Zscaler**。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. 在**屬性對應**部分中查看從 Azure AD 同步到 Zscaler 的組屬性。 選擇為 **"匹配屬性"** 的屬性用於匹配 Zscaler 中的組以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. 若要設定範圍篩選，請參閱[範圍篩選教學課程](./../active-directory-saas-scoping-filters.md)中提供的下列指示。

15. 要為 Zscaler 啟用 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. 通過在 **"設置"** 部分中選擇"**範圍"** 中所需的值，定義要預配到 Zscaler 的使用者和/或組。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/scoping.png)

17. 當您準備好要佈建時，按一下 [儲存]****。

    ![茲卡尺器配置](./media/zscaler-provisioning-tutorial/save-provisioning.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **"同步詳細資訊"** 部分監視進度並遵循指向預配活動報告的連結，該報表描述 Azure AD 預配服務在 Zscaler 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
