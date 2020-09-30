---
title: 快速入門：在 Web 上試用 Content Moderator - Content Moderator
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將使用線上「Content Moderator 審核工具」，無須撰寫任何程式碼，即可測試 Content Moderator 的基本功能。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 7be14c07a08d58f02890a48d39dd0a8010101374
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332622"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>快速入門：在 Web 上試用 Content Moderator

在此快速入門中，您將使用線上「Content Moderator 審核工具」，無須撰寫任何程式碼，即可測試 Content Moderator 的基本功能。 如果您想要將此服務更快整合至您的應用程式，請參閱[後續步驟](#next-steps)一節中的其他快速入門。

## <a name="prerequisites"></a>必要條件

- 網頁瀏覽器

## <a name="set-up-the-review-tool"></a>設定審核工具
「Content Moderator 審核工具」是一個 Web 型工具，可讓人工審核者協助認知服務進行決策。 在本指南中，您將完成設定審核工具的簡短程序，以便了解 Content Moderator 服務如何運作。 請前往 [Content Moderator 審核工具](https://contentmoderator.cognitive.microsoft.com/)網站並註冊。

![Content Moderator 首頁](images/homepage.PNG)

## <a name="create-a-review-team"></a>建立檢閱小組

接著，建立審核小組。 在工作案例中，這會是將手動審核服務仲裁決策的一群人。 若要建立小組，您必須選取 [區域]，並提供 [小組名稱] 和 [小組識別碼]。 如果您想要邀請同事加入小組，只要在這裡輸入他們的電子郵件地址即可。

> [!NOTE]
> [小組名稱] 是檢閱小組的易記名稱。 這是顯示在 Azure 入口網站中的名稱。 [小組識別碼] 是用來以程式設計方式識別檢閱小組的項目。

> [!div class="mx-imgBorder"]
> ![邀請小組成員](images/create-team.png)

如果您選擇使用客戶管理的金鑰 (CMK) 來加密資料，則系統會針對 E0 定價層中的內容仲裁資源提示您輸入 [資源識別碼]。 您提供的資源必須是新的。 

> [!div class="mx-imgBorder"]
> ![使用 CMK 邀請小組成員](images/create-team-cmk.png)

如果您嘗試重複使用內容仲裁資源，您會看到下列警告： 

> [!div class="mx-imgBorder"]
> ![CMK 失敗](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>上傳範例內容

現在，您已做好上傳範例內容的準備。 選取 [試用] > [影像]、[試用] > [文字] 或 [試用] > [影片]。

![嘗試影像或文字審核](images/tryimagesortext.png)

提交您的內容以供仲裁。 就內部而言，審核工具會呼叫仲裁 API 來掃描您的內容。 掃描完成之後，您會看到一則訊息，通知您有結果等待您的審核。

![審核檔案](images/submitted.png)

## <a name="review-moderation-tags"></a>審核仲裁標記

審核已套用的仲裁標記。 您可以查看您的內容已套用哪些標記，以及每個類別的分數。 請參閱[影像](image-moderation-api.md)、[文字](text-moderation-api.md)和[影片](video-moderation-api.md)仲裁主題，來深入了解不同的內容標記表示的意義。

![檢閱結果](images/reviewresults_text.png)

在專案中，您或審核小組可以視需要變更這些標記或新增更多標記。 您將透過 [下一步] 按鈕來提交這些變更。 當您的商務應用程式呼叫 Moderator API 時，已加上標記的內容會在這裡排入佇列，準備供人工審核小組進行審核。 您可以使用此方法快速審核大量內容。

到目前為止，您已使用「Content Moderator 審核工具」來查看 Content Moderator 服務的功能範例。 接著，您可以深入了解審核工具及如何使用「審核 API」將其整合至軟體專案，或是跳到[後續步驟](#next-steps)一節，以了解如何在您的應用程式中使用「仲裁 API」本身。

## <a name="learn-more-about-the-review-tool"></a>深入了解審核工具

若要深入了解如何使用「Content Moderator 審核工具」，請參閱[審核工具](Review-Tool-User-Guide/human-in-the-loop.md)指南，以及參閱「審核工具 API」以了解如何微調人工審核體驗：
- [作業 API](try-review-api-job.md) 會使用審核 API 掃描您的內容，並在檢閱工具中產生檢閱。 
- [檢閱 API](try-review-api-review.md) 會直接為人工審核者建立影像、文字或影片檢閱，無須先掃描內容。 
- [工作流程 API](try-review-api-workflow.md) 會建立、更新並取得您小組建立的自訂工作流程詳細資料。

或者，繼續進行後續步驟，以開始在您的程式碼中使用「仲裁 API」。

## <a name="next-steps"></a>後續步驟

了解如何在您的應用程式中使用「仲裁 API」本身。
- 實作影像仲裁。 使用 [API 主控台](try-image-api.md)或依照 [.NET SDK 快速入門](dotnet-sdk-quickstart.md)的指示掃描影像，並使用標籤、信賴分數和其他擷取資訊來偵測潛在的成人和猥褻內容。
- 實作文字仲裁。 使用 [API 主控台](try-text-api.md)或使用 [.NET SDK 快速入門](dotnet-sdk-quickstart.md)來掃描文字內容，以找出潛在的粗話、機器輔助的不必要文字分類 (預覽)，以及個人資料。
- 實作影片仲裁。 請遵循[適用於 C# 的影片仲裁操作指南](video-moderation-api.md)以掃描影片並偵測潛在成人和不雅內容。 
