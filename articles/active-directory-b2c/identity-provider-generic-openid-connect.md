---
title: 使用 OpenID 連接設置註冊和登錄
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 B2C 中的任何 OpenID 連接標識提供程式 （IdP） 設置註冊和登錄。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188251"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 透過 OpenID Connect 設定註冊和登入

[OpenID 連接](openid-connect.md)是構建在 OAuth 2.0 之上的身份驗證協定，可用於安全使用者登錄。 Azure AD B2C 中支援大多數使用此協定的標識提供程式。 本文說明如何將自訂 OpenID Connect 識別提供者新增至使用者流程中。

## <a name="add-the-identity-provider"></a>新增識別提供者

1. 以 Azure AD B2C 租戶的全域管理員身份登錄到[Azure 門戶](https://portal.azure.com/)。
1. 通過按一下頂部功能表中的 **"目錄 + 訂閱**"篩選器並選擇包含租戶的目錄，請確保使用的目錄包含 Azure AD B2C 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，搜尋並選取 [Azure AD B2C]****。
1. 選擇**標識提供程式**，然後選擇 **"新建 OpenID 連接提供程式**"。

## <a name="configure-the-identity-provider"></a>設定識別提供者

每個 OpenID Connect 標識提供程式都描述了一個中繼資料文檔，其中包含執行登錄所需的所有資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 OpenID Connect 中繼資料文件一律位於以 `.well-known\openid-configuration` 結尾的端點上。 針對您想要新增的 OpenID Connect 識別提供者，請輸入其中繼資料 URL。

## <a name="client-id-and-secret"></a>用戶端識別碼和祕密

為允許使用者登入，識別提供者會要求開發人員在其服務中註冊應用程式。 此應用程式具有稱為**用戶端識別碼**的識別碼與**用戶端祕密**。 從識別提供者複製這些值，然後在對應的欄位中輸入這些值。

> [!NOTE]
> 用戶端祕密為選擇性項目。 但是，如果要使用授權代碼流，則必須輸入用戶端金鑰，該[授權代碼流](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)使用該金鑰來交換權杖的代碼。

## <a name="scope"></a>影響範圍

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

* **使用者 ID**：輸入為登錄使用者提供*唯一識別碼*的聲明。
* **顯示名稱**：輸入為使用者提供*顯示名稱*或*全名*的聲明。
* **給定名稱**：輸入提供使用者*名字*的聲明。
* **姓氏**：輸入提供使用者*姓氏*的聲明。
* **電子郵件**：輸入提供使用者*電子郵件地址*的聲明。
