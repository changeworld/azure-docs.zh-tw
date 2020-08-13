---
title: 快速入門：向 Microsoft 身分識別平台註冊應用程式 | Azure
description: 在本快速入門中，您將了解如何新增應用程式，並向 Microsoft 身分識別平台註冊該應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115351"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入門：使用 Microsoft 身分識別平台來註冊應用程式

在本快速入門中，您會使用 Azure 入口網站中的**應用程式註冊**體驗來註冊應用程式。 

當您以 Azure Active Directory 租用戶註冊應用程式時，您的應用程式就會與 Microsoft 身分識別平台整合。 企業開發人員和軟體即服務 (SaaS) 提供者可以開發可與 Microsoft 身分識別平台整合的商業雲端服務或企業營運應用程式。 整合可為這類服務提供安全的登入和授權。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure AD 租用戶](quickstart-create-new-tenant.md)。

## <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 入口網站註冊新的應用程式

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取您的帳戶。 將您的入口網站工作階段設定為您想要的 Azure AD 租用戶。
1. 搜尋並選取 [Azure Active Directory]  。 在 [管理]  底下選取 [應用程式註冊]  。
1. 選取 [新增註冊]  。
1. 在 [註冊應用程式]  中，輸入要向使用者顯示且有意義的應用程式名稱。
1. 指定可以使用應用程式的人員，如下所示：

    | 支援的帳戶類型 | 描述 |
    |-------------------------|-------------|
    | **僅此組織目錄中的帳戶** | 如果您要建置企業營運 (LOB) 應用程式，請選取此選項。 如果您未在目錄中註冊應用程式，則無法使用此選項。<br><br>此選項對應至僅限 Azure AD 的單一租用戶。<br><br>除非您在目錄外註冊應用程式，否則會預設使用此選項。 如果在目錄外註冊應用程式，則會預設使用 Azure AD 多租用戶和個人 Microsoft 帳戶。 |
    | **任何組織目錄中的帳戶** | 如果您想要鎖定所有商業和教育客戶，請選取此選項。<br><br>此選項對應至僅限 Azure AD 的多租用戶。<br><br>如果您將應用程式註冊為僅限 Azure AD 的單一租用戶，則可透過 [驗證]  頁面，將其更新為 Azure AD 多租用戶以及重新更新為單一租用戶。 |
    | **任何組織目錄中的帳戶及個人的 Microsoft 帳戶** | 選取此選項以鎖定最廣泛的一組客戶。<br><br>此選項對應至 Azure AD 多租用戶和個人 Microsoft 帳戶。<br><br>如果您將應用程式註冊為 Azure AD 多租用戶和個人 Microsoft 帳戶，則無法在 UI 中變更此設定。 相反地，您必須使用應用程式資訊清單編輯器來變更支援的帳戶類型。 |

1. 在 [重新導向 URI (選擇性)]  底下，選取您要建立的應用程式類型：**Web** 或**公用用戶端 (行動和傳統型)** 。 然後輸入應用程式的重新導向 URI 或回覆 URL。

    * 若為 Web 應用程式，請提供應用程式的基底 URL。 例如，`https://localhost:31544` 可能是在您的本機電腦上執行之 Web 應用程式的 URL。 使用者會使用此 URL 來登入 Web 用戶端應用程式。
    * 若為公用用戶端應用程式，請提供 Azure AD 用來傳回權杖回應的 URI。 輸入應用程式特定的值，例如 `myapp://auth`。

    如需 Web 應用程式或原生應用程式的範例，請參閱 [Microsoft 身分識別平台](./index.yml)中的快速入門。

1. 完成時，選取 [註冊]  。

    ![顯示在 Azure 入口網站註冊新應用程式的畫面](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD 會將唯一的應用程式識別碼或用戶端識別碼指派給您的應用程式。 入口網站會開啟應用程式的 [概觀]  頁面。 若要在應用程式中新增功能，您可以選取其他組態選項，包括商標、憑證和祕密、API 權限等等。

![新註冊應用程式的 [概觀] 頁面範例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>後續步驟

* 若要存取 Web API，請參閱[快速入門：設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)

* 如需有關權限的資訊，請參閱 [Microsoft 身分識別平台端點中的權限和同意](v2-permissions-and-consent.md)。

* 若要公開 Web API，請參閱[快速入門：設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)。

* 若要管理支援的帳戶，請參閱[快速入門：修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)。

* 若要建立應用程式並新增功能，請參閱 [Microsoft 身分識別平台](./index.yml)中的快速入門。

* 若要了解代表已註冊的應用程式和它們之間關係的兩個 Azure AD 物件，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。

* 若要深入了解開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。