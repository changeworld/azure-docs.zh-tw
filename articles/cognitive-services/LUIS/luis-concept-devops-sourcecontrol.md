---
title: 原始檔控制和開發分支-LUIS
description: 如何在原始檔控制下維護 Language Understanding (LUIS) 應用程式。 如何在開發分支中工作時，將更新套用至 LUIS 應用程式。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 0466105ab99d191b5dd9beab1d5d5b61f4b3225e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790879"
---
# <a name="devops-practices-for-luis"></a>適用於 LUIS 的 DevOps 實務

開發 Language Understanding (LUIS) 應用程式的軟體工程師，可以遵循下列指導方針，來套用 [原始檔控制](luis-concept-devops-sourcecontrol.md)、 [自動化組建](luis-concept-devops-automation.md)、 [測試](luis-concept-devops-testing.md)和 [發行管理](luis-concept-devops-automation.md#release-management) 的 DevOps 實務。

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS 的原始程式碼控制和分支策略

DevOps 成功所依據的關鍵因素之一是 [原始檔控制](/azure/devops/user-guide/source-control?view=azure-devops)。 原始檔控制系統可讓開發人員在程式碼上共同作業，並追蹤變更。 使用分支可讓開發人員在不同版本的程式碼基底之間切換，以及獨立地與小組的其他成員一起工作。 當開發人員提出 [提取要求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) 將某個分支的更新建議至另一個分支，或合併變更時，這些可以是 [自動化組建](luis-concept-devops-automation.md) 的觸發程式，以建立並持續測試程式碼。

您可以使用本檔中所述的概念和指導方針，在追蹤原始檔控制系統中的變更時開發 LUIS 應用程式，並遵循這些軟體工程的最佳作法：

- **原始檔控制**
  - LUIS 應用程式的原始程式碼採用人們看得懂的格式。
  - 您可以利用可重複的方式，從來源建立模型。
  - 原始程式碼可由原始程式碼儲存機制來管理。
  - 認證和密碼（例如撰寫和訂用帳戶金鑰）永遠不會儲存在原始程式碼中。

- **分支和合併**
  - 開發人員可以從獨立的分支工作。
  - 開發人員可以同時在多個分支中工作。
  - 您可以透過重設基底或合併，將 LUIS 應用程式的變更從某個分支整合至另一個分支。
  - 開發人員可以將 PR 合併至父分支。

- **版本控制**
  - 大型應用程式中的每個元件都應該獨立設定版本，讓開發人員只需查看版本號碼，就可以偵測重大變更或更新。

- **程式碼評論**
  - PR 中的變更會顯示為人類可讀取的原始程式碼，可在接受 PR 之前先進行檢查。

## <a name="source-control"></a>原始檔控制

若要在原始程式碼管理系統中維護 LUIS 應用程式的 [應用程式架構定義](./app-schema-definition.md) ，請使用 [LUDown 格式 (`.lu` ](/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  應用程式的) 標記法。 `.lu` 格式是慣用格式 `.json` 的格式，因為它是人們可讀取的格式，可讓您更輕鬆地在 pr 中進行和檢查變更。

### <a name="save-a-luis-app-using-the-ludown-format"></a>使用 LUDown 格式儲存 LUIS 應用程式

若要以格式儲存 LUIS 應用程式 `.lu` ，並將它放在原始檔控制之下：

- 任一： [](./luis-how-to-manage-versions.md#other-actions) `.lu` 從[LUIS 入口網站](https://www.luis.ai/)匯出應用程式版本，並將其新增至您的原始檔控制存放庫

- 或者：使用文字編輯器來建立 `.lu` LUIS 應用程式的檔案，並將它新增至您的原始檔控制存放庫

> [!TIP]
> 如果您正在使用 LUIS 應用程式的 JSON 匯出，則可以 [將它轉換成 LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert)。  使用 `--sort` 選項可確保意圖和語句會依字母順序排序。  
> 請 **注意，** 內建在 LUIS 入口網站中的 LU 匯出功能已將輸出排序。

### <a name="build-the-luis-app-from-source"></a>從來源組建 LUIS 應用程式

針對 LUIS 應用程式，若要 *從來源建立* ，表示要透過匯 [入 `.lu` 來源來建立新的 LUIS 應用程式版本](./luis-how-to-manage-versions.md#import-version) ，以 [定型版本](./luis-how-to-train.md) 並 [加以發行](./luis-how-to-publish-app.md)。 您可以在 LUIS 入口網站或命令列中執行此動作：

- 使用 LUIS 入口網站從原始檔控制匯入應用程式的 [ `.lu` 版本](./luis-how-to-manage-versions.md#import-version) ，以及 [定型](./luis-how-to-train.md) 和 [發佈](./luis-how-to-publish-app.md) 應用程式。

- 在命令列或 CI/CD 工作流程中，使用[LUIS 的 Bot Framework 命令列介面](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)，將[](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport)應用程式的 `.lu` 版本從原始檔控制匯入 LUIS 應用程式，以及[定型](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)和[發佈](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)應用程式。

### <a name="files-to-maintain-under-source-control"></a>原始檔控制下要維護的檔案

您應在原始檔控制下維護 LUIS 應用程式的下列檔案類型：

- `.lu` LUIS 應用程式的檔案

- [單元測試定義](luis-concept-devops-testing.md#writing-tests) 檔 (語句和預期的結果) 

- [批次測試](./luis-how-to-batch-test.md#batch-test-file) 檔案 (語句和預期的結果，) 用於效能測試

### <a name="credentials-and-keys-are-not-checked-in"></a>未簽入認證和金鑰

請勿在您簽入存放庫的檔案中包含訂用帳戶金鑰或類似的機密值，但未經授權的人員可能會看到這些檔案。 您應該防止簽入的金鑰和其他值包括：

- LUIS 撰寫和預測金鑰
- LUIS 撰寫和預測端點
- Azure 訂用帳戶金鑰
- 存取權杖，例如用於自動化驗證之 Azure [服務主體](/cli/azure/ad/sp?view=azure-cli-latest) 的權杖

#### <a name="strategies-for-securely-managing-secrets"></a>安全管理秘密的策略

安全管理秘密的策略包括：

- 如果您使用 Git 版本控制，您可以將執行時間秘密儲存在本機檔案中，並藉由新增模式來比對檔案名與 [.gitignore](https://git-scm.com/docs/gitignore) 檔案，以防止簽入檔案
- 在自動化工作流程中，您可以將秘密安全地儲存在該自動化技術所提供的參數設定中。 例如，如果您使用 [GitHub Actions](https://github.com/features/actions)，就可以安全地將秘密儲存在 [GitHub 秘密](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。

## <a name="branching-and-merging"></a>分支和合併

Git 之類的分散式版本控制系統，讓小組成員能夠透過與他人共用的開發分支，來發佈、共用、檢查和反復查看程式碼變更。 採用適用于您小組的 [Git 分支策略](/azure/devops/repos/git/git-branching-guidance) 。

無論您採用哪一種分支策略，全都是小組成員可以在 *功能分支* 內的方案之外，與其他分支中的工作分開處理。

若要支援與 LUIS 專案在分支中獨立運作：

- **主要分支有自己的 LUIS 應用程式。** 此應用程式代表您的方案目前的解決方案狀態，而其目前的使用中版本應該一律對應至 `.lu` 主要分支中的來源。 `.lu`此應用程式來源的所有更新都應經過審核和測試，以便將此應用程式部署到任何時間的組建環境，例如生產環境。 當的更新 `.lu` 從功能分支合併到主要時，您應該在 LUIS 應用程式中建立新的版本，並增加 [版本號碼](#versioning)。

- **每個功能分支都必須使用自己的 LUIS 應用程式實例**。 開發人員在功能分支中使用此應用程式，而不會影響在其他分支中工作的開發人員。 此「開發分支」應用程式是一種工作複本，應該在刪除功能分支時予以刪除。

![Git 功能分支](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>開發人員可以從獨立分支工作

開發人員可以透過下列方式，在 LUIS 應用程式上與其他分支分開處理更新：

1. 從主要分支建立功能分支 (取決於您的分支策略，通常是主要或開發) 。

1. [在 LUIS 入口網站中建立新的 LUIS 應用程式](./luis-how-to-start-new-app.md) (「*開發分支應用程式*」 ) 僅支援功能分支中的工作。

   * 如果 `.lu` 您的方案來源已經存在於您的分支中，因為它是在專案的另一個分支中完成工作之後儲存的，請匯入檔案以建立您的開發分支 LUIS 應用程式 `.lu` 。

   * 如果您要開始使用新的專案，您的存放庫中還不會有 `.lu` 主要 LUIS 應用程式的來源。 您將會 `.lu` 在完成功能分支工作時從入口網站匯出開發分支應用程式，並將其提交為 PR 的一部分，以建立檔案。

1. 使用開發分支應用程式的作用中版本，以執行所需的變更。 建議您只在適用于所有功能分支工作的單一版本開發分支應用程式中工作。 如果您在開發分支應用程式中建立多個版本，請小心追蹤哪個版本包含您想要在提高 PR 時簽入的變更。

1. 測試更新-請參閱 [LUIS DevOps 測試](luis-concept-devops-testing.md) ，以取得測試開發分支應用程式的詳細資料。

1. `.lu`從 [[版本] 清單](./luis-how-to-manage-versions.md)匯出開發分支應用程式的作用中版本。

1. 簽入您的更新，並邀請對等評論您的更新。 如果您使用的是 GitHub，則會引發 [提取要求](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)。

1. 核准變更時，請將更新合併到主要分支中。 至此，您將會使用 main 中更新 [](./luis-how-to-manage-versions.md)的來建立 *主要* LUIS 應用程式的新版本 `.lu` 。 如需設定版本名稱的考慮，請參閱 [版本控制](#versioning) 。

1. 刪除功能分支時，最好先刪除您為功能分支工作所建立的 dev branch LUIS 應用程式。

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>開發人員可以同時在多個分支中工作

如果您遵循上面所述的模式， [讓開發人員可以從獨立分支運作](#developers-can-work-from-independent-branches)，則您會在每個功能分支中使用唯一的 LUIS 應用程式。 單一開發人員可以同時處理多個分支，只要它們切換至目前正在處理之分支的正確 dev branch LUIS 應用程式即可。

建議您針對功能分支和您為功能分支工作所建立的 dev branch LUIS 應用程式使用相同的名稱，讓您較不可能不慎在錯誤的應用程式上工作。

如上所述，為了簡單起見，我們建議您在每個開發分支應用程式中使用單一版本。 如果您使用多個版本，當您在開發分支應用程式之間切換時，請小心啟用正確的版本。

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>多個開發人員可以同時在相同的分支上工作

您可以同時支援多個開發人員同時處理相同的功能分支：

- 開發人員會在正常情況下，簽出與其他開發人員一起提交的相同功能分支和推送和提取變更。

- 如果您遵循上面所述的模式， [讓開發人員可以從獨立分支進行工作](#developers-can-work-from-independent-branches)，則此分支將使用唯一的 LUIS 應用程式來支援開發。 在功能分支中開始工作的開發小組的第一個成員，將會建立該「dev 分支」 LUIS 應用程式。

- [將小組成員新增為](./luis-how-to-collaborate.md) DEV branch LUIS 應用程式的參與者。

- 當功能分支工作完成時，請從 [版本] 清單匯出開發分支 LUIS 應用程式的作用中版本 `.lu` 、將更新的檔案儲存在存放庫中， [](./luis-how-to-manage-versions.md) `.lu` 然後簽入並 PR 變更。

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>使用重設基底或合併，將變更從某個分支合併到另一個分支

您小組中其他工作的其他開發人員可能會在 `.lu` 您建立功能分支之後，對來源進行更新並將其合併到主要分支。 您可能會想要將其變更併入您的工作版本，然後才能繼續在您的功能分支內進行變更。 您可以藉由 [重設基底或合併至 main](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) ，方式與任何其他程式碼資產相同。 由於 LUIS 應用程式的 LUDown 格式是人類看得懂的，因此它支援使用標準合併工具進行合併。

如果您要在功能分支中重定基底 LUIS 應用程式，請遵循下列秘訣：

- 在重設基底或合併之前，請 `.lu` 先從入口網站重新匯出您的應用程式，以確定應用程式來源的本機複本具有您已使用 LUIS 入口網站套用的所有最新變更。 如此一來，您可以確定您在入口網站中所做的任何變更，但尚未匯出任何變更都不會遺失。

- 在合併期間，使用標準工具來解決任何合併衝突。

- 請不要忘記重設基底或合併後，將應用程式重新匯入回到入口網站，以便在您繼續套用您自己的變更時，使用更新的應用程式。

### <a name="merge-prs"></a>Merge Pr

在您的 PR 獲得核准之後，您就可以將變更合併至主要分支。 適用于 LUIS 應用程式的 LUDown 來源並無任何特殊考慮：它是人類可閱讀的，因此支援使用標準合併工具進行合併。 任何合併衝突的解決方式，都與其他原始檔相同。

合併 PR 之後，建議您清除：

- 刪除存放庫中的分支

- 刪除您為功能分支工作所建立的「dev 分支」 LUIS 應用程式。

以與應用程式程式碼資產相同的方式，您應該撰寫單元測試以伴隨 LUIS 應用程式更新。 您應採用持續整合工作流程來進行測試：

- Pr 合併前的 PR 中的更新
- PR 核准之後的主要分支 LUIS 應用程式，且變更已合併至主要分支。

如需 LUIS DevOps 測試的詳細資訊，請參閱 [DevOps FOR LUIS 測試](luis-concept-devops-testing.md)。 如需如何執行工作流程的詳細資訊，請參閱 [LUIS DevOps 的自動化工作流程](luis-concept-devops-automation.md)。

## <a name="code-reviews"></a>程式碼檢閱

LUDown 格式的 LUIS 應用程式是人類看得懂的，可支援 PR 中適用于評論的變更通訊。 單元測試檔案也會以 LUDown 格式撰寫，也可以輕鬆地在 PR 中可檢閱 oss。

## <a name="versioning"></a>版本控制

應用程式是由多個元件所組成，這些元件可能包含在 [Azure Bot Service](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)、 [QnA Maker](https://www.qnamaker.ai/)、 [azure 語音服務](../speech-service/overview.md)等專案中執行的 bot。 若要達到鬆散結合的應用程式目標，請使用 [版本控制](/azure/devops/learn/git/what-is-version-control) ，讓應用程式的每個元件獨立設定版本，讓開發人員只需查看版本號碼，就可以偵測重大變更或更新。 如果您在自己的存放庫中維護 LUIS 應用程式，就可以更輕鬆地將它與其他元件分開。

適用于主要分支的 LUIS 應用程式應套用版本控制配置。 當您將 LUIS 應用程式的更新合併到 `.lu` 主要分支時，您會接著將該更新的來源匯入至主要分支的 LUIS 應用程式中的新版本。

建議您針對主要 LUIS 應用程式版本使用數值版本設定配置，例如：

`major.minor[.build[.revision]]`

每次更新時，版本號碼都會以最後一個數位遞增。

主要/次要版本可用來指出 LUIS 應用程式功能的變更範圍：

* 主要版本：重大變更，例如支援新的 [意圖](./luis-concept-intent.md) 或 [實體](./luis-concept-entity-types.md)
* 次要版本：回溯相容的次要變更，例如重要的新訓練
* 組建：沒有任何功能變更，只是不同的組建。

一旦您決定了主要 LUIS 應用程式最新版本的版本號碼，您必須建立並測試新的應用程式版本，並將其發佈至可在不同組建環境中使用的端點，例如品質保證或生產環境。 強烈建議您在持續整合 (CI) 工作流程中自動化上述所有步驟。

請參閱：
- [自動化工作流程](luis-concept-devops-automation.md) ，以取得如何執行 CI 工作流程來測試和發行 LUIS 應用程式的詳細資料。
- 如需如何部署 LUIS 應用程式的詳細資訊， [Release Management](luis-concept-devops-automation.md#release-management) 。

### <a name="versioning-the-feature-branch-luis-app"></a>版本設定「feature 分支」 LUIS 應用程式

當您使用的「開發分支」 LUIS 應用程式已建立為支援功能分支中的工作時，您將會在工作完成時匯出您的應用程式，而您將會在 PR 中包含更新的 `'lu` 。 在將 PR 合併到 main 之後，您的存放庫中的分支和「dev 分支」 LUIS 應用程式應該刪除。 因為此應用程式只是為了支援功能分支中的工作，所以沒有您需要在此應用程式中套用的特定版本設定配置。

當您的 PR 中的變更合併到主要時（也就是應該套用版本控制時），因此主要的所有更新會獨立建立版本。

## <a name="next-steps"></a>後續步驟

* 瞭解 [LUIS DevOps 的測試](luis-concept-devops-testing.md)
* 瞭解如何 [使用 GitHub 執行 LUIS 的 DevOps](luis-how-to-devops-with-github.md)
