---
title: 使用 Microsoft 帳戶設定註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Microsoft 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387995"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中使用 Microsoft 帳戶作為[識別提供者](openid-connect.md)，您必須在 Azure AD 租用戶中建立應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。 如果您還沒有 Microsoft 帳戶，可以在 [https://www.live.com/](https://www.live.com/) 取得。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 輸入應用程式的**名稱**。 例如 *MSAapp1*。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶 (任何 Azure AD 目錄 - 多租用戶) 和個人 Microsoft 帳戶 (例如 Skype、Xbox)]。

   如需不同帳戶類型選項的詳細資訊，請參閱[快速入門：向 Microsoft 身分識別平台註冊應用程式](../active-directory/develop/quickstart-register-app.md)。
1. 在 [重新導向 URI (選擇性)] 底下，選取 [Web]，然後在文字方塊中輸入 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp`。 以您的 Azure AD B2C 租用戶名稱取代 `<tenant-name>`。
1. 選取 [註冊]
1. 記錄應用程式 [概觀] 頁面上所顯示的**應用程式 (用戶端) 識別碼**。 當您在下一節中設定識別提供者時，將需要這項資訊。
1. 選取 [憑證及祕密]
1. 按一下 [新增用戶端密碼]
1. 輸入該密碼的 [描述] (例如*應用程式密碼 1*)，然後按一下 [新增]。
1. 記錄 [值] 欄中顯示的應用程式密碼。 當您在下一節中設定識別提供者時，將需要這項資訊。

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [Microsoft 帳戶]。
1. 輸入 [名稱]。 例如 *MSA*。
1. 在 [用戶端識別碼]，輸入您先前建立之 Azure AD 應用程式的應用程式 (用戶端) 識別碼。
1. 在 [用戶端密碼] 中，輸入您記下的用戶端密碼。
1. 選取 [儲存]。
