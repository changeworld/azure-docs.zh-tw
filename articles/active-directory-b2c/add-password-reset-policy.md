---
title: 設定密碼重設流程
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中設定密碼重設流程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618716"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定密碼重設流程

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>密碼 rest 流程

密碼重設原則可讓使用者重設自己忘記的密碼。 密碼重設流程包含下列步驟： 
1. 在註冊和登入頁面上，使用者按一下 [忘記密碼？] 連結免費建立一個帳戶。 Azure AD B2C 會將 AADB2C90118 錯誤碼傳回給您的應用程式。 應用程式會叫用密碼重設原則來處理此錯誤碼。 
1. 使用者會提供並驗證其電子郵件，並提供一次時間密碼。
1. 輸入新密碼。

![密碼重設流程](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Prerequisites

如果您尚未這麼做，請 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>建立密碼重設使用者流程

若要讓您應用程式的使用者能夠重設其密碼，請使用密碼重設使用者流程。

1. 在 Azure AD B2C 租用戶的 [概觀] 功能表中，選取 [使用者流程]，然後選取 [新增使用者流程]。
1. 在 [建立使用者流程] 頁面上，選取 [密碼重設] 使用者流程。 
1. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。
1. 輸入使用者流程的 [名稱]。 例如，*passwordreset1*。
1. 針對 [識別提供者]啟用 [使用電子郵件地址重設密碼]。
2. 在 [應用程式宣告] 底下，按一下 [顯示更多]，並選擇您要以傳送回應用程式的授權權杖傳回的宣告。 例如，選取 [使用者的物件識別碼]。
3. 按一下 [確定]。
4. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]、確認先前所建立帳戶的電子郵件地址，然後選取 [繼續]。
1. 您現在應該有機會變更使用者的密碼。 變更密碼，然後選取 [繼續]。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>建立密碼重設原則

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義使用者旅程的 XML 檔案。 我們提供的入門套件包含數個預先建立的原則，包括：註冊和登入、密碼重設和設定檔編輯原則。 如需詳細資訊，請參閱 [Azure AD B2C 中的開始使用自訂原則](custom-policy-get-started.md)。

::: zone-end

## <a name="next-steps"></a>後續步驟

設定 [設定檔編輯流程](add-profile-editing-policy.md)。