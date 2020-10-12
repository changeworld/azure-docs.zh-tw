---
title: 新增 web API 應用程式-Azure Active Directory B2C |Microsoft Docs
description: 瞭解如何將 web API 應用程式新增至您的 Active Directory B2C 租使用者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 438d2fec9efc6a1ad3fcdaa2829573e7205820ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385955"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>將 Web API 應用程式新增至您的 Azure Active Directory B2C 租用戶

 在您的租使用者中註冊 web API 資源，讓他們可以接受並回應出示存取權杖的用戶端應用程式要求。 本文說明如何在 Azure Active Directory B2C (Azure AD B2C) 中註冊 web API。

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用我們新的整合**應用程式註冊**體驗，或使用舊版**應用程式 (舊版)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如，*webapi1*。
1. 在 [重新導向 URI] 底下，選取 [Web]，然後輸入 Azure AD B2C 應傳回您應用程式所要求之任何權杖的端點。 在生產應用程式中，您可能會將重新導向 URI 設定為類似的端點 `https://localhost:5000` 。 在開發或測試期間，您可以將它設定為 `https://jwt.ms` Microsoft 擁有的 web 應用程式，以顯示權杖的已解碼內容 (權杖的內容永遠不會離開瀏覽器) 。 您可以隨時在已註冊的應用程式中新增及修改重新導向 URI。
1. 選取 [註冊]。
1. 記錄 **應用程式 (用戶端) 識別碼** ，以便在您的 Web API 程式碼中使用。

如果您的應用程式會執行隱含授與流程，例如以 JavaScript 為基礎的單一頁面應用程式 (SPA) ，您可以遵循下列步驟來啟用流程：

1. 在 [管理] 底下，選取 [驗證]。
1. 在 [隱含授與] 底下，同時選取 [存取權杖] 和 [識別碼權杖] 核取方塊。
1. 選取 [儲存]。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式 (舊版)]，然後選取 [新增]。
5. 輸入應用程式的名稱。 例如，*webapi1*。
6. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
7. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在生產應用程式中，您可以將回復 URL 設定為值，例如 `https://localhost:44332` 。 基於測試目的，請將 [回復 URL] 設定為 `https://jwt.ms` 。
8. 針對**應用程式識別碼 URI**，請輸入您的 Web API 所使用的識別碼。 系統會為您產生包含網域的完整識別碼 URI。 例如： `https://contosotenant.onmicrosoft.com/api` 。
9. 按一下 [建立]。
10. 在 [屬性] 頁面上，記錄您會在設定 Web 應用程式時使用的應用程式識別碼。

* * *

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 在本教學課程中，您會使用範圍來定義 Web API 的讀取和寫入權限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>授與權限

若要從應用程式呼叫受保護的 Web API，您必須為應用程式授與對 API 的權限。 例如，在 [教學課程：在 Azure Active Directory B2C 中註冊應用程式](tutorial-register-applications.md)時，會在 Azure AD B2C 中註冊名為 *webapp1* 的 web 應用程式。 您可以使用此應用程式來呼叫 Web API。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

您的應用程式會進行註冊，以呼叫受保護的 Web API。 使用者會透過 Azure AD B2C 進行驗證以使用應用程式。 該應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。
