---
title: LUIS 應用程式的連續工作流程
description: 如何針對 Language Understanding (LUIS) 的 DevOps 執行 CI/CD 工作流程。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: a74ad7b995f0112346e2212866655107f72c03e7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324802"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>適用于 LUIS DevOps 的持續整合與持續傳遞工作流程

開發 Language Understanding (LUIS) 應用程式的軟體工程師，可以針對 [原始檔控制](luis-concept-devops-sourcecontrol.md)、 [自動化組建](luis-concept-devops-automation.md)、 [測試](luis-concept-devops-testing.md)和 [發行管理](luis-concept-devops-automation.md#release-management)套用 DevOps 實務。 本文說明針對 LUIS 執行自動化組建的概念。

## <a name="build-automation-workflows-for-luis"></a>LUIS 的組建自動化工作流程

![CI 工作流程](./media/luis-concept-devops-automation/luis-automation.png)

在您的原始程式碼管理 (SCM) 系統中，將自動化組建管線設定為在下列事件執行：

1. 當[提取要求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (pr) 引發時，就會觸發**pr 工作流程**。 此工作流程會先驗證 PR 的內容， *再* 將更新合併到主要分支。
1. 當更新推送至主要分支時觸發的**CI/CD 工作流程**，例如在合併 PR 的變更時。 此工作流程可確保主要分支的所有更新品質。

**CI/CD 工作流程**結合兩個互補開發流程：

* [持續整合](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) (CI) 是在共用存放庫中經常認可程式碼，並對其執行自動化組建的工程實務。 結合了自動化 [測試](luis-concept-devops-testing.md) 方法，持續整合可讓我們確認每個更新的 LUDown 來源都仍然有效，而且可以匯入 LUIS 應用程式中，但也會通過一組測試來確認已定型的應用程式可以辨識您解決方案所需的意圖和實體。

* [持續傳遞](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) (CD) 進一步採用持續整合概念，將應用程式自動部署到可進行更深入測試的環境。 CD 可讓我們及早瞭解我們的變更所引發的任何未預期問題，並瞭解我們的測試涵蓋範圍中的差距。

持續整合和持續傳遞的目標是要確保「主機永遠可交付」。 針對 LUIS 應用程式，這表示我們可以在需要時，從主要分支 LUIS 應用程式取得任何版本，並將它傳送到生產環境。

### <a name="tools-for-building-automation-workflows-for-luis"></a>為 LUIS 建立自動化工作流程的工具

有不同的組建自動化技術可用來建立組建自動化工作流程。 它們都要求您可以使用命令列介面來編寫步驟的腳本 (CLI) 或 REST 呼叫，讓它們可以在組建伺服器上執行。

使用下列工具來為 LUIS 建立自動化工作流程：

* [Bot Framework TOOLS LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) 來使用 LUIS apps 和版本、在 LUIS 服務內進行定型、測試和發佈。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 查詢 Azure 訂用帳戶、提取 LUIS 撰寫和預測金鑰，以及建立用於自動化驗證的 azure [服務主體](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) 。

* [NLU。](https://github.com/microsoft/NLU.DevOps) 用於 [測試 LUIS 應用程式](luis-concept-devops-testing.md) 以及分析測試結果的 DevOps 工具。

### <a name="the-pr-workflow"></a>PR 工作流程

如前所述，您會將此工作流程設定為在開發人員引發 PR 時執行，以建議將變更從功能分支合併到主要分支。 其目的是要先驗證 PR 中的變更品質，再將其合併到主要分支。

此工作流程應：

* 藉由匯入 PR 中的來源來建立暫存 LUIS 應用程式 `.lu` 。
* 訓練併發布 LUIS 應用程式版本。
* 對它執行所有 [單元測試](luis-concept-devops-testing.md) 。
* 如果所有測試都通過，則傳遞工作流程，否則會將它失敗。
* 清除並刪除暫存應用程式。

如果您的 SCM 支援，請設定分支保護規則，讓此工作流程必須在 PR 可以完成之前順利完成。

### <a name="the-master-branch-cicd-workflow"></a>主要分支 CI/CD 工作流程

將此工作流程設定為在 PR 中的更新合併到主要分支之後執行。 其目的是要藉由測試更新，讓主要分支的品質列保持在高。 如果更新符合品質列，此工作流程會將新的 LUIS 應用程式版本部署至環境，讓您可以進行更深入的測試。

此工作流程應：

* 使用更新的原始程式碼，在您的主要 LUIS 應用程式中建立新版本 (您為主要分支維護的應用程式) 。

* 訓練併發布 LUIS 應用程式版本。

  > [!NOTE]
  > 如在 [自動化組建工作流程中執行測試](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) 所述，您必須發佈受測的 LUIS 應用程式版本，才能使用 NLU 之類的工具。DevOps 可以存取它。 LUIS 只針對 LUIS 應用程式支援兩個命名的發行集位置、 *預備* 和 *生產環境* ，但您也可以 [直接發佈版本](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) 並 [依版本進行查詢](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3#changes-by-slot-name-and-version-name)。 在您的自動化工作流程中使用直接版本發行，以避免受限於使用已命名的發行位置。

* 執行所有 [單元測試](luis-concept-devops-testing.md)。

* 選擇性地執行 [批次測試](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) 來測量 LUIS 應用程式版本的品質和精確度，並將其與某些基準進行比較。

* 如果測試順利完成：
  * 標記存放庫中的來源。
  * 執行持續傳遞 (CD) 作業，將 LUIS 應用程式版本部署至環境以進行進一步的測試。

### <a name="continuous-delivery-cd"></a>持續傳遞 (CD) 

CI/CD 工作流程中的 CD 工作會在組建和自動化單元測試成功時，有條件地執行。 它的工作是自動將 LUIS 應用程式部署到可進行更多測試的環境。

針對如何以最佳方式部署 LUIS 應用程式，並沒有一個建議的解決方案，您必須執行適用于您專案的程式。 [LUIS DevOps 範本](https://github.com/Azure-Samples/LUIS-DevOps-Template)存放庫會為此提供簡單的解決方案，以將[新的 LUIS 應用程式版本發佈](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)到*生產*發行位置。 這適用于簡單的設定。 但是，如果您需要同時支援許多不同的生產環境（例如 *開發*、 *預備* 和 *UAT*），則每個應用程式的兩個命名發佈位置限制將會證明不足。

部署應用程式版本的其他選項包括：

* 將應用程式版本保留發佈至直接版本端點，並依照需要使用直接版本端點來執行程式來設定下游生產環境。
* 針對每個生產環境維護不同的 LUIS 應用程式，並撰寫自動化步驟，將 `.lu` LUIS 應用程式中的新版本匯入至目標生產環境、定型及發佈。
* 將已測試的 LUIS 應用程式版本匯出至 [LUIS docker 容器](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto?tabs=v3) ，並將 LUIS 容器部署至 Azure [容器實例](https://docs.microsoft.com/azure/container-instances/)。

## <a name="release-management"></a>版本管理

一般來說，我們建議您只在非生產環境中進行持續傳遞，例如開發和預備。 大部分的小組都需要手動審核和核准程式，以部署至生產環境。 在生產環境部署中，您可能會想要確定當開發小組的重要人員可供支援或在低流量期間進行時，就會發生這種情況。

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [使用 GitHub 執行 LUIS 的 DevOps](luis-how-to-devops-with-github.md)
* 瞭解如何 [使用 NLU 撰寫 GitHub Actions 的工作流程。DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
