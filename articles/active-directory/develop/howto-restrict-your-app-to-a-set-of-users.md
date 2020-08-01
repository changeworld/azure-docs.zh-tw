---
title: 將 Azure AD 應用程式限制為一組使用者 |Azure
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
ms.openlocfilehash: 6e20b2f8c0e12fdb23668d16e2cbd7936cc4e48c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445470"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>如何：將您的 Azure AD 應用程式限制為 Azure AD 租使用者中的一組使用者

根據預設，在 Azure Active Directory (Azure AD) 租用戶中註冊的應用程式可供成功通過驗證的所有租用戶使用者使用。

同樣地，在[多租用戶](howto-convert-app-to-be-multi-tenant.md)應用程式的案例中，於此應用程式佈建所在的 Azure AD 租用戶中，使用者一旦成功驗證其各自的租用戶後，都能夠存取此應用程式。

租用戶系統管理員和開發人員通常需要將應用程式限制為僅供一組特定的使用者存取。 開發人員可以使用 Azure 角色型存取控制（Azure RBAC）之類的熱門授權模式來完成相同的工作，但這種方法在開發人員中需要大量的工作量。

租使用者系統管理員和開發人員也可以使用 Azure AD 的這項內建功能，將應用程式限制為租使用者中一組特定的使用者或安全性群組。

## <a name="supported-app-configurations"></a>支援的應用程式設定

將應用程式限制為僅供租用戶中一組特定使用者或安全性群組存取的選項，適用於下列類型的應用程式︰

- 設定了同盟單一登入與 SAML 型驗證的應用程式
- 使用 Azure AD 預先驗證的應用程式 Proxy 應用程式
- 直接建置在 Azure AD 應用程式平台上，且會在使用者或系統管理員同意該應用程式之後，使用 OAuth 2.0/OpenID Connect 驗證的應用程式。

     > [!NOTE]
     > 這項功能只適用於 Web 應用程式/Web API 和企業應用程式。 註冊為[原生](quickstart-v1-integrate-apps-with-azure-ad.md)的應用程式無法限制為僅供租用戶中的一組使用者或安全性群組存取。

## <a name="update-the-app-to-enable-user-assignment"></a>更新應用程式以啟用使用者指派

有兩種方式可建立具有已啟用使用者指派的應用程式。 其中一個需要**全域管理員**角色，第二個則否。

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>企業應用程式（需要全域管理員角色）

1. 移至[**Azure 入口網站**](https://portal.azure.com/)，並以**全域管理員**身分登入。
1. 在頂端列中，選取已登入的帳戶。 
1. 在 [目錄]**** 底下，選取要在其中註冊應用程式的 Azure AD 租用戶。
1. 在左側的導覽中，選取 [Azure Active Directory]****。 如果 [流覽] 窗格中無法使用 [Azure Active Directory]，請遵循下列步驟：

    1. 在左側主導覽功能表的頂端選取 [所有服務]****。
    1. 在篩選搜尋方塊中輸入**Azure Active Directory** ，然後從結果中選取 [ **Azure Active Directory** ] 專案。

1. 在 [ **Azure Active Directory** ] 窗格中，從**Azure Active Directory**左側導覽功能表中選取 [**企業應用程式**]。
1. 選取 [**所有應用程式**] 以查看所有應用程式的清單。

     如果看不到您希望在此顯示的應用程式，請使用 [所有應用程式] 清單頂端的各種篩選條件來限制清單，或向下捲動清單以找出應用程式。

1. 從清單中選取想要作為使用者或安全性群組指派目標的應用程式。
1. 在應用程式的 **[總覽**] 頁面上，從應用程式的左側導覽功能表中選取 [**屬性**]。
1. 找出 [需要使用者指派嗎？]**** 設定，並將它設定為 [是]****。 當此選項設定為 **[是]** 時，租使用者中的使用者必須先指派給此應用程式，否則將無法登入此應用程式。
1. 選取 [儲存]**** 以儲存此設定變更。

### <a name="app-registration"></a>應用程式註冊

1. 移至 [ [**Azure 入口網站**](https://portal.azure.com/)]。
1. 在頂端列中，選取已登入的帳戶。 
1. 在 [目錄]**** 底下，選取要在其中註冊應用程式的 Azure AD 租用戶。
1. 在左側的導覽中，選取 [Azure Active Directory]****。
1. 在 [ **Azure Active Directory** ] 窗格中，從**Azure Active Directory**左側導覽功能表中選取 [**應用程式註冊**]。
1. 建立或選取您要管理的應用程式。 您必須是此應用程式註冊的**擁有**者。
1. 在應用程式的 **[總覽**] 頁面上，在頁面頂端的 [基本資訊] 底下，遵循 [**本機目錄中的受控應用程式**] 連結。 這會帶您前往應用程式註冊的_受控企業應用程式_。
1. 從左側的導覽分頁中，選取 [**屬性**]。
1. 找出 [需要使用者指派嗎？]**** 設定，並將它設定為 [是]****。 當此選項設定為 **[是]** 時，租使用者中的使用者必須先指派給此應用程式，否則將無法登入此應用程式。
1. 選取 [儲存]**** 以儲存此設定變更。

## <a name="assign-users-and-groups-to-the-app"></a>將使用者和群組指派給應用程式

在設定完應用程式以啟用使用者指派後，您便可繼續作業並將使用者和群組指派給應用程式。

1. 在應用程式的左側導覽功能表中，選取 [**使用者和群組**] 窗格。
1. 在 [使用者和群組] 清單頂端，選取 [新增使用者] 按鈕，以開啟 [新增指派] 窗格。
1. 選取 [新增指派]**** 窗格中的 [使用者]**** 選取器。 

     隨即會顯示使用者和安全性群組清單，以及用來搜尋並找出特定使用者或群組的文字方塊。 此畫面可讓您一次選取多個使用者和群組。

1. 在您選好使用者和群組後，請按底部的 [選取] 按鈕，以移至下一個部分。
1. 選擇性如果您已在應用程式中定義應用程式角色，您可以使用 [**選取角色**] 選項，將選取的使用者和群組指派給其中一個應用程式角色。 
1. 按底部的 [指派] 按鈕，即可將使用者和群組指派給應用程式。 
1. 確認您新增的使用者和群組有顯示在更新後的 [使用者和群組] 清單中。

## <a name="more-information"></a>詳細資訊

- [如何：在您的應用程式中新增應用程式角色](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [使用應用程式角色與角色宣告將授權新增至 ASP.NET Core Web 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [在您的應用程式中使用安全性群組和應用程式角色 (影片)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory 現在具有群組宣告和應用程式角色](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)