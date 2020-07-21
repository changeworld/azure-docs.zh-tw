---
title: 測試 LUIS 應用程式的 DevOps
description: 如何在 DevOps 環境中測試您的 Language Understanding （LUIS）應用程式。
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: 2556d2e904aff720bc02e4c7d58bf5a72af4d413
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538066"
---
# <a name="testing-for-luis-devops"></a>測試 LUIS DevOps

開發 Language Understanding （LUIS）應用程式的軟體工程師可以遵循這些指導方針，[在原始檔控制](luis-concept-devops-sourcecontrol.md)、[自動化組建](luis-concept-devops-automation.md)、[測試](luis-concept-devops-testing.md)和[發行管理](luis-concept-devops-automation.md#release-management)方面套用 DevOps 做法。

在敏捷式軟體發展方法中，測試在建立品質軟體方面扮演了有機的角色。 LUIS 應用程式的每一項重大變更，都應該伴隨著測試開發人員在應用程式中所建立的新功能所設計的測試。 這些測試會簽入您的原始程式碼存放庫，以及 `.lu` LUIS 應用程式的來源。 當應用程式符合測試時，就會完成變更的執行。

測試是[CI/CD 工作流程](luis-concept-devops-automation.md)的重要部分。 當提取要求（PR）中提議 LUIS 應用程式的變更，或將變更合併到您的主要分支之後，CI 工作流程應該執行測試，以確認更新尚未造成任何衰退。

## <a name="how-to-do-unit-testing-and-batch-testing"></a>如何進行單元測試和批次測試

您需要在持續整合工作流程中執行的 LUIS 應用程式有兩種不同類型的測試：

- **單元測試**-驗證 LUIS 應用程式主要功能的簡單測試。 當預期的意圖和預期的實體會針對指定的測試語句傳回時，單元測試就會通過。 所有單元測試都必須通過，測試回合才能順利完成。  
這類測試與您可以在[LUIS 入口網站](https://www.luis.ai/)中執行的[互動式測試](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)類似。

- **批次測試**-批次測試是您目前已定型模型的完整測試，以測量其效能。 不同于單元測試，批次測試不會通過 | 測試失敗。 使用批次測試的預期不是每個測試都會傳回預期的意圖和預期的實體。 相反地，批次測試可協助您在應用程式中查看每個意圖和實體的精確度，並協助您在進行改善時，比較一段時間。  
這種測試與您可以在 LUIS 入口網站中以互動方式執行的[批次測試](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)相同。

您可以從專案的開頭使用單元測試。 當您已開發 LUIS 應用程式的架構，而且您正在努力改善其精確度時，批次測試只是真正的價值。

針對單元測試和批次測試，請確定您的測試語句與定型語句分開。 如果您在定型的相同資料上進行測試，當您的應用程式只是過度學習到測試資料時，就會得到假的印象。 模型必須看不到測試，才能測試其一般化的程度。

### <a name="writing-tests"></a>撰寫測試

當您撰寫一組測試時，您必須針對每個測試定義：

* 測試語句
* 預期的意圖
* 預期的實體。

使用 LUIS[批次檔語法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-syntax-template-for-intents-with-entities)，在 JSON 格式的檔案中定義一組測試。 例如：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

一些測試控管，例如[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)也支援 LUDown 格式的測試檔案。

#### <a name="designing-unit-tests"></a>設計單元測試

單元測試應設計來測試 LUIS 應用程式的核心功能。 在您應用程式開發的每個反復專案（或短期衝刺）中，您應該撰寫足夠數目的測試，以確認您在該反復專案中執行的主要功能是否正常運作。

在每個單元測試中，針對指定的測試語句，您可以：

* 測試是否傳回正確的意圖
* 測試「金鑰」實體（這是對您的解決方案而言很重要的實體）會傳回。
* 測試意圖和實體的[預測分數](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)是否超過您定義的臨界值。 例如，您可以決定只有在意圖的預測分數和您的關鍵實體超過0.75 時，才會考慮測試已通過。

在單元測試中，最好是測試您的索引鍵實體是否已在預測回應中傳回，但忽略任何誤報。 「*誤報」是在*預測回應中找到，但在測試的預期結果中未定義的實體。 藉由忽略誤報，讓撰寫單元測試變得較不繁瑣，同時仍可讓您專注于測試在預測回應中傳回解決方案的關鍵資料。

> [!TIP]
> [NLU。DevOps](https://github.com/microsoft/NLU.DevOps)工具支援您所有的 LUIS 測試需求。 `compare`在[單元測試模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)中使用時，命令會判斷提示所有測試都成功，而且會忽略未在預期結果中標記之實體的錯誤正面結果。

#### <a name="designing-batch-tests"></a>設計批次測試

批次測試集應包含大量測試案例，其設計目的是要在 LUIS 應用程式中的所有意圖和所有實體之間進行測試。 如需定義批次測試集的詳細資訊，請參閱[LUIS 入口網站中的批次測試](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)。

### <a name="running-tests"></a>執行測試

LUIS 入口網站提供可協助進行互動式測試的功能：

* [**互動式測試**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)可讓您提交範例語句，並取得 LUIS 辨識意圖和實體的回應。 您可以透過視覺效果檢查確認測試是否成功。

* [**批次測試**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)會使用批次測試檔案做為輸入，以驗證您的有效定型版本來測量其預測精確度。 批次測試可協助您查看使用中版本中每個意圖和實體的精確度，並以圖表顯示結果。

#### <a name="running-tests-in-an-automated-build-workflow"></a>在自動化組建工作流程中執行測試

LUIS 入口網站中的互動式測試功能很有用，但針對 DevOps，在 CI/CD 工作流程中執行的自動化測試會提供特定需求：

* 測試控管必須在組建伺服器上的工作流程步驟中執行。 這表示工具必須能夠在命令列上執行。
* 測試控管必須能夠對端點執行一組測試，並自動根據實際結果來驗證預期的結果。
* 如果測試失敗，測試控管必須傳回狀態碼以停止工作流程，並「使組建失敗」。

LUIS 不提供命令列工具或高階 API 來提供這些功能。 我們建議使用[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)工具，可在命令列和在 CI/CD 工作流程內的自動化測試期間，執行測試並驗證結果。

LUIS 入口網站中提供的測試功能不需要已發佈的端點，而且是 LUIS 撰寫功能的一部分。 當您在自動化組建工作流程中執行測試時，您必須發佈 LUIS 應用程式版本以測試到端點，以便測試控管（例如 NLU）。DevOps 可以在測試過程中傳送預測要求。

> [!TIP]
> * 如果您要執行自己的測試解決方案，並撰寫程式碼以將測試語句傳送至端點，請記住，如果您使用 LUIS 撰寫金鑰，允許的交易速率限制為5TPS。 請節流傳送速率，或改用預測金鑰。
> * 將測試查詢傳送至端點時，請記得 `log=false` 在預測要求的查詢字串中使用。 這可確保您的測試語句不會由 LUIS 記錄下來，最後會出現在 LUIS[主動式學習](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)功能所呈現的端點語句審核清單中，因此不會意外加入至應用程式的定型語句。

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令列和 CI/CD 工作流程中執行單元測試

您可以使用[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)封裝，以在命令列執行測試：

* 使用 NLU。DevOps[測試命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)，將測試檔案中的測試提交至端點，並在檔案中捕捉實際的預測結果。
* 使用 NLU。DevOps [compare] 命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)可將實際結果與輸入測試檔案中所定義的預期結果進行比較。 `compare`命令會產生 NUnit 測試輸出，並在[單元測試模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)中使用旗標時 `--unit-test` ，判斷所有測試都通過。

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令列和 CI/CD 工作流程中執行批次測試

您也可以使用 NLU。DevOps 封裝，以在命令列執行批次測試。

* 使用 NLU。DevOps[測試命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)，將測試檔案中的測試提交至端點，並在檔案中捕捉實際的預測結果，與單元測試相同。
* 使用 NLU。DevOps[比較](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)[效能測試模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode)中的命令若要測量應用程式的效能，您也可以將應用程式的效能與基準效能基準測試進行比較，例如，最新認可至主要或目前版本的結果。 在效能測試模式中， `compare` 命令會產生 JSON 格式的 NUnit 測試輸出和[批次測試結果](https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#batch-test)。

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS 不具決定性的定型和對測試的影響

當 LUIS 定型模型（例如意圖）時，它需要兩個正面資料-您所提供用來為模型和負面資料定型應用程式的標示定型語句-*不*是有效使用該模型的範例。 在定型期間，LUIS 會從您為其他模型提供的所有正面資料建立一個模型的負面資料，但在某些情況下，可能會產生資料不平衡。 為了避免這種不平衡，LUIS 會以不具決定性的方式來取樣負資料的子集，以優化較佳的平衡定型集、改善的模型效能，以及更快速的定型時間。

這種不具決定性定型的結果，是您可能會在[不同的定型會話之間取得稍微不同的預測回應](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)，通常用於[預測分數](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)不高的意圖和/或實體。

如果您想要針對您所建立的 LUIS 應用程式版本停用不具決定性的定型以進行測試，請使用[版本設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) ，並將設定 `UseAllTrainingData` 設為 `true` 。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[執行 CI/CD 工作流程](luis-concept-devops-automation.md)
* 瞭解如何[使用 GitHub 來執行 LUIS 的 DevOps](luis-how-to-devops-with-github.md)