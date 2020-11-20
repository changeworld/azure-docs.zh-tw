---
title: 使用 WhoIAM 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 在本教學課程中，您將瞭解如何整合 Azure AD B2C authentication 與 WhoIAM 以進行使用者驗證。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953537"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 WhoIAM 的教學課程

在此範例教學課程中，我們會提供指引，說明如何在您的環境中設定 [WhoIAM](https://www.whoiam.ai/brims/) 品牌身分識別管理系統 (BRIMS) ，並將其與 Active Directory B2C (Azure AD B2C) 整合。

BRIMS 是在您的環境中部署的一組應用程式和服務。 它會提供使用者群的語音、SMS 和電子郵件驗證。 BRIMS 可與您現有的身分識別和存取管理解決方案搭配運作，且與平臺無關。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的[Azure AD B2C 租](./tutorial-create-tenant.md)使用者。

- WhoIAM [試用帳戶](https://www.whoiam.ai/contact-us/)。

## <a name="scenario-description"></a>案例描述

WhoIAM 整合包含下列元件：

- Azure AD B2C 的租使用者。 這是授權伺服器，會根據定義的自訂原則來驗證使用者的認證。 也稱為身分識別提供者。

- 管理用戶端及其設定的管理入口網站。

- 透過端點公開各種功能的 API 服務。  

- Azure Cosmos DB，作為 BRIMS 系統管理入口網站和 API 服務的後端。

下列架構圖顯示執行。

![Azure AD B2C 與 WhoIAM 整合的架構圖表。](media/partner-whoiam/whoiam-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者會收到頁面，以針對使用 Azure AD B2C 作為身分識別提供者的應用程式，開始註冊或登入要求。
| 2. | 在驗證過程中，使用者要求驗證其電子郵件或電話的擁有權，或使用其語音作為生物特徵辨識驗證因素。  
| 3. | Azure AD B2C 會呼叫 BRIMS API 服務，並傳遞使用者的電子郵件地址、電話號碼和聲音錄製。
| 4. | BRIMS 使用可完全自訂的電子郵件和 SMS 範本等預先定義的設定，以與應用程式的樣式一致的方式與使用者互動。
| 5. | 當使用者的身分識別驗證完成之後，BRIMS 會將權杖傳回 Azure AD B2C，以指出驗證的結果。 Azure AD B2C 然後將應用程式的存取權授與使用者，或驗證嘗試失敗。  

## <a name="sign-up-with-whoiam"></a>使用 WhoIAM 註冊

1. 聯絡 [WhoIAM](https://www.whoiam.ai/contact-us/) 並建立 BRIMS 帳戶。

2. 使用您所提供的註冊指導方針，並設定下列 Azure 服務：

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：用於安全的密碼儲存，例如郵件服務密碼。

    - [Azure App Service](https://azure.microsoft.com/services/app-service/)：用來裝載 BRIMS API 和系統管理員入口網站服務。

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)：用來驗證系統管理員入口網站的系統管理使用者。

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：用來儲存和取出設定。

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (選擇性) ：用來登入 API 和系統管理員入口網站。

3. 在您的 Azure 環境中部署 BRIMS API 和 BRIMS administration 入口網站。

4. 您可以在 BRIMS 註冊檔中取得 Azure AD B2C 自訂原則範例。 遵循檔來設定您的應用程式，並使用 BRIMS 平臺進行使用者身分識別驗證。  

如需 WhoIAM BRIMS 的詳細資訊，請參閱 [產品檔](https://www.whoiam.ai/brims/)。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者。 在 [原則] 之下，選取 [Identity Experience Framework]。

2. 選取您先前建立的 **SignUpSignIn**。

3. 選取 [ **執行使用者流程** ]，然後：

   a. 針對 [ **應用程式**]，選取 (範例為 JWT) 的已註冊應用程式。

   b. 針對 [ **回復 url**]，選取重新 **導向 url**。

   c. 選取 [執行使用者流程]。

4. 請完成註冊流程，並建立帳戶。

5. 建立使用者屬性之後，會在流程期間呼叫 BRIMS 服務。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](./custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](./custom-policy-get-started.md?tabs=applications)