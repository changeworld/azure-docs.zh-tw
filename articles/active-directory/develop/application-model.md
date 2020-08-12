---
title: 應用程式模型 |Azure
titleSuffix: Microsoft identity platform
description: 深入瞭解註冊應用程式以與 Microsoft 身分識別平臺 (v2.0) 進行整合的過程。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5aca96a9c3bc4e8f1061f677e316565b10014ac9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117476"
---
# <a name="application-model"></a>應用程式模型

應用程式可以登入使用者本身，或將登入委派給身分識別提供者。 本主題討論向 Microsoft 身分識別平臺註冊應用程式所需的步驟。

## <a name="registering-an-application"></a>註冊應用程式

若要讓識別提供者知道使用者具有特定應用程式的存取權，使用者和應用程式都必須向識別提供者註冊。 當您向 Azure AD 註冊應用程式時，您會提供應用程式的身分識別設定，讓它能夠與 Microsoft 身分識別平臺整合。 註冊應用程式也可讓您：

* 在登入對話方塊中自訂應用程式的商標。 這很重要，因為這是使用者在您的應用程式中的第一個使用經驗。
* 決定您是否要讓使用者只在屬於您的組織時才登入。 這是單一租使用者應用程式。 或允許使用者使用任何工作或學校帳戶登入。 這是多租使用者應用程式。 您也可以允許個人 Microsoft 帳戶，或 LinkedIn、Google 等的社交帳戶。
* 要求範圍許可權。 例如，您可以要求「使用者讀取」範圍，以授與讀取登入使用者設定檔的許可權。
* 定義定義 Web API 存取的範圍。 一般而言，當應用程式想要存取您的 API 時，它必須要求您所定義之範圍的許可權。
* 與 Microsoft 身分識別平臺共用秘密，以證明應用程式的身分識別。  這在應用程式是機密用戶端應用程式的情況下是相關的。 機密用戶端應用程式是可安全保存認證的應用程式。 他們需要信任的後端伺服器來儲存認證。

註冊之後，應用程式將會獲得一個唯一的識別碼，讓應用程式在要求權杖時與 Microsoft 身分識別平臺共用。 如果應用程式是[機密用戶端應用程式](developer-glossary.md#client-application)，它也會共用秘密或公開金鑰，視是否使用憑證或密碼而定。

Microsoft 身分識別平臺會使用可滿足兩個主要功能的模型來表示應用程式：

* 依支援的驗證通訊協定識別應用程式
* 提供驗證所需的所有識別碼、Url、秘密和相關資訊

Microsoft 身分識別平臺：

* 保留在執行時間支援驗證所需的所有資料
* 保存所有資料，以決定應用程式可能需要存取哪些資源，以及在何種情況下應滿足指定的要求
* 提供基礎結構，以在應用程式開發人員的租使用者內和任何其他 Azure AD 租使用者中執行應用程式布建
* 在權杖要求期間處理使用者同意，並協助跨租使用者動態布建應用程式

**同意**是資源擁有者授與授權給用戶端應用程式，以代表資源擁有者存取受保護資源的許可權。 Microsoft 身分識別平臺：

* 讓使用者與系統管理員能夠針對應用程式是否能代表他們存取資源，動態地授與或拒絕同意。
* 授與管理員決定允許應用程式執行哪些作業、有哪些使用者可以使用特定應用程式，以及目錄資源之存取方式的最終決定權。

## <a name="multi-tenant-apps"></a>多租用戶應用程式

在 Microsoft 身分識別平臺中，[應用程式物件](developer-glossary.md#application-object)會描述應用程式。 在部署階段，Microsoft 身分識別平臺會使用應用程式物件作為藍圖來建立[服務主體](developer-glossary.md#service-principal-object)，以代表目錄或租使用者內應用程式的實體實例。 服務主體會定義應用程式可以在特定的目標目錄中實際執行的動作、誰可以使用它、可存取的資源等。 Microsoft 身分識別平台會透過[同意](developer-glossary.md#consent)從應用程式物件建立服務主體。

下圖顯示由同意驅動的簡化 Microsoft 身分識別平台佈建流程。 它會顯示兩個租使用者： *A*和*B*。

* *租使用者 A*擁有應用程式。
* *租使用者 B*透過服務主體將應用程式具現化。

![由同意驅動的簡化佈建流程](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在這個佈建流程中：

1. 租用戶 B 的使用者嘗試透過應用程式登入，授權端點會要求應用程式提供權杖。
1. 系統會取得並驗證使用者認證以進行驗證。
1. 系統會提示使用者提供同意，讓應用程式取得對租使用者 B 的存取權。
1. Microsoft 身分識別平臺會使用租使用者 A 中的應用程式物件作為藍圖，在租使用者 B 中建立服務主體。
1. 使用者會收到要求的權杖。

您可以為其他租使用者重複此程式。 租用戶 A 會保留應用程式的藍圖 (應用程式物件)。 應用程式被授與同意之所有其他租使用者的使用者和管理員，可以透過每個租使用者中對應的服務主體物件，持續控制允許應用程式執行的動作。 如需詳細資訊，請參閱 [Microsoft 身分識別平台中的應用程式和服務主體物件](app-objects-and-service-principals.md)。

## <a name="next-steps"></a>後續步驟

如需涵蓋驗證和授權基本概念的其他主題：

* 若要了解 Microsoft 身分識別平台中驗證和授權的基本概念，請參閱[驗證與授權](authentication-vs-authorization.md)。
* 若要了解存取權杖、重新整理權杖和識別碼權杖如何用於驗證和授權，請參閱[安全性權杖](security-tokens.md)。
* 若要了解 Microsoft 身分識別平台中 Web、傳統型和行動應用程式的登入流程，請參閱[應用程式登入流程](app-sign-in-flow.md)。

若要深入瞭解應用程式模型：

* 如需有關 Microsoft 身分識別平臺中的應用程式物件和服務主體的詳細資訊，請參閱[如何和為何將應用程式新增至 Azure AD](active-directory-how-applications-are-added.md) 。
* 如需單一租使用者應用程式和多租使用者應用程式的詳細資訊，請參閱[Azure Active Directory 中](single-and-multi-tenant-apps.md)的租用。
* 如需有關 Azure AD 如何同時提供 Azure Active Directory B2C，讓組織可以使用像是 Google 帳戶的社交身分識別來登入使用者（通常是客戶）的詳細資訊，請參閱[Azure Active Directory B2C 檔](../../active-directory-b2c/index.yml)。