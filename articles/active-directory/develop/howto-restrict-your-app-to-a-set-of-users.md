---
title: 將 Azure AD 應用程式限制為一組使用者 |蔚藍
titleSuffix: Microsoft identity platform
description: 了解如何將已在 Azure AD 中註冊的應用程式限制為僅供一組選取的使用者存取。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f6ac11fc5c7bbe7a8f81e6ea89e2c582ebcf264
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178733"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>如何：將 Azure AD 應用程式限制為 Azure AD 租使用者中的一組使用者

根據預設，在 Azure Active Directory (Azure AD) 租用戶中註冊的應用程式可供成功通過驗證的所有租用戶使用者使用。

同樣地，在[多租用戶](howto-convert-app-to-be-multi-tenant.md)應用程式的案例中，於此應用程式佈建所在的 Azure AD 租用戶中，使用者一旦成功驗證其各自的租用戶後，都能夠存取此應用程式。

租用戶系統管理員和開發人員通常需要將應用程式限制為僅供一組特定的使用者存取。 開發人員可以使用常見的授權模式（例如 Azure 角色型存取控制 (Azure RBAC) ）來達成相同目的，但這種方法需要開發人員的大量工作。

租使用者系統管理員和開發人員可以使用 Azure AD 的內建功能，將應用程式限制為租使用者中一組特定的使用者或安全性群組。

## <a name="supported-app-configurations"></a>支援的應用程式設定

將應用程式限制為僅供租用戶中一組特定使用者或安全性群組存取的選項，適用於下列類型的應用程式︰

- 針對使用 SAML 型驗證進行同盟單一登入所設定的應用程式。
- 使用 Azure AD 預先驗證的應用程式 proxy 應用程式。
- 直接建置在 Azure AD 應用程式平台上，且會在使用者或系統管理員同意該應用程式之後，使用 OAuth 2.0/OpenID Connect 驗證的應用程式。

     > [!NOTE]
     > 這項功能只適用於 Web 應用程式/Web API 和企業應用程式。 註冊為[原生](./quickstart-register-app.md)的應用程式無法限制為僅供租用戶中的一組使用者或安全性群組存取。

## <a name="update-the-app-to-enable-user-assignment"></a>更新應用程式以啟用使用者指派

有兩種方式可以建立具有已啟用使用者指派的應用程式。 其中一個需要 **全域管理員** 角色，第二個則不需要。

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>企業應用程式 (需要全域管理員角色) 

1. 以 **全域管理員** 身分登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span></a> 。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [**管理**] 底下，選取 [**企業應用** 程式  >  **所有應用程式**]。
1. 從清單中選取您想要指派使用者或安全性群組的應用程式。 
    使用視窗頂端的篩選準則來搜尋特定的應用程式。
1. 在應用程式的 **[總覽** ] 頁面的 [ **管理**] 底下，選取 [ **屬性**]。
1. 找出 [需要使用者指派嗎？] 設定，並將它設定為 [是]。 當此選項設定為 **[是]** 時，租使用者中的使用者必須先指派給此應用程式，否則他們將無法登入此應用程式。
1. 選取 [儲存]。

### <a name="app-registration"></a>應用程式註冊

1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [管理]  底下選取 [應用程式註冊]  。
1. 建立或選取您想要管理的應用程式。 您必須是此應用程式的 **擁有** 者。
1. 在應用程式的 **[總覽**] 頁面上，選取 [**基本** 資訊] 區段中的 [**本機目錄中的受控應用程式**] 連結。
1. 在 [ **管理**] 底下，選取 [ **屬性**]。
1. 找出 [需要使用者指派嗎？] 設定，並將它設定為 [是]。 當此選項設定為 **[是]** 時，租使用者中的使用者必須先指派給此應用程式，否則他們將無法登入此應用程式。
1. 選取 [儲存]。

## <a name="assign-users-and-groups-to-the-app"></a>將使用者和群組指派給應用程式

在設定完應用程式以啟用使用者指派後，您便可繼續作業並將使用者和群組指派給應用程式。

1. 在 [**管理**] 底下，選取 [**使用者和群組**  >  **新增使用者/群組**]。
1. 選取 [ **使用者** ] 選取器。 

     隨即會顯示使用者和安全性群組清單，以及用來搜尋並找出特定使用者或群組的文字方塊。 此畫面可讓您一次選取多個使用者和群組。

1. 當您完成選取使用者和群組之後，請選取 [ **選取**]。
1.  (選擇性) 如果您已在應用程式中定義應用程式角色，則可以使用 [ **選取角色** ] 選項，將選取的使用者和群組指派給應用程式的其中一個角色。 
1. 選取 [ **指派** ] 以完成將使用者和群組指派給應用程式的作業。 
1. 確認您新增的使用者和群組有顯示在更新後的 [使用者和群組] 清單中。

## <a name="more-information"></a>詳細資訊

- [如何：在應用程式中新增應用程式角色](./howto-add-app-roles-in-azure-ad-apps.md)
- [使用應用程式角色與角色宣告將授權新增至 ASP.NET Core Web 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [在您的應用程式中使用安全性群組和應用程式角色 (影片)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory 現在具有群組宣告和應用程式角色](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 應用程式資訊清單](./reference-app-manifest.md)
