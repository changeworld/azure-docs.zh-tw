---
title: 如何為 Azure AD 應用程式設定密碼單一登入 |Microsoft Docs
description: 如何在 Microsoft 身分識別平臺（Azure AD）中，為您的 Azure AD 企業應用程式設定密碼單一登入（SSO）
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063521"
---
# <a name="configure-password-single-sign-on"></a>設定密碼單一登入

當您[將資源庫應用程式](add-gallery-app.md)或[非資源庫 web 應用](add-non-gallery-app.md)程式新增至 Azure AD 企業應用程式時，您可以使用的其中一個單一登入選項為 [[密碼單一登入](what-is-single-sign-on.md#password-based-sso)]。 此選項適用于具有 HTML 登入頁面的任何 web。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 這也適用于數個使用者需要共用單一帳戶的案例，例如組織的社交媒體應用程式帳戶。 

密碼型 SSO 是開始將應用程式整合到 Azure AD 快速的絕佳方式，可讓您：

-   針對已經與 Azure AD 整合的應用程式，安全地儲存和重播使用者名稱和密碼，以啟用**使用者的單一登入**

-   針對需要使用者名稱和密碼以外的更多欄位才能登入的應用程式，**支援需要多個登入欄位的應用程式**

-   針對使用者在**應用程式存取面板**輸入認證時所看到的使用者名稱和密碼輸入欄位，[自訂標籤](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   針對**使用者**在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)手動輸入的任何現有應用程式帳戶，允許他們提供自己的使用者名稱和密碼

-   允許**商務群組的成員**使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能，指定要指派給使用者的使用者名稱和密碼

-   允許**系統管理員**在使用 [更新認證] 功能登入應用程式時，指定個人或群組所要使用的使用者名稱和密碼 

## <a name="before-you-begin"></a>開始之前

如果尚未將應用程式新增至您的 Azure AD 租用戶，請參閱[新增資源庫應用程式](add-gallery-app.md)或[新增非資源庫應用程式](add-non-gallery-app.md)。

## <a name="open-the-app-and-select-password-single-sign-on"></a>開啟應用程式，然後選取 [密碼] [單一登入]

1. 以雲端應用程式系統管理員或 Azure AD 租用戶的應用程式系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至**Azure Active Directory** > **企業應用程式**。 Azure AD 租用戶中應用程式的隨機樣本隨即出現。 

3. 在 [應用程式類型] 功能表中，選取 [所有應用程式]，然後選取 [套用]。

4. 在搜尋方塊中輸入應用程式的名稱，然後從結果中選取應用程式。

5. 在 [管理] 區段中，選取 [單一登入]。 

6. 選取 [**密碼型**]。

7. 輸入應用程式網頁型登入頁面的 URL。 這個字串必須是包含 [使用者名稱] 輸入欄位的頁面。

   ![密碼單一登入](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. 選取 [儲存]。 Azure AD 嘗試剖析登入頁面的使用者名稱輸入和密碼輸入。 如果嘗試成功，就大功告成了。 
 
> [!NOTE]
> 下一個步驟是[將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)。 指派使用者和群組之後，您可以提供認證，以在使用者登入應用程式時代表使用者使用。 選取 [**使用者和群組**]，選取使用者或群組的資料列核取方塊，然後按一下 [**更新認證**]。 然後，輸入要代表使用者或群組使用的使用者名稱和密碼。 否則，系統會在啟動時提示使用者輸入認證。
 

## <a name="manual-configuration"></a>手動設定

如果 Azure AD 的剖析嘗試失敗，您可以手動設定登入。

1. 在 [ **\<應用程式名稱 >** 設定] 底下，選取 [**設定 \<應用程式名稱] > [密碼] [單一登入設定**]，以顯示 [**設定登入**] 頁面。 

2. 選取 [手動偵測登**入欄位**]。 說明如何手動偵測登入欄位的其他指示隨即出現。

   ![手動設定密碼型單一登入](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 選取 [ **Capture 登入欄位**]。 [捕捉狀態] 頁面會在新的索引標籤中開啟，顯示訊息**中繼資料捕捉目前正在進行中**。

4. 如果 [**需要的存取面板延伸**模組] 方塊出現在新的索引標籤中，請選取 [**立即安裝**] 以安裝**我的應用程式安全登入擴充**功能瀏覽器延伸模組。 （瀏覽器擴充功能需要 Microsoft Edge、Chrome 或 Firefox）。然後安裝、啟動及啟用擴充功能，並重新整理 [捕捉狀態] 頁面。

   瀏覽器擴充功能接著會開啟另一個索引標籤，顯示輸入的 URL。
5. 在具有所輸入 URL 的索引標籤中，完成登入程式。 填入 [使用者名稱] 和 [密碼] 欄位，然後嘗試登入。 （您不需要提供正確的密碼）。

   會出現提示，要求您儲存已捕捉的登入欄位。
6. 選取 **[確定]** 。 瀏覽器延伸模組會以**應用程式已更新的訊息中繼資料來**更新 [捕捉狀態] 頁面。 [瀏覽器] 索引標籤隨即關閉。

7. 在 [Azure AD**設定**登入] 頁面中，選取 **[確定]，我可以成功登入應用程式**。

8. 選取 **[確定]** 。

在登入頁面的 capture 之後，您可以指派使用者和群組，而且您可以設定認證原則，就像一般的[密碼 SSO 應用程式](what-is-single-sign-on.md)一樣。

> [!NOTE]
> 您可以在應用程式的 [設定] 索引標籤上使用 [上傳標誌] 按鈕，來上傳應用程式的圖格標誌。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
