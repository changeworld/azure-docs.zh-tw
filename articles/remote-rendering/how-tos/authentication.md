---
title: 驗證
description: 解釋驗證的工作原理
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681632"
---
# <a name="configure-authentication"></a>設定驗證

Azure 遠端呈現使用與 Azure[空間錨點 (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp)相同的身份驗證機制。 用戶端需要設定*帳戶金鑰*、*身份驗證權杖*或*AccessToken*才能成功呼叫 REST API。 *帳戶金鑰*可以在 Azure 門戶上的遠端呈現帳戶的「密鑰」選項卡中獲取。 *身份驗證權杖*是 Azure AD 權杖,可以使用[ADAL 庫](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)獲取。 *AccessToken*是一個 MR 權杖,可以從 Azure 混合實境安全權杖服務 (STS) 獲取。

## <a name="authentication-for-deployed-applications"></a>已部署應用程式的身份驗證

 建議使用帳戶金鑰快速載入,但僅在開發/原型設計期間使用。 強烈建議不要使用其中的嵌入式帳戶密鑰將應用程式運送到生產部門,而是使用基於使用者或基於服務的 Azure AD 身份驗證方法。

 Azure AD 身份驗證在[Azure 空間錨點 (ASA)](https://docs.microsoft.com/azure/spatial-anchors/)服務的[Azure AD 使用者身份驗證](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication)部分中介紹。

## <a name="role-based-access-control"></a>角色型存取控制

為了協助控制給服務的應用程式、服務或 Azure AD 使用者的存取等級,已建立以下角色,以便您根據需要根據 Azure 遠端呈現帳戶進行配置:

* **遠端呈現管理員**:為使用者提供 Azure 遠端呈現的轉換、管理會話、呈現和診斷功能。
* **遠端呈現客戶端**:為使用者提供 Azure 遠端呈現的管理會話、呈現和診斷功能。

## <a name="next-steps"></a>後續步驟

* [建立帳戶](create-an-account.md)
* [使用 Azure 前端 API 進行認證](frontend-apis.md)
* [電源外殼文稿範例](../samples/powershell-example-scripts.md)
