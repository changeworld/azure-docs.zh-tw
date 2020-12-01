---
title: 疑難排解-QnA Maker
description: 關於 QnA Maker 服務的最常見問題策劃清單，可協助您更快速地採用此服務，並獲得更好的結果。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: e8b1d985fcb2852df52382e005ec0f0266e23d9d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345639"
---
# <a name="troubleshooting-for-qna-maker"></a>針對 QnA Maker 進行疑難排解

關於 QnA Maker 服務的最常見問題策劃清單，可協助您更快速地採用此服務，並獲得更好的結果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>管理預測

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

<details>
<summary><b>如何改善查詢預測的輸送量效能？</b></summary>

**答**：輸送量效能問題指出您需要針對您的 App Service 和認知搜尋進行擴大。 請考慮將複本新增至認知搜尋，以改善效能。

深入瞭解 [定價層](Concepts/azure-resources.md)。
</details>

<details>
<summary><b>如何取得 QnAMaker 服務端點</b></summary>

**答**：當您聯絡 QnAMaker 支援或 UserVoice 時，QnAMaker 服務端點適用于偵錯工具的用途。 端點是下列格式的 URL： `https://your-resource-name.azurewebsites.net` 。

1. 在 [Azure 入口網站](https://portal.azure.com)中移至 QnAMaker 服務 (資源群組)

    ![Azure 入口網站中的 QnAMaker Azure 資源群組](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選取與 QnA Maker 資源相關聯的 App Service。 一般而言，名稱相同。

     ![選取 QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 端點 URL 可在 [總覽] 區段中取得

    ![QnAMaker 端點](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

<details>
<summary><b>如何改善查詢預測的輸送量效能？</b></summary>

**答**：輸送量效能問題指出您需要擴大認知搜尋。 請考慮將複本新增至認知搜尋，以改善效能。

深入瞭解 [定價層](Concepts/azure-resources.md)。
</details>

---

## <a name="manage-the-knowledge-base"></a>管理知識庫

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

<details>
<summary><b>我不小心刪除了一部分的 QnA Maker，該怎麼辦？</b></summary>

**答**：請勿刪除隨 QnA Maker 資源（例如搜尋或 Web 應用程式）建立的任何 Azure 服務。 這些是 QnA Maker 運作的必要項，如果您刪除其中一個，QnA Maker 將會停止正常運作。

所有刪除都是永久的，包括問答組、檔案、URL、自訂問答、知識庫或 Azure 資源。 務必先從 [設定] 頁面匯出知識庫，再刪除知識庫的任何部分。

</details>

<details>
<summary><b>為何我的 URL/檔案不會擷取問答組？</b></summary>

**答**： QnA Maker 無法從有效的常見問題 url 自動解壓縮某些問題和解答 (QnA) 內容。 在這種情況下，您可以將 QnA 內容貼到 .txt 檔案中，並確認工具是否可加以擷取。 或者，您也可以透過 [QnA Maker 入口網站](https://qnamaker.ai) \(英文\) 以編輯方式將內容新增至您的知識庫。

</details>

<details>
<summary><b>我可建立多大的知識庫？</b></summary>

**答**：知識庫的大小取決於您在建立 QnA Maker 服務時所選擇的 Azure 搜尋服務 SKU。 如需詳細資訊，請參閱[這裡](./concepts/azure-resources.md)。

</details>

<details>
<summary><b>當我嘗試建立新的知識庫時，為何我在下拉式清單中未看到任何項目？</b></summary>

**答**：您還沒有在 Azure 中建立任何 QnA Maker 服務。 請參閱[這裡](./How-To/set-up-qnamaker-service-azure.md)以了解如何這麼做。

</details>

<details>
<summary><b>如何與他人共用知識庫？</b></summary>

**答**：共用在 QnA Maker 服務層級上運作，也就是說，服務中的所有知識庫都將會共用。 請參閱[這裡](./index.yml)以了解如何在知識庫上共同作業。

</details>

<details>
<summary><b>是否可以與不屬於相同 AAD 租用戶的參與者共用知識庫，以修改該知識庫？</b></summary>

**答**：共用以 azure 角色型存取控制為基礎， (azure RBAC) 。 如果您可以與另一個使用者共用 Azure 中的「任何」資源，便代表您也可以共用 QnA Maker。

</details>

<details>
<summary><b>如果您有具有5個 QnAMaker 知識庫的 App Service 方案。您可以將讀取/寫入權限指派給5個不同的使用者，讓每一位使用者只能存取1個 QnAMaker 的知識庫嗎？</b></summary>

**答**：您可以共用整個 QnAMaker 服務，而不是個別的知識庫。

</details>

<details>
<summary><b>如何變更找不到適當相符項目時的預設訊息？</b></summary>

**答**：預設訊息是 App service 中設定的一部分。
- 在 Azure 入口網站中，移至您的 App Service 資源

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 按一下 [設定] 選項

![qnamaker appservice 設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 變更 **DefaultAnswer** 設定的值
- 重新啟動 App Service

![qnamaker appservice 重新啟動](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>為什麼我的 SharePoint 連結無法供擷取？</b></summary>

**答**：如需詳細資訊，請參閱 [資料來源位置](./concepts/data-sources-and-content.md#data-source-locations) 。

</details>

<details>
<summary><b>我對知識庫所做的更新不會在發行時反映出來。為什麼不呢？</b></summary>

**答**：每個編輯作業（不論是在資料表更新、測試或設定中）都必須先儲存才能發行。 在每次編輯作業之後，請務必按一下 [ **儲存並定型** ] 按鈕。

</details>

<details>
<summary><b>知識庫是否支援豐富的資料或多媒體？</b></summary>

**答案**：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>檔案和 Url 的多媒體自動解壓縮

* URL-有限的 HTML 至 Markdown 轉換功能。
* 檔案-不支援

#### <a name="answer-text-in-markdown"></a>回答 markdown 中的文字
一旦 QnA 組位於知識庫中，您就可以編輯答案的 markdown 文字，以包含可從公用 Url 取得媒體的連結。


</details>

<details>
<summary><b>QnA Maker 是否支援非英文的語言？</b></summary>

**答**：請參閱 [支援語言](./overview/language-support.md)的詳細資料。

如果您有多種語言的內容，請務必為每一種語言建立個別的服務。

</details>

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

<details>
<summary><b>為何我的 URL/檔案不會擷取問答組？</b></summary>

**答**： QnA Maker 無法從有效的常見問題 url 自動解壓縮某些問題和解答 (QnA) 內容。 在這種情況下，您可以將 QnA 內容貼到 .txt 檔案中，並確認工具是否可加以擷取。 或者，您也可以透過 [QnA Maker 入口網站](https://qnamaker.ai) \(英文\) 以編輯方式將內容新增至您的知識庫。

</details>

<details>
<summary><b>我可建立多大的知識庫？</b></summary>

**答**：知識庫的大小取決於您在建立 QnA Maker 服務時所選擇的 Azure 搜尋服務 SKU。 如需詳細資訊，請參閱[這裡](./concepts/azure-resources.md)。

</details>

<details>
<summary><b>當我嘗試建立新的知識庫時，為何我在下拉式清單中未看到任何項目？</b></summary>

**答**：您還沒有在 Azure 中建立任何 QnA Maker 服務。 請參閱[這裡](./How-To/set-up-qnamaker-service-azure.md)以了解如何這麼做。

</details>

<details>
<summary><b>如何與他人共用知識庫？</b></summary>

**答**：共用在 QnA Maker 服務層級上運作，也就是說，服務中的所有知識庫都將會共用。 請參閱[這裡](./index.yml)以了解如何在知識庫上共同作業。

</details>

<details>
<summary><b>您是否可以與不在相同 Azure Active Directory 租使用者中的參與者共用知識庫，以修改知識庫？</b></summary>

**答**：共用以 azure 角色型存取控制為基礎， (azure RBAC) 。 如果您可以與另一個使用者共用 Azure 中的「任何」資源，便代表您也可以共用 QnA Maker。

</details>

<details>
<summary><b>是否能將讀取/寫入權限指派給 5 個不同的使用者，使他們只能個別存取 1 個 QnAMaker 知識庫？</b></summary>

**答**：您可以共用整個 QnAMaker 服務，而不是個別的知識庫。

</details>

<details>
<summary><b>為什麼我的 SharePoint 連結無法供擷取？</b></summary>

**答**：如需詳細資訊，請參閱 [資料來源位置](./concepts/data-sources-and-content.md#data-source-locations) 。

</details>

<details>
<summary><b>我對知識庫所做的更新不會在發行時反映出來。為什麼不呢？</b></summary>

**答**：每個編輯作業（不論是在資料表更新、測試或設定中）都必須先儲存才能發行。 在每次編輯作業之後，請務必按一下 [ **儲存並定型** ] 按鈕。

</details>

<details>
<summary><b>知識庫是否支援豐富的資料或多媒體？</b></summary>

**答案**：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>檔案和 Url 的多媒體自動解壓縮

* URL-有限的 HTML 至 Markdown 轉換功能。
* 檔案-不支援

#### <a name="answer-text-in-markdown"></a>回答 markdown 中的文字
一旦 QnA 組位於知識庫中，您就可以編輯答案的 markdown 文字，以包含可從公用 Url 取得媒體的連結。


</details>

<details>
<summary><b>QnA Maker 是否支援非英文的語言？</b></summary>

**答**：請參閱 [支援語言](./overview/language-support.md)的詳細資料。

如果您有多種語言的內容，請務必為每一種語言建立個別的服務。

</details>

---

## <a name="manage-service"></a>管理服務

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

<details>
<summary><b>何時應該重新啟動我的應用程式服務？</b></summary>

**答**：請在 [**使用者設定**][頁面](https://www.qnamaker.ai/UserSettings)上的 [**端點索引鍵**] 資料表中，于知識庫的版本值旁邊，重新整理您的 app service。

</details>

<details>
<summary><b>我刪除了現有的搜尋服務。如何修正此問題？</b></summary>

**答**：如果您刪除 Azure 認知搜尋索引，此作業為最終的，且無法復原索引。

</details>

<details>
<summary><b>我已刪除 `testkb` 搜尋服務中的索引。如何修正此問題？</b></summary>

**答**：您的舊資料無法復原。 建立新的 QnA Maker 資源，然後再次建立您的知識庫。

</details>

<details>
<summary><b>何時應重新整理端點金鑰？</b></summary>

**答**：如果您懷疑端點金鑰已遭入侵，請加以重新整理。

</details>

<details>
<summary><b>我可以使用相同的 Azure 認知搜尋資源來取得使用多種語言的知識庫嗎？</b></summary>

**答**：若要使用多種語言和多個知識庫，使用者必須為每個語言建立 QnA Maker 資源。 這會為每個語言建立個別的 Azure 搜尋服務。 在單一 Azure 搜尋服務中混用不同的語言知識庫，將會使結果的相關性降低。

</details>

<details>
<summary><b>如何變更 QnA Maker 所使用的 Azure 認知搜尋資源名稱？</b></summary>

**答**： Azure 認知搜尋資源的名稱是 QnA Maker 資源名稱，並在結尾附加一些隨機字母。 這讓您難以區別 QnA Maker 的多個搜尋服務資源。 建立個別的搜尋服務 (以您想要) 的方式命名，並將其連線到 QnA 服務。 這些步驟類似于 [升級 Azure 搜尋](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)服務所需執行的步驟。

</details>

<details>
<summary><b>當 QnA Maker 傳回時 `Runtime core is not initialized,` ，如何修正此問題？</b></summary>

**答**：您的 app service 的磁碟空間可能已滿。 修正磁碟空間的步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 QnA Maker 的 App service，然後停止服務。
1. 在仍在 App service 上的情況下，依序選取 [ **開發工具**]、[ **Advanced tools**] 和 [ **Go**]。 這會開啟新的瀏覽器視窗。
1. 選取 [ **Debug 主控台**]，然後選取 [ **CMD** ] 以開啟命令列工具。
1. 流覽至 _site/wwwroot/Data/QnAMaker/_ 目錄。
1. 移除名稱開頭為的所有資料夾 `rd` 。

    **請勿刪除** 下列內容：

    * KbIdToRankerMappings.txt 檔案
    * 檔案上的 EndpointSettings.js
    * EndpointKeys 資料夾

1. 啟動 App service。
1. 請存取您的知識庫以確認它現在可以運作。

</details>

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)


<details>
<summary><b>我刪除了現有的搜尋服務。如何修正此問題？</b></summary>

**答**：如果您刪除 Azure 認知搜尋索引，此作業為最終的，且無法復原索引。

</details>

<details>
<summary><b>我已刪除 `testkb` 搜尋服務中的索引。如何修正此問題？</b></summary>

**答**：您的舊資料無法復原。 建立新的 QnA Maker 資源，然後再次建立您的知識庫。

</details>

<details>
<summary><b>我可以使用相同的 Azure 認知搜尋資源來取得使用多種語言的知識庫嗎？</b></summary>

**答**：若要使用多種語言和多個知識庫，使用者必須為每個語言建立 QnA Maker 資源。 這會為每個語言建立個別的 Azure 搜尋服務。 在單一 Azure 搜尋服務中混用不同的語言知識庫，將會使結果的相關性降低。

</details>

<details>
<summary><b>如何變更 QnA Maker 所使用的 Azure 認知搜尋資源名稱？</b></summary>

**答**： Azure 認知搜尋資源的名稱是 QnA Maker 資源名稱，並在結尾附加一些隨機字母。 這讓您難以區別 QnA Maker 的多個搜尋服務資源。 建立個別的搜尋服務 (以您想要) 的方式命名，並將其連線到 QnA 服務。 這些步驟類似于 [升級 Azure 搜尋](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)服務所需執行的步驟。

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>與其他服務 (包括 Bot) 整合

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

<details>
<summary><b>我是否需要使用 Bot Framework 才能使用 QnA Maker？</b></summary>

**答**：不，您不需要使用 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 搭配 QnA Maker。 不過，QnA Maker 會以 [Azure Bot 服務](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0)中的數個範本之一來提供。 Bot Service 可透過 Microsoft Bot Framework 快速開發智慧型 Bot，並且可在無伺服器環境中執行。

</details>

<details>
<summary><b>如何使用 QnA Maker 來建立新的 bot？</b></summary>

**答**：請依照 [此](./Quickstarts/create-publish-knowledge-base.md) 檔中的指示，使用 Azure bot 服務建立您的 Bot。

</details>

<details>
<summary><b>如何? 將不同的知識庫與現有的 Azure bot 服務搭配使用？</b></summary>

**答**：您需要具有知識庫的下列資訊：

* 知識庫識別碼。
* `host`發佈之後，在 [**設定**] 頁面上找到知識庫的已發佈端點自訂子功能變數名稱稱，稱為。
* 知識庫發佈的端點金鑰-在您發行之後，在 [ **設定** ] 頁面上找到。

使用這項資訊，移至您在 Azure 入口網站中的 bot app service。 在 [ **設定-> 設定-> 應用程式設定**] 下，變更這些值。

知識庫的端點索引鍵會標示 `QnAAuthkey` 在 ABS 服務中。

</details>

<details>
<summary><b>有兩個或多個用戶端應用程式可以共用知識庫嗎？</b></summary>

**答**：是，您可以從任意數目的用戶端查詢知識庫。 如果知識庫的回應似乎緩慢或超時，請考慮升級與知識庫相關聯之 app service 的服務層級。

</details>

<details>
<summary><b>我該如何在我的網站中內嵌 QnA Maker 服務？</b></summary>

**答**：請依照下列步驟，將 QnA Maker 服務內嵌為網站中的網路聊天控制項：

1. 依照[這裡](./Quickstarts/create-publish-knowledge-base.md)的指示建立您的常見問題集 Bot。
2. 依照[這裡](/azure/bot-service/bot-service-channel-connect-webchat)的步驟啟用網路聊天

</details>

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)


<details>
<summary><b>我是否需要使用 Bot Framework 才能使用 QnA Maker？</b></summary>

**答**：不，您不需要使用 [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 搭配 QnA Maker。 不過，QnA Maker 會以 [Azure Bot 服務](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0)中的數個範本之一來提供。 Bot Service 可透過 Microsoft Bot Framework 快速開發智慧型 Bot，並且可在無伺服器環境中執行。

</details>

<details>
<summary><b>如何使用 QnA Maker 來建立新的 bot？</b></summary>

**答**：請依照 [此](./Quickstarts/create-publish-knowledge-base.md) 檔中的指示，使用 Azure bot 服務建立您的 Bot。

</details>

<details>
<summary><b>如何? 將不同的知識庫與現有的 Azure bot 服務搭配使用？</b></summary>

**答**：您需要具有知識庫的下列資訊：

* 知識庫識別碼。
* `host`發佈之後，在 [**設定**] 頁面上找到知識庫的已發佈端點自訂子功能變數名稱稱，稱為。
* 知識庫發佈的端點金鑰-在您發行之後，在 [ **設定** ] 頁面上找到。

使用這項資訊，移至您在 Azure 入口網站中的 bot app service。 在 [ **設定-> 設定-> 應用程式設定**] 下，變更這些值。

知識庫的端點索引鍵會標示 `QnAAuthkey` 在 ABS 服務中。

</details>

<details>
<summary><b>有兩個或多個用戶端應用程式可以共用知識庫嗎？</b></summary>

**答**：是，您可以從任意數目的用戶端查詢知識庫。 如果知識庫的回應似乎緩慢或超時，請考慮升級與知識庫相關聯之 app service 的服務層級。

</details>

<details>
<summary><b>我該如何在我的網站中內嵌 QnA Maker 服務？</b></summary>

**答**：請依照下列步驟，將 QnA Maker 服務內嵌為網站中的網路聊天控制項：

1. 依照[這裡](./Quickstarts/create-publish-knowledge-base.md)的指示建立您的常見問題集 Bot。
2. 依照[這裡](/azure/bot-service/bot-service-channel-connect-webchat)的步驟啟用網路聊天

---

## <a name="data-storage"></a>資料儲存體

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

<details>
<summary><b>會儲存哪些資料以及儲存在哪裡？</b></summary>

**答案**：

當您建立 QnA Maker 服務時，您選取了 Azure 區域。 您的知識庫和記錄檔會儲存在此區域中。

</details>

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

<details>
<summary><b>會儲存哪些資料以及儲存在哪裡？</b></summary>

**答案**：

當您建立 QnA Maker 服務時，您選取了 Azure 區域。 您的知識庫和記錄檔會儲存在此區域中。

</details>

---