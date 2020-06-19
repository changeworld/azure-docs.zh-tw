---
title: 自訂語音語音服務的 CI/CD
titleSuffix: Azure Cognitive Services
description: 將 DevOps 與自訂語音和 CI/CD 工作流程搭配使用。 為您自己的專案實行現有的 DevOps 解決方案。
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: 463f5d5360e19fdd7f49139aea4c6dc65baf903c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081300"
---
# <a name="cicd-for-custom-speech"></a>自訂語音的 CI/CD

實行自動化的訓練、測試和發行管理，讓您可以在將更新套用到定型和測試資料時，持續改進自訂語音模型。 藉由有效實現 CI/CD 工作流程，您可以確保一律可使用最佳的自訂語音模型端點。

[持續整合](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration)（CI）是在共用存放庫中經常認可更新，以及在其上執行自動化組建的工程實務。 自訂語音的 CI 工作流程會從其資料來源訓練新模型，並在新模型上執行自動化測試，以確保它的執行效果優於先前的模型。

[持續傳遞](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery)（CD）會從 CI 進程取得模型，並為每個改良的自訂語音模型建立端點。 CD 可輕鬆地將端點整合到解決方案中。

自訂 CI/CD 解決方案是可行的，但對於健全、預先建立的解決方案，請使用[語音 DevOps 範本存放庫](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)，它會使用 GitHub 動作來執行 CI/CD 工作流程。

## <a name="cicd-workflows-for-custom-speech"></a>自訂語音的 CI/CD 工作流程

這些工作流程的目的是要確保每個自訂語音模型的辨識精確度比前一個組建更好。 如果測試和/或訓練資料的更新改善了精確度，這些工作流程會建立新的自訂語音端點。

Git 伺服器（例如 GitHub 和 Azure DevOps）可以在特定 Git 事件發生時執行自動化的工作流程，例如合併或提取要求。 例如，當測試資料的更新推送至*主要*分支時，可以觸發 CI 工作流程。 不同的 Git 伺服器將會有不同的工具，但會允許腳本命令列介面（CLI）命令，使其可在組建伺服器上執行。

在此過程中，工作流程應該命名和儲存資料、測試、測試檔案、模型和端點，讓它們可以追溯到其來源的認可或版本。 將這些資產命名也很有説明，以便在更新測試資料與定型資料之後，輕鬆查看所建立的資源。

### <a name="ci-workflow-for-testing-data-updates"></a>用於測試資料更新的 CI 工作流程

CI/CD 工作流程的主要目的是要使用定型資料來建立新的模型，並使用測試資料來測試該模型，以建立相較于先前效能最佳的模型（「基準模型」），是否改善了「[文字錯誤率](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer)」（WER）。 如果新模型的執行效能較佳，它會成為新的基準模型，以用於比較未來的模型。

測試資料更新的 CI 工作流程應該重新測試目前的基準模型與更新的測試資料，以計算修訂的 WER。 這可確保當新模型的 WER 與基準測試的 WER 比較時，這兩個模型都已針對相同的測試資料進行測試，而您的比較方式與 like 類似。

此工作流程應該會在測試資料的更新和下列情況觸發：

- 針對更新過的測試資料測試基準測試模型。
- 使用更新的資料，儲存測試輸出，其中包含基準測試模型的 WER。
- 這些測試中的 WER 會變成新的基準 WER，未來的模型必須能發揮其效能。
- CD 工作流程不會針對測試資料的更新執行。

### <a name="ci-workflow-for-training-data-updates"></a>用於定型資料更新的 CI 工作流程

定型資料的更新代表自訂模型的更新。

此工作流程應該會在定型資料的更新和上觸發：

- 使用更新的定型資料來定型新模型。
- 針對測試資料測試新的模型。
- 儲存測試輸出，其中包含 WER。
- 將新模型的 WER 與效能評定模型中的 WER 進行比較。
- 如果 WER 無法改善，請停止工作流程。
- 如果 WER 改善，請執行 CD 工作流程來建立自訂語音端點。

### <a name="cd-workflow"></a>CD 工作流程

在定型資料的更新改善模型的辨識之後，CD 工作流程應該會自動執行以建立該模型的新端點，並讓該端點可用於解決方案。

#### <a name="release-management"></a>版本管理

大部分的小組都需要手動審查和核准程式，才能部署到生產環境。 針對生產環境部署，您可能想要確定開發小組的重要人員可支援，或在低流量期間發生。

### <a name="tools-for-custom-speech-workflows"></a>自訂語音工作流程的工具

針對自訂語音使用下列適用于 CI/CD 自動化工作流程的工具：

- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來建立 azure 服務主體驗證、查詢 azure 訂用帳戶，以及在 azure Blob 中儲存測試結果。
- [Azure 語音 CLI](https://github.com/msimecek/Azure-Speech-CLI)可從命令列或自動化工作流程與語音服務互動。

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>使用 GitHub 動作 DevOps 自訂語音的解決方案

如需自訂語音的已實行 DevOps 解決方案，請移至[語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存放庫。 建立範本的複本，並使用健全的 DevOps 系統開始開發自訂模型，其中包含使用 GitHub 動作的測試、定型和版本控制。 儲存機制提供範例測試和訓練資料，協助設定和說明工作流程。 初始設定之後，請將範例資料取代成您的專案資料。

[語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存放庫提供的基礎結構和詳細指引如下：

- 將範本存放庫複製到您的 GitHub 帳戶，然後建立 Azure 資源和 GitHub 動作 CI/CD 工作流程的[服務主體](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。
- 逐步解說「[開發內部迴圈](https://mitchdenny.com/the-inner-loop/)」。 從功能分支更新定型和測試資料、使用暫時的開發模型來測試變更，以及提出提取要求來建議和檢查變更。
- 將提取要求中的定型資料更新為*master*時，使用 GITHUB 動作 CI 工作流程來定型模型。
- 執行自動化精確度測試，以建立模型的[文字](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer)錯誤率（WER）。 將測試結果儲存在 Azure Blob 中。
- 執行 CD 工作流程，以在 WER 改善時建立端點。

## <a name="next-steps"></a>後續步驟

深入瞭解使用語音 DevOps：

- 使用[語音 DevOps 範本](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)存放庫，以 GitHub 動作來執行自訂語音的 DevOps。
