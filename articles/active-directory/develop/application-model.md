---
title: 應用程式模型 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的過程。
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
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795661"
---
# <a name="application-model"></a>應用程式模型

應用程式可以登入使用者，或將登入委派給身分識別提供者。 本文討論使用 Microsoft 身分識別平臺來註冊應用程式所需的步驟。

## <a name="register-an-application"></a>註冊應用程式

若要讓身分識別提供者知道使用者可以存取特定的應用程式，使用者和應用程式都必須向身分識別提供者註冊。 當您向 Azure Active Directory (Azure AD) 註冊您的應用程式時，您會為應用程式提供身分識別設定，讓它能夠與 Microsoft 身分識別平臺整合。 註冊應用程式也可讓您：

* 在 [登入] 對話方塊中自訂應用程式的商標。 此品牌很重要，因為登入是使用者對應用程式的第一個體驗。
* 決定您是否要讓使用者只有在隸屬于您的組織時才能登入。 此架構稱為單一租使用者應用程式。 或者，您可以允許使用者使用任何公司或學校帳戶（也稱為多租使用者應用程式）來登入。 您也可以允許個人 Microsoft 帳戶或 LinkedIn、Google 等的社交帳戶。
* 要求範圍許可權。 例如，您可以要求「使用者. 讀取」範圍，授與讀取已登入使用者之設定檔的許可權。
* 定義定義 web API 存取權的範圍。 一般而言，當應用程式想要存取您的 API 時，它必須要求您所定義範圍的許可權。
* 與 Microsoft 身分識別平臺共用秘密，以證明應用程式的身分識別。 在應用程式是機密用戶端應用程式的情況下，使用秘密是相關的。 機密用戶端應用程式是可以安全保存認證的應用程式。 需要受信任的後端伺服器才能儲存認證。

註冊應用程式之後，它會提供一個唯一的識別碼，在要求權杖時，它會與 Microsoft 身分識別平臺共用。 如果應用程式是 [機密用戶端應用程式](developer-glossary.md#client-application)，它也會根據使用的憑證或密碼，共用秘密或公開金鑰。

Microsoft 身分識別平臺使用可滿足兩個主要功能的模型來代表應用程式：

* 依支援的驗證通訊協定來識別應用程式。
* 提供驗證所需的所有識別碼、Url、秘密和相關資訊。

Microsoft 身分識別平臺：

* 保存在執行時間支援驗證所需的所有資料。
* 保存所有資料，以決定應用程式可能需要存取哪些資源，以及在哪些情況下應該滿足指定的要求。
* 提供基礎結構，以在應用程式開發人員的租使用者內，以及任何其他 Azure AD 租使用者中，執行應用程式布建。
* 在權杖要求期間處理使用者同意，並促進跨租使用者的應用程式動態布建。

*同意* 是資源擁有者授與授權給用戶端應用程式，以在特定許可權下代表資源擁有者存取受保護資源的程式。 Microsoft 身分識別平臺可讓：

* 使用者和系統管理員可動態授與或拒絕應用程式代表其存取資源的同意。
* 系統管理員最終決定哪些應用程式可以執行哪些應用程式，以及哪些使用者可以使用特定應用程式，以及如何存取目錄資源。

## <a name="multi-tenant-apps"></a>多租用戶應用程式

在 Microsoft 身分識別平臺中， [應用程式物件](developer-glossary.md#application-object) 會描述應用程式。 在部署時，Microsoft 身分識別平臺會使用應用程式物件作為藍圖來建立 [服務主體](developer-glossary.md#service-principal-object)，以代表目錄或租使用者內的應用程式具體實例。 服務主體會定義應用程式可在特定目標目錄中實際執行的作業、可使用該目錄的資源、可存取的資源等。 Microsoft 身分識別平臺會透過 [同意](developer-glossary.md#consent)，從應用程式物件建立服務主體。

下圖顯示由同意驅動的簡化 Microsoft 身分識別平台佈建流程。 它會顯示兩個租使用者： *A* 和 *B*。

* *租使用者 A* 擁有應用程式。
* *租使用者 B* 會透過服務主體具現化應用程式。

![此圖顯示由同意驅動的簡化布建流程。](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

在這個佈建流程中：

1. 租使用者 B 的使用者嘗試使用應用程式登入。 授權端點會要求應用程式的權杖。
1. 系統會取得並驗證使用者認證以進行驗證。
1. 系統會提示使用者提供同意，讓應用程式取得租使用者 B 的存取權。
1. Microsoft 身分識別平臺會使用租使用者 A 中的應用程式物件作為藍圖，在租使用者 B 中建立服務主體。
1. 使用者會收到要求的權杖。

您可以針對更多租使用者重複此程式。 租用戶 A 會保留應用程式的藍圖 (應用程式物件)。 所有其他租使用者的使用者和系統管理員，其同意可透過每個租使用者中對應的服務主體物件，繼續控制允許的應用程式。 如需詳細資訊，請參閱 [Microsoft 身分識別平臺中的應用程式和服務主體物件](app-objects-and-service-principals.md)。

## <a name="next-steps"></a>後續步驟

如需有關 Microsoft 身分識別平臺中的驗證和授權的詳細資訊，請參閱下列文章：

* 若要瞭解驗證與授權的基本概念，請參閱 [驗證與授權](authentication-vs-authorization.md)的比較。
* 若要瞭解如何在驗證與授權中使用存取權杖、重新整理權杖和識別碼權杖，請參閱 [安全性權杖](security-tokens.md)。
* 若要瞭解 web、desktop 和 mobile apps 的登入流程，請參閱 [應用程式登入流程](app-sign-in-flow.md)。

如需應用程式模型的詳細資訊，請參閱下列文章：

* 如需有關 Microsoft 身分識別平臺中的應用程式物件和服務主體的詳細資訊，請參閱 [如何及為何將應用程式新增至 Azure AD](active-directory-how-applications-are-added.md)。
* 如需單一租使用者應用程式和多租使用者應用程式的詳細資訊，請參閱 [Azure Active Directory 中的](single-and-multi-tenant-apps.md)租使用者。
* 如需如何 Azure AD 也提供 Azure Active Directory B2C 的詳細資訊，讓組織可以使用社交身分識別（例如 Google 帳戶）來登入使用者（通常是客戶），請參閱 [Azure Active Directory B2C 檔](../../active-directory-b2c/index.yml)。