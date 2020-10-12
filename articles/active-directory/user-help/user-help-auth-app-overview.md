---
title: 什麼是 Microsoft Authenticator 應用程式？ - Azure Active Directory | Microsoft Docs
description: 了解 Microsoft Authenticator 應用程式，包括其本質和運作方式，以及本節的內容中包含哪些資訊。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055226"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>什麼是 Microsoft Authenticator 應用程式？

當您使用雙因素驗證時，Microsoft Authenticator 應用程式可協助您登入帳戶。 雙因素驗證有助於您以更安全的方式存取帳戶，尤其是在檢視機密資訊時。 您有可能忘記密碼，或密碼有可能遭竊或遭到破解，而雙因素驗證可作為額外的安全性步驟，協助您保護帳戶而使他人難以入侵。

您可以透過多種方式使用 Microsoft Authenticator 應用程式，包括：

- 在您以使用者名稱和密碼登入後，回應驗證的提示。

- 搭配指紋、臉部或 PIN 使用您的使用者名稱、驗證器應用程式和行動裝置來登入，而無須輸入密碼。

- 為任何支援驗證器應用程式的其他帳戶產生程式碼。

> [!Important]
> Microsoft Authenticator 應用程式適用於任何使用雙因素驗證且支援限時單次密碼 (TOTP) 標準的帳戶。
>
>本文適用於嘗試下載 Microsoft Authenticator 應用程式並將其作為安全性驗證方法的使用者。 如果您是系統管理員，且想要了解如何使用 Authenticator 應用程式為您的員工和其他使用者開啟無密碼登入，請參閱[使用 Microsoft Authenticator 應用程式啟用無密碼登入 (預覽)](../authentication/howto-authentication-passwordless-phone.md)。

## <a name="terminology"></a>詞彙

| 詞彙|描述|
| ----|-----------|
| 雙因素驗證 | 必須明確使用兩項驗證資訊 (例如密碼和 PIN) 的驗證程序。 Microsoft Authenticator 應用程式支援標準雙因素驗證和無密碼登入。 |
| 多重要素驗證 (MFA) | 所有雙因素驗證都是多重要素驗證，必須根據組織的需求使用*至少*兩項驗證資訊。 |
| Microsoft 帳戶 (也稱為 MSA) | 您可以建立自己的個人帳戶，以取得消費者導向的 Microsoft 產品和雲端服務（例如 Outlook、OneDrive、Xbox LIVE 或 Microsoft 365）的存取權。 Microsoft 帳戶會建立並儲存在 Microsoft 所執行的 Microsoft 取用者身分識別帳戶系統中。 |
| 公司帳戶或學校帳戶 | 您的組織會建立您的工作或學校帳戶 (例如 alain@contoso.com) ，讓您存取內部和可能受限制的資源，例如 Microsoft Azure、Windows Intune 和 Microsoft 365。 |
| 驗證碼 | 每個新增的帳戶下，會出現在驗證器應用程式中的六位數代碼。 驗證碼每隔 30 秒就會變更，避免多次使用同一個驗證碼。 這也是所謂的單次密碼 (OTP)。 |

## <a name="how-two-factor-verification-works-with-the-app"></a>雙因素驗證與應用程式搭配運作的方式

雙因素驗證可透過下列方式與 Microsoft Authenticator 應用程式搭配運作：

- **通知。** 在您要登入工作/學校帳戶或個人 Microsoft 帳戶的裝置中輸入您的使用者名稱和密碼，Microsoft Authenticator 應用程式會隨即傳送通知，詢問您是否要**核准登入**。 如果您可以辨識該登入嘗試，請選擇 [核准]  。 否則，請選擇 [拒絕]  。 如果您選擇 [拒絕]  ，您也可以將要求標示為詐騙。

- **驗證碼。** 在您要登入工作/學校帳戶或個人 Microsoft 帳戶的裝置中輸入您的使用者名稱和密碼，然後從 Microsoft Authenticator 應用程式的 [帳戶]  畫面中複製相關聯的驗證碼。 驗證碼也是所謂的單次密碼 (OTP) 驗證。

- **無密碼登入。** 在您要登入工作/學校帳戶或個人 Microsoft 帳戶的裝置中輸入您的使用者名稱，然後經由行動裝置使用指紋、臉部或 PIN 確認您的身分。 使用此方法時不需要輸入密碼。

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>是否要使用裝置的生物特徵辨識功能

如果您使用 PIN 來完成驗證程序，則可以設定 Microsoft Authenticator 應用程式以改用裝置的指紋或臉部辨識 (生物特徵辨識) 功能。 您可以藉由選取選項來使用裝置的生物識別功能 (而非 PIN) 作為身分識別，在首次使用驗證器應用程式來驗證帳戶時設定此動作。

## <a name="who-decides-if-you-use-this-feature"></a>由誰決定是否使用這項功能？

視帳戶類型之不同，您的組織可能會決定您必須使用雙因素驗證，或是您可以自行決定。

- **公司或學校帳戶。** 如果您使用公司或學校帳戶 (例如 alain@contoso.com)，則會由您的組織決定您是否必須使用雙因素驗證，以及特定的驗證方法。 如需如何將公司或學校帳戶新增至 Microsoft Authenticator 應用程式的詳細資訊，請參閱[新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md)。

- **個人 Microsoft 帳戶。** 您可以選擇為個人 Microsoft 帳戶 (例如 alain@outlook.com) 設定雙因素驗證。 如需如何新增個人 Microsoft 帳戶的詳細資訊，請參閱[新增個人帳戶](user-help-auth-app-add-personal-ms-account.md)。

- **非 Microsoft 帳戶。** 您可以選擇為非 Microsoft 帳戶 (例如 alain@gmail.com) 設定雙因素驗證。 您的非 Microsoft 帳戶可能並非使用雙因素驗證一詞，但您應可在 [安全性]  或 [登入]  設定中找到這項功能。 Microsoft Authenticator 應用程式適用於任何支援 TOTP 標準的帳戶。 如需如何新增非 Microsoft 帳戶的詳細資訊，請參閱[新增非 Microsoft 帳戶](user-help-auth-app-add-non-ms-account.md)。

## <a name="in-this-section"></a>本節內容

| 發行項 | 描述 |
| ------ | ------------ |
| [下載並安裝應用程式](user-help-auth-app-download-install.md) | 說明應在何處及如何取得執行 Android 和 iOS 的裝置所適用的 Microsoft Authenticator 應用程式，並加以安裝。 |
| [新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md) | 說明如何將不同的公司或學校帳戶和個人帳戶新增至 Microsoft Authenticator 應用程式。 |
| [新增個人帳戶](user-help-auth-app-add-personal-ms-account.md) | 說明如何將個人 Microsoft 帳戶新增至 Microsoft Authenticator 應用程式。 |
| [新增非 Microsoft 帳戶](user-help-auth-app-add-non-ms-account.md) | 說明如何將非 Microsoft 帳戶新增至 Microsoft Authenticator 應用程式。 |
| [手動新增帳戶](user-help-auth-app-add-account-manual.md) | 說明您在無法掃描隨附的 QR 代碼時，如何以手動方式將帳戶新增至 Microsoft Authenticator 應用程式。 |
| [使用應用程式登入](user-help-auth-app-sign-in.md) | 說明如何使用 Microsoft Authenticator 應用程式登入您不同的帳戶。|
| [備份及復原帳戶認證](user-help-auth-app-backup-recovery.md) | 說明如何使用 Microsoft Authenticator 應用程式來備份和復原您的帳戶認證。 |
| [Microsoft Authenticator 應用程式常見問題集](user-help-auth-app-faq.md) | 提供應用程式常見問題集的解答。 |