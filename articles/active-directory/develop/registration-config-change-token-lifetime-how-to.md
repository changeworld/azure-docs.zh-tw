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
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883348"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何為自訂開發的應用程式變更權杖存留期預設值

本文演示如何使用 Azure AD PowerShell 設置權杖存留期策略。 Azure AD Premium 可讓應用程式開發人員與租用戶系統管理員為針對非機密用戶端簽發的權杖設定存留期。 權杖存留期原則是以整個租用戶為基礎所設定，或針對要存取的資源所設定。

1. 若要設定權杖存留期原則，您必須下載 [Azure AD PowerShell 模組 (英文)](https://www.powershellgallery.com/packages/AzureADPreview)。
1. 執行 **Connect-AzureAD -Confirm** 命令。

    下列範例原則會設定單一要素重新整理權杖存留期上限。 建立原則：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure AD 中可設定的權杖存留期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) \(英文\)，以了解如何設定 Azure AD 所簽發的權杖存留期，包括如何為貴組織中的所有應用程式、多租用戶應用程式或貴組織中的特定服務主體設定權杖存留期。 
* [Azure AD 權杖參考](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
