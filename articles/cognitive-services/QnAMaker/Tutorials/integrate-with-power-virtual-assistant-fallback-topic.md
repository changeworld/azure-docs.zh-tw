---
title: 教學課程：與 Power Virtual Agents 整合 - QnA Maker
description: 在本教學課程中，使用主動式學習來改善知識庫的品質。 在不移除或變更現有問題的情況下，進行審查、接受或拒絕、新增。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: 42b50fcf0df27ddbc3e587a7d8e038e4979935ae
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777417"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>教學課程：將知識庫新增至 Power Virtual Agents
建立並擴充 [Power Virtual Agents](https://powervirtualagents.microsoft.com/) Bot，以從您的知識庫提供解答。

在本教學課程中，您會了解如何：

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Power Virtual Agents Bot
> * 建立系統後援主題
> * 將 QnA Maker 當作動作新增至主題作為 Power Automate 流程
> * 建立 Power Automate 解決方案
> * 將 Power Automate 流程新增至解決方案
> * 發佈 Power Virtual Agents
> * 測試 Power Virtual Agents，並接收來自 QnA Maker 知識庫的解答

## <a name="integrate-an-agent-with-a-knowledge-base"></a>整合代理程式與知識庫

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) 可讓小組使用引導式的無程式碼圖形化介面，建立強大的 Bot。 您不需要資料科學家或開發人員。

在 Power Virtual Agents 中所建立的代理程式包含一系列主題 (主題領域)，以便藉由執行動作來回答使用者問題。 如果找不到解答，則系統後援可傳回答案。

設定代理程式，以將問題傳送至您的知識庫，作為主題動作的一部分或作為「系統後援」主題路徑的一部分。 兩者都使用動作來連線到您的知識庫並傳回解答。

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate 會連線到 `GenerateAnswer` 動作

若要將您的代理程式連線到您的知識庫，請使用 Power Automate 建立動作。 Power Automate 提供一個流程，以連線至 QnA Maker 的 `GenerateAnswer` API。

設計並儲存流程後，即可在 Power Automate 解決方案中使用。 在您的代理程式中使用該解決方案作為動作。

## <a name="connect-an-agent-to-your-knowledge-base"></a>將代理程式連線至知識庫

以下概要說明如何將 Power Virtual Agents 中的代理程式連線至 QnA Maker 中的知識庫。

* 在 [QnA Maker](https://www.qnamaker.ai/) 入口網站中：
    * 建置和發佈知識庫。
    * 複製知識庫詳細資料，包括識別碼、執行階段端點金鑰和執行階段端點主機。
* 在 [Power Virtual Agents](https://powerva.microsoft.com/) 入口網站中：
    * 建置代理程式主題。
    * 呼叫動作 (對 Power Automate 流程)。
* 在 [Power Automate](https://us.flow.microsoft.com/) 入口網站中：
    * 搜尋「使用 QnA Maker 產生答案」範本
    * 使用範本來設定流程，以使用 [QnA Maker 的 GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) \(英文\)。
        * QnA Maker 發佈的知識庫資訊：
            * 知識庫識別碼
            * QnA Maker 資源端點主機
            * QnA Maker 資源端點金鑰
        * 輸入 - 使用者查詢
        * 輸出 - 知識庫解答
    * 建立解決方案並新增流程，或將流程新增至現有的解決方案。
* 返回 Power Virtual Agents：
    * 選取解決方案的輸出作為主題的訊息。

## <a name="create-and-publish-a-knowledge-base"></a>建立和發佈知識庫

1. 依照[快速入門](../Quickstarts/create-publish-knowledge-base.md)建立知識庫。 請不要完成最後一節關於建立 Bot 的相關資訊。 相反地，請使用此教學課程來建立具有 Power Virtual Agents 的 Bot。

    > [!div class="mx-imgBorder"]
    > ![已發佈知識庫設定的螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    輸入在 [QnA Maker](https://www.qnamaker.ai/) 入口網站的 [設定] 頁面上找到的已發佈知識庫設定。 您需要此資訊，以便 [Power Automate 步驟](#create-a-power-automate-flow-to-connect-to-your-knowledge-base)設定您的 QnA Maker `GenerateAnswer` 連線。

1. 在 QnA Maker 入口網站的 [設定] 頁面上，尋找端點金鑰、端點主機和知識庫識別碼。

## <a name="create-an-agent-in-power-virtual-agents"></a>在 Power Virtual Agents 中建立代理程式

1. [登入 Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)。 使用您的學校或公司電子郵件帳戶。

1. 如果這是您的第一個 Bot，您應該位於代理程式的**首頁**。 如果這不是您的第一個 Bot，請從頁面右上方的區域選取 Bot，然後選取 [+ 新增 Bot]。

    > [!div class="mx-imgBorder"]
    > ![Power Virtual Agents 首頁的螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Bot 中提供的主題

代理程式會使用主題集合來回答您的主題領域中的問題。 在本教學課程中，代理程式有許多為您提供的主題，可分為使用者主題和系統主題。

從左側導覽區中選取 [主題]，以查看 Bot 提供的主題。

> [!div class="mx-imgBorder"]
> ![代理程式中提供之主題的螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>建立系統後援主題

雖然代理程式可以從任何主題連線到您的知識庫，但本教學課程使用「系統後援」主題。 代理程式在找不到解答時會使用後援主題。 代理程式會將使用者的文字傳遞至 QnA Maker 的 `GenerateAnswer` API、接收來自知識庫的解答，並以訊息的形式向使用者顯示。

1. 在 [Power Virtual Agents](https://powerva.microsoft.com/#/) 入口網站的右上角選取 [設定] (齒輪圖示)。 接著選取 [系統後援]。

    > [!div class="mx-imgBorder"]
    > ![系統後援的 Power Virtual Agents 功能表項目螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. 選取 [+ 新增]，以新增系統後援主題。

    > [!div class="mx-imgBorder"]
    > ![新增後援主題的螢幕擷取畫面。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. 新增主題後，選取 [移至後援主題]，以在撰寫畫布上撰寫後援主題。

    > [!TIP]
    > 如果您需要回到後援主題，您可以在 [系統] 主題的 [主題] 區段中取得該主題。

## <a name="use-the-authoring-canvas-to-add-an-action"></a>使用製作畫布來新增動作

使用 Power Virtual Agents 製作畫布，將後援主題連線到您的知識庫。 本主題以無法辨識的使用者文字開始。 新增動作，以將該文字傳遞至 QnA Maker，然後以訊息形式顯示解答。 本教學課程稍後會以[個別的步驟](#add-your-solutions-flow-to-power-virtual-agents)來處理顯示解答的最後一個步驟。

本節會建立後援主題交談流程。

1. 新的後援動作可能已經有交談流程元素。 選取 [選項] 功能表，以刪除 [呈報] 項目。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

1. 選取流至 [訊息] 方塊的 **+** 連接器，然後選取 [呼叫動作]。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

1. 選取 [建立流程]。 此流程會帶您前往 Power Automate 入口網站。

    > [!div class="mx-imgBorder"]
    > ![建立流程的螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power Automate 會開啟新的範本。 您將不會使用這個新範本。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>建立 Power Automate 流程以連線到您的知識庫

以下程序會建立 Power Automate 流程，以便：
* 接受傳入的使用者文字，並傳送至 QnA Maker。
* 傳回熱門回應給您的專員。

1. 在 **Power Automate** 中，從左側導覽區選取 [範本]。 如果系統詢問您是否要離開瀏覽器頁面，請接受 [離開]。

1. 在範本頁面上，搜尋**使用 QnA Maker 產生答案**範本，然後選取該範本。 此範本具有使用您的知識庫設定呼叫 QnA Maker 的所有步驟，而且會傳回最熱門的答案虛擬機器範本。

1. 在 QnA Maker 流程的新畫面上，選取 [繼續]。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

1. 選取 [產生答案] 動作方塊，然後填入來自先前標題為[建立和發佈知識庫](#create-and-publish-a-knowledge-base)之小節的 QnA Maker 設定。 下圖中的**服務主機**指的是您的知識庫主機 **Host**，其格式為 `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker`。


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

1. 選取 [儲存] 以儲存流程。

## <a name="create-a-solution-and-add-the-flow"></a>建立解決方案並新增流程

為了讓代理程式能夠尋找並連線至流程，流程必須包含在 Power Automate 解決方案中。

1. 仍在 Power Automate 入口網站中，從左側導覽選取 [解決方案]。

1. 選取 [+ 新增解決方案]。

1. 輸入顯示名稱。 解決方案清單包含貴組織或學校中的每個解決方案。 選擇可協助篩選出您解決方案的命名慣例。 例如，您可以在您的解決方案名稱前面加上電子郵件： `jondoe-power-virtual-agent-qnamaker-fallback`。

1. 從選項清單中選取您的發行者。

1. 接受名稱和版本的預設值。

1. 選取 [建立] 來完成程序。

## <a name="add-your-flow-to-the-solution"></a>將您的流程新增至解決方案

1. 在解決方案清單中，選取您剛建立的解決方案。 其應該位於清單頂端。 若非如此，請依您的電子郵件名稱 (這是解決方案名稱的一部分) 進行搜尋。

1. 在解決方案中，選取 [+ 新增現有]，然後從清單中選取 [流程]。

1. 從 [解決方案之外] 清單尋找您的流程，然後選取 [新增] 以完成程序。 如果有許多流程，請查看 [已修改] 資料行，以尋找最近的流程。

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>將解決方案的流程新增至 Power Virtual Agents

1. 返回含有 Power Virtual Agents 中您代理程式的瀏覽器索引標籤。 撰寫畫布應該仍然開啟。

1. 選取 [訊息] 動作方塊上方的 **+** 連接器，以在流程中插入新步驟。 然後選取 [呼叫動作]。

1. 從 [流程] 快顯視窗中，選取名為**使用 QnA Maker 知識庫產生答案 ...** 的新流程。新動作會出現在流程中。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

1. 若要正確地將輸入變數設定為 QnA Maker 動作，請選取 [選取變數]，然後選取 [bot.UnrecognizedTriggerPhrase]。

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::


1. 若要正確地將輸出變數設定為 QnA Maker 動作，請在 [訊息] 動作中選取 [UnrecognizedTriggerPhrase]，然後選取圖示以插入變數 `{x}`，然後選取 [FinalAnswer]。

1. 從內容工具列中選取 [儲存]，以儲存主題的製作畫布詳細資料。

最後代理程式畫布看起來會像這樣。

> [!div class="mx-imgBorder"]
> ![螢幕擷取畫面顯示最終代理程式畫布，其中具有 [觸發片語]、[動作]，以及 [訊息] 區段。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>測試代理程式

1. 在測試窗格中，切換 [在主題間追蹤]。 這可讓您監看主題之間和單一主題中的進度。

1. 依照下面的順序輸入使用者文字，以測試代理程式。 撰寫畫布會以綠色勾號來回報成功的步驟。

    |問題順序|測試問題|目的|
    |--|--|--|
    |1|您好|開始交談|
    |2|營業時間|範例主題。 已為您設定，不需進行任何額外的工作。|
    |3|是|用以回覆 `Did that answer your question?`|
    |4|非常好|用以回覆 `Please rate your experience.`|
    |5|是|用以回覆 `Can I help with anything else?`|
    |6|如何改善查詢預測的輸送量效能？|此問題會觸發後援動作，該動作會將文字傳送給您的知識庫進行回答， 而後就會顯示解答。 個別動作的綠色核取記號表示每個動作都成功。|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="對話流程的部分螢幕擷取畫面，其中已反白顯示刪除選項。":::

## <a name="publish-your-bot"></a>發佈您的 Bot

為了讓貴學校或組織的所有成員都能使用此代理程式，您必須予以發佈。

1. 從左側導覽中，選取 [發行]。 接著選取頁面上的 [發佈]。

1. 在示範網站上試用您的 Bot (尋找 [發佈] 下的連結)。

    新的網頁隨即開啟，其中包含您的 Bot。 向 Bot 詢問相同的測試問題：`How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![最終代理程式畫布的螢幕擷取畫面](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>共用您的 Bot

若要共用示範網站，請將其設定為通道。

1. 從左側導覽中，選取 [管理] > [通道]。

1. 從通道清單中選取 [示範網站]。

1. 複製連結，然後選取 [儲存]。 將您的示範網站連結貼到貴學校或組織成員的電子郵件中。

## <a name="clean-up-resources"></a>清除資源

當您處理完知識庫時，從 QnA Maker 入口網站中移除 QnA Maker 資源。

## <a name="next-step"></a>後續步驟

[取得知識庫的分析](../How-To/get-analytics-knowledge-base.md)

深入了解：
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker 連接器](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/)和[連接器設定](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)