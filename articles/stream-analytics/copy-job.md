---
title: 複製或備份 Azure 流分析作業
description: 本文介紹如何複製或備份 Azure 流分析作業。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771490"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>複製或備份 Azure 流分析作業

您可以使用視覺化工作室代碼或視覺化工作室複製或備份已部署的 Azure 流分析作業。 

## <a name="before-you-begin"></a>開始之前
* 如果沒有 Azure 訂閱，請創建[一個免費帳戶](https://azure.microsoft.com/free/)。

* 登錄到 Azure[門戶](https://portal.azure.com/)。

* [為視覺化工作室代碼安裝 Azure 流分析擴展](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)，或[為視覺化工作室安裝 Azure 流分析工具](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)。  

## <a name="visual-studio-code"></a>Visual Studio Code

1. 按一下視覺化工作室代碼活動欄上的**Azure**圖示，然後展開**流分析**節點。 作業應顯示在訂閱下。

   ![打開流分析資源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 要將作業匯出到本地專案，請在視覺化工作室代碼中的**流分析資源管理器**中找到要匯出的作業。 然後為專案選擇一個資料夾。

    ![在視覺化工作室代碼中匯出 ASA 作業](./media/vscode-explore-jobs/export-job.png)

    專案將匯出到您選擇的資料夾，並將其添加到當前工作區。

    ![在視覺化工作室代碼中匯出 ASA 作業](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. 要將作業發佈到其他區域或使用其他名稱進行備份，請選擇 **"從訂閱中選擇要**在查詢編輯器 （.asaql）\*中發佈"，然後按照說明進行操作。

    ![在視覺化工作室代碼中發佈到 Azure](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. 按照[將部署的 Azure 流分析作業匯出到專案說明](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)。

2. 在查詢\*編輯器中打開 .asaql 檔，在腳本編輯器中選擇 **"提交到 Azure"，** 然後按照說明將作業發佈到其他區域或使用新名稱進行備份。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用視覺化工作室代碼創建流分析作業](quick-create-vs-code.md)
* [快速入門：使用視覺化工作室創建流分析作業](stream-analytics-quick-create-vs.md)
* [使用 Azure Pipelines 來部署具有 CI/CD 的 Azure 串流分析作業](stream-analytics-tools-visual-studio-cicd-vsts.md)
