---
title: 新增資源庫應用程式 - Azure Active Directory | Microsoft Docs
description: 了解如何將應用程式從 Azure AD 資源庫新增至您的 Azure 企業應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f2002414836dcb77a09f633f30ec0fcbb7981de
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760519"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>將資源庫應用程式新增至 Azure AD 組織

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合並以企業單一登入啟用的應用程式。 本文說明將應用程式從資源庫新增至 Azure AD 組織的一般步驟。

> [!IMPORTANT]
> 首先，在[如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中檢查您的應用程式。 您可能會發現新增及設定所要新增資源庫應用程式的逐步指引。

## <a name="add-a-gallery-application"></a>新增資源庫應用程式

1. 以 Azure AD 租用戶全域管理員、雲端應用程式系統管理員或應用程式系統管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure 入口網站](https://portal.azure.com)的左方瀏覽窗格中，選取 [Azure Active Directory]  。

1. 在 [Azure Active Directory]  視格中，選取 [企業應用程式]  。

    ![開啟企業應用程式](media/add-gallery-app/open-enterprise-apps.png)


3. 若要將資源庫應用程式新增至您的租用戶，請選取 [新增應用程式]  。

    ![選取 [新增應用程式] 以將資源庫應用程式新增至您的租用戶](media/add-gallery-app/new-application.png)

 4. 切換至新的資源庫預覽體驗：在 [新增應用程式]  頁面頂端的橫幅中，選取顯示為 [按一下這裡試用改良過的新版應用程式資源庫]  的連結。

5. [瀏覽 Azure AD 資源庫]  窗格隨即開啟，並顯示雲端平台、內部部署應用程式和精選應用程式的圖格。 請注意，[精選應用程式]  區段中所列的應用程式，會有圖示指出它們是否支援同盟單一登入 (SSO) 和佈建。

    ![依名稱或類別搜尋應用程式](media/add-gallery-app/browse-gallery.png)

6. 瀏覽您要新增之應用程式的資源庫，或在搜尋方塊中輸入應用程式的名稱加以搜尋。 然後，從結果中選取應用程式。 (選擇性) 在表單中，您可以編輯應用程式名稱以符合您的組織需求。

    ![說明如何從資源庫新增應用程式](media/add-gallery-app/create-application.png)

7. 選取 [建立]  。 [開始使用] 頁面會隨即顯示，其中包含可為您組織設定應用程式的選項。

## <a name="configure-user-sign-in-properties"></a>設定使用者登入屬性

1. 選取 [屬性]  ，以開啟屬性窗格來進行編輯。

    ![編輯屬性窗格](media/add-gallery-app/edit-properties.png)

1. 設定下列選項，以決定已指派或未指派給應用程式的使用者如何登入應用程式，以及使用者是否可以在存取面板中看到該應用程式。

    - [為使用者啟用登入]  可決定指派給應用程式的使用者是否可以登入。
    - [需要使用者指派]  可決定未指派給應用程式的使用者是否可以登入。
    - [可讓使用者看見]  可決定指派給應用程式的使用者是否可以在存取面板和 O365 啟動器中看見應用程式。

      **已指派**的使用者行為：

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

      **未指派**的使用者行為：

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

1. 若要使用自訂標誌，請建立 215 x 215 像素的標誌，並將它儲存為 PNG 格式。 然後瀏覽至您的標誌並加以上傳。

    ![變更標誌](media/add-gallery-app/change-logo.png)

1. 完成之後，選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

現在您已將應用程式新增至您的 Azure AD 組織，[請選擇您想使用的單一登入方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)並參閱下列適當的文章：

- [設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)
- [設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)
- [設定連結的登入](configure-linked-sign-on.md)

