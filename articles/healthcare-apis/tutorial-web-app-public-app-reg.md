---
title: Web 應用程式教學課程 - 用戶端應用程式設定
description: 此教學課程將逐步解說註冊公用應用程式，以準備部署 Web 應用程式的步驟
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870450"
---
# <a name="client-application-registration"></a>用戶端應用程式註冊
在上一個教學課程中，您已部署並設定您的 Azure API for FHIR。 既然您已設定 Azure API for FHIR，我們將註冊公用用戶端應用程式。 如需更多詳細資料或進行疑難排解，您可以閱讀完整的[註冊公用用戶端應用程式](register-public-azure-ad-client-app.md)操作指南，但我們已針對此教學課程擷取了下列主要步驟。

1. 瀏覽至 Azure Active Directory
1. 選取 [應用程式註冊]****  -->  [新增註冊]****。
1. 命名您的應用程式，並將重新導向 URI 設定為 https://www.getpostman.com/oauth2/callback


![用戶端應用程式註冊](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>用戶端應用程式設定
一旦註冊您的用戶端應用程式，請從 [概觀] 頁面複製應用程式 (用戶端) 識別碼。 稍後在存取用戶端時您會需要此值。

![複製應用程式識別碼](media/tutorial-web-app/app-id.png)

接下來，設定正確的驗證選項。 從左側選取 [驗證]****。 勾選 [存取權杖]**** 和 [識別碼權杖]**** 方塊。 您也可以設定重新導向 URI 以便在此教學課程的第四個部分中建立 Web 應用程式時使用。 若要這麼做，請將 https://\<WEB-APP-NAME>.azurewebsites.net 新增至重新導向 URI 清單。 如果您在[撰寫 Web 應用程式](tutorial-web-app-write-web-app.md)的步驟中選擇不同的名稱，您必須返回並更新該名稱。

![應用程式驗證設定](media/tutorial-web-app/app-authentication.png)

既然您已設定正確的驗證，請設定 API 權限。 
1. 選取 [API 權限]****，然後按一下 [新增權限]****
1. 在 [我的組織使用的 API]**** 底下，搜尋 Azure 醫療保健 API
1. 選取 **user_impersonation**，然後按一下 [新增權限]****

## <a name="next-steps"></a>後續步驟
您現在有一個公用用戶端應用程式。 在下一個教學課程中，我們將逐步解說如何透過 Postman 來測試及取得此應用程式的存取權。

>[!div class="nextstepaction"]
>[在 Postman 中測試用戶端應用程式](tutorial-web-app-test-postman.md)
