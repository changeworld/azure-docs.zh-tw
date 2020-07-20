---
title: 原始檔控制和開發分支-LUIS
description: 如何在原始檔控制下維護您的 Language Understanding （LUIS）應用程式。 如何在開發分支中工作時，將更新套用至 LUIS 應用程式。
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 2d060fefbd32ecea1f91e6b062da7606699a63c4
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783778"
---
# <a name="devops-practices-for-luis"></a>LUIS 的 DevOps 做法

開發 Language Understanding （LUIS）應用程式的軟體工程師可以遵循這些指導方針，[在原始檔控制](luis-concept-devops-sourcecontrol.md)、[自動化組建](luis-concept-devops-automation.md)、[測試](luis-concept-devops-testing.md)和[發行管理](luis-concept-devops-automation.md#release-management)方面套用 DevOps 做法。

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS 的原始檔控制和分支策略

DevOps 成功所依賴的其中一個關鍵因素是[原始檔控制](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)。 原始檔控制系統可讓開發人員在程式碼上共同作業，並追蹤變更。 使用分支可以讓開發人員在不同版本的程式碼基底之間切換，並獨立與小組的其他成員一起工作。 當開發人員引發[提取要求](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests)（PR）以將更新從某個分支提議至另一個分支，或合併變更時，這些可以是[自動化組建](luis-concept-devops-automation.md)的觸發程式，以建立和持續測試程式碼。

藉由使用本檔中所述的概念和指導方針，您可以在追蹤原始檔控制系統中的變更時開發 LUIS 應用程式，並遵循下列軟體工程最佳作法：

- **原始檔控制**
  - LUIS 應用程式的原始程式碼是人們看得懂的格式。
  - 您可以透過可重複的方式，從來源建立模型。
  - 原始程式碼可以由原始程式碼存放庫來管理。
  - 認證和秘密（例如撰寫和訂用帳戶金鑰）永遠不會儲存在原始程式碼中。

- **分支和合併**
  - 開發人員可以從獨立分支工作。
  - 開發人員可以同時在多個分支中工作。
  - 您可以透過重訂基底或合併，將 LUIS 應用程式的變更從某個分支整合到另一個分支。
  - 開發人員可以將 PR 合併到父分支。

- **版本控制**
  - 大型應用程式中的每個元件都應該獨立建立版本，讓開發人員只需查看版本號碼，就可以偵測重大變更或更新。

- **程式碼審查**
  - PR 中的變更會呈現為人類可讀的原始程式碼，並可在接受 PR 之前進行檢查。

## <a name="source-control"></a>原始檔控制

若要在原始程式碼管理系統中維護 LUIS 應用程式的[應用程式架構定義](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition)，請使用應用程式的[LUDown 格式（ `.lu` ）](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)標記法。 `.lu`格式是慣用 `.json` 的格式，因為它是人類看得懂的，這可讓您更輕鬆地在 pr 中進行變更並加以審查。

### <a name="save-a-luis-app-using-the-ludown-format"></a>使用 LUDown 格式儲存 LUIS 應用程式

若要以格式儲存 LUIS 應用程式 `.lu` ，並將它放在原始檔控制底下：

- 可能： [Export the app version](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) `.lu` 從[LUIS 入口網站](https://www.luis.ai/)匯出應用程式版本，並將它新增至您的原始檔控制存放庫

- 或：使用文字編輯器來建立 `.lu` LUIS 應用程式的檔案，並將它新增至您的原始檔控制存放庫

> [!TIP]
> 如果您使用的是 LUIS 應用程式的 JSON 匯出，可以使用[BotBuilder 工具 LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)[將它轉換成 LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) 。 使用 `--sort` 選項可確保意圖和語句會依字母順序排序。  
> 請注意， **。** LUIS 入口網站內建的 LU 匯出功能已經排序輸出。

### <a name="build-the-luis-app-from-source"></a>從來源建立 LUIS 應用程式

針對 LUIS 應用程式，若要*從來源組建*，請匯[入 `.lu` 來源以建立新的 LUIS 應用程式版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)，以[定型版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)並[加以發行](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)。 您可以在 LUIS 入口網站中，或在命令列中執行此動作：

- 使用 LUIS 入口網站，從原始檔控制匯入應用程式的[ `.lu` 版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version)，並[訓練](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)並[發佈](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)應用程式。

- 在命令列或 CI/CD 工作流程中，使用[適用于 LUIS 的 Bot Framework 命令列介面](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)， [import](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` 從原始檔控制將應用程式版本匯入 LUIS 應用程式，並將應用程式[定型](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)和[發佈](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)。

### <a name="files-to-maintain-under-source-control"></a>要在原始檔控制之下維護的檔案

下列 LUIS 應用程式的檔案類型應該在原始檔控制下維護：

- `.lu`LUIS 應用程式的檔案

- [單元測試定義](luis-concept-devops-testing.md#writing-tests)檔（語句和預期的結果）

- 用於效能測試的[批次測試](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format)檔案（語句和預期的結果）

### <a name="credentialsand-keys-are-not-checked-in"></a>認證和金鑰未簽入

請不要在您簽入存放庫的檔案中包含訂用帳戶金鑰或類似的機密值，因為未經授權的人員可能會看到這些檔案。 您應該避免簽入的金鑰和其他值包括：

- LUIS 撰寫和預測金鑰
- LUIS 撰寫和預測端點
- Azure 訂用帳戶金鑰
- 存取權杖，例如用於自動化驗證之 Azure[服務主體](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)的權杖

#### <a name="strategies-for-securely-managing-secrets"></a>安全管理秘密的策略

安全管理秘密的策略包括：

- 如果您使用 Git 版本控制，您可以將執行時間秘密儲存在本機檔案中，並藉由新增模式來比對檔案名與[.gitignore](https://git-scm.com/docs/gitignore)檔案，以避免簽入檔案
- 在自動化工作流程中，您可以安全地在該自動化技術所提供的參數設定中儲存秘密。 例如，如果您使用的是[Github 動作](https://github.com/features/actions)，則可以安全地將秘密儲存在[github 秘密](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)中。

## <a name="branching-and-merging"></a>分支和合併

Git 之類的分散式版本控制系統提供彈性，讓小組成員透過與他人共用的開發分支來發行、共用、審查及反復執行程式碼變更。 採用適用于您小組的[Git 分支策略](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance)。

無論您採用哪一種分支策略，都是小組成員可以在*功能分支*中，獨立于其他分支中進行的工作來處理解決方案。

支援使用 LUIS 專案在分支中獨立運作：

- **主要分支有自己的 LUIS 應用程式。** 此應用程式代表您的專案解決方案目前的狀態，而其目前的作用中版本應一律對應到 `.lu` 主要分支中的來源。 `.lu`應檢查並測試此應用程式之來源的所有更新，如此一來，就可以將此應用程式部署到任何時間都建立環境，例如生產環境。 當的更新 `.lu` 從功能分支合併至 master 時，您應該在 LUIS 應用程式中建立新的版本，並增加[版本號碼](#versioning)。

- **每個功能分支都必須使用自己的 LUIS 應用程式實例**。 開發人員在功能分支中使用此應用程式，而不會影響在其他分支中工作的開發人員。 此「開發分支」應用程式是一個工作複本，在刪除功能分支時應該予以刪除。

![Git 功能分支](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>開發人員可以從獨立分支工作

開發人員可以藉由下列方式，獨立于其他分支，在 LUIS 應用程式上處理更新：

1. 從主要分支建立功能分支（視您的分支策略而定，通常是主要或開發）。

1. [在 LUIS 入口網站中建立新的 LUIS 應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app)（「*開發分支應用程式*」），只是為了支援功能分支中的工作。

   * 如果您的 `.lu` 方案來源已經存在於您的分支中，因為它是在先前專案的另一個分支中完成工作後儲存，所以請匯入檔案來建立 dev BRANCH LUIS 應用程式 `.lu` 。

   * 如果您要開始處理新的專案，則存放庫中還沒有 `.lu` 主要 LUIS 應用程式的來源。 `.lu`當您完成功能分支工作時，您將從入口網站匯出開發分支應用程式，並將其提交為 PR 的一部分，以建立檔案。

1. 請在開發分支應用程式的作用中版本上執行所需的變更。 建議您只在開發分支應用程式的單一版本中，針對所有功能分支工作使用。 如果您在 dev 分支應用程式中建立一個以上的版本，請小心追蹤哪個版本包含您要在引發 PR 時簽入的變更。

1. 測試更新-如需測試開發分支應用程式的詳細資訊，請參閱[測試 LUIS DevOps](luis-concept-devops-testing.md) 。

1. `.lu`從 [[版本] 清單](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)中匯出 dev 分支應用程式的作用中版本。

1. 簽入您的更新，並邀請對等的更新檢查。 如果您使用的是 GitHub，則會提出[提取要求](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)。

1. 當變更經過核准後，請將更新合併到主要分支中。 此時，您將使用 master 中更新的來建立*主要*LUIS 應用程式的新[版本](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) `.lu` 。 如需設定版本名稱的考慮，請參閱[版本控制](#versioning)。

1. 刪除功能分支時，最好先刪除您為功能分支工作所建立的 dev branch LUIS 應用程式。

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>開發人員可以同時在多個分支中工作

如果您遵循上面所述的模式，[開發人員可以從獨立分支工作](#developers-can-work-from-independent-branches)，則您將在每個功能分支中使用唯一的 LUIS 應用程式。 單一開發人員可以同時處理多個分支，只要他們切換至目前正在處理之分支的正確 dev branch LUIS 應用程式即可。

我們建議您針對功能分支和針對功能分支工作所建立的 dev branch LUIS 應用程式，使用相同的名稱，讓您不會意外地在錯誤的應用程式上工作。

如先前所述，我們建議您在每個開發分支應用程式中使用單一版本，以簡化。 如果您使用多個版本，當您在開發分支應用程式之間切換時，請小心啟動正確的版本。

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>多個開發人員可以同時在相同的分支上工作

您可以同時支援多個開發人員同時處理相同的功能分支：

- 開發人員簽出相同的功能分支和推播和提取變更，由自己和其他開發人員所提交，正常運作時也會繼續進行。

- 如果您遵循上述的模式，[開發人員可以從獨立分支工作](#developers-can-work-from-independent-branches)，則此分支會使用唯一的 LUIS 應用程式來支援開發。 該「dev branch」 LUIS 應用程式將由在功能分支中開始工作的開發小組第一個成員建立。

- [將小組成員新增為](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate)DEV branch LUIS 應用程式的參與者。

- 當 feature 分支工作完成時，請從 [版本] 清單中匯出 dev branch LUIS 應用程式的作用中版本 `.lu` ，並將更新的檔案儲存在存放庫[versions list](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) `.lu` 中，然後簽入並 PR 變更。

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>使用重訂基底或合併將某個分支的變更併入另一個分支

小組的其他開發人員在另一個分支中工作，可能會在 `.lu` 您建立功能分支之後，對來源進行更新並將其合併至主要分支。 在您繼續于功能分支中進行變更之前，您可能會想要將變更併入您的工作版本中。 您可以使用與任何其他程式碼資產相同的方式，透過[重訂基底或合併至 master 來](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)執行這項操作。 由於 LUDown 格式的 LUIS 應用程式是人類可讀取的，因此支援使用標準合併工具進行合併。

如果您要在功能分支中重定基底您的 LUIS 應用程式，請遵循下列秘訣：

- 在重設基底或合併之前，請 `.lu` 先從入口網站重新匯出應用程式，以確定您應用程式的來源本機複本具有您使用 LUIS 入口網站套用的所有最新變更。 如此一來，您就可以確保您在入口網站中所做的任何變更，而且尚未匯出。

- 在合併期間，使用標準工具來解決任何合併衝突。

- 在重訂基底或合併完成後，別忘了將應用程式重新匯入至入口網站，讓您在繼續套用自己的變更時，使用更新的應用程式。

### <a name="merge-prs"></a>合併 Pr

在您的 PR 獲得核准之後，您就可以將變更合併到您的主要分支。 LUIS 應用程式的 LUDown 來源不會有任何特殊考慮：它是人類看得懂的，因此支援使用標準合併工具進行合併。 任何合併衝突的解決方式可能與其他原始檔相同。

在您的 PR 合併完成後，建議您進行清除：

- 刪除存放庫中的分支

- 刪除您為功能分支工作所建立的「dev branch」 LUIS 應用程式。

與應用程式代碼資產的方式相同，您應該撰寫單元測試，以伴隨 LUIS 應用程式更新。 您應該採用持續整合工作流程來測試：

- Pr 中的更新會在 PR 合併之前
- 已核准 PR 並將變更合併至 master 之後，主要分支 LUIS 應用程式。

如需測試 LUIS DevOps 的詳細資訊，請參閱[測試 DevOps FOR LUIS](luis-concept-devops-testing.md)。 如需有關如何執行工作流程的詳細資訊，請參閱[LUIS DevOps 的自動化工作流程](luis-concept-devops-automation.md)。

## <a name="code-reviews"></a>程式碼檢閱

LUDown 格式的 LUIS 應用程式是人類看得懂的，它支援在適合審查的 PR 中進行變更的通訊。 單元測試檔案也會以 LUDown 格式撰寫，而且也可以輕鬆地在 PR 中檢閱。

## <a name="versioning"></a>版本控制

應用程式包含多個元件，可能包括在[Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)中執行的 bot、 [QnA Maker](https://www.qnamaker.ai/)、 [azure 語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)等等。 若要達到鬆散結合的應用程式目標，請使用[版本控制](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control)，讓應用程式的每個元件都能獨立建立版本，讓開發人員只需查看版本號碼，就可以偵測重大變更或更新。 如果您在自己的存放庫中維護 LUIS 應用程式，則可以更輕鬆地從其他元件獨立進行版本。

適用于 master 分支的 LUIS 應用程式應該已套用版本設定配置。 當您將 LUIS 應用程式的更新合併至 `.lu` master 時，您會接著在主要分支的 LUIS 應用程式中，將該更新的來源匯入至新版本。

建議您針對主要 LUIS 應用程式版本使用數值版本控制配置，例如：

`major.minor[.build[.revision]]`

每次更新時，版本號碼都會以最後一個數位遞增。

主要/次要版本可用來指出 LUIS 應用程式功能變更的範圍：

* 主要版本：重大變更，例如對新[意圖](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent)或[實體](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types)的支援
* 次要版本：回溯相容的次要變更，例如在重要的新訓練之後
* Build：沒有任何功能變更，只是不同的組建。

一旦您判斷出主要 LUIS 應用程式的最新修訂版本號碼，就需要建立並測試新的應用程式版本，並將其發佈至可用於不同組建環境（例如，品質保證或生產環境）的端點。 強烈建議您在持續整合（CI）工作流程中將所有這些步驟自動化。

請參閱：
- [自動化工作流程](luis-concept-devops-automation.md)，以取得如何執行 CI 工作流程來測試和發行 LUIS 應用程式的詳細資料。
- 如需如何部署 LUIS 應用程式的相關資訊，請[Release Management](luis-concept-devops-automation.md#release-management) 。

### <a name="versioning-the-feature-branch-luis-app"></a>版本設定「功能分支」 LUIS 應用程式

當您使用已建立的「dev branch」 LUIS 應用程式來支援功能分支中的工作時，您將會在工作完成時匯出您的應用程式，而且您將會 `'lu` 在 PR 中包含更新的。 當 PR 合併到 master 之後，您的存放庫中的分支和「開發分支」 LUIS 應用程式應該會被刪除。 由於此應用程式只是為了支援功能分支中的工作，因此您不需要在此應用程式內套用特定的版本控制配置。

當您的 PR 中的變更合併至 master 時，也就是應該套用版本設定時，讓 master 的所有更新都可以獨立建立版本。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[測試 LUIS DevOps](luis-concept-devops-testing.md)
* 瞭解如何[使用 GitHub 來執行 LUIS 的 DevOps](luis-how-to-devops-with-github.md)
