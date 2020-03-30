---
title: 為 Azure 媒體服務 v3 REST API 呼叫配置郵遞員
description: 本文說明如何設定 Postman，以便用來呼叫 Azure 媒體服務 (AMS) REST API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779632"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>為媒體服務 v3 REST API 呼叫配置郵遞員

本文說明如何設定 **Postman**，以便用來呼叫 Azure 媒體服務 (AMS) REST API。 本文說明如何將環境和集合檔案匯入 **Postman**。 集合包含呼叫 Azure 媒體服務 (AMS) REST API 之 HTTP 要求的分組定義。 環境檔案包含集合所使用的變數。

在開始開發之前，請查看[使用媒體服務 v3 API 進行開發](media-services-apis-overview.md)。

## <a name="prerequisites"></a>Prerequisites

- [創建媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- 取得所需資訊來[存取 API](access-api-cli-how-to.md)
- 安裝 [Postman](https://www.getpostman.com/) \(英文\) REST 用戶端，來執行在某些 AMS REST 教學課程中所示範的 REST API。 

    我們使用的是 **Postman**，但任何 REST 工具都適用。 其他替代方案為：搭配 REST 外掛程式的 **Visual Studio Code**，或 **Telerik Fiddler**。 

> [!IMPORTANT]
> 查看[命名約定](media-services-apis-overview.md#naming-conventions)。

## <a name="download-postman-files"></a>下載 Postman 檔案

複製包含 Postman 集合和環境檔案的 GitHub 存放庫。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>設定 Postman

### <a name="configure-the-environment"></a>建立環境 

1. 開啟 **Postman** 應用程式。
2. 選取畫面右側的 [管理環境]**** 選項。

    ![管理 env](./media/develop-with-postman/postman-import-env.png)
4. 按一下 [管理環境]**** 對話方塊中的 [匯入]****。
2. 瀏覽至您在複製 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 時下載的 `Azure Media Service v3 Environment.postman_environment.json` 檔案。
6. 新增 **Azure Media Service v3 Environment** 環境。

    > [!Note]
    > 使用您從前述的**存取媒體服務 API** 一節中取得的值來更新存取變數。

7. 按兩下選取的檔案，並輸入您依照存取 API 步驟所取得的值。
8. 關閉對話方塊。
9. 從下拉式清單中選取 **Azure Media Service v3 Environment** 環境。

    ![選擇 env](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>設定集合

1. 按一下 [匯入]**** 來匯入集合檔案。
1. 瀏覽至您在複製 `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` 時下載的 `Media Services v3.postman_collection.json` 檔案
3. 選擇 **Media Services v3.postman_collection.json** 檔案。

    ![匯入檔案](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>取得 Azure AD 權杖 

在開始操作 AMS v3 資源之前，您需要先取得和設定服務主體驗證的 Azure AD 權杖。

1. 在 Postman 應用的左側視窗中，選擇"步驟 1：獲取 AAD 身份驗證權杖"。
2. 然後，選取 [取得服務主體驗證的 Azure AD 權杖]。
3. 按 [傳送]****。

    此時會傳送下列 **POST** 作業。

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 傳回的回應會包含權杖，並將 "AccessToken" 環境變數設定為權杖值。  

    ![取得 AAD 權杖](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>疑難排解 

* 如果應用程式在"HTTP 504：閘道超時"時失敗，請確保位置變數未顯式設置為媒體服務帳戶的預期位置以外的值。 
* 如果收到"未找到帳戶"錯誤，請另一請檢查以確保 Body JSON 消息中的位置屬性設置為媒體服務帳戶位於的位置。 

## <a name="see-also"></a>另請參閱

- [將檔案上傳至媒體服務帳戶 - REST](upload-files-rest-how-to.md)
- [使用媒體服務建立篩選 - REST](filters-dynamic-manifest-rest-howto.md)
- [以 Azure Resource Manager 為基礎的 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>後續步驟

- [使用 REST 串流檔案](stream-files-tutorial-with-rest.md)。  
- [教程：基於 URL 對遠端檔進行編碼並資料流視頻 - REST](stream-files-tutorial-with-rest.md)
