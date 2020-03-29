---
title: 在視覺化工作室代碼中流覽 Azure 流分析作業
description: 本文介紹如何將 Azure 流分析作業匯出到本地專案、列出作業並查看作業實體。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479237"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>使用視覺化工作室代碼流覽 Azure 流分析（預覽版）

Azure 視覺化工作室代碼擴展的 Azure 流分析為開發人員提供了管理其流分析作業的羽量級體驗。 它可以在 Windows、Mac 和 Linux 上使用。 使用 Azure 流分析擴展，您可以：

- [創建](quick-create-vs-code.md)、啟動和停止作業
- 將現有作業匯出到本地專案
- 列出作業和查看作業實體

## <a name="export-a-job-to-a-local-project"></a>將作業匯出到本地專案

要將作業匯出到本地專案，請在視覺化工作室代碼中的**流分析資源管理器**中找到要匯出的作業。 然後為專案選擇一個資料夾。 專案將匯出到您選擇的資料夾，您可以從 Visual Studio Code 繼續管理作業。 有關使用視覺化工作室代碼管理流分析作業的詳細資訊，請參閱視覺化工作室代碼[快速入門](quick-create-vs-code.md)。

![在視覺化工作室代碼中匯出 ASA 作業](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出作業和查看作業實體

您可以使用作業檢視，從 Visual Studio 與 Azure 串流分析工作互動。


1. 按一下視覺化工作室代碼活動欄上的**Azure**圖示，然後展開**流分析節點**。 作業應顯示在訂閱下。

   ![打開流分析資源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 展開作業節點，可以打開和查看作業查詢、配置、輸入、輸出和函數。 

3. 按右鍵作業節點，然後選擇**門戶節點中的"打開工作檢視**"以在 Azure 門戶中打開工作檢視。

   ![在門戶中打開工作檢視](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code 中建立 Azure 串流分析雲端作業 (預覽)](quick-create-vs-code.md)
