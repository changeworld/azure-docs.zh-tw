---
title: 故障排除 - QnA 製造商
description: 有關 QnA Maker 服務的最常見問題清單將説明您更快地採用該服務，並取得更好的結果。
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: e002efe74bf7bcd3d944b01b0a25a731a2db3f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284257"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA 製造商的故障排除

有關 QnA Maker 服務的最常見問題清單將説明您更快地採用該服務，並取得更好的結果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>管理預測

<details>
<summary><b>如何提高查詢預測的輸送量性能？</b></summary>

**答**：輸送量性能問題表明您需要擴展應用服務和認知搜索。 請考慮向認知搜索添加副本以提高性能。

瞭解有關[定價層的更多。](Concepts/azure-resources.md)
</details>

<details>
<summary><b>如何獲取 QnAMaker 服務終結點</b></summary>

**答**：當您聯繫 QnAMaker 支援或使用者語音時，QnAMaker 服務終結點可用於調試目的。 終結點是此表單中的 URL： https://your-resource-name.azurewebsites.net。

1. 在 [Azure 入口網站](https://portal.azure.com)中移至 QnAMaker 服務 (資源群組)

    ![Azure 入口網站中的 QnAMaker Azure 資源群組](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 選擇與 QnA 製造商資源關聯的應用服務。 通常，名稱相同。

     ![選取 QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 終結點 URL 在"概述"部分提供

    ![QnAMaker 端點](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>管理知識庫

<details>
<summary><b>我不小心刪除了一部分的 QnA Maker，該怎麼辦？</b></summary>

**答**：不要刪除與 QnA Maker 資源（如搜索或 Web 應用）一起創建的任何 Azure 服務。 這些是 QnA 製造商工作所必需的，如果您刪除一個，QnA 製造商將停止正常工作。

所有刪除都是永久的，包括問答組、檔案、URL、自訂問答、知識庫或 Azure 資源。 務必先從 [設定]**** 頁面匯出知識庫，再刪除知識庫的任何部分。

</details>

<details>
<summary><b>為何我的 URL/檔案不會擷取問答組？</b></summary>

**答**：QnA Maker 可能無法自動從有效的常見問題解答 URL 中提取一些問答 （QnA） 內容。 在這種情況下，您可以將 QnA 內容貼到 .txt 檔案中，並確認工具是否可加以擷取。 或者，您也可以透過 [QnA Maker 入口網站](https://qnamaker.ai) \(英文\) 以編輯方式將內容新增至您的知識庫。

</details>

<details>
<summary><b>我可建立多大的知識庫？</b></summary>

**答**：知識庫的大小取決於您在創建 QnA Maker 服務時選擇的 Azure 搜索 SKU。 如需詳細資訊，請參閱[這裡](./Tutorials/choosing-capacity-qnamaker-deployment.md)。

</details>

<details>
<summary><b>當我嘗試建立新的知識庫時，為何我在下拉式清單中未看到任何項目？</b></summary>

**答**：您尚未在 Azure 中創建任何 QnA Maker 服務。 請參閱[這裡](./How-To/set-up-qnamaker-service-azure.md)以了解如何這麼做。

</details>

<details>
<summary><b>如何與他人共用知識庫？</b></summary>

**答**：在 QnA Maker 服務等級共用工作，即服務中的所有知識庫都將共用。 請參閱[這裡](./How-To/collaborate-knowledge-base.md)以了解如何在知識庫上共同作業。

</details>

<details>
<summary><b>是否可以與不屬於相同 AAD 租用戶的參與者共用知識庫，以修改該知識庫？</b></summary>

**答**：共用基於 Azure 基於角色的存取控制 （RBAC）。 如果您可以與另一個使用者共用 Azure 中的「任何」__ 資源，便代表您也可以共用 QnA Maker。

</details>

<details>
<summary><b>如果您有一個應用服務方案，有 5 個 QnAMaker 知識庫。您能否為 5 個不同的使用者分配讀取/寫入權限，以便每個使用者只能訪問 1 個 QnAMaker 知識庫？</b></summary>

**答**：您可以共用整個 QnAMaker 服務，而不是單個知識庫。

</details>

<details>
<summary><b>如何變更找不到適當相符項目時的預設訊息？</b></summary>

**答**：預設消息是應用服務中設置的一部分。
- 在 Azure 入口網站中，移至您的 App Service 資源

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 按一下 [設定]**** 選項

![qnamaker appservice 設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 變更 **DefaultAnswer** 設定的值
- 重新啟動 App Service

![qnamaker appservice 重新啟動](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>為什麼我的 SharePoint 連結無法供擷取？</b></summary>

**答案**： 有關詳細資訊[，請參閱資料來源位置](./Concepts/knowledge-base.md#data-source-locations)。

</details>

<details>
<summary><b>我對知識庫所做的更新不會反映在發佈上。為什麼不呢？</b></summary>

**答**：每次編輯操作（無論是在表更新、測試還是設置中）都需要保存才能發佈。 請務必在每次編輯操作後按一下"**保存"和"訓練"** 按鈕。

</details>

<details>
<summary><b>知識庫是否支援豐富的資料或多媒體？</b></summary>

**答案**：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>檔和 URL 的多媒體自動提取

* URLS - 有限的 HTML 到標記轉換功能。
* 檔 - 不支援

#### <a name="answer-text-in-markdown"></a>以標記形式回答文本
一旦 QnA 集位於知識庫中，您可以編輯答案的標記文本，以包括公共 URL 中可用的媒體連結。


</details>

<details>
<summary><b>QnA Maker 是否支援非英文的語言？</b></summary>

**答**：查看有關[受支援語言](./Overview/languages-supported.md)的更多詳細資訊。

如果您有多種語言的內容，請務必為每一種語言建立個別的服務。

</details>

## <a name="manage-service"></a>管理服務

<details>
<summary><b>何時應該重新啟動我的應用程式服務？</b></summary>

**答**：當警告圖示位於 **"使用者設置"**[頁上](https://www.qnamaker.ai/UserSettings)**的"終結點鍵**"表中的知識庫的版本值旁邊時，請刷新應用服務。

</details>

<details>
<summary><b>我刪除了現有的搜索服務。如何解決此問題？</b></summary>

**答**：如果刪除 Azure 認知搜索索引，則操作為最終操作，無法恢復該索引。

</details>

<details>
<summary><b>我在搜索服務中刪除`testkb`了索引。如何解決此問題？</b></summary>

**答**：無法恢復您的舊資料。 創建新的 QnA Maker 資源，然後再次創建您的知識庫。

</details>

<details>
<summary><b>何時應重新整理端點金鑰？</b></summary>

**答**：如果您懷疑終結點金鑰已洩露，請刷新它們。

</details>

<details>
<summary><b>是否可以使用多種語言對知識庫使用相同的 Azure 認知搜索資源？</b></summary>

**答**：要使用多種語言和多種知識庫，使用者必須為每個語言創建 QnA Maker 資源。 這將創建單獨的 Azure 搜索服務每種語言。 在單一 Azure 搜尋服務中混用不同的語言知識庫，將會使結果的相關性降低。

</details>

<details>
<summary><b>如何更改 QnA Maker 使用的 Azure 認知搜索資源的名稱？</b></summary>

**答**：Azure 認知搜索資源的名稱是 QnA Maker 資源名稱，末尾附加了一些隨機字母。 這讓您難以區別 QnA Maker 的多個搜尋服務資源。 創建單獨的搜索服務（按您希望的方式命名該服務），並將其連接到 QnA 服務。 這些步驟與[升級 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)所需的步驟類似。

</details>

<details>
<summary><b>當 QnA`Runtime core is not initialized,`製造商返回時，如何修復它？</b></summary>

**答**：應用服務的磁碟空間可能已滿。 修復磁碟空間的步驟：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇 QnA Maker 的應用服務，然後停止該服務。
1. 在應用服務上時，選擇**開發工具**，然後選擇**高級工具**，然後**轉到**。 這將打開一個新的瀏覽器視窗。
1. 選擇**調試主控台**，然後**選擇 CMD**以打開命令列工具。
1. 導航到_網站/wwwroot/資料/QnAMaker/_ 目錄。
1. 刪除其名稱以`rd`開頭的所有資料夾。

    **不要刪除**以下內容：

    * KbIdToRanker 映射.txt 檔
    * 端點設置.json 檔
    * 終結點鍵資料夾

1. 啟動應用服務。
1. 訪問您的知識庫以驗證它現在是否有效。

</details>

## <a name="integrate-with-other-services-including-bots"></a>與其他服務 (包括 Bot) 整合

<details>
<summary><b>我是否需要使用 Bot Framework 才能使用 QnA Maker？</b></summary>

**答**：不，您不需要將[機器人框架](https://github.com/Microsoft/botbuilder-dotnet)與 QnA 製造商一起使用。 但是，QnA Maker 是 [Azure 機器人服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)中的多個範本之一。 Bot Service 可透過 Microsoft Bot Framework 快速開發智慧型 Bot，並且可在無伺服器環境中執行。

</details>

<details>
<summary><b>如何使用 QnA Maker 創建新的機器人？</b></summary>

**答**：按照[本文檔](./Quickstarts/create-publish-knowledge-base.md)中的說明使用 Azure 機器人服務創建自動程式。

</details>

<details>
<summary><b>如何使用現有 Azure 自動程式服務使用不同的知識庫？</b></summary>

**答**：您需要獲得以下有關知識庫的資訊：

* 知識庫 ID。
* 知識庫的已發佈終結點自訂子功能變數名稱（稱為`host`）在發佈後在 **"設置"** 頁上找到。
* 知識庫的已發佈終結點金鑰 - 發佈後在 **"設置"** 頁上找到。

使用此資訊，請轉到 Azure 門戶中的自動程式應用服務。 在**設置 -> 配置 -> 應用程式設定下**，更改這些值。

知識庫的終結點金鑰在 ABS 服務`QnAAuthkey`中標記。

</details>

<details>
<summary><b>兩個或多個用戶端應用程式可以共用知識庫嗎？</b></summary>

**答**：是的，可以從任意數量的用戶端查詢知識庫。 如果來自知識庫的回應似乎很慢或超時，請考慮升級與知識庫關聯的應用服務的服務層。

</details>

<details>
<summary><b>我該如何在我的網站中內嵌 QnA Maker 服務？</b></summary>

**答**：按照以下步驟將 QnA Maker 服務嵌入到您的網站中，作為網路聊天控制項：

1. 依照[這裡](./Quickstarts/create-publish-knowledge-base.md)的指示建立您的常見問題集 Bot。
2. 依照[這裡](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步驟啟用網路聊天

</details>

## <a name="data-storage"></a>資料儲存體

<details>
<summary><b>會儲存哪些資料以及儲存在哪裡？</b></summary>

**答案**：

當您建立 QnA Maker 服務時，您選取了 Azure 區域。 您的知識庫和記錄檔會儲存在此區域中。

</details>