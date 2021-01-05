---
title: Web 應用程式教學課程 - 用戶端應用程式設定
description: 此教學課程將逐步解說註冊公用應用程式，以準備部署 Web 應用程式的步驟
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802604"
---
# <a name="client-application-registration"></a>用戶端應用程式註冊
在上一個教學課程中，您已部署並設定您的 Azure API for FHIR。 既然您已設定 Azure API for FHIR，我們將註冊公用用戶端應用程式。 如需更多詳細資料或進行疑難排解，您可以閱讀完整的[註冊公用用戶端應用程式](register-public-azure-ad-client-app.md)操作指南，但我們已針對此教學課程擷取了下列主要步驟。

1. 瀏覽至 Azure Active Directory
1. 選取 [應用程式註冊]  -->  [新增註冊]。
1. 為您的應用程式命名
1. 選取 [公用用戶端/原生 (行動和桌面)]，並將重新導向 URL 設定為 `https://www.getpostman.com/oauth2/callback` 。

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="[註冊應用程式] 窗格的螢幕擷取畫面，以及範例應用程式名稱和重新導向 URL。":::

## <a name="client-application-settings"></a>用戶端應用程式設定

一旦註冊了用戶端應用程式，請從 [概觀] 頁面複製應用程式 (用戶端) 識別碼和租用戶識別碼。 稍後在存取用戶端時會需要這兩個值。

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="[用戶端應用程式設定] 窗格的螢幕擷取畫面，其中已醒目提示應用程式和目錄識別碼。":::

### <a name="connect-with-web-app"></a>使用 Web 應用程式連線

如果您已[撰寫 Web 應用程式](tutorial-web-app-write-web-app.md) 以與 Azure API for FHIR 連線，也必須設定正確的驗證選項。 

1. 在左側功能表的 [管理]下，選取 [驗證]。 

1. 若要新增平台組態，請選取 [Web]。

1. 設定重新導向 URI 以便在此教學課程的第四個部分中建立 Web 應用程式時使用。 若要這麼做，請將 `https://\<WEB-APP-NAME>.azurewebsites.net` 新增至重新導向 URI 清單。 如果您在[撰寫 Web 應用程式](tutorial-web-app-write-web-app.md)的步驟中選擇不同的名稱，您必須返回並更新該名稱。

1. 選取 [存取權杖] 和 [識別碼權杖] 核取方塊。

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="[應用程式驗證設定] 刀鋒視窗的螢幕擷取畫面，並已醒目提示新增平台步驟。":::

## <a name="add-api-permissions"></a>新增 API 權限

設定正確的驗證之後，請接著設定 API 權限：

1. 選取 [API 權限]，然後按一下 [新增權限]。
1. 在 [我的組織使用的 API] 底下，搜尋 Azure 醫療保健 API。
1. 選取 **user_impersonation**，然後按一下 [新增權限]。

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="[新增 API 權限] 刀鋒視窗的螢幕擷取畫面，其中已醒目提示新增 API 權限的步驟。":::

## <a name="next-steps"></a>後續步驟
您現在有一個公用用戶端應用程式。 在下一個教學課程中，我們將逐步解說如何透過 Postman 來測試及取得此應用程式的存取權。

>[!div class="nextstepaction"]
>[在 Postman 中測試用戶端應用程式](tutorial-web-app-test-postman.md)
