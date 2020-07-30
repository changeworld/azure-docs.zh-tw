---
title: 如何為 Azure AD 應用程式設定密碼型單一登入
description: 如何在 Microsoft 身分識別平臺（Azure AD）中為您的 Azure AD 應用程式設定密碼型單一登入（SSO）
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: c3f9f96c6429d4925c60a56cd450a9c2ee7dde24
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419949"
---
# <a name="configure-password-based-single-sign-on"></a>設定以密碼為基礎的單一登入

在應用程式管理的[快速入門系列](view-applications-portal.md)中，您已瞭解如何使用 Azure AD 做為應用程式的身分識別提供者（IdP）。 在快速入門手冊中，您會設定 SAML 型 SSO。 除了 SAML，還有一個用於密碼型 SSO 的選項。 本文會詳細說明單一登入的密碼型選項。 

此選項適用于具有 HTML 登入頁面的任何網站。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 這也適用于數個使用者需要共用單一帳戶的案例，例如組織的社交媒體應用程式帳戶。 

密碼型 SSO 是開始將應用程式整合到 Azure AD 快速的絕佳方式，可讓您：

- 針對您已整合的應用程式，安全地儲存及重新執行使用者名稱和密碼，為您的使用者啟用單一登入 Azure AD

- 針對需要使用者名稱和密碼以外的更多欄位才能登入的應用程式，支援需要多個登入欄位的應用程式

- 針對使用者在[應用程式存取面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)輸入認證時所看到的使用者名稱和密碼輸入欄位，自訂標籤

- 允許您的使用者針對他們以手動方式輸入的任何現有應用程式帳戶，提供自己的使用者名稱和密碼。

- 允許商務群組的成員使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能，指定要指派給使用者的使用者名稱和密碼

-   允許系統管理員在使用 [更新認證] 功能登入應用程式時，指定個人或群組所要使用的使用者名稱和密碼 

## <a name="before-you-begin"></a>開始之前

視所使用的應用程式而定，使用 Azure AD 做為身分識別提供者，以及設定單一登入（SSO）的方式都很簡單或很複雜。 有些應用程式只能透過幾個動作來設定。 其他則需要深入設定。 若要快速增加，請逐步執行應用程式管理的[快速入門系列](view-applications-portal.md)。 如果您要新增的應用程式很簡單，那麼您可能不需要閱讀這篇文章。 如果您要新增的應用程式需要自訂設定，而且您需要使用密碼型 SSO，則本文適用于您。

> [!IMPORTANT] 
> 在某些情況下，[**單一登入**] 選項不會出現在**企業應用**程式中的應用程式導覽中。 
>
> 如果應用程式是使用**應用程式註冊**註冊，則單一登入功能會設定為預設使用 OIDC OAuth。 在此情況下，[**企業應用程式**] 下的流覽中不會顯示 [**單一登入**] 選項。 當您使用**應用程式註冊**新增自訂應用程式時，您可以在資訊清單檔中設定選項。 若要深入瞭解資訊清單檔案，請參閱[Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要深入瞭解 SSO 標準，請參閱[使用 Microsoft 身分識別平臺的驗證和授權](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 當應用程式裝載于另一個租使用者時，或您的帳戶沒有必要許可權（全域管理員、雲端應用程式系統管理員、應用程式系統管理員或服務主體的擁有者）時，流覽中將遺失**單一登入**的其他案例。 許可權也可能會導致您可以開啟**單一登入**但無法儲存的案例。 若要深入瞭解 Azure AD 系統管理角色，請參閱（ https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。


## <a name="basic-configuration"></a>基本設定

在[快速入門系列](view-applications-portal.md)中，您已瞭解如何將應用程式新增至您的租使用者，讓 Azure AD 知道它正作為應用程式的身分識別提供者（IdP）使用。 某些應用程式已預先設定，而且它們會顯示在 Azure AD 資源庫中。 其他應用程式不在資源庫中，您必須建立泛型應用程式並手動進行設定。 視應用程式而定，可能無法使用以密碼為主的 SSO 選項。 如果您在應用程式的單一登入頁面上看不到以密碼為基礎的選項清單，則無法使用。

密碼型 SSO 的設定頁面很簡單。 它只包含應用程式所使用之登入頁面的 URL。 這個字串必須是包含 [使用者名稱] 輸入欄位的頁面。

輸入 URL 之後，請選取 [**儲存**]。 Azure AD 會剖析登入頁面的 HTML，以取得使用者名稱和密碼輸入欄位。 如果嘗試成功，就大功告成了。
 
下一個步驟是[將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)。 指派使用者和群組之後，您可以提供認證，以在使用者登入應用程式時代表使用者使用。 選取 [**使用者和群組**]，選取使用者或群組的資料列核取方塊，然後選取 [**更新認證**]。 然後，輸入要代表使用者或群組使用的使用者名稱和密碼。 否則，系統會在啟動時提示使用者輸入認證。
 

## <a name="manual-configuration"></a>手動設定

如果 Azure AD 的剖析嘗試失敗，您可以手動設定登入。

1. 在** \<application name> [設定] 下，** 選取 [**設定密碼] [ \<application name> 單一登入設定**] 以顯示 [**設定登入**] 頁面。 

2. 選取 [手動偵測登**入欄位**]。 說明如何手動偵測登入欄位的其他指示隨即出現。

   ![手動設定密碼型單一登入](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 選取 [ **Capture 登入欄位**]。 [捕捉狀態] 頁面會在新的索引標籤中開啟，顯示訊息**中繼資料捕捉目前正在進行中**。

4. 如果 [**需要的存取面板延伸**模組] 方塊出現在新的索引標籤中，請選取 [**立即安裝**] 以安裝**我的應用程式安全登入擴充**功能瀏覽器延伸模組。 （瀏覽器擴充功能需要 Microsoft Edge、Chrome 或 Firefox）。然後安裝、啟動及啟用擴充功能，並重新整理 [捕捉狀態] 頁面。

   瀏覽器擴充功能接著會開啟另一個索引標籤，顯示輸入的 URL。
5. 在具有所輸入 URL 的索引標籤中，完成登入程式。 填入 [使用者名稱] 和 [密碼] 欄位，然後嘗試登入。 （您不需要提供正確的密碼）。

   會出現提示，要求您儲存已捕捉的登入欄位。
6. 選取 [確定]。 瀏覽器延伸模組會以**應用程式已更新的訊息中繼資料來**更新 [捕捉狀態] 頁面。 [瀏覽器] 索引標籤隨即關閉。

7. 在 [Azure AD**設定**登入] 頁面中，選取 **[確定]，我可以成功登入應用程式**。

8. 選取 [確定]。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
