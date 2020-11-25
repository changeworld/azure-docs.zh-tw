---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 890b19cd8d6f4bc836f6f541a830c1a5feb8458f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025575"
---
若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合 **應用程式註冊** 體驗，或使用舊版 **應用程式 (舊版)** 體驗。 [深入了解新的體驗](../articles/active-directory-b2c/app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如 *testapp1*。
1. 選取 [任何組織目錄或任何身分識別提供者中的帳戶]。
1. 在 [重新導向 URI] 底下，選取 [Web]，然後在 URL 文字方塊中輸入 `https://jwt.ms`。
1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。

應用程式註冊完成後，請啟用隱含授與流程：

1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [試用全新體驗] (若顯示的話)。
1. 在 [隱含授與] 底下，同時選取 [存取權杖] 和 [識別碼權杖] 核取方塊。
1. 選取 [儲存]。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如 *testapp1*。
1. 針對 [Web 應用程式/Web API]，選取 [是]。
1. 針對 [回覆 URL]，輸入 `https://jwt.ms`
1. 選取 [建立]。