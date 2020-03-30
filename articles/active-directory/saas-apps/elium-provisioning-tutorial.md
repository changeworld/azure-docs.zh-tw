---
title: 教程：使用 Azure 活動目錄配置 Elium 以自動預配使用者 |微軟文檔
description: 瞭解如何將 Azure 活動目錄配置為自動預配和取消向 Elium 預配使用者帳戶。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058458"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>教程：為自動使用者預配配置 Elium

本教程演示如何配置 Elium 和 Azure 活動目錄 （Azure AD） 以自動預配和取消向 Elium 預配使用者或組。

> [!NOTE]
> 本教程介紹在 Azure AD 使用者預配服務之上構建的連接器。 有關此服務的作用及其工作方式以及常見問題的重要詳細資訊，請參閱[使用 Azure 活動目錄 自動預配和取消預配到 SaaS 應用程式](../app-provisioning/user-provisioning.md)。
>
> 此連接器目前為預覽版。 有關預覽版中 Azure 功能的一般使用條款，請參閱[Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

本教程假定您已經具備以下先決條件：

* Azure AD 租用戶
* [Elium 租戶](https://www.elium.com/pricing/)
* 具有管理員許可權的 Elium 中的使用者帳戶

## <a name="assigning-users-to-elium"></a>將使用者分配給 Elium

Azure AD 使用稱為*分配*的概念來確定哪些使用者有權訪問選定的應用。 在自動使用者預配的上下文中，只有分配給 Azure AD 中應用程式的使用者和組才會同步。

在配置和啟用自動使用者預配之前，請決定 Azure AD 中的哪些使用者和組需要訪問 Elium。 然後，按照"[將使用者或組分配到企業應用](../manage-apps/assign-user-or-group-access-portal.md)"中的步驟將這些使用者和組分配給 Elium。

## <a name="important-tips-for-assigning-users-to-elium"></a>將使用者分配給 Elium 的重要提示 

我們建議您將單個 Azure AD 使用者分配給 Elium 以測試自動使用者預配配置。 以後可以分配更多的使用者和組。

將使用者分配給 Elium 時，必須在分配對話方塊中選擇有效的特定于應用程式的角色（如果有可用）。 具有**預設存取權限**角色的使用者將從預配中排除。

## <a name="set-up-elium-for-provisioning"></a>設置用於預配的 Elium

在使用 Azure AD 配置 Elium 以自動預配使用者之前，必須在 Elium 上啟用跨域標識管理系統 （SCIM） 預配。 請遵循下列步驟：

1. 登錄到 Elium 並轉到**我的個人資料** > **設置**。

    ![設置功能表項目在 Elium](media/Elium-provisioning-tutorial/setting.png)

1. 在左下角，在**ADVANCED**下，選擇 **"安全**"。

    ![Elium 中的安全連結](media/Elium-provisioning-tutorial/security.png)

1. 複製**租戶 URL**和**機密權杖**值。 稍後將在 Azure 門戶中 Elium 應用程式的**預配**選項卡中的相應欄位中使用這些值。

    ![Elium 中的租戶 URL 和秘密權杖欄位](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>從庫中添加 Elium

要配置 Elium 以使用 Azure AD 進行自動使用者預配，還必須將 Azure AD 應用程式庫中的 Elium 添加到託管軟體即服務 （SaaS） 應用程式清單中。 請遵循下列步驟：

1. 在[Azure 門戶](https://portal.azure.com)中，在左側導航面板中，選擇**Azure 活動目錄**。

    ![Azure 活動目錄功能表項目](common/select-azuread.png)

1. 轉到**企業應用程式**，然後選擇 **"所有應用程式**"。

     ![Azure AD 企業應用程式邊欄選項卡](common/enterprise-applications.png)

1. 要添加新應用程式，請在窗格頂部選擇 **"新建應用程式**"。

    ![新的應用程式連結](common/add-new-app.png)

1. 在搜索框中，鍵入**Elium**，在結果清單中選擇**Elium，** 然後選擇 **"添加"** 以添加應用程式。

    ![庫搜索框](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>配置自動使用者預配到 Elium

本節將指導您完成將 Azure AD 預配服務配置為根據 Azure AD 中的使用者和組分配在 Elium 中創建、更新和禁用使用者和組的步驟。

> [!TIP]
> 您也可以按照[Elium 單登錄教程](Elium-tutorial.md)中的說明，選擇基於安全斷言標記語言 （SAML） 為 Elium 啟用單一登入。 您可以獨立于自動使用者預配配置單一登入，儘管這兩個功能相輔相成。

要在 Azure AD 中配置 Elium 的自動使用者預配，請按照以下步驟操作：

1. 登錄到 Azure[門戶](https://portal.azure.com)，選擇**企業應用程式**，然後選擇**所有應用程式**。

    ![Azure AD 企業應用程式邊欄選項卡](common/enterprise-applications.png)

1. 在應用程式清單中，選取 [Elium]****。

    ![企業應用程式邊欄選項卡中的應用程式清單](common/all-applications.png)

1. 選擇 **"預配"** 選項卡。

    ![企業應用程式邊欄選項卡中的預配選項卡](common/provisioning.png)

1. 將**預配模式**設置為 **"自動**"。

    ![預配模式的自動設置](common/provisioning-automatic.png)

1. 在 **"管理認證"** 部分中，在 **"租戶 URL"** 欄位中鍵入**\<租戶URL\>/scim/v2。** （**租戶URL**是較早從 Elium 管理主控台檢索的值。還在 **"機密權杖"** 欄位中鍵入 Elium**機密權杖**值。 最後，選擇 **"測試連接**"以驗證 Azure AD 是否可以連接到 Elium。 如果連接失敗，請確保您的 Elium 帳戶具有管理員許可權，然後重試。

    ![管理員憑據中的租戶 URL 和秘密權杖欄位](common/provisioning-testconnection-tenanturltoken.png)

1. 在 **"通知電子郵件"** 欄位中，輸入將接收預配錯誤通知的個人或組的電子郵件地址。 然後，選擇"**在發生故障時發送電子郵件通知**"核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

1. 按一下 [儲存]****。

1. 在 **"映射"** 部分中，選擇**將 Azure 活動目錄使用者同步到 Elium**。

    ![同步連結，將 Azure AD 使用者映射到 Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. 在**屬性對應**部分中查看從 Azure AD 同步到 Elium 的使用者屬性。 選擇為 **"匹配屬性"** 的屬性用於與 Elium 中的使用者帳戶匹配以進行更新操作。 選取 [儲存]**** 認可任何變更。

    ![Azure AD 和 Elium 之間的屬性對應](media/Elium-provisioning-tutorial/userattribute.png)

1. 要配置範圍篩選器，請按照[範圍篩選器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中的說明進行操作。

1. 要啟用 Elium 的 Azure AD 預配服務，在 **"設置"** 部分將**預配狀態**更改為 **"打開**"。

    ![預配狀態設置為"打開"](common/provisioning-toggle-on.png)

1. 通過在 **"設置"** 部分的 **"範圍**"下拉式清單方塊中選擇所需的值，定義要預配到 Elium 的使用者和組。

    ![預配範圍清單方塊](common/provisioning-scope.png)

1. 準備好預配時，請選擇"**保存**"。

    ![保存用於預配配置的按鈕](common/provisioning-configuration-save.png)

此操作將開始**在"設置"** 部分中定義"**範圍"** 中定義的所有使用者和組的初始同步。 此初始同步過程所需的時間比以後的同步長。 有關預配所需的時間的詳細資訊，請參閱[預配使用者需要多長時間？](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

使用 **"目前狀態**"部分監視進度並關注指向預配活動報告的連結。 預配活動報告描述 Azure AD 預配服務在 Elium 上執行的所有操作。 有關詳細資訊，請參閱[檢查使用者預配的狀態](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。 要讀取 Azure AD 預配日誌，請參閱[報告自動使用者帳戶預配](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)
