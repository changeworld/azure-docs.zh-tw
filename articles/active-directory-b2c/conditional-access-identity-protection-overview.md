---
title: Azure AD B2C 中的 Identity Protection 和條件式存取
description: 了解 Identity Protection 如何讓您發現風險性登入和風險偵測。 了解條件式存取如何讓您根據 Azure AD B2C 租用戶中的風險事件來強制執行組織原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4a71740c487896208a3da7bf35bb39899c56937
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952058"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C 的 Identity Protection 和條件式存取

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

使用 Azure AD Identity Protection 和條件式存取來增強 Azure Active Directory B2C (Azure AD B2C) 的安全性。 系統會自動偵測 Identity Protection 風險偵測特徵，包括有風險性使用者和有風險性登入，並顯示在您的 Azure AD B2C 租用戶中。 您可以建立條件式存取原則，以使用這些風險偵測來判斷動作並強制執行組織原則。 綜合這些功能，可讓 Azure AD B2C 應用程式擁有者更進一步控制有風險的驗證和存取原則。
  
如果您已經熟悉 [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 和 Azure AD 中的[條件式存取](../active-directory/conditional-access/overview.md)，那麼使用這些功能搭配 Azure AD B2C 將會是熟悉的體驗，而本文將討論其中的微小差異。

![B2C 租用戶中的條件式存取](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> 若要使用條件式存取，您需要 Azure AD B2C Premium P2。

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C 的 Identity Protection 和條件式存取優點  

藉由搭配條件式存取原則與 Identity Protection 風險偵測，您可以使用適當的原則動作來回應有風險的驗證。

- **在應用程式和客戶群的驗證風險上獲得新的可見度層級**。 透過 Azure AD 和 Microsoft 帳戶之間每月數十億個的驗證結果通知，風險偵測演算法現在會針對您的本地消費者或公民驗證，將驗證標示為低、中或高風險。
- **設定自有調適性驗證來自動解決風險**。 針對指定的應用程式，您可以要求一組特定使用者提供第二個驗證因素，如同多重要素驗證 (MFA)。 或者，您也可以根據偵測到的風險層級來封鎖存取。 如同其他 Azure AD B2C 體驗，您可以使用組織的訴求、風格和品牌來自訂產生的終端使用者體驗。 您也可以顯示使用者無法取得存取權的緩和替代方案。
- **根據位置、群組和應用程式來控制存取**。  條件式存取也可以用來控制非風險型情況。 例如，您可以對存取特定應用程式的客戶要求使用 MFA，或封鎖來自指定地理位置的存取。
- **與 Azure AD B2C 使用者流程和 Identity Experience Framework 自訂原則整合**。 使用您現有的自訂體驗，並加入與條件式存取連結所需的控制項。 您也可以執行用來授與存取權的進階案例，例如知識型存取或您自己慣用的 MFA 提供者。

## <a name="feature-differences-and-limitations"></a>功能差異和限制

Azure AD B2C 中的 Identity Protection 和條件式存取運作方式通常與 Azure AD 相同，但有下列例外狀況：

- 資訊安全中心無法在 Azure AD B2C 中使用。

- Azure AD B2C 租用戶中的 ROPC 伺服器對伺服器流程不支援 Identity Protection 和條件式存取。

- 在 Azure AD B2C 租用戶中，Identity Protection 風險偵測僅適用於本機 B2C 帳戶，而不適用於 Google 或 Facebook 之類的社交身分識別。

- 在 Azure AD B2C 租用戶中，您可以使用 Identity Protection 風險偵測的子集。 請參閱[設定 Identity Protection](conditional-access-identity-protection-setup.md#set-up-identity-protection)。

- 條件式存取裝置的合規性功能無法在 Azure AD B2C 租用戶中使用。


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>整合條件式存取與使用者流程和自訂原則

在 Azure AD B2C 中，您可以從內建的使用者流程中觸發條件式存取條件。 您也可以將條件式存取納入自訂原則中。 就像 B2C 使用者流程的其他層面一樣，您可以根據組織的訴求、品牌和緩和替代方案來自訂使用者體驗訊息。 請參閱[定義條件式存取技術設定檔](conditional-access-technical-profile.md)。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

您也可以使用 Microsoft Graph API 來管理 Azure AD B2C 中的條件式存取原則。 如需詳細資訊，請參閱[條件式存取文件](../active-directory/conditional-access/overview.md)和 [Microsoft Graph 參考](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md)。

## <a name="next-steps"></a>後續步驟

- [設定 Azure AD B2C 的 Identity Protection 和條件式存取](conditional-access-identity-protection-setup.md)
- [了解 Azure AD 中的 Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)
- [了解條件式存取](../active-directory/conditional-access/overview.md)