---
title: 快速入門：使用批次問題來測試知識庫
description: 使用 QnA Maker 批次測試工具來測試 QnA Maker 資源中的知識庫，以取得預期答案、信賴分數和多回合提示。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/26/2020
ms.openlocfilehash: ac406761abf922ff5f0d86eacd3e419c374ce096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776760"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>快速入門：使用批次問題和預期答案來測試知識庫

使用 QnA Maker 批次測試工具來測試 QnA Maker 資源中的知識庫，以取得預期答案、信賴分數和多回合提示。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [建立 QnA Maker 服務](create-publish-knowledge-base.md)，或使用採用了英文版的現有服務。
* 下載[多回合範例 `.docx` 檔案](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* 下載[批次測試工具](https://aka.ms/qnamakerbatchtestingtool)，從 `.zip` 檔案擷取出可執行檔。

## <a name="sign-into-qna-maker-portal"></a>登入 QnA Maker 入口網站

[登入](https://www.qnamaker.ai/) QnA Maker 入口網站。

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>透過多回合 sample.docx 檔案建立新的知識庫

1. 從工具列中選取 [建立知識庫]。
1. 您應該已經有 QnA Maker 資源，因此請略過**步驟 1**，前往**步驟 2** 以選取現有資源的資訊：
    * Azure Active Directory 識別碼
    * Azure 訂用帳戶名稱
    * Azure QnA 服務名稱
    * 語言 - 英文
1. 輸入名稱 `Multi-turn batch test quickstart` 作為知識庫的名稱。

1. 在 [步驟 4] 中，使用下表進行設定：

    |設定|值|
    |--|--|
    |**啟用從 URL、.pdf 或 .docx 檔案進行多回合擷取。**|已檢查|
    |**預設答案文字**| `Batch test - default answer not found.`|
    |**+ 新增檔案**|選取 [必要條件] 中所下載的 `.docx` 檔案清單。|
    |**閒聊**|選取 [Professional]|

1. 在**步驟 5** 中，選取 [建立知識庫]。

    當建立程序完成時，入口網站會顯示可編輯的知識庫。

## <a name="save-train-and-publish-knowledge-base"></a>知識庫的儲存、定型和發佈

1. 從工具列中選取 [儲存並定型] 以儲存知識庫。
1. 從工具列中選取 [發佈]，然後再次選取 [發佈] 以發佈知識庫。 發佈會讓知識庫可供公用 URL 端點執行查詢。 當發佈程序完成時，請儲存 [發佈] 頁面上所顯示的主機 URL 和端點金鑰資訊。

    |必要資料| 範例|
    |--|--|
    |所發佈的主機|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |所發佈的金鑰|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (`Endpoint` 後面所顯示的 32 個字元字串)|
    |應用程式識別碼|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST` 內所顯示的其中 36 個字元字串) |

## <a name="create-batch-test-file-with-question-ids"></a>建立具有問題識別碼的批次測試檔案

為了使用批次測試工具，請使用文字編輯器建立名為 `batch-test-data-1.tsv` 的檔案。 此檔案必須為 UTF-8 格式，且擁有下列資料行並以定位字元加以分隔。

|TSV 輸入檔欄位|注意|範例|
|--|--|--|
|知識庫識別碼|在 [發佈] 頁面上所找到的知識庫識別碼。 藉由在單一檔案中使用不同的知識庫識別碼，便能以單一檔案在同一個服務中一次測試數個知識庫。|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST` 內所顯示的其中 36 個字元字串) |
|問題|使用者會輸入的問題文字。 最多可輸入 1,000 個字元。|`How do I sign out?`|
|中繼資料標記|選用|`topic:power` 會使用「key:value」格式|
|Top 參數|選用|`25`|
|預期答案的識別碼|選用|`13`|

針對此知識庫，請在檔案中新增只有 2 個必要資料行的 3 個資料列。 第一個資料行是知識庫的識別碼，第二個資料行則應該是下列問題清單：

|資料行 2 - 問題|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

這些問題是來自知識庫的確切用語，而且應該會傳回值為 100 的信賴分數。

接下來，使用相同的知識庫識別碼，在另外 3 個資料列上新增幾個與上述問題類似、但未完全相同的問題：

|資料行 2 - 問題|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> 請確定每個資料行都只以定位字元分隔符號加以分隔。 資料行資料的前端或尾端會加上空格，當類型或大小不正確時，這些空格會導致程式擲回例外狀況。

在 Excel 中開啟的批次測試檔案看起來會像下圖。 為確保安全，知識庫識別碼已取代為 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 在進行您自己的批次測試時，請確定資料行所顯示的是您的知識庫識別碼。

> [!div class="mx-imgBorder"]
> ![從批次測試輸入 .tsv 檔案的第一個版本](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>測試批次檔案

請在命令列中使用下列 CLI 格式來執行批次測試程式。

以您自己的服務名稱和端點金鑰值取代 `YOUR-RESOURCE-NAME` 和 `ENDPOINT-KEY`。 這些值可在 QnA Maker 入口網站的 [設定] 頁面上找到。

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
測試會完成並產生 `out.tsv` 檔案：

> [!div class="mx-imgBorder"]
> ![從批次測試輸出 .tsv 檔案的第一個版本](../media/batch-test/batch-test-1-output.png)

為確保安全，知識庫識別碼已取代為 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 在進行您自己的批次測試時，資料行會顯示您的知識庫識別碼。

在第 4 個資料行中，信賴分數的測試輸出顯示前 3 個問題如預期般傳回值為 100 的分數，其原因是每個問題都與知識庫中所顯示的問題完全相同。 最後三個問題由於使用了新的問題用語，因此不會傳回值為 100 的信賴分數。 為了增加測試和使用者兩者的分數，您必須在知識庫中新增更多替代問題。

## <a name="testing-with-the-optional-fields"></a>使用選擇性欄位進行測試

在了解格式和程序後，便可以產生測試檔案，以便針對來自資料來源 (例如來自聊天記錄) 的知識庫執行測試。

由於資料來源和程序皆已自動化，因此可使用不同設定來執行測試檔案多次，藉此確定正確的值。

例如，如果您擁有聊天記錄，而且想要確定哪些聊天記錄文字適用於哪些中繼資料欄位，請建立測試檔案並為每個資料列設定中繼資料欄位。 執行測試，然後檢閱符合中繼資料的資料列。 一般來說，相符項目應該會是正數，但請檢閱結果以找出「誤判為真」的項目。 「誤判為真」的項目是雖然符合中繼資料，但就文字來說不應該會相符的資料列。

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>在輸入批次測試檔案中使用選擇性欄位

請使用下列圖表來了解如何尋找選擇性資料的欄位值。

|資料行編號|選擇性資料行|資料位置|
|--|--|--|
|3|中繼資料|匯出現有「key:value」配對的現有知識庫。|
|4|top|建議以 `25` 作為預設值。|
|5|問答集識別碼|匯出識別碼值的現有知識庫。 另請注意，輸出檔案中會傳回識別碼。|

## <a name="add-metadata-to-the-knowledge-base"></a>將中繼資料新增至知識庫

1. 在 QnA 入口網站的 [編輯] 頁面上，將 `topic:power` 的中繼資料新增至下列問題：

    |問題|
    |--|
    |為 Surface Pro 4 充電|
    |檢查電池電量|

    兩個 QnA 配對已設定了中繼資料。

    > [!TIP]
    > 為了查看每個集合的中繼資料和 QnA 識別碼，請匯出知識庫。 選取 [設定] 頁面，然後選取 [匯出] 為 `.xls` 檔案。 尋找所下載的這個檔案，並以 Excel 開啟來檢閱中繼資料和識別碼。

1. 選取 [儲存並定型]，然後選取 [發佈] 頁面和 [發佈] 按鈕。 這些動作會將變更提供給批次測試使用。 從 [設定] 頁面下載知識庫。

    所下載的檔案會有正確的中繼資料格式，以及正確的問答集識別碼。 在下一節使用這些欄位

    > [!div class="mx-imgBorder"]
    > ![已匯出的知識庫 (含有中繼資料)](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>建立第二個批次測試

批次測試有兩種主要案例：
* **處理聊天記錄檔** - 針對先前沒見過的問題確定最佳解答 - 最常見的情況是您需要處理查詢的記錄檔時，例如聊天機器人的使用者問題。 建立只包含必要資料行的批次檔案測試。 此測試會傳回每個問題的最佳答案。 這並不表示最佳答案就是正確的答案。 完成此測試之後，請繼續進行驗證測試。
* **驗證測試** - 驗證預期的答案。 這項測試會要求批次測試中的所有問題和相匹配的預期答案都已經過驗證。 其中的某些程序可能需要手動進行。

下列程序假設此案例是要處理聊天記錄

1. 建立新的批次測試檔案以包含選擇性資料 `batch-test-data-2.tsv`。 從原始的批次測試輸入檔案新增 6 個資料列，然後為每個資料列新增中繼資料、top 和 QnA 配對識別碼。

    為了模擬針對知識庫來自動檢查聊天記錄中新文字的程序，請將每個資料行的中繼資料設定為相同值：`topic:power`。

    > [!div class="mx-imgBorder"]
    > ![從批次測試輸入 .tsv 檔案的第二個版本](../media/batch-test/batch-test-2-input.png)

1. 再次執行測試，這次變更輸入和輸出檔案名稱，以指出這是第二項測試。

    > [!div class="mx-imgBorder"]
    > ![從批次測試輸出 .tsv 檔案的第二個版本](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>測試結果和自動化測試系統

此測試輸出檔案可在自動化連續測試管線中加以剖析。

此特定測試輸出應該解讀為：每個資料列都已使用中繼資料進行篩選，而且因為每個資料列都不符合知識庫中的中繼資料，所以傳回了這些不相符資料列所適用的預設回答 (「在知識庫中未找到正確的匹配項目」)。 至於確實相符的資料列，則會傳回 QnA 識別碼和分數。

所有資料列都傳回了不正確的標籤，原因是沒有資料列符合預期的答案識別碼。

您應該能夠從這些結果中發現，您可以取得聊天記錄，並將其中的文字作為每個資料列的查詢。 在對資料沒有任何了解的情況下，結果會讓您對資料有更多了解，以供您隨後使用：

* 中繼資料
* QnA 識別碼
* score

對測試來說，使用中繼資料進行篩選是不是個好主意？ 可以說是，也可以說不是。 測試系統應該為每個中繼資料配對建立測試檔案，也應該建立不含任何中繼資料配對的測試。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續測試知識庫，請刪除批次檔案工具和測試檔案。

如果您不打算繼續使用此知識庫，請使用下列步驟刪除知識庫：

1. 在 QnA Maker 入口網站中，從頂端功能表中選取 [我的知識庫]。
1. 在知識庫清單中，在本快速入門的知識庫資料列上選取 [刪除] 圖示。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://go.microsoft.com/fwlink/?linkid=2092179)
