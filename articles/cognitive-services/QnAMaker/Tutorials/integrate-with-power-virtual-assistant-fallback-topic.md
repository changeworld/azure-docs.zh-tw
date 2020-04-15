---
title: 教學課程：與 Power Virtual Agent 整合 - QnA Maker
description: 在本教學課程中，使用主動式學習來改善知識庫的品質。 在不移除或變更現有問題的情況下，進行審查、接受或拒絕、新增。
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398738"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>教學課程：將知識庫新增至 Power Virtual Agent
建立並擴充 [Power Virtual Agent](https://powervirtualagents.microsoft.com/)Bot，以從您的知識庫提供解答。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 建立 Power Virtual Agent
> * 建立系統後援主題
> * 將 QnA Maker 當作動作新增至主題作為 Power Automate 流程
> * 建立 Power Automate 解決方案
> * 將 Power Automate 流程新增至解決方案
> * 發佈 Power Virtual Agent
> * 測試 Power Virtual Agent、接收來自 QnA Maker 知識庫的解答

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>整合 Power Virtual Agent 與知識庫

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) 可讓小組使用引導式、無程式碼的圖形化介面，輕鬆建立功能強大的 Bot，而不需要資料科學家或開發人員介入。

所建立的 Power Virtual Agent 包含一系列主題 (主題領域)，以便藉由執行動作來回答使用者問題。 如果找不到解答，則系統後援可傳回答案。

設定代理程式，以將問題傳送至您的知識庫，作為主題動作的一部分或作為**系統後援**主題路徑的一部分。 兩者都使用相同的動作機制來連線到您的知識庫並傳回解答。

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate 會連線到 GenerateAnswer 動作

若要將您的代理程式連線到您的知識庫，請使用 Power Automate 建立動作。 Power Automate 提供一個程序**流程**，以連線至 QnA Maker 的 GenerateAnswer API。

設計並儲存**流程**後，即可在 Power Automate **解決方案**中使用。  將 GenerateAnswer 流程新增至解決方案後，請在您的代理程式中使用該解決方案作為動作。

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>將代理程式連線至知識庫的處理步驟

下列步驟會以概觀的形式呈現，協助您了解這些步驟與將 Power Virtual Agent 連線至 QnA Maker 知識庫的目標有何關聯。

使用 Power Virtual Agent 搭配 QnA Maker 的步驟：
* 在 [QnA Maker](https://www.qnamaker.ai/) 入口網站中
    * 建立和發佈知識庫
    * 複製知識庫詳細資料，包括知識庫識別碼、執行階段端點金鑰和執行階段端點主機。
* 在 [Power Virtual Agent](https://powerva.microsoft.com/) 入口網站中
    * 建立代理程式主題
    * 呼叫動作 (對 Power Automate 流程)
* 在 [Power Automate](https://us.flow.microsoft.com/) 入口網站中
    * 使用連接器建立對 [QnA Maker 的 GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/) 的流程
        * QnA Maker 發佈的知識庫資訊
            * 知識庫識別碼
            * QnA Maker 資源端點主機
            * QnA Maker 資源端點金鑰
        * 輸入 - 使用者查詢
        * 輸出 - 知識庫解答
    * 建立解決方案並新增流程
* 返回 Power Virtual Agent
    * 選取解決方案的輸出作為主題的訊息

## <a name="create-and-publish-a-knowledge-base"></a>建立和發佈知識庫

1. 依照[快速入門](../Quickstarts/create-publish-knowledge-base.md)建立知識庫。 請勿完成最後一節來建立 Bot。 本教學課程會取代快速入門的最後一節，因為本教學課程使用 Power Virtual Agent 來建立 Bot，而不是快速入門中的 Bot Framework Bot。

    > [!div class="mx-imgBorder"]
    > ![輸入在 [QnA Maker] (https://www.qnamaker.ai/) 入口網站的 [設定] 頁面上找到的已發佈知識庫設定。](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    您需要此資訊，以便 [Power Automate 步驟](#create-power-automate-flow-to-connect-to-your-knowledge-base)設定您的 QnA Maker GenerateAnswer 連線。

1. 在 QnA Maker 入口網站的 [設定]  頁面上，尋找端點金鑰、端點主機和知識庫識別碼。

## <a name="create-power-virtual-agent"></a>建立 Power Virtual Agent

1. 使用您的學校或公司電子郵件帳戶[登入](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) Power Virtual Agent。
1. 如果這是您的第一個 Bot，您應該位於代理程式的 [首頁]  。 如果這不是您的第一個 Power Virtual Agent，請從右上方導覽選取 Bot，然後選取 [+ 新增 Bot]  。

    > [!div class="mx-imgBorder"]
    > ![輸入在 [QnA Maker] (https://www.qnamaker.ai/) 入口網站的 [設定] 頁面上找到的已發佈知識庫設定。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Bot 中提供了數個主題

代理程式會使用主題集合來回答您的主題領域中的問題。 在本教學課程中，代理程式有許多為您提供的主題，可分為 [使用者主題]  和 [系統主題]  。

> [!div class="mx-imgBorder"]
> ![代理程式會使用主題集合來回答您的主題領域中的問題。 在本教學課程中，代理程式提供了許多主題，可分為 [使用者主題] 和 [系統主題]。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>建立 Power Virtual Agent 的系統後援主題

雖然代理程式可以從任何主題連線到您的知識庫，但本教學課程使用系統**後援**主題。 代理程式在找不到解答時會使用後援主題。 代理程式會將使用者的文字傳遞至 QnA Maker 的 GenerateAnswer API、接收來自知識庫的解答，並以訊息的形式向使用者顯示。

1. 在 [Power Virtual Agents](https://powerva.microsoft.com/#/) 入口網站中，於導覽的右上角選取 [設定]  頁面。 此頁面的圖示為齒輪。 選取 [系統後援]  。

    > [!div class="mx-imgBorder"]
    > ![系統後援的 Power Virtual Agent 功能表項目](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. 在快顯 [設定]  視窗上，選取 [+ 新增]  以新增系統後援主題。

    > [!div class="mx-imgBorder"]
    > ![在 [設定] 視窗上，新增後援主題。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. 新增主題後，選取 [移至後援主題]  ，以在撰寫畫布上撰寫後援主題。

    > [!TIP]
    > 如果您需要回到後援主題，您可以在 [系統]  主題的 [主題]  區段中取得該主題。

## <a name="use-authoring-canvas-to-add-an-action"></a>使用撰寫畫布來新增動作

使用 Power Virtual Agents 撰寫畫布，將後援主題連線到您的知識庫。 本主題的開頭為 [無法辨識的使用者文字]  。 新增動作，以將該文字傳遞至 QnA Maker，然後以訊息形式顯示解答。 本教學課程稍後會以[個別的步驟](#add-solutions-flow-to-power-virtual-agent)來處理顯示解答的最後一個步驟。

本節會建立後援主題交談流程。

1. 新的後援動作可能已經有交談流程元素。 選取 [選項] 功能表，以刪除 [呈報]  項目。

    > [!div class="mx-imgBorder"]
    > ![使用觸發片語啟動後援動作](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. 選取源自 [訊息]  方塊的 **+** 連接器，然後選取 [呼叫動作]  。

    > [!div class="mx-imgBorder"]
    > ![呼叫動作](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. 選取 [建立流程]  。 此程序會帶您前往 **Power Automate**，這是另一個瀏覽器型入口網站。

    > [!div class="mx-imgBorder"]
    > ![呼叫動作](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>建立 Power Automate 流程以連線到您的知識庫

以下程序會建立 **Power Automate** 流程，以便：
* 接受傳入的使用者文字
* 將其傳送至 QnA Maker
* 將 QnA Maker 首要解答指派給變數
* 將變數 (首要解答) 當作回應傳回給您的代理程式

1. 在 **Power Automate** 中，系統會為您啟動 [流程範本]  。 在 [Power Virtual Agents]  流程項目上，選取 [編輯]  以將來自代理程式的輸入變數設定為您的知識庫。 以文字為基礎的輸入變數是來自您代理程式的使用者提交文字問題。

    > [!div class="mx-imgBorder"]
    > ![將您的輸入變數設定為文字字串](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. 新增文字輸入，並將變數命名為 `InputText` 且具有 `IncomingUserQuestion` 描述。 此命名方式有助於區別輸入文字與您稍後建立的輸出文字。

    > [!div class="mx-imgBorder"]
    > ![新增文字輸入，並將變數命名為 `InputText` 且具有 `UserQuestion` 描述](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. 選取源自 [Power Virtual Agents]  方塊的 **+** 連接器，以在流程中插入新步驟 (在 [將值傳回給 Power Virtual Agent]  之前)，然後選取 [新增動作]  。

1. 搜尋 `Qna` 以尋找 **QnA Maker** 動作，然後選取 [產生解答]  。

    > [!div class="mx-imgBorder"]
    > ![搜尋 'Qna' 以尋找 [QnA Maker] 動作，然後選取 [產生解答]](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    QnA Maker 的三 (3) 個必要連線設定會出現在動作和來自 Power Virtual Agent 的問題設定中。

    > [!div class="mx-imgBorder"]
    > ![QnA Maker 的連線設定會出現在動作中。](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. 使用您的知識庫識別碼、端點主機和端點金鑰來設定動作。 在 QnA Maker 入口網站中，可在知識庫的 [設定]  頁面上找到這些值。

    > [!div class="mx-imgBorder"]
    > ![輸入在 [QnA Maker] (https://www.qnamaker.ai/) 入口網站的 [設定] 頁面上找到的已發佈知識庫設定。](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. 若要設定 [問題]  ，請選取文字方塊，然後從清單中選取 `InputText`。

1. 若要在流程中插入新步驟，請選取源自 [產生解答]  動作方塊的 **+** 連接器，然後選取 [新增動作]  。

1. 若要新增變數以擷取從 GenerateAnswer 傳回的解答文字，請搜尋並選取 `Initialize variable` 動作。

    將變數的名稱設定為 `OutgoingQnAAnswer`，然後選取 [字串]  作為類型。 請勿設定 [值]  。

    > [!div class="mx-imgBorder"]
    > ![將變數的名稱設定為 'QnAAnswer'，然後選取 [字串] 作為類型](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. 若要在流程中插入新步驟，請選取源自 [初始化變數]  動作方塊的 **+** 連接器，然後選取 [新增動作]  。

1. 若要將整個知識庫 JSON 回應設定為變數，請搜尋並選取 `Apply to each` 動作。 選取 GenerateAnswer `answers`。

1. 若只要傳回首要解答，請在相同的 [套用至各項]  方塊中，選取 [新增動作]  。 搜尋並選取 [設定變數]  。

    在 [設定變數]  方塊中，選取 [名稱]  的文字方塊，然後從清單中選取 [OutgoingQnAAnswer]  。

    選取 [值]  的文字方塊，然後從清單中選取 [回答答案]  。

    > [!div class="mx-imgBorder"]
    > ![設定變數的名稱和值 ](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. 若要傳回變數 (和其值)，請選取 [將值傳回給 Power Virtual Agent]  流程項目，接著選取 [編輯]  ，然後選取 [新增輸出]  。 選取 [文字]  輸出類型，然後輸入 `FinalAnswer` 的 [標題]  。 選取 [值]  的文字方塊，然後選取 `OutgoingQnAAnswer` 變數。

    > [!div class="mx-imgBorder"]
    > ![設定傳回值](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. 選取 [儲存]  以儲存流程。

## <a name="create-solution-and-add-flow"></a>建立解決方案並新增流程

為了讓 Power Virtual Agent 能夠尋找並連線至流程，流程必須包含在 Power Automate 解決方案中。

1. 仍在 Power Automate 入口網站中，從左側導覽選取 [解決方案]  。

1. 選取 [+ 新增解決方案]  。

1. 輸入顯示名稱。 解決方案清單包含貴組織或學校中的每個解決方案。 選擇可協助您只篩選出您的解決方案的命名慣例，例如在您的解決方案名稱前面加上您的電子郵件：`jondoe-power-virtual-agent-qnamaker-fallback`。

1. 從選項清單中選取您的發行者。

1. 接受名稱和版本的預設值。

1. 選取 [建立]  來完成程序。

## <a name="add-flow-to-solution"></a>將流程新增至解決方案

1. 在解決方案清單中，選取您剛建立的解決方案。 其應該位於清單頂端。 若非如此，請依您的電子郵件名稱 (這是解決方案名稱的一部分) 進行搜尋。

1. 在解決方案中，選取 [+ 新增現有]  ，然後從清單中選取 [流程]  。

1. 尋找您的流程，然後選取 [新增]  來完成程序。 如果有許多流程，請查看 [已修改]  資料行，以尋找最近的流程。

## <a name="add-solutions-flow-to-power-virtual-agent"></a>將解決方案的流程新增至 Power Virtual Agent

1. 返回含有您的 Power Virtual Agent 的瀏覽器索引標籤。 撰寫畫布應該仍然開啟。

1. 選取 [訊息]  動作方塊下的 **+** 連接器，以在流程中插入新步驟，然後選取 [呼叫動作]  。

1. 在新動作中，選取 [UnrecognizedTriggerPhrase]  的輸入值。 這會將來自代理程式的文字傳遞給流程。

    > [!div class="mx-imgBorder"]
    > ![在新動作中，選取 [UnrecognizedTriggerPhrase] 的輸入值。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. 選取 [動作]  方塊下的 **+** 連接器，以在流程中插入新步驟，然後選取 [顯示訊息]  。

1. 輸入訊息文字 `Your answer is:`，然後使用就地工具列的功能，選取 `FinalAnswer` 作為內容變數。

    > [!div class="mx-imgBorder"]
    > ![從 Power Automate 流程輸入訊息文字和 `FinalAnswer`。](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. 從內容工具列中選取 [儲存]  ，以儲存主題的撰寫畫布詳細資料。

最終的畫布如下所示。

> [!div class="mx-imgBorder"]
> ![最終代理程式畫布](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>測試 Power Virtual Agent

1. 在測試窗格中，切換 [在主題間追蹤]  。 這可讓您監看主題之間和單一主題中的進度。

1. 依照下面提供的順序輸入使用者文字，以測試代理程式。 撰寫畫布會以綠色勾號來回報成功的步驟。

|問題順序|測試問題|目的|
|--|--|--|
|1|您好|開始交談|
|2|營業時間|範例主題 - 已為您設定，您不需進行任何額外的工作。|
|3|是|用以回覆 `Did that answer your question?`|
|4|非常好|用以回覆 `Please rate your experience.`|
|5|是|用以回覆 `Can I help with anything else?`|
|6|什麼是知識庫？|此問題會觸發後援動作，該動作會將文字傳送給您的知識庫進行回答，而後就會顯示解答。 |

> [!div class="mx-imgBorder"]
> ![最終代理程式畫布](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>發佈您的 Bot

為了讓貴學校或組織的所有成員都能使用此代理程式，您必須予以發佈。

1. 從左側導覽選取 [發佈]  ，然後選取頁面上的 [發佈]  。

1. 在示範網站 (以 [發佈]  按鈕下方的連結提供) 上試用您的 Bot。

    新的網頁隨即開啟，其中包含您的 Bot。 向 Bot 詢問相同的測試問題：`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![最終代理程式畫布](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>共用您的 Bot

若要共用示範網站，請將其設定為通道。

1. 選取 [管理]  ，然後從左側導覽選取 [通道]  。

1. 從通道清單中選取 [示範網站]  。

1. 複製連結，然後選取 [儲存]  。 將您的示範網站連結貼到貴學校或組織成員的電子郵件中。

## <a name="clean-up-resources"></a>清除資源

當您處理完知識庫時，從 QnA Maker 入口網站中移除 QnA Maker 資源。

## <a name="next-step"></a>後續步驟

[取得知識庫的分析](../How-To/get-analytics-knowledge-base.md)

深入了解：
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker 連接器](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/)和[連接器設定](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)