---
title: 如何設定 Postman-Azure 數位 Twins |Microsoft Docs
description: 瞭解如何設定和使用 Postman 來測試 Azure 數位 Twins Api。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297163"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>如何針對 Azure Digital Twins 設定 Postman

本文說明如何設定 Postman REST 用戶端以進行互動，並測試 Azure Digital Twins 管理 API。 具體而言，其說明：

* 如何設定 Azure Active Directory 應用程式以使用 OAuth 2.0 隱含授與流程。
* 如何使用 Postman REST 用戶端，以向您的管理 API 提出權杖關聯的 HTTP 要求。
* 如何使用 Postman，以向您的管理 API 提出多部分的 POST 要求。

## <a name="postman-summary"></a>Postman 摘要

使用 REST 用戶端工具 (例如 [Postman](https://www.getpostman.com/) \(英文\)) 來準備您的本機測試環境，藉以開始使用 Azure Digital Twins。 Postman 用戶端可協助快速建立複雜的 HTTP 要求。 前往 [www.getpostman.com/apps](https://www.getpostman.com/apps) 以下載 Postman 用戶端的桌面版本。

[Postman](https://www.getpostman.com/) \(英文\) 是一個 REST 測試工具，可將重要的 HTTP 要求功能定位為以外掛程式為基礎的實用桌面 GUI。

透過 Postman 用戶端，解決方案開發人員可以指定 HTTP 要求的種類（*POST*、 *GET*、 *UPDATE*、 *PATCH*和*DELETE*）、要呼叫的 API 端點，以及使用 TLS。 Postman 也支援新增 HTTP 要求標頭、參數、表單資料和內文。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>設定 Azure Active Directory 以使用 OAuth 2.0 隱含授權流程

1. 請依照[快速入門](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步驟來建立和設定 Azure Active Directory 應用程式。 或者，您可以重複使用現有的應用程式註冊。

    [![設定新的 Postman 重新導向 URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 現在，將重新**導向 URI**新增`https://www.getpostman.com/oauth2/callback`至。

1. 選取 [**隱含授** > 與**存取權杖**] 核取方塊，以允許使用 OAuth 2.0 隱含授與流程。 選取 [**設定**]，然後按一下 [**儲存**]。

1. 複製 Azure Active Directory 應用程式的**用戶端識別碼**。

## <a name="obtain-an-oauth-20-token"></a>取得 OAuth 2.0 權杖

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

安裝和設定 Postman 以取得 Azure Active Directory token。 之後，使用取得的權杖來向 Azure Digital Twins 提出已驗證的 HTTP 要求：

1. 確認您的**授權 URL** 正確無誤。 其應採用下列格式：

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | 更換為 | 範例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 您的租使用者或組織的名稱。 使用易記名稱，而不是 Azure Active Directory 應用程式註冊的英數位元**租使用者識別碼**。 | `microsoft` |

1. 前往 [www.getpostman.com](https://www.getpostman.com/) 以下載應用程式。

1. 我們想要提出 GET 要求。 選取 [**授權**] 索引標籤，選取 [OAuth 2.0]，然後選取 [**取得新的存取權杖**]。

    | 欄位  | 值 |
    |---------|---------|
    | 授與類型 | `Implicit` |
    | 回呼 URL | `https://www.getpostman.com/oauth2/callback` |
    | 驗證 URL | 使用**步驟 1**中的**授權 URL** |
    | 用戶端識別碼 | 使用在上一節中建立或重複使用之 Azure Active Directory 應用**程式的應用程式識別碼** |
    | 影響範圍 | 保留空白 |
    | State | 保留空白 |
    | 用戶端驗證 | `Send as Basic Auth header` |

1. 用戶端現在應該如下所示：

    [![Postman 用戶端權杖範例](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. 選取 [要求權杖]****。
  
1. 向下捲動，然後選取 [使用權杖]****。

## <a name="make-a-multipart-post-request"></a>提出多部分的 POST 要求

完成先前的步驟之後，設定 Postman 以提出已驗證的 HTTP 多部分 POST 要求：

1. 在 [**標頭**] 索引標籤下，新增 HTTP 要求標頭索引`multipart/mixed`鍵 content-type，並**輸入**值。

   [![指定內容類型多部分/混合](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 將非文字資料序列化到檔案。 JSON 資料會儲存為 JSON 檔案。
1. 在 [**主體**] 索引卷`form-data`標下，選取 []。 
1. 藉由指派索引**鍵**名稱，選取`File`來新增每個檔案。
1. 接著，透過 [選擇檔案]**** 按鈕選取每個檔案。

   [![Postman 用戶端表單主體範例](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 用戶端不需要多部分區塊具有手動指派的 **Content-type** 或 **Content-disposition**。
   > * 您不需要為每個部分指定這些標頭。
   > * 您必須針對整個要求選取 `multipart/mixed` 或其他適當的 **Content-type**。

1. 最後，選取 [**傳送**] 以提交多部分 HTTP POST 要求。 `200`或`201`的狀態碼表示成功的要求。 適當的回應訊息會出現在用戶端介面中。

1. 藉由呼叫 API 端點來驗證您的 HTTP POST 要求資料： 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Digital Twins 管理 API，以及如何使用它們，請參閱[如何使用 Azure Digital Twins 管理 API](how-to-navigate-apis.md)。

- 使用多部分要求[將 blob 新增至 Azure Digital Twins 的實體](./how-to-add-blobs.md)。

- 若要了解如何使用管理 API 進行驗證，請閱讀[使用 API 進行驗證](./security-authenticating-apis.md)。
