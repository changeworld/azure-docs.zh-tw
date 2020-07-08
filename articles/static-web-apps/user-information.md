---
title: 存取 Azure Static Web Apps 中的使用者資訊
description: 瞭解如何讀取授權提供者傳回的使用者資料。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595137"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>存取 Azure Static Web Apps 預覽版本中的使用者資訊

Azure Static Web Apps 透過[直接存取端點](#direct-access-endpoint)提供驗證相關的使用者資訊給 [API 函式](#api-functions)。

許多使用者介面高度依賴使用者驗證資料。 直接存取端點是公用程式 API，會公開使用者資訊，而不需要實作自訂函式。 除了便利之外，直接存取端點不會因與無伺服器架構相關聯的冷啟動延遲而變動。

## <a name="client-principal-data"></a>用戶端主體資料

用戶端主體資料物件會將使用者識別資訊公開給您的應用程式。 下列屬性可在用戶端主體物件中精選使用：

| 屬性  | 描述 |
|-----------|---------|
| `identityProvider` | [識別提供者](authentication-authorization.md)的名稱。 |
| `userId` | 使用者的 Azure Static Web Apps 專用唯一識別碼。 <ul><li>每個應用程式中的這個值是唯一的。 例如，相同的使用者在不同的靜態 Web 應用程式資源上會傳回不同的 `userId` 值。<li>在使用者存留期，此值會持續存在。 如果您刪除並將相同的使用者新增回應用程式，會產生新的 `userId`。</ul>|
| `userDetails` | 使用者的使用者名稱或電子郵件地址。 有些提供者會傳回[使用者的電子郵件地址](authentication-authorization.md)，有些則會傳送[使用者控制碼](authentication-authorization.md)。 |
| `userRoles`     | [使用者獲派角色](authentication-authorization.md)的陣列。 |

以下是用戶端主體物件的範例：

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>直接存取端點

您可以將 `GET` 要求傳送至 `/.auth/me` 路由，並接收用戶端主體資料的直接存取權。 當您的檢視狀態依賴授權資料時，請使用此方法來獲得最佳效能。

針對已登入的使用者，回應會包含用戶端主體 JSON 物件。 未經驗證的使用者所提出的要求會傳回 `null`。

使用 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API，您可以使用下列語法來存取用戶端主體資料。

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API 函式

用戶端主體資料會傳遞至 `x-ms-client-principal` 要求標頭中的 API 函式。 用戶端主體資料會以 [Base64](https://www.wikipedia.org/wiki/Base64) 編碼字串的形式傳送，其中包含已序列化的 JSON 物件。

下列範例函式顯示如何讀取和傳回使用者資訊。

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

假設上述函式的名稱為 `user`，您可以使用 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> 瀏覽器 API，使用下列語法來存取 API 的回應。

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> Internet Explorer 不支援 [fetch](https://caniuse.com/#feat=fetch) API 和 [await](https://caniuse.com/#feat=mdn-javascript_operators_await) 運算子。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [進行應用程式設定](application-settings.md)
