---
title: 設定註冊，並以 LinkedIn 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 LinkedIn 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259453"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 LinkedIn 帳戶登入

## <a name="create-a-linkedin-application"></a>建立 LinkedIn 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中使用 LinkedIn 帳戶做為 [識別提供者](authorization-code-flow.md) ，您需要在代表該帳戶的租使用者中建立應用程式。 如果您還沒有 LinkedIn 帳戶，您可以在中註冊 [https://www.linkedin.com/](https://www.linkedin.com/) 。

1. 使用您的 LinkedIn 帳戶認證登入 [LinkedIn 開發人員網站](https://www.developer.linkedin.com/)。
1. 選取 **我的應用程式**，然後按一下 [ **建立應用程式**]。
1. 輸入 **應用程式名稱**、 **LinkedIn 頁面**、 **隱私權原則 URL**和 **應用程式標誌**。
1. 同意 LinkedIn **API 使用** 規定，然後按一下 [ **建立應用程式**]。
1. 選取 [ **驗證** ] 索引標籤。在 [ **驗證金鑰**] 下，複製 **用戶端識別碼** 和 **用戶端密碼**的值。 您必須同時將 LinkedIn 設定為租使用者中的身分識別提供者。 **用戶端密碼**是重要的安全性認證。
1. 選取 **您的應用程式的 [授權重新導向 url**] 旁的 [編輯鉛筆]，然後選取 [新增重新 **導向 url**]。 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ， `your-tenant-name` 並以您的租使用者名稱取代。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。 選取 [更新]。
2. 根據預設，您的 LinkedIn 應用程式不會針對與登入相關的範圍核准。 若要要求審查，請選取 [ **產品** ] 索引標籤，然後選取 [ **使用 LinkedIn 登入**]。 當評論完成時，將會在您的應用程式中新增所需的範圍。
   > [!NOTE]
   > 您可以在 [ **OAuth 2.0 範圍**] 區段的 [**驗證**] 索引標籤上，查看應用程式目前允許的範圍。

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>將 LinkedIn 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **LinkedIn**]。
1. 輸入 [名稱]。 例如， *LinkedIn*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立之 LinkedIn 應用程式的用戶端識別碼。
1. 針對 **用戶端密碼**，請輸入您所記錄的用戶端密碼。
1. 選取 [儲存]。

## <a name="migration-from-v10-to-v20"></a>從1.0 版遷移至 v2。0

LinkedIn 最近 [將其 api 從 v1.0 更新至](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)v2.0。 在進行遷移的過程中，Azure AD B2C 只能在註冊期間取得 LinkedIn 使用者的完整名稱。 如果電子郵件地址是註冊期間所收集的其中一個屬性，則使用者必須手動輸入電子郵件地址並進行驗證。
