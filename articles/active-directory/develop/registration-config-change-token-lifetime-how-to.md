---
title: 變更自訂 Azure AD 應用程式的權杖存留期預設值
description: 如何為針對 Azure AD 開發的應用程式更新權杖存留期原則
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5dec4958c3a1d955d8f8c1dce90e27696fbebfe5
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063343"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何為自訂開發的應用程式變更權杖存留期預設值

本文說明如何使用 Azure AD PowerShell 來設定存取權杖存留期原則。 Azure AD Premium 可讓應用程式開發人員與租用戶系統管理員為針對非機密用戶端簽發的權杖設定存留期。 權杖存留期原則是以整個租用戶為基礎所設定，或針對要存取的資源所設定。

> [!IMPORTANT]
> 5月2020之後，租使用者將無法再設定重新整理和會話權杖存留期。  Azure Active Directory 將會在2021年1月30日之後停止接受現有的重新整理和會話權杖設定。 您仍然可以在淘汰之後設定存取權杖存留期。 如需詳細資訊，請參閱 [Azure AD 中的可設定權杖存留期](./active-directory-configurable-token-lifetimes.md)。
> 我們已在 Azure AD 條件式存取中實行 [驗證會話管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)   。 您可以使用這項新功能，藉由設定 [登入頻率] 來設定重新整理權杖存留期。  

若要設定存取權杖存留期原則，您必須下載 [Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureADPreview)。
執行 **Connect-AzureAD -Confirm** 命令。

以下是要求使用者在您的 web 應用程式中更頻繁地進行驗證的範例原則。 此原則會設定 web 應用程式的服務主體存取權的存留期。 建立原則，並將它指派給您的服務主體。 您也需要取得服務主體的 ObjectId。

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure AD 中可設定的權杖存留期](./active-directory-configurable-token-lifetimes.md) \(英文\)，以了解如何設定 Azure AD 所簽發的權杖存留期，包括如何為貴組織中的所有應用程式、多租用戶應用程式或貴組織中的特定服務主體設定權杖存留期。 
* [Azure AD Token 參考](./id-tokens.md)
