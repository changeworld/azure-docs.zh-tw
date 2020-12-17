---
title: 設定設定檔編輯流程
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 中設定設定檔編輯流程。
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
ms.openlocfilehash: 1cf66918d8b5211dcc03be944dbcb92cdf6e0773
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618714"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中設定設定檔編輯流程

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>設定檔編輯流程

設定檔編輯原則可讓使用者管理其配置檔案屬性，例如顯示名稱、姓氏、名字、城市和其他屬性。 設定檔編輯流程包含下列步驟： 

1. 使用本機或社交帳戶註冊或登入。 如果會話仍在使用中，Azure AD B2C 會授權使用者，並跳至下一個步驟。
1. Azure AD B2C 從目錄讀取使用者設定檔，並讓使用者編輯屬性。

![設定檔編輯流程](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Prerequisites

如果您尚未這麼做，請 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>建立設定檔編輯使用者流程

如果您要在您的應用程式中允許使用者編輯其設定檔，您可使用設定檔編輯使用者流程。

1. 在 Azure AD B2C 租用戶 [概觀] 頁面的功能表中，選取 [使用者流程]，然後選取 [新增使用者流程]。
1. 在 [建立使用者流程] 頁面上，選取 [設定檔編輯] 使用者流程。 
1. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。
1. 輸入使用者流程的 [名稱]。 例如，*profileediting1*。
1. 針對身分 **識別提供者**，選取 [ **電子郵件登入**]。
1. 針對 [使用者屬性]，選擇您要客戶能夠在其設定檔中編輯的屬性。 例如，選取 [顯示更多]，然後選擇 [顯示名稱] 和 [職稱] 的屬性和宣告。 按一下 [確定]。
1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

### <a name="test-the-user-flow"></a>測試使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。
1. 針對 [應用程式]，選取您先前註冊名為 *webapp1* 的 Web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [執行使用者流程]，然後使用您先前建立的帳戶登入。
1. 現在您有機會變更使用者的顯示名稱和職稱。 按一下 **[繼續]** 。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>建立設定檔編輯原則

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義使用者旅程的 XML 檔案。 我們提供的入門套件包含數個預先建立的原則，包括：註冊和登入、密碼重設和設定檔編輯原則。 如需詳細資訊，請參閱 [Azure AD B2C 中的開始使用自訂原則](custom-policy-get-started.md)。

::: zone-end

## <a name="next-steps"></a>後續步驟

* 新增 [具有社交識別提供者](add-identity-provider.md)的登入。