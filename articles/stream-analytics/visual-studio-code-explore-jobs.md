---
title: 探索 Visual Studio Code 中的 Azure 串流分析作業
description: 本文說明如何將 Azure 串流分析作業匯出至本機專案、列出作業和查看作業實體。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 00705e40ca17959701af325ed52a4c3754d35122
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039053"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>使用 Visual Studio Code 探索 Azure 串流分析（預覽）

適用于 Visual Studio Code 擴充功能的 Azure 串流分析可為開發人員提供管理其串流分析作業的輕量體驗。 它可用於 Windows、Mac 和 Linux。 使用 Azure 串流分析擴充功能，您可以：

- [建立](quick-create-vs-code.md)、啟動和停止作業
- 將現有的作業匯出至本機專案
- 列出作業和查看作業實體

## <a name="export-a-job-to-a-local-project"></a>將作業匯出至本機專案

若要將作業匯出至本機專案，請在 Visual Studio Code 的**串流分析 Explorer**中找出您想要匯出的作業。 然後選取專案的資料夾。 專案會匯出至您選取的資料夾，您可以繼續從 Visual Studio Code 管理作業。 如需使用 Visual Studio Code 管理串流分析作業的詳細資訊，請參閱 Visual Studio Code[快速入門](quick-create-vs-code.md)。

![在 Visual Studio Code 中匯出 ASA 作業](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出作業和查看作業實體

您可以使用作業檢視，從 Visual Studio 與 Azure 串流分析工作互動。


1. 按一下 Visual Studio Code 活動列上的 [ **Azure** ] 圖示，然後展開 [**串流分析] 節點**。 您的作業應該會出現在您的訂用帳戶底下。

   ![開啟串流分析 Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. 展開您的作業節點，您可以開啟並查看作業查詢、設定、輸入、輸出和函數。 

3. 以滑鼠右鍵按一下您的作業節點，然後選擇 [**在入口網站中開啟工作檢視**] 節點，以在 Azure 入口網站中開啟工作檢視。

   ![在入口網站中開啟工作檢視](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code 中建立 Azure 串流分析雲端作業 (預覽)](quick-create-vs-code.md)
