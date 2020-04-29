---
title: 設定註冊，並以 Microsoft 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，在您的應用程式中為具有 Microsoft 帳戶的客戶提供註冊和登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188013"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

若要使用 Microsoft 帳戶做為 Azure Active Directory B2C （Azure AD B2C）中的身分[識別提供者](openid-connect.md)，您需要在 Azure AD 租使用者中建立應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。 如果您還沒有 Microsoft 帳戶，您可以在上[https://www.live.com/](https://www.live.com/)取得一個。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]  。
1. 輸入應用程式的 [**名稱**]。 例如 *MSAapp1*。
1. 在 [**支援的帳戶類型**] 底下，選取 **[任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶] （例如 Skype、Xbox、Outlook.com）**。 此選項的目標是最廣泛的 Microsoft 身分識別集。

   如需不同帳戶類型選項的詳細資訊，請參閱[快速入門：使用 Microsoft 身分識別平臺註冊應用程式](../active-directory/develop/quickstart-register-app.md)。
1. 在 [重新**導向 URI （選用）**] 底下， `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`選取 [ **Web** ]，然後在文字方塊中輸入。 將`your-tenant-name`取代為您的 Azure AD B2C 租使用者名稱。
1. 選取 [**註冊**]
1. 記錄 [應用程式總覽] 頁面上顯示的**應用程式（用戶端）識別碼**。 當您在下一節中設定身分識別提供者時，需要此資訊。
1. 選取**憑證 & 秘密**
1. 按一下 [**新增用戶端密碼**]
1. 輸入密碼的**描述**，例如 [*應用程式密碼 1*]，然後按一下 [**新增**]。
1. 記錄 [**值**] 資料行中顯示的應用程式密碼。 當您在下一節中設定身分識別提供者時，需要此資訊。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租使用者的全域管理員身分登入[Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選取 [**識別提供者**]，然後選取 [ **Microsoft 帳戶**]。
1. 輸入 [**名稱**]。 例如， *MSA*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立之 Azure AD 應用程式的應用程式（用戶端）識別碼。
1. 針對 [**用戶端密碼**]，輸入您所記錄的用戶端密碼。
1. 選取 [儲存]  。
