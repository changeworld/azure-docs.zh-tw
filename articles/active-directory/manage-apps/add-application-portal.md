---
title: 快速入門：將應用程式新增至 Azure Active Directory 租用戶
description: 本快速入門會使用 Azure 入口網站，將資源庫應用程式新增至 Azure Active Directory (Azure AD) 租用戶。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b89ab7e44bc71fa442f332cbba35af450f7d5155
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760655"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>快速入門：新增應用程式到您的 Azure Active Directory 租用戶

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在資源庫中。 本快速入門會使用 Azure 入口網站，將資源庫應用程式新增至 Azure Active Directory (Azure AD) 租用戶。

將應用程式新增至 Azure AD 租用戶之後，您可以：

- 使用條件式存取原則來管理應用程式的使用者存取。
- 設定單一登入功能，讓使用者可以使用他們的 Azure AD 帳戶登入應用程式。

## <a name="before-you-begin"></a>開始之前

若要將應用程式新增至您的租用戶，您需要：

- Azure AD 訂用帳戶
- 已針對應用程式啟用單一登入的訂用帳戶

以 Azure AD 租用戶全域管理員、雲端應用程式系統管理員或應用程式系統管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。

若要測試本教學課程中的步驟，我們建議您使用非生產環境。 如果您沒有 Azure AD 的非生產環境，您可以[取得為期一個月的試用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-an-application-to-your-azure-ad-tenant"></a>將應用程式新增至 Azure AD 租用戶

將資源庫應用程式新增至 Azure AD 租用戶：

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]  。

2. 在 [Azure Active Directory]  視格中，選取 [企業應用程式]  。 [所有應用程式]  窗格會隨即開啟，並顯示您 Azure AD 租用戶中應用程式的隨機樣本。

3. 若要將資源庫應用程式新增至您的租用戶，請選取 [新增應用程式]  。 

    ![選取 [新增應用程式] 以將資源庫應用程式新增至您的租用戶](media/add-application-portal/new-application.png)

 4. 切換至新的資源庫預覽體驗：在 [新增應用程式]  頁面頂端的橫幅中，選取顯示為 [按一下這裡試用改良過的新版應用程式資源庫]  的連結。

5. [瀏覽 Azure AD 資源庫 (預覽)]  窗格隨即開啟，並顯示雲端平台、內部部署應用程式和精選應用程式的圖格。 請注意，[精選應用程式]  區段中所列的應用程式，會有圖示指出它們是否支援同盟單一登入 (SSO) 和佈建。

    ![依名稱或類別搜尋應用程式](media/add-application-portal/browse-gallery.png)

6. 您可以瀏覽您要新增之應用程式的資源庫，或在搜尋方塊中輸入應用程式的名稱加以搜尋。 然後，從結果中選取應用程式。 在表單中，您可以編輯應用程式名稱，以符合您的組織需求。 在此範例中，我們已將名稱變更為 **GitHub-test**。

    ![說明如何從資源庫新增應用程式](media/add-application-portal/create-application.png)

7. 選取 [建立]  。 [開始使用] 頁面會隨即顯示，其中包含可為您組織設定應用程式的選項。

您已完成新增應用程式。 下一節我們將示範如何變更應用程式的標誌和其他屬性。

## <a name="find-your-azure-ad-tenant-application"></a>尋找 Azure AD 租用戶應用程式

讓我們假設您剛才必須先離開，然後現在回來繼續設定您的應用程式。 您要做的第一件事是尋找應用程式。

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，選取 [Azure Active Directory]  。
1. 在 [Azure Active Directory]  視格中，選取 [企業應用程式]  。
1. 從 [應用程式類型]  下拉式清單中，選取 [所有應用程式]  ，然後選取 [套用]  。 若要深入了解檢視選項，請參閱[檢視租用戶應用程式](view-applications-portal.md)。
1. 您現在可以在您的 Azure AD 租用戶中，看到所有應用程式的清單。 此清單是隨機取樣。 若要查看更多應用程式，請選取一或多次 [顯示更多]  。
1. 若要在租用戶中快速尋找應用程式，請在搜尋方塊中輸入應用程式名稱，然後選取 [套用]  。 此範例會尋找我們先前新增的 GitHub-test 應用程式。

    ![說明如何使用搜尋方塊尋找應用程式](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>設定使用者登入屬性

現在，您已找到應用程式，您可以開啟它並設定應用程式屬性。

若要編輯應用程式屬性：

1. 選取應用程式以將其開啟。
2. 選取 [屬性]  ，以開啟屬性窗格來進行編輯。

    ![顯示 [屬性] 畫面和可編輯的應用程式屬性](media/add-application-portal/edit-properties.png)

3. 請花一點時間了解登入選項。 這些選項會決定已在應用程式中指派或取消指派的使用者要如何登入應用程式。 而且，這些選項也會決定使用者是否可在存取面板中看見應用程式。

    - [為使用者啟用登入？]  可決定指派給應用程式的使用者是否可以登入。
    - [需要使用者指派？]  可決定未指派給應用程式的使用者是否可以登入。
    - [可讓使用者看見？]  可決定指派給應用程式的使用者是否可以在存取面板和 O365 啟動器中看見應用程式。

4. 您可以使用下表來協助您選擇最符合需求的選項。

   - **已指派**的使用者行為：

       | 應用程式屬性設定 | | | 已指派的使用者體驗 | |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 已指派的使用者可以登入？ | 已指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

   - **未指派**的使用者行為：

       | 應用程式屬性設定 | | | 未指派的使用者體驗 | |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 未指派的使用者可以登入？ | 未指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *使用者是否可以在存取面板和 Office 365 應用程式啟動器中看到應用程式？

## <a name="use-a-custom-logo"></a>使用自訂標誌

使用自訂標誌：

1. 建立 215 x 215 像素的標誌，並將它儲存為 PNG 格式。
1. 由於您已經找到您的應用程式，請選取該應用程式。
1. 選取左窗格中的 [屬性]  。
1. 上傳標誌。
1. 完成之後，選取 [儲存]  。 

    ![說明如何從應用程式的 [屬性] 頁面變更標誌](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 這個 [屬性]  窗格上顯示的縮圖不會立即更新。 您可以關閉屬性再重新加以開啟，以查看更新的圖示。

## <a name="next-steps"></a>後續步驟

現在您已將應用程式新增至您的 Azure AD 組織，[請選擇您想使用的單一登入方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)並參閱下列適當的文章：

- [設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)
- [設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)
- [設定連結的登入](configure-linked-sign-on.md)
