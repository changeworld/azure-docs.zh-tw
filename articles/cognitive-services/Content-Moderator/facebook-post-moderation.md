---
title: 教學課程：仲裁 Facebook 內容 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將了解如何使用機器學習型 Content Moderator 來協助仲裁 Facebook 文章和留言。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 478f7b7671a71d0d1f1f56c5d1d9889db81f7d37
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760188"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>教學課程：使用 Azure Content Moderator 仲裁 Facebook 文章和留言

在本教學課程中，您將了解如何使用 Azure Content Moderator 來協助仲裁 Facebook 頁面上的文章和留言。 Facebook 會將訪客張貼的內容傳送給 Content Moderator 服務。 然後您的 Content Moderator 工作流程就會根據內容分數和閾值，發佈內容或在審核工具內建立審核項目。 如需此案例的工作範例，請參閱 [Build 2017 示範影片](https://channel9.msdn.com/Events/Build/2017/T6033)。

> [!IMPORTANT]
> 2018 年時，Facebook 已對 Facebook 應用程式實作更嚴格的調查原則。 如果您的應用程式未經過 Facebook 檢閱小組的檢閱與核准，您將無法完成本教學課程的步驟。

本教學課程說明如何：

> [!div class="checklist"]
> * 建立 Content Moderator 小組。
> * 建立接聽來自 Content Moderator 和 Facebook 之 HTTP 事件的 Azure Functions。
> * 使用 Facebook 應用程式將 Facebook 頁面連結至 Content Moderator。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

此圖說明此案例中的每個元件：

![透過 "FBListener" 接收 Facebook 資訊並透過 "CMListener" 傳送資訊的 Content Moderator 圖示](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>必要條件

- Content Moderator 訂用帳戶金鑰。 請依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示，訂閱 Content Moderator 服務並取得金鑰。
- [Facebook 帳戶](https://www.facebook.com/)。

## <a name="create-a-review-team"></a>建立檢閱小組

如需有關如何註冊 [Content Moderator 審核工具](https://contentmoderator.cognitive.microsoft.com/)並建立審核小組的指示，請參閱[在 Web 上試用 Content Moderator](quick-start.md) 快速入門。 請記下 [認證] 頁面上的 [小組識別碼] 值。

## <a name="configure-image-moderation-workflow"></a>設定影像仲裁工作流程

請參閱[定義、測試和使用工作流程](review-tool-user-guide/workflows.md)指南來建立自訂影像工作流程。 Content Moderator 會使用此工作流程來自動檢查 Facebook 上的影像，並將部分影像傳送給檢閱工具。 請記下工作流程的**名稱**。

## <a name="configure-text-moderation-workflow"></a>設定文字仲裁工作流程

同樣地，請參閱[定義、測試和使用工作流程](review-tool-user-guide/workflows.md)指南，但這次要建立自訂文字工作流程。 Content Moderator 會使用此工作流程來自動檢查文字內容。 請記下工作流程的**名稱**。

![設定文字工作流程](images/text-workflow-configure.PNG)

使用 [執行工作流程] 按鈕來測試工作流程。

![測試文字工作流程](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>建立 Azure Functions

登入 [Azure 入口網站](https://portal.azure.com/)並遵循下列步驟：

1. 建立「Azure 函數應用程式」，如[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal)所示。
1. 移至新建立的「函式應用程式」。
1. 在應用程式中，移至 [平台功能] 索引標籤，然後選取 [設定]。 在下一頁的 [應用程式設定] 區段中，選取 [新增應用程式設定] 來新增下列索引鍵/值組：
    
    | 應用程式設定名稱 | value   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | 您的 Content Moderator TeamId  | 
    | `cm:SubscriptionKey` | 您的 Content Moderator 訂用帳戶金鑰 - 請參閱 [Credentials](review-tool-user-guide/credentials.md) |
    | `cm:Region` | 您的 Content Moderator 區域名稱 (不含空格)。 您可以在 Azure 資源 [概觀] 索引標籤的 [位置] 欄位中找到此名稱。|
    | `cm:ImageWorkflow` | 要在「影像」上執行之工作流程的名稱 |
    | `cm:TextWorkflow` | 要在「文字」上執行之工作流程的名稱 |
    | `cm:CallbackEndpoint` | CMListener 函式應用程式 (稍後將在本指南中建立) 的 URL |
    | `fb:VerificationToken` | 您建立的祕密權杖，用來訂閱 Facebook 摘要事件 |
    | `fb:PageAccessToken` | Facebook 圖形 API 存取權杖永不過期，可讓函式代表您「隱藏/刪除」文章。 您會在稍後的步驟中取得此權杖。 |

    按一下頁面頂端的 [儲存] 按鈕。

1. 返回 [平台功能] 索引標籤。使用左側窗格中的 [ **+** ] 按鈕來顯示 [新增函式] 窗格。 您即將建立的函式會收到來自 Facebook 的事件。

    ![醒目提示新增函式按鈕的 Azure Functions 窗格。](images/new-function.png)

    1. 按一下顯示 **Http 觸發程序**的圖格。
    1. 輸入名稱 **FBListener**。 [授權等級] 欄位應該設定為 [函式]。
    1. 按一下 [建立]。
    1. 使用來自 **FbListener/run.csx** 的內容取代 **run.csx** 的內容

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. 建立名為 **CMListener** 的新 **Http 觸發程序**函式。 此函式會從 Content Moderator 接收事件。 使用來自 **CMListener/run.csx** 的內容取代 **run.csx** 的內容

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>設定 Facebook 頁面和應用程式

1. 建立 Facebook 應用程式。

    ![Facebook 開發人員頁面](images/facebook-developer-app.png)

    1. 瀏覽至 [Facebook 開發人員入口網站](https://developers.facebook.com/)
    1. 按一下 [我的應用程式]。
    1. 新增應用程式。
    1. 為其命名
    1. 選取 [Webhook]-> [設定]
    1. 在下拉式功能表中選取 [頁面]，然後選取 [訂閱此物件]
    1. 提供 **FBListener Url** 作為 [回呼網址]，並提供您已在 [函數應用程式設定] 底下設定的 [驗證權杖]
    1. 訂閱之後，向下捲動以摘要並選取 [訂閱]。
    1. 按一下 [摘要] 資料列的 [測試] 按鈕，將測試訊息傳送至您的 FBListener Azure 函式，然後點擊 [傳送至我的伺服器] 按鈕。 您應該會看到 FBListener 正在接收要求。

1. 建立 Facebook 頁面。

    > [!IMPORTANT]
    > 在 2018 年，Facebook 已對 Facebook 應用程式實作更嚴格的調查。 如果您的應用程式未經過 Facebook 檢閱小組的檢閱與核准，您將無法執行第 2 節、第 3 節及第 4 節。

    1. 瀏覽至 [Facebook](https://www.facebook.com/bookmarks/pages)，然後建立一個**新的 Facebook 頁面**。
    1. 依照下列步驟來允許「Facebook 應用程式」存取此頁面：
        1. 瀏覽至[圖形 API 測試工具](https://developers.facebook.com/tools/explorer/)。
        1. 選取 [應用程式]。
        1. 選取 [粉絲專頁存取權杖]，傳送一個 **Get** 要求。
        1. 按一下回應中的 [粉絲專頁 ID]。
        1. 現在，將 **/subscribed_apps** 附加至 URL，然後傳送一個 **Get** (空回應) 要求。
        1. 提交一個 **Post** 要求。 您會得到像是 **success: true** 的回應。

3. 建立永不過期的「圖形 API」存取權杖。

    1. 瀏覽至[圖形 API 測試工具](https://developers.facebook.com/tools/explorer/)。
    2. 選取 [應用程式] 選項。
    3. 選取 [取得用戶存取權杖] 選項。
    4. 在 [選擇權限] 底下，選取 [manage_pages] 和 [publish_pages] 選項。
    5. 我們將在下一個步驟中使用**存取權杖** (短期權杖)。

4. 我們會在接下來幾個步驟中使用 Postman。

    1. 開啟 **Postman** (或從[這裡](https://www.getpostman.com/)取得)。
    2. 匯入兩個檔案：
        1. [Postman 集合](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 環境](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 更新下列環境變數：
    
        | Key | 值   | 
        | -------------------- |-------------|
        | appId   | 在這裡插入您的「Facebook 應用程式編號」  | 
        | appSecret | 在這裡插入您「Facebook 應用程式」的祕密 | 
        | short_lived_token | 插入您在上一個步驟中產生的短期用戶存取權杖 |
    4. 現在，執行集合中所列的 3 個 API： 
        1. 選取 [Generate Long-Lived Access Token] \(產生長期存取權杖\) ，然後按一下 [Send] \(傳送\)。
        2. 選取 [Get User ID] \(取得使用者識別碼)，然後按一下 [Send] \(傳送\)。
        3. 選取 [Get Permanent Page Access Token] \(取得永久頁面存取權杖\) ，然後按一下 [Send] \(傳送\)。
    5. 從回應中複製 **access_token** 值，然後將其指派給 [fb:PageAccessToken] 應用程式設定。

此解決方案會將張貼在您 Facebook 頁面上的所有影像和文字傳送給 Content Moderator。 接著將會叫用您先前設定的工作流程。 未通過工作流程中所定義準則的內容，將會傳送至審核工具中的審核項目。 其餘內容則會自動發佈。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您設定程式來分析產品影像，以便依產品類型加以標記，並允許審核小組對內容仲裁做出明智的決策。 接下來，請深入了解影像仲裁的詳細資料。

> [!div class="nextstepaction"]
> [影像仲裁](./image-moderation-api.md)
