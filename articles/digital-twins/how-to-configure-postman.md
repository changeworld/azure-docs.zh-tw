---
title: 如何配置郵遞員 - Azure 數位孿生 |微軟文檔
description: 瞭解如何配置和使用 Postman 來測試 Azure 數位孿生 API。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
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

通過 Postman 用戶端，解決方案開發人員可以指定 HTTP 要求 *（POST、GET、**更新**、PATCH*和*GET**DELETE）、* 要調用的 API 終結點以及 TLS 的使用類型。 Postman 也支援新增 HTTP 要求標頭、參數、表單資料和內文。

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>設定 Azure Active Directory 以使用 OAuth 2.0 隱含授權流程

1. 按照["快速入門"](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步驟創建和配置 Azure 活動目錄應用程式。 或者，您可以重用現有的應用註冊。

    [![配置新的郵遞員重定向 URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 現在，將**重定向 URI**添加到`https://www.getpostman.com/oauth2/callback`。

1. 選擇 **"隱式授予** > **訪問權杖"** 核取方塊以允許使用 OAuth 2.0 隱式授予流。 選擇 **"配置**"，然後**保存**。

1. 複製 Azure 活動目錄應用的**用戶端 ID。**

## <a name="obtain-an-oauth-20-token"></a>取得 OAuth 2.0 權杖

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

設置和配置 Postman 以獲取 Azure 活動目錄權杖。 之後，使用取得的權杖來向 Azure Digital Twins 提出已驗證的 HTTP 要求：

1. 確認您的**授權 URL** 正確無誤。 其應採用下列格式：

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 名稱  | 更換為 | 範例 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 租戶或組織的名稱。 使用人性化名稱，而不是 Azure 活動目錄應用註冊的字母數位**租戶 ID。** | `microsoft` |

1. 前往 [www.getpostman.com](https://www.getpostman.com/) 以下載應用程式。

1. 我們想提出GET請求。 選擇 **"授權"** 選項卡，選擇 OAuth 2.0，然後選擇 **"獲取新訪問權杖**"。

    | 欄位  | 值 |
    |---------|---------|
    | 授與類型 | `Implicit` |
    | 回呼 URL | `https://www.getpostman.com/oauth2/callback` |
    | 驗證 URL | 使用**步驟 1**中的**授權 URL** |
    | 用戶端識別碼 | 使用從上一節創建或重用的 Azure 活動目錄應用**的應用程式 ID** |
    | 影響範圍 | 保留空白 |
    | State | 保留空白 |
    | 用戶端驗證 | `Send as Basic Auth header` |

1. 用戶端現在應該如下所示：

    [![郵遞員用戶端權杖示例](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. 選取 [要求權杖]****。
  
1. 向下捲動，然後選取 [使用權杖]****。

## <a name="make-a-multipart-post-request"></a>提出多部分的 POST 要求

完成先前的步驟之後，設定 Postman 以提出已驗證的 HTTP 多部分 POST 要求：

1. 在 **"標題"** 選項卡下，添加具有值`multipart/mixed`的 HTTP 要求標頭鍵**內容類型**。

   [![指定內容類型多部分/混合](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 將非文字資料序列化到檔案。 JSON 資料會儲存為 JSON 檔案。
1. 在"**正文"** 選項卡下`form-data`，選擇 。 
1. 通過分配**金鑰**名稱來添加每個檔，選擇`File`。
1. 接著，透過 [選擇檔案]**** 按鈕選取每個檔案。

   [![郵遞員用戶端表單正文示例](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 用戶端不需要多部分區塊具有手動指派的 **Content-type** 或 **Content-disposition**。
   > * 您不需要為每個部分指定這些標頭。
   > * 您必須針對整個要求選取 `multipart/mixed` 或其他適當的 **Content-type**。

1. 最後，選擇 **"發送**"以提交多部分 HTTP POST 請求。 或`200``201`指示成功請求的狀態碼。 相應的回應訊息將顯示在用戶端介面中。

1. 通過調用 API 終結點驗證 HTTP POST 請求資料： 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Digital Twins 管理 API，以及如何使用它們，請參閱[如何使用 Azure Digital Twins 管理 API](how-to-navigate-apis.md)。

- 使用多部分要求[將 blob 新增至 Azure Digital Twins 的實體](./how-to-add-blobs.md)。

- 若要了解如何使用管理 API 進行驗證，請閱讀[使用 API 進行驗證](./security-authenticating-apis.md)。
