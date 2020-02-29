---
title: 設定註冊，並以 Facebook 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Facebook 帳戶的客戶得以註冊和登入您的應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188268"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Facebook 帳戶登入

## <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要在 Azure Active Directory B2C （Azure AD B2C）中使用 Facebook 帳戶做為[識別提供者](authorization-code-flow.md)，您需要在代表它的租使用者中建立應用程式。 如果您還沒有 Facebook 帳戶，您可以在[https://www.facebook.com/](https://www.facebook.com/)註冊。

1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
1. 如果您尚未這麼做，您需要註冊為 Facebook 開發人員。 若要這麼做，請選取頁面右上角的 [**開始**使用]，接受 Facebook 的原則，然後完成註冊步驟。
1. 選取**我的應用程式**然後**建立應用程式**。
1. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
1. 選取 [**建立應用程式識別碼**]。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
1. 選取 [設定] > [基本]。
1. 選擇**分類**，例如 `Business and Pages`。 此為 Facebook 所需的值，但不會用於 Azure AD B2C。
1. 在頁面底部選取 [新增平台]，然後選取 [網站]。
1. 在 [Site URL] \(網站 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/`，並將 `your-tenant-name` 取代為您的租用戶名稱。 輸入**隱私權原則 URL** 的 URL，如 `http://www.contoso.com`。 原則 URL 是您需維護以提供應用程式隱私權資訊的網頁。
1. 選取 [Save Changes] \(儲存變更\)。
1. 在頁面頂端複製 [應用程式識別碼] 的值。
1. 選取 [**顯示**] 並複製 [**應用程式密碼**] 的值。 您必須同時使用這兩個值，將 Facebook 設定為租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。
1. 選取 [**產品**] 旁的加號，然後選取 [ **Facebook 登**入] 下的 [**設定**]。
1. 在 **[Facebook 登**入] 底下，選取 [**設定**]。
1. 在 [Valid OAuth redirect URIs] \(有效的 OAuth 重新導向 URI\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 選取頁面底部的 [**儲存變更**]。
1. 若要讓您的 Facebook 應用程式可供 Azure AD B2C，請選取頁面右上方的狀態選取器並開啟**它，** 讓應用程式變成公用，然後選取 [**切換模式]** 。  此時，狀態應該會從 [開發] 變更為 [作用中]。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>將 Facebook 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 [ **Facebook**]。
1. 輸入 [名稱]。 例如， *Facebook*。
1. 針對 [**用戶端識別碼**]，輸入您稍早建立的 Facebook 應用程式的應用程式識別碼。
1. 針對 [**用戶端密碼**]，輸入您所記錄的應用程式密碼。
1. 選取 [儲存]。
