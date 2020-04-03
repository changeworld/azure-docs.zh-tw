---
title: 教程:使用 Azure 活動目錄配置閃爍以自動預配使用者 |微軟文件
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消將使用者帳戶預配到 Blink。
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
ms.openlocfilehash: b19052b8a71b582b47308c41d170f50352d643e2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618347"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>教學:自動使用者預配設定閃爍

本教學的目的是演示在 Blink 和 Azure 活動目錄 (Azure AD) 中執行的步驟,以將 Azure AD 配置為自動預配和取消向 Blink 預配使用者。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [閃爍租戶](https://joinblink.com/pricing)
* 具有管理員許可權的閃爍中的使用者帳戶。

## <a name="assigning-users-to-blink"></a>將使用者配置給閃爍

Azure 活動目錄使用稱為*分配*的概念來確定哪些使用者應接收對選定應用的訪問許可權。 在自動使用者預配的上下文中,只有分配給 Azure AD 中應用程式的使用者和/或組成員才會同步。

在配置和啟用自動使用者預配之前,應決定 Azure AD 中的哪些使用者和/或組成員需要存取 Blink。 決定後,您可以按照此處的說明將這些使用者和/或組分配給 Blink:
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>將使用者配置給閃爍的重要提示

* 建議將單個 Azure AD 使用者分配給 Blink 以測試自動使用者預配配置。 其他使用者及/或群組可能會稍後再指派。

* 將使用者分配給 Blink 時,必須在分配對話方塊中選擇任何有效的特定於應用程式的角色(如果可用)。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-blink-for-provisioning"></a>用於預先定義的設定閃爍

1. 在上記錄[支援案例](https://support.joinblink.com)或電子郵件**閃爍支援**以support@joinblink.com請求 SCIM 權杖。 .

2.  複製**SCIM 身份認證權杖**。 此值將在 Azure 門戶中 Blink 應用程式的預配選項卡中的「機密權杖」欄位中輸入。

## <a name="add-blink-from-the-gallery"></a>從庫加入閃爍

在使用 Azure AD 配置 Blink 以自動預先使用者之前,需要將「閃爍」從 Azure AD 應用程式庫添加到託管 SaaS 應用程式清單中。

**要從 Azure AD 應用程式庫加入閃爍,請執行以下步驟:**

1. 在**[Azure 門戶](https://portal.azure.com)** 中,在左側導航面板中,選擇**Azure 活動目錄**。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 轉到**企業應用程式**,然後選擇 **"所有應用程式**"。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 要新增新應用程式,請選擇窗格頂部的 **「新建應用程式**」按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋框中,在結果面板中輸入 **「閃爍**」,然後按下「**添加**」按鈕以添加應用程式。 **Blink**

    ![在結果清單中閃爍](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>將自動使用者預先設定為閃爍 

本節將指導您完成配置 Azure AD 預配服務的步驟,以便根據 Azure AD 中的使用者和/或組分配在 Blink 中創建、更新和禁用使用者。

> [!TIP]
> 您也可以選擇啟用基於 SAML 的單點登入閃爍,按照[閃爍單點登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)中提供的說明。 單一登錄可以獨立於自動使用者預配進行配置,儘管這兩個功能相互補充

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>在 Azure AD 中設定閃爍的自動使用者預配:

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選擇**企業應用程式**,然後選擇**所有應用程式**。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Blink]****。

    ![應用程式清單中的閃爍連結](common/all-applications.png)

3. 選擇 **「預配」** 選項卡。

    ![預配選項卡](common/provisioning.png)

4. 將**預配模式**設定為 **「自動**」。

    ![預配選項卡](common/provisioning-automatic.png)

5. 在 **"管理認證為「**`https://api.joinblink.com/scim`部份下 」,在**租戶網址**中輸入 。 輸入在**秘密權杖**中檢索到的**SCIM 身份驗證權杖**值。 按下 **「測試連線**」以確保 Azure AD 可以連接到閃爍。 如果連接失敗,請確保您的 Blink 帳戶具有管理員許可權,然後重試。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]****。

8. 在 **「映射」** 部分下,選擇 **「將 Azure 活動目錄使用者同步到閃爍**」。。

    ![閃爍使用者對應](media/blink-provisioning-tutorial/User_mappings.png)

9. 在**屬性映射**部分中查看從 Azure AD 同步到閃爍的使用者屬性。 選擇為 **「匹配屬性」** 的屬性用於符合 Blink 中的使用者帳戶以進行更新操作。 選取 [儲存]**** 按鈕以認可所有變更。

    ![閃爍使用者屬性](media/blink-provisioning-tutorial/User_attributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 要啟用"閃爍的 Azure AD 預配服務",在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 通過在 **「設定」** 部分中選擇「**範圍」** 中所需的值,定義要預配到 Blink 的使用者。

    ![佈建範圍](common/provisioning-scope.png)

15. 當您準備好要佈建時，按一下 [儲存]****。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此操作將開始**在「設定」** 部分中定義的 **「範圍**」中定義的所有使用者的初始同步。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 可以使用 **「同步詳細資訊」** 部分監視進度並遵循指向預配活動報告的連結,該報表描述 Azure AD 預配服務在 Blink 上執行的所有操作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

