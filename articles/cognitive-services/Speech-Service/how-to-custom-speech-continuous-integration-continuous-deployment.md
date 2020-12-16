---
title: 自訂語音-語音服務的 CI/CD
titleSuffix: Azure Cognitive Services
description: 使用自訂語音和 CI/CD 工作流程來套用 DevOps。 為您自己的專案執行現有的 DevOps 解決方案。
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: 7e27c3dd6e70d9a532c326d8187d82e14bf7ddda
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591611"
---
# <a name="cicd-for-custom-speech"></a>自訂語音的 CI/CD

當您將更新套用至定型和測試資料時，請執行自動化訓練、測試和發行管理，以持續改進自訂語音模型。 透過有效的 CI/CD 工作流程的執行，您可以確保最佳的自訂語音模型端點一律可供使用。

[持續整合](/azure/devops/learn/what-is-continuous-integration) (CI) 是在共用存放庫中經常認可更新，並對其執行自動化組建的工程實務。 自訂語音的 CI 工作流程會從其資料來源將新模型定型，並在新模型上執行自動化測試，以確保其執行效能優於先前的模型。

[持續傳遞](/azure/devops/learn/what-is-continuous-delivery) (CD) 會從 CI 程式取得模型，並為每個改良的自訂語音模型建立端點。 CD 可讓端點輕鬆地整合到解決方案中。

您可以使用自訂的 CI/CD 解決方案，但針對強大的預先建立解決方案，請使用 [語音 DevOps 範本存放庫](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)，它會使用 GitHub Actions 來執行 CI/CD 工作流程。

## <a name="cicd-workflows-for-custom-speech"></a>自訂語音的 CI/CD 工作流程

這些工作流程的目的是要確保每個自訂語音模型的辨識精確度比上一個組建更好。 如果測試和/或定型資料的更新改善了精確度，則這些工作流程會建立新的自訂語音端點。

Git 伺服器（例如 GitHub 和 Azure DevOps）可以在特定的 Git 事件發生時執行自動化的工作流程，例如合併或提取要求。 例如，當測試資料的更新推送至 *主要* 分支時，就會觸發 CI 工作流程。 不同的 Git 伺服器會有不同的工具，但會允許 (CLI) 命令的腳本命令列介面，讓它們可以在組建伺服器上執行。

在過程中，工作流程應該命名和儲存資料、測試、測試檔案、模型和端點，讓它們可以追溯到其來源的認可或版本。 將這些資產命名也很有説明，讓您很容易就能看到在更新測試資料與定型資料之後所建立的資產。

### <a name="ci-workflow-for-testing-data-updates"></a>用於測試資料更新的 CI 工作流程

CI/CD 工作流程的主要目的是要使用定型資料來建立新的模型，並使用測試資料來測試該模型，以建立相較于上一個效能最佳的模型 (「效能評定模型」 ) [， (WER](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy)) 是否已改善。 如果新模型的執行效能較佳，則會變成新的基準模型，以供比較未來的模型。

測試資料更新的 CI 工作流程應該使用更新的測試資料來重新測試目前的基準模型，以計算修訂的 WER。 這可確保當新模型的 WER 與基準測試的 WER 進行比較時，這兩個模型都已針對相同的測試資料進行測試，而且您會像這樣進行比較。

此工作流程應該會觸發測試資料的更新，以及：

- 針對已更新的測試資料測試基準模型。
- 使用更新的資料來儲存測試輸出，其中包含基準測試模型的 WER。
- 這些測試的 WER 將會成為新的效能評定 WER，未來的模型必須要有這種情況。
- CD 工作流程不會執行測試資料的更新。

### <a name="ci-workflow-for-training-data-updates"></a>用於定型資料更新的 CI 工作流程

定型資料的更新表示自訂模型的更新。

此工作流程應該會觸發對定型資料的更新，以及：

- 使用更新的定型資料來訓練新模型。
- 針對測試資料測試新的模型。
- 儲存包含 WER 的測試輸出。
- 從基準模型將 WER 從新模型與 WER 進行比較。
- 如果 WER 無法改善，請停止工作流程。
- 如果 WER 改善，請執行 CD 工作流程來建立自訂語音端點。

### <a name="cd-workflow"></a>CD 工作流程

在訓練資料的更新改善模型的辨識之後，CD 工作流程應該會自動執行以建立該模型的新端點，並讓該端點可供使用，以便用於解決方案中。

#### <a name="release-management"></a>版本管理

大部分的小組都需要手動審核和核准程式，以部署至生產環境。 在生產環境部署中，您可能會想要確定當開發小組的重要人員可供支援或在低流量期間進行時，就會發生這種情況。

### <a name="tools-for-custom-speech-workflows"></a>自訂語音工作流程的工具

使用下列工具來進行自訂語音的 CI/CD 自動化工作流程：

- [Azure CLI](/cli/azure/?view=azure-cli-latest) 建立 azure 服務主體驗證、查詢 azure 訂用帳戶，以及將測試結果儲存在 azure Blob 中。
- [Azure 語音 CLI](spx-overview.md) 可從命令列或自動化工作流程與語音服務互動。

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>使用 GitHub Actions DevOps 自訂語音的解決方案

若為自訂語音已實行的 DevOps 解決方案，請移至 [語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存放庫。 建立範本的複本，並使用健全的 DevOps 系統開始開發自訂模型，包括使用 GitHub Actions 的測試、定型和版本控制。 此儲存機制提供測試和定型資料範例，以協助您進行設定並說明工作流程。 初始設定之後，請將範例資料取代為您的專案資料。

[語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存放庫提供基礎結構和詳細的指導方針：

- 將範本存放庫複製到您的 GitHub 帳戶，然後為 GitHub Actions CI/CD 工作流程建立 Azure 資源和 [服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 。
- 逐步解說「[開發內部迴圈](https://mitchdenny.com/the-inner-loop/)」。 從功能分支更新定型和測試資料、使用暫時性開發模型來測試變更，以及提出要求和檢查變更的提取要求。
- 將提取要求中的定型資料更新為 *主要* 時，請使用 GitHub Actions CI 工作流程將模型定型。
- 執行自動化的精確度測試，以 (WER) 建立模型的 [文字](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) 錯誤率。 將測試結果儲存在 Azure Blob 中。
- 執行 CD 工作流程，以在 WER 改善時建立端點。

## <a name="next-steps"></a>後續步驟

深入瞭解 DevOps with Speech：

- 使用 [語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) 存放庫，利用 GitHub Actions 來執行自訂語音的 DevOps。