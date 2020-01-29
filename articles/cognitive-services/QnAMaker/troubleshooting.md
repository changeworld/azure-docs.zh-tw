---
title: 疑難排解-QnA Maker
titleSuffix: Azure Cognitive Services
description: 有關 QnA Maker 服務的常見問題策劃清單，可協助您更快速地採用服務，並獲得更好的結果。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/23/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e56722831ef05364b8ee4d342553158fba19ffaa
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760720"
---
# <a name="troubleshooting-for-qna-maker"></a>疑難排解 QnA Maker

有關 QnA Maker 服務的常見問題策劃清單，可協助您更快速地採用服務，並獲得更好的結果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>如何取得 QnAMaker 服務端點

當您聯繫 QnAMaker 支援或 UserVoice 時，QnAMaker 服務端點適用于偵錯工具。 端點是下列格式的 URL： https://your-resource-name.azurewebsites.net 。

1. 在 [Azure 入口網站](https://portal.azure.com)中移至 QnAMaker 服務 (資源群組)

    ![Azure 入口網站中的 QnAMaker Azure 資源群組](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取與 QnA Maker 資源相關聯的 App Service。 一般來說，名稱是相同的。

     ![選取 QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. [總覽] 區段提供端點 URL

    ![QnAMaker 端點](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)


## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>使用 QnA Maker 入口網站中的說明 bot

QnA Maker 會在 QnA Maker 入口網站中提供**協助**bot，協助您。 您可以在每個網頁上取得說明 bot。 Bot 會使用 QnA Maker 來提供解答，並提供[ C# bot Framework 程式碼專案](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)給 bot，讓您可以快速啟動並執行自己的回應 bot。

![![QnA Maker 在 QnA Maker 入口網站中提供可協助您的 * * 說明 * * bot。](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>管理知識庫

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>我不小心刪除了一部分的 QnA Maker，該怎麼辦？

請勿刪除與 QnA Maker 資源一起建立的任何 Azure 服務，例如搜尋或 Web 應用程式。 這些是 QnA Maker 運作的必要項，如果您刪除一個，QnA Maker 將會停止正常運作。

所有刪除都是永久的，包括問答組、檔案、URL、自訂問答、知識庫或 Azure 資源。 務必先從 [設定] 頁面匯出知識庫，再刪除知識庫的任何部分。

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>為何我的 URL/檔案不會擷取問答組？

有可能是因為 QnA Maker 無法從有效的常見問題集 URL 自動擷取某些問答 (QnA) 內容。 在這種情況下，您可以將 QnA 內容貼到 .txt 檔案中，並確認工具是否可加以擷取。 或者，您也可以透過 [QnA Maker 入口網站](https://qnamaker.ai) \(英文\) 以編輯方式將內容新增至您的知識庫。

### <a name="how-large-a-knowledge-base-can-i-create"></a>我可建立多大的知識庫？

知識庫的大小取決於您在建立 QnA Maker 服務時所選擇的 Azure 搜尋服務 SKU。 如需詳細資訊，請參閱[這裡](./Tutorials/choosing-capacity-qnamaker-deployment.md)。

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>當我嘗試建立新的知識庫時，為何我在下拉式清單中未看到任何項目？

您尚未在 Azure 中建立任何 QnA Maker 服務。 請參閱[這裡](./How-To/set-up-qnamaker-service-azure.md)以了解如何這麼做。

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>如何與他人共用知識庫？

共用會在 QnA Maker 服務層級上運作，也就是說，將會共用服務中的所有知識庫。 請參閱[這裡](./How-To/collaborate-knowledge-base.md)以了解如何在知識庫上共同作業。

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>是否可以與不屬於相同 AAD 租用戶的參與者共用知識庫，以修改該知識庫？

共用是以 Azure 角色型存取控制 (RBAC) 為基礎。 如果您可以與另一個使用者共用 Azure 中的「任何」資源，便代表您也可以共用 QnA Maker。

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>假設我有有 5 個 QnAMaker 知識庫的 App Service 方案。 是否能將讀取/寫入權限指派給 5 個不同的使用者，使他們只能個別存取 1 個 QnAMaker 知識庫？

您可以共用整個 QnAMaker 服務，但不能共用個別的知識庫。

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>如何變更找不到適當相符項目時的預設訊息？

預設訊息是您 App Service 中的部分設定。
- 在 Azure 入口網站中，移至您的 App Service 資源

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 按一下 [設定] 選項

![qnamaker appservice 設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 變更 **DefaultAnswer** 設定的值
- 重新啟動 App Service

![qnamaker appservice 重新啟動](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>為什麼我的 SharePoint 連結無法供擷取？

如需詳細資訊，請參閱[資料來源位置](./Concepts/data-sources-supported.md#data-source-locations)。

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>我對知識庫所做的更新並未在發佈時反映。 為何會這樣呢？

無論是資料表更新、測試或設定，每個編輯作業都必須先儲存才能加以發佈。 每次編輯作業之後，請務必按一下 [**儲存並定型**] 按鈕。

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>知識庫是否支援豐富的資料或多媒體？

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>檔案和 Url 的多媒體自動解壓縮

* URL-有限的 HTML 對 Markdown 轉換功能。
* 檔案-不支援

#### <a name="answer-text-in-markdown"></a>在 markdown 中回答文字
一旦 QnA 集位於知識庫中，您就可以編輯答案的 markdown 文字，以包含可從公用 Url 取得之媒體的連結。

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker 是否支援非英文的語言？

請參閱更多關於[支援的語言](./Overview/languages-supported.md)的詳細資料。

如果您有多種語言的內容，請務必為每一種語言建立個別的服務。

## <a name="manage-service"></a>管理服務

### <a name="when-should-i-restart-my-app-service"></a>何時應該重新啟動我的應用程式服務？

當 [**使用者設定**][頁面](https://www.qnamaker.ai/UserSettings)上 [**端點索引鍵**] 資料表中的 [知識庫版本] 值旁有警告時，請重新整理您的應用程式服務。

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>我刪除了現有的搜尋服務。 我該怎麼辦？

如果您刪除 Azure 認知搜尋索引，此作業會是最終的，而且無法復原索引。

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>我在搜尋服務中刪除了我的 `testkb` 索引。 我該怎麼辦？

您的舊資料無法復原。 建立新的 QnA Maker 資源，然後再次建立您的知識庫。

### <a name="when-should-i-refresh-my-endpoint-keys"></a>何時應重新整理端點金鑰？

若您懷疑端點金鑰已遭入侵，請加以重新整理。

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>我可以使用相同的 Azure 認知搜尋資源來取得使用多種語言的知識庫嗎？

若要使用多種語言和多個知識庫，使用者必須針對每個語言建立 QnA Maker 資源。 這會為每個語言建立個別的 Azure 搜尋服務。 在單一 Azure 搜尋服務中混用不同的語言知識庫，將會使結果的相關性降低。

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>如何變更 QnA Maker 所使用的 Azure 認知搜尋資源名稱？

Azure 認知搜尋資源的名稱是 QnA Maker 資源名稱，並在結尾附加一些隨機字母。 這讓您難以區別 QnA Maker 的多個搜尋服務資源。 建立個別的搜尋服務（以您想要的方式命名），並將它連線到您的 QnA 服務。 這些步驟類似于[升級 Azure 搜尋](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)服務所需執行的步驟。

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>當 QnA Maker 傳回時 `Runtime core is not initialized,` 如何修正此問題？

您的 app service 的磁碟空間可能已滿。 修復磁碟空間的步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中，選取您 QnA Maker 的 App service，然後停止服務。
1. 在仍在 App service 的情況下，依序選取 [**開發工具**]、[ **Advanced tools**] 和 [ **Go**]。 這會開啟新的瀏覽器視窗。
1. 依序選取 [ **Debug console**] 和 [ **CMD** ] 以開啟命令列工具。
1. 流覽至_site/wwwroot/Data/QnAMaker/_ 目錄。
1. 移除名稱開頭為 `rd`的所有資料夾。

    **請勿刪除**下列各項：

    * KbIdToRankerMappings .txt 檔案
    * EndpointSettings json 檔案
    * EndpointKeys 資料夾

1. 啟動 App service。
1. 存取您的知識庫，以確認它現在可以運作。


## <a name="integrate-with-other-services-including-bots"></a>與其他服務 (包括 Bot) 整合

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>我是否需要使用 Bot Framework 才能使用 QnA Maker？

否，您不需要使用[Bot Framework](https://github.com/Microsoft/botbuilder-dotnet)搭配 QnA Maker。 不過，QnA Maker 是以 [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)中的數個範本之一的形式提供。 Bot Service 可透過 Microsoft Bot Framework 快速開發智慧型 Bot，並且可在無伺服器環境中執行。

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>如何使用 QnA Maker 建立新的 bot？

請遵循[這份](./Tutorials/create-qna-bot.md)文件中的指示，以使用 Azure Bot 服務建立您的 Bot。

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>如何? 搭配現有的 Azure bot 服務使用不同的知識庫？

您需要有下列有關知識庫的資訊：

* 知識庫識別碼。
* 在您發佈之後，知識庫的已發佈端點自訂子功能變數名稱稱（稱為 `host`）會在 [**設定**] 頁面上找到。
* 知識庫的已發佈端點金鑰-發行後，在 [**設定**] 頁面上找到。

透過這項資訊，請移至 Azure 入口網站中 bot 的 app service。 在 設定 底下 > 設定 下 **> 應用程式設定**，變更這些值。

在 ABS 服務中，知識庫的端點金鑰會標示為 `QnAAuthkey`。

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>有兩個或多個用戶端應用程式可以共用知識庫嗎？

是，您可以從任意數目的用戶端查詢知識庫。 如果知識庫的回應似乎緩慢或超時，請考慮升級與知識庫相關聯之 app service 的服務層級。

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>我該如何在我的網站中內嵌 QnA Maker 服務？

依照以下步驟，將 QnA Maker 服務作為網站聊天控制項內嵌至您的網站中：

1. 依照[這裡](./Tutorials/create-qna-bot.md)的指示建立您的常見問題集 Bot。
2. 依照[這裡](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步驟啟用網路聊天

## <a name="data-storage"></a>資料儲存體

### <a name="what-data-is-stored-and-where-is-it-stored"></a>會儲存哪些資料以及儲存在哪裡？

當您建立 QnA Maker 服務時，您選取了 Azure 區域。 您的知識庫和記錄檔會儲存在此區域中。
