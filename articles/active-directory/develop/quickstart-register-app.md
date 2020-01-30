---
title: 使用 Microsoft 身分識別平台來註冊應用程式 | Azure
description: 了解如何使用 Microsoft 身分識別平台來新增和註冊應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: bb741fe36e00c3d528470866669e64e9b0b221ae
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704131"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入門：使用 Microsoft 身分識別平台來註冊應用程式

企業開發人員和軟體即服務 (SaaS) 提供者可以開發可與 Microsoft 身分識別平台整合的商業雲端服務或企業營運應用程式，以提供安全的登入和授權給其服務。

本快速入門會說明如何在 Azure 入口網站中使用**應用程式註冊**體驗新增和註冊應用程式，以便讓應用程式可以和 Microsoft 身分識別平台整合。 若要深入了解新應用程式註冊體驗中的新功能與功能改善，請參閱[此部落格文章](https://developer.microsoft.com/graph/blogs/new-app-registration/)。

## <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 搜尋並選取 [Azure Active Directory]  。 在 [Active Directory]  頁面上，選取 [應用程式註冊]  ，然後選取 [新增註冊]  。
1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：

   - **名稱** - 輸入會顯示給應用程式使用者的有意義應用程式名稱。
   - **支援的帳戶類型** - 選取要讓應用程式支援的帳戶。

       | 支援的帳戶類型 | 描述 |
       |-------------------------|-------------|
       | **僅此組織目錄中的帳戶** | 如果您要建置企業營運 (LOB) 應用程式，請選取此選項。 如果您未在目錄中註冊應用程式，則無法使用此選項。<br><br>此選項對應至僅限 Azure AD 的單一租用戶。<br><br>除非您在目錄外註冊應用程式，否則會預設使用此選項。 如果在目錄外註冊應用程式，則會預設使用 Azure AD 多租用戶和個人 Microsoft 帳戶。 |
       | **任何組織目錄中的帳戶** | 如果您想要鎖定所有商業和教育客戶，請選取此選項。<br><br>此選項對應至僅限 Azure AD 的多租用戶。<br><br>如果您將應用程式註冊為僅限 Azure AD 的單一租用戶，則可透過 [驗證]  刀鋒視窗，將其更新為 Azure AD 多租用戶以及重新更新為單一租用戶。 |
       | **任何組織目錄中的帳戶及個人的 Microsoft 帳戶** | 選取此選項以鎖定最廣泛的一組客戶。<br><br>此選項對應至 Azure AD 多租用戶和個人 Microsoft 帳戶。<br><br>如果您將應用程式註冊為 Azure AD 多租用戶和個人 Microsoft 帳戶，則無法在 UI 中變更此選擇。 相反地，您必須使用應用程式資訊清單編輯器來變更支援的帳戶類型。 |

   - **重新導向 URI (選擇性)** - 選取您要建置的應用程式類型 ([Web]  或 [公用用戶端 (行動和傳統型)]  )，然後輸入應用程式的重新導向 URI (或回覆 URL)。
       - 若為 Web 應用程式，請提供應用程式的基底 URL。 例如，`https://localhost:31544` 可能是在您的本機電腦上執行之 Web 應用程式的 URL。 使用者會使用此 URL 來登入 Web 用戶端應用程式。
       - 若為公用用戶端應用程式，請提供 Azure AD 用來傳回權杖回應的 URI。 輸入應用程式特定的值，例如 `myapp://auth`。

     若要查看 Web 應用程式或原生應用程式的特定範例，請參閱我們的[快速入門](https://docs.microsoft.com/azure/active-directory/develop)。

1. 完成時，選取 [註冊]  。

    [![顯示在 Azure 入口網站註冊新應用程式的畫面](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD 會將唯一的應用程式 (用戶端) 識別碼指派給您的應用程式，然後系統會帶您進入應用程式的 [概觀]  頁面。 若要在應用程式中新增額外的功能，您可以選取其他組態選項，包括商標、憑證和祕密、API 權限等等。

[![新註冊應用程式 [概觀] 頁面的範例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 了解[權限和同意](v2-permissions-and-consent.md)。
- 若要在應用程式註冊中啟用其他設定功能 (例如認證和權限，以及讓其他租用戶的使用者能夠登入)，請參閱下列快速入門：
    - [設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
    - [設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)
    - [修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)
- 選擇[快速入門](https://docs.microsoft.com/azure/active-directory/develop)來快速建置應用程式並新增諸如取得權杖、重新整理權杖、讓使用者登入、顯示一些使用者資訊等功能。
- 若要深入了解代表已註冊應用程式的兩個 Azure AD 物件，以及它們之間的關聯性，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。
- 若要深入了解開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。
