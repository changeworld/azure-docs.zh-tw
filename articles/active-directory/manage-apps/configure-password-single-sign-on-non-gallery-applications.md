---
title: 如何設定 Azure AD apps 的密碼型單一登入
description: '如何在 Microsoft 身分識別平臺中為您的 Azure AD 應用程式設定密碼單一登入 (SSO)  (Azure AD) '
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: e04a3aab128bb8f0bdee01361bc0d09aad6ed2fb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049055"
---
# <a name="configure-password-based-single-sign-on"></a>設定以密碼為基礎的單一登入

在應用程式管理的 [快速入門系列](view-applications-portal.md) 中，您已瞭解如何使用 Azure AD 作為應用程式 (IdP) 的身分識別提供者。 在快速入門手冊中，您會設定以 SAML 為基礎的 SSO。 另一個選項是以密碼為基礎的 SSO。 本文將詳細說明密碼型 SSO 選項的詳細資訊。 

此選項適用于具有 HTML 登入頁面的任何網站。 以密碼為基礎的 SSO 也稱為密碼保存庫。 以密碼為基礎的 SSO 可讓您管理不支援身分識別同盟之 web 應用程式的使用者存取和密碼。 如果有數個使用者需要共用單一帳戶（例如組織的社交媒體應用程式帳戶），這也很有用。

密碼型 SSO 可讓您快速開始將應用程式整合到 Azure AD，並可讓您：

- 藉由安全地儲存並重新執行使用者名稱和密碼，為您的使用者啟用單一登入

- 針對需要使用者名稱和密碼以外的更多欄位才能登入的應用程式，支援需要多個登入欄位的應用程式

- 自訂使用者輸入認證時，使用者在 [我的應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 看到的使用者名稱和密碼欄位標籤

- 允許您的使用者為其手動輸入的任何現有應用程式帳戶提供自己的使用者名稱和密碼。

- 允許商務群組的成員使用[自助應用程式存取](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能，指定要指派給使用者的使用者名稱和密碼

-   允許系統管理員指定當個人或群組以更新認證功能登入應用程式時，所要使用的使用者名稱和密碼 

## <a name="before-you-begin"></a>開始之前

使用 Azure AD 作為身分識別提供者 (IdP) 並設定單一登入 (SSO) 可以簡單或複雜，視所使用的應用程式而定。 有些應用程式只能透過幾個動作來設定。 其他則需要深入設定。 若要快速加速，請逐步完成應用程式管理的 [快速入門系列](view-applications-portal.md) 。 如果您要新增的應用程式很簡單，您可能不需要閱讀這篇文章。 如果您要新增的應用程式需要自訂設定，而您需要使用密碼型 SSO，則這篇文章適用于您。

> [!IMPORTANT] 
> 在某些案例中，在**企業應用**程式中，**單一登入**選項不會在應用程式的導覽中。 
>
> 如果應用程式是使用 **應用程式註冊** 註冊，則單一登入功能預設會設定為使用 OIDC OAuth。 在此情況下， **單一登入** 選項不會顯示在 [ **企業應用程式**] 下的導覽中。 當您使用 **應用程式註冊** 新增自訂應用程式時，您可以在資訊清單檔中設定選項。 若要深入瞭解資訊清單檔，請參閱 [Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要深入瞭解 SSO 標準，請參閱 [使用 Microsoft 身分識別平臺的驗證與授權](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 當應用程式裝載于另一個租使用者時，或您的帳戶沒有必要許可權 (全域管理員、雲端應用程式系統管理員、應用程式系統管理員或服務主體的擁有者) 時，流覽中將遺失 **單一登入** 的情況。 許可權也可能會導致您無法開啟 **單一登入** ，但無法儲存的情況。 若要深入瞭解 Azure AD 系統管理角色，請參閱 (https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。


## <a name="basic-configuration"></a>基本設定

在 [快速入門系列](view-applications-portal.md)中，您已瞭解如何將應用程式新增至您的租使用者，此應用程式可讓 Azure AD 知道它正作為應用程式 (IdP) 的身分識別提供者使用。 某些應用程式已預先設定，且會顯示在 Azure AD 資源庫中。 其他應用程式不在資源庫中，您必須建立一般應用程式，並手動進行設定。 視應用程式而定，可能無法使用以密碼為基礎的 SSO 選項。 如果您在應用程式的單一登入頁面上沒有看到以密碼為基礎的選項清單，則無法使用。

> [!IMPORTANT]
> 密碼型 SSO 需要我的應用程式瀏覽器擴充功能。 若要深入瞭解，請參閱 [規劃我的應用程式部署](access-panel-deployment-plan.md)。

以密碼為基礎的 SSO 的設定頁面很簡單。 它只包含應用程式所使用之登入頁面的 URL。 此字串必須是包含使用者名稱輸入欄位的頁面。

輸入 URL 之後，請選取 [ **儲存**]。 Azure AD 會針對使用者名稱和密碼輸入欄位剖析登入頁面的 HTML。 如果嘗試成功，您就完成了。
 
下一步是要將 [使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)。 指派使用者和群組之後，您可以在使用者登入應用程式時，提供要用於使用者的認證。 選取 [ **使用者和群組**]，選取使用者或群組之資料列的核取方塊，然後選取 [ **更新認證**]。 最後，輸入使用者或群組要使用的使用者名稱和密碼。 如果您沒有這麼做，系統會在啟動時提示使用者自行輸入認證。
 

## <a name="manual-configuration"></a>手動設定

如果 Azure AD 的剖析嘗試失敗，您可以手動設定登入。

1. 在 [設定] 底下，選取 [**設定 \<application name> 密碼單一登入設定** ** \<application name> ]，以**顯示 [**設定登入**] 頁面。 

2. 選取 [手動偵測登 **入欄位**]。 描述手動偵測登入欄位的其他指示隨即出現。

   ![手動設定以密碼為基礎的單一登入](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 選取 [ **Capture 登入欄位**]。 [捕捉狀態] 頁面隨即在新的索引標籤中開啟，其中顯示訊息 **中繼資料捕捉目前正在進行中**。

4. 如果新的索引標籤中出現 [ **需要我的應用程式擴充** 功能] 方塊，請選取 [ **立即安裝** ] 以安裝 **我的應用程式安全登入擴充** 功能瀏覽器擴充功能。  (瀏覽器擴充功能需要 Microsoft Edge、Chrome 或 Firefox。 ) 然後安裝、啟動和啟用擴充功能，然後重新整理 [捕捉狀態] 頁面。

   然後，瀏覽器擴充功能會開啟另一個索引標籤，以顯示輸入的 URL。
5. 在具有所輸入 URL 的索引標籤中，進行登入程式。 填入 [使用者名稱] 和 [密碼] 欄位，然後嘗試登入。  (您不需要提供正確的密碼。 ) 

   提示會要求您儲存已捕捉的登入欄位。
6. 選取 [確定]。 瀏覽器擴充功能會使用已 **更新的應用程式訊息中繼資料來**更新 [capture status] 頁面。 [瀏覽器] 索引標籤會關閉。

7. 在 Azure AD **設定** 登入] 頁面中，選取 **[確定]，我可以成功登入應用程式**。

8. 選取 [確定]。

## <a name="next-steps"></a>後續步驟

- [將使用者或群組指派給應用程式](methods-for-assigning-users-and-groups.md)
- [設定使用者帳戶自動佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
