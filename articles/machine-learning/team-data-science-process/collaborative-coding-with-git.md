---
title: 使用 Git 共同作業撰寫程式碼 - Team Data Science Process
description: 如何使用 Git 搭配敏捷式計劃進行資料科學專案的共同作業程式碼開發。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721892"
---
# <a name="collaborative-coding-with-git"></a>使用 Git 共同撰寫程式碼

本文介紹如何使用 Git 作為資料科學專案的協作代碼開發框架。 本文介紹如何將 Azure Repos 中的代碼連結到 Azure 板中的[敏捷開發](agile-development.md)工作項，如何執行代碼評審，以及如何創建和合併更改的拉取請求。

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>將工作項鍊接到 Azure 存儲庫分支 

Azure DevOps 提供了一種將 Azure 板使用者情景或任務工作項與 Azure 存儲庫存儲庫分支連接的便捷方法。 您可以將使用者情景或任務直接連結到與其關聯的代碼。 

要將工作項連接到新分支，請選擇工作項旁邊的 **"操作**省略點 **"（...），** 並在內容功能表上滾動到 並選擇 **"新建分支**"。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

在"**創建分支"對話方塊中**，提供新的分支名稱和基本 Azure 存儲庫 Git 存儲庫和分支。 基本存放裝置庫必須與工作項位於同一 Azure DevOps 專案中。 基分支可以是主分支或其他現有分支。 選擇 **"創建分支**"。 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

您還可以使用 Windows 或 Linux 中的以下 Git bash 命令創建新分支：

```bash
git checkout -b <new branch name> <base branch name>

```
如果未指定\<>的基本分支名稱，則新分支基於`master`。 

要切換到工作分支，請運行以下命令： 

```bash
git checkout <working branch name>
```

切換到工作分支後，可以開始開發代碼或文檔工件以完成工作項。 運行`git checkout master`將切換您回`master`分支。

最好為每個使用者情景工作項創建 Git 分支。 然後，對於每個任務工作項，可以基於使用者情景分支創建分支。 當您有多個人員為同一專案或同一使用者情景的不同任務處理不同的使用者情景時，組織與使用者故事-任務關係對應的層次結構中的分支。 通過讓每個團隊成員在不同的分支上工作，或者共用分支時，可以在不同的代碼或其他工件上工作，從而最大限度地減少衝突。 

下圖顯示了 TDSP 的建議分支策略。 您可能不需要如下所示的分支數，尤其是在只有一兩個人處理專案或只有一個人處理使用者情景的所有任務時。 但是，將開發分支與主分支分開始終是一種好的做法，它有助於防止發佈分支被開發活動中斷。 有關 Git 分支模型的完整說明，請參閱[成功的 Git 分支模型](https://nvie.com/posts/a-successful-git-branching-model/)。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

您也可以將工作項目連結至現有的分支。 在工作項**的詳細資訊**頁上，選擇 **"添加連結**"。 然後選擇一個現有分支將工作項鍊接到 ，然後選擇 **"確定**"。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>處理分支並提交更改 

對工作項進行更改（如將 R 指令檔添加到本地電腦的`script`分支）後，可以使用以下 Git bash 命令將更改從本地分支提交到上游工作分支：

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>創建拉取請求

在一個或多個提交和推送後，當您準備好將當前工作分支合併到其基本分支時，可以在 Azure Repos 中創建並提交*拉取請求*。 

從 Azure DevOps 專案的主頁中，指向左側導航中的 **"存儲庫** > **拉取"請求**。 然後選擇 **"新建拉取請求"** 按鈕或 **"創建拉取請求**連結"。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

在 **"新拉取請求"** 螢幕上，如有必要，導航到要合併更改的 Git 存儲庫和分支。 添加或更改所需的任何其他資訊。 在 **"檢閱者**"下，添加檢閱者的名稱，然後選擇"**創建**"。 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>檢閱及合併

創建拉取請求後，檢閱者會收到電子郵件通知以查看拉取請求。 檢閱者測試更改是否正常工作，並在可能的情況下與要求者檢查更改。 檢閱者可以進行注釋、請求更改，並根據評估批准或拒絕拉取請求。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

檢閱者批准更改後，您或具有合併許可權的其他人可以將工作分支合併到其基礎分支。 選擇 **"完成**"，然後在 **"完成拉取請求"** 對話方塊中選擇 **"完成合併**"。 您可以選擇在工作分支合併後將其刪除。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

確認請求標記為 **"已完成**"。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

當您返回左側導航中的**Repos**時，可以看到自`script`刪除分支以來已切換到主分支。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

您還可以使用以下 Git bash 命令將`script`工作分支合併到其基分支，並在合併後刪除工作分支：

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>後續步驟

[執行資料科學任務](execute-data-science-tasks.md)演示如何使用實用程式來完成幾個常見的資料科學任務，如互動式資料探索、資料分析、報告和模型創建。

[示例演練](walkthroughs.md)列出了特定方案的演練，包含連結和縮略圖說明。 連結的方案說明了如何將雲和本地工具和服務合併到工作流或管道中以創建智慧應用程式。 

