---
title: Azure AD .NET 通訊協定概觀 | Microsoft Docs
description: 如何使用 HTTP 訊息來使用 Azure AD 授權存取租用戶中的 Web 應用程式和 Web API。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522426"
---
## <a name="register-your-application-with-your-ad-tenant"></a>向 AD 租用戶註冊應用程式
首先，向您的 Azure Active Directory （Azure AD）租使用者註冊您的應用程式。 這會讓應用程式獲得應用程式識別碼，以及讓它可以接收權杖。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
   
1. 在頁面右上角選取您的帳戶，然後選取 [**切換目錄**] 流覽，然後選取適當的租使用者，以選擇您的 Azure AD 租使用者。 
   - 如果您的帳戶下只有一個 Azure AD 租使用者，或如果您已選取適當的 Azure AD 租使用者，請略過此步驟。
   
1. 在 Azure 入口網站中，搜尋並選取  **Azure Active Directory**。
   
1. 在**Azure Active Directory**左側功能表中，選取 [**應用程式註冊**]，然後選取 [**新增註冊**]。
   
1. 遵照提示進行，並建立新的應用程式。 本教學課程的 web 應用程式或公用用戶端（行動 & 桌面）應用程式並不重要，但如果您想要 web 應用程式或公用用戶端應用程式的特定範例，請參閱我們的[快速入門](../articles/active-directory/develop/v1-overview.md)。
   
   - [名稱] 為應用程式名稱，並能向使用者描述您的應用程式。
   - 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
   - 提供重新**導向 URI**。 對於 web 應用程式，這是使用者可以登入之應用程式的基底 URL。  例如， `http://localhost:12345`。 若為公用用戶端（行動 & 桌面），Azure AD 會使用它來傳回權杖回應。 輸入應用程式特定的值。  例如， `http://MyFirstAADApp`。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 完成註冊後，Azure AD 會為您的應用程式指派唯一的用戶端識別碼（**應用程式**識別碼）。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。
   
1. 若要在 Azure 入口網站中尋找您的應用程式，請選取 [**應用程式註冊**]，然後選取 [**查看所有應用程式**]。
