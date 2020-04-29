---
title: 驗證
description: 說明驗證的運作方式
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681632"
---
# <a name="configure-authentication"></a>設定驗證

Azure 遠端呈現使用與[Azure 空間錨點（ASA）](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)相同的驗證機制。 用戶端必須設定*AccountKey*、 *AuthenticationToken*或*ACCESSTOKEN* ，才能成功呼叫 REST api。 您可以在 Azure 入口網站上遠端轉譯帳戶的 [金鑰] 索引標籤中取得*AccountKey* 。 *AuthenticationToken*是 Azure AD token，可以使用[ADAL 程式庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)來取得。 *AccessToken*是一種 MR token，可從 Azure Mixed Reality Security token SERVICE （STS）取得。

## <a name="authentication-for-deployed-applications"></a>已部署應用程式的驗證

 建議使用帳戶金鑰來快速入門，但僅限開發/原型設計期間。 強烈建議您不要使用內嵌的帳戶金鑰將應用程式運送到生產環境，而是改為使用以使用者為基礎或以服務為基礎的 Azure AD 驗證方法。

 Azure AD 驗證會在[Azure 空間錨點（ASA）](https://docs.microsoft.com/azure/spatial-anchors/)服務的[Azure AD 使用者驗證](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)一節中說明。

## <a name="role-based-access-control"></a>角色型存取控制

為了協助控制授與服務的應用程式、服務或 Azure AD 使用者的存取層級，系統已為您建立下列角色，以視需要針對您的 Azure 遠端轉譯帳戶進行指派：

* **遠端轉譯系統管理員**：為使用者提供轉換、管理會話、轉譯和診斷功能，以進行 Azure 遠端呈現。
* **遠端轉譯用戶端**：為使用者提供管理會話、轉譯和診斷功能，以進行 Azure 遠端呈現。

## <a name="next-steps"></a>後續步驟

* [建立帳戶](create-an-account.md)
* [使用 Azure 前端 Api 進行驗證](frontend-apis.md)
* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
