---
title: 使用 OpenID Connect 設定註冊和登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中設定註冊，並以任何 OpenID Connect 識別提供者（IdP）登入。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a7896e0385978c4221edc233c931bad720c60f8a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847598"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 透過 OpenID Connect 設定註冊和登入

[OpenID connect](openid-connect.md)是以 OAuth 2.0 為基礎的驗證通訊協定，可用於安全的使用者登入。 Azure AD B2C 中支援使用此通訊協定的大部分識別提供者。 本文說明如何將自訂 OpenID Connect 識別提供者新增至使用者流程中。

## <a name="add-the-identity-provider"></a>新增識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 按一下頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [**識別提供者**]，然後選取 **[新增 OpenID Connect 提供者]** 。

## <a name="configure-the-identity-provider"></a>設定識別提供者

每個 OpenID Connect 識別提供者都會描述元資料檔案，其中包含執行登入所需的大部分資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 OpenID Connect 中繼資料文件一律位於以 `.well-known\openid-configuration` 結尾的端點上。 針對您想要新增的 OpenID Connect 識別提供者，請輸入其中繼資料 URL。

## <a name="client-id-and-secret"></a>用戶端識別碼和祕密

為允許使用者登入，識別提供者會要求開發人員在其服務中註冊應用程式。 此應用程式具有稱為**用戶端識別碼**的識別碼與**用戶端祕密**。 從識別提供者複製這些值，然後在對應的欄位中輸入這些值。

> [!NOTE]
> 用戶端祕密為選擇性項目。 不過，如果您想要使用[授權碼流程](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)（使用密碼來交換權杖的程式碼），則必須輸入用戶端密碼。

## <a name="scope"></a>範圍

範圍會定義您想要從自訂識別提供者蒐集的資訊與權限。 OpenID Connect 要求必須包含 `openid` 範圍值，以便接收來自識別提供者的識別碼權杖。 沒有識別碼權杖，使用者就無法使用自訂識別提供者來登入 Azure AD B2C。 您可以附加其他範圍 (以空格分隔)。 請參閱自訂識別提供者的文件，查看其他可用的範圍。

## <a name="response-type"></a>回應類型

回應類型會說明要在對自訂識別提供者進行的 `authorization_endpoint` 初始呼叫中傳回哪種資訊。 您可以使用下列回應類型：

* `code`：依照[授權碼流程](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)，將授權碼傳回至 Azure AD B2C。 Azure AD B2C 會繼續呼叫 `token_endpoint` 來交換權杖的授權碼。
* `id_token`：識別碼權杖會從自訂識別提供者傳回到 Azure AD B2C。

## <a name="response-mode"></a>回應模式

回應模式可定義應用於將資料從自訂識別提供者傳送回 Azure AD B2C 的方法。 您可以使用下列回應模式：

* `form_post`：建議使用此回應模式，以獲得最佳安全性。 回應會透過 HTTP `POST` 方法傳輸，並使用 `application/x-www-form-urlencoded` 格式在主體中將程式碼或權杖編碼。
* `query`：傳回授權碼或權杖作為查詢參數。

## <a name="domain-hint"></a>網域提示

網域提示可用於直接跳到指定識別提供者的登入頁面，使用者不需在可用的識別提供者清單中進行選取。 若要允許這種行為，請輸入網域提示的值。 若要跳至自訂識別提供者，請在呼叫 Azure AD B2C 進行登入時，將參數 `domain_hint=<domain hint value>` 附加至您的要求結尾。

## <a name="claims-mapping"></a>宣告對應

在自訂識別提供者將識別碼權杖傳送回 Azure AD B2C 之後，Azure AD B2C 必須能夠將來自所接收權杖的宣告對應至 Azure AD B2C 可辨識及使用的宣告。 針對下列每個對應，請參閱自訂識別提供者的文件，以了解在識別提供者的權杖中傳回的宣告：

* **使用者識別碼**：輸入可為登入使用者提供*唯一識別碼*的宣告。
* **顯示名稱**：輸入可為使用者提供*顯示名稱*或*完整名稱*的宣告。
* **指定的名稱**：輸入可提供使用者*名字*的宣告。
* **姓氏**：輸入可提供使用者*姓氏*的宣告。
* **電子郵件**：輸入提供使用者*電子郵件地址*的宣告。
