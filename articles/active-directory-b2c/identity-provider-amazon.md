---
title: 設定註冊，並以 Amazon 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Amazon 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b22c4fb8f5c54437281e90a74032e8ee1d05bcb8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846636"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Amazon 帳戶登入

## <a name="create-an-amazon-application"></a>建立 Amazon 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 Amazon 帳戶做為[識別提供者](authorization-code-flow.md)，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 Amazon 帳戶，您可以在[https://www.amazon.com/](https://www.amazon.com/)註冊。

1. 以您的 Amazon 帳戶認證登入 [Amazon Developer Center (Amazon 開發人員中心)](https://login.amazon.com/)。
1. 若您尚未執行此動作，請按一下 [註冊]、遵循開發人員註冊步驟，並接受原則。
1. 選取 [註冊新應用程式]。
1. 請輸入 [名稱]、[描述]，以及 [隱私權注意事項 URL]，然後按一下 [儲存]。 隱私權注意事項是您要提供給使用者之隱私權資訊的管理頁面。
1. 在 [Web 設定] 區段中，複製 [用戶端識別碼] 的值。 選取 [顯示密碼] 以取得用戶端密碼，然後複製該密碼。 您必須使用這兩個值，將 Amazon 帳戶設為租用戶中的識別提供者。 **用戶端密碼** 是重要的安全性認證。
1. 在 [Web 設定] 區段中，選取 [編輯]，然後在 [允許的 JavaScript 原始來源] 中，輸入 `https://your-tenant-name.b2clogin.com`，並在 [允許的傳回 URL] 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
1. 按一下 [檔案]。

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>將 Amazon 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 [ **Amazon**]。
1. 輸入 [名稱]。 例如， *Amazon*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 Amazon 應用程式的用戶端識別碼。
1. 針對 [**用戶端密碼**]，輸入您所記錄的用戶端密碼。
1. 選取 [儲存]。
