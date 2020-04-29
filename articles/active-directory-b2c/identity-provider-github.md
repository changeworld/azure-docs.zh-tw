---
title: 設定註冊，並以 GitHub 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 GitHub 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188200"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>建立 GitHub OAuth 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 GitHub 帳戶做為[識別提供者](authorization-code-flow.md)，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 GitHub 帳戶，您可以在[https://www.github.com/](https://www.github.com/)註冊。

1. 使用您的 GitHub 認證登入 [GitHub 開發人員](https://github.com/settings/developers)網站。
1. 選取 [OAuth 應用程式]****，然後選取 [新增 OAuth 應用程式]****。
1. 輸入 [Application name] \(應用程式名稱\)**** 和您的 [Homepage URL] \(首頁 URL\)****。
1. 在 [授權回呼 URL]**** 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，仍請在輸入租用戶名稱時，全部使用小寫字母。
1. 按一下 **[Register application]** (註冊應用程式)。
1. 複製 [**用戶端識別碼**] 和 [**用戶端密碼**] 的值。 您必須同時將識別提供者新增至您的租用戶。

## <a name="configure-a-github-account-as-an-identity-provider"></a>將 GitHub 帳戶設為識別提供者

1. 以 Azure AD B2C 租使用者的全域管理員身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選取 [**識別提供者**]，然後選取 **[GitHub （預覽）**]。
1. 輸入 [**名稱**]。 例如， *GitHub*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 GitHub 應用程式的用戶端識別碼。
1. 針對 [**用戶端密碼**]，輸入您所記錄的用戶端密碼。
1. 選取 [儲存]  。
