---
title: 複製或備份 Azure 串流分析作業
description: 本文說明如何複製或備份 Azure 串流分析作業。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: ba63358347cf9722d2cafa35598b9b3b37f49dc3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129451"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>複製或備份 Azure 串流分析作業

您可以使用 Visual Studio Code 或 Visual Studio 來複製或備份已部署的 Azure 串流分析作業。 將作業複製到另一個區域時，並不會複製最後的輸出時間。 因此，當您啟動複製的作業時，無法使用 [ [**上次停止時間**](./start-job.md#start-options) ] 選項。

## <a name="before-you-begin"></a>開始之前
* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。

* 登入 [Azure 入口網站](https://portal.azure.com/)。

* 安裝適用于 [Visual Studio Code 的 Azure 串流分析擴充](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) 功能或 [適用于 Visual Studio 的 azure 串流分析工具](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension)。  

## <a name="visual-studio-code"></a>Visual Studio Code

1. 按一下 Visual Studio Code 活動列上的 **Azure** 圖示，然後展開 [ **串流分析** ] 節點。 您的工作應該會出現在您的訂用帳戶底下。

   ![開啟串流分析 Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. 若要將作業匯出至本機專案，請在 Visual Studio Code 的 **串流分析 Explorer** 中找出您想要匯出的作業。 然後選取專案的資料夾。

    ![在 Visual Studio Code 中找出 ASA 作業](./media/vscode-explore-jobs/export-job.png)

    專案會匯出至您選取的資料夾，並新增至您目前的工作區。

    ![Visual Studio Code 中的匯出 ASA 作業](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. 若要使用另一個名稱將作業發佈到另一個區域或備份，請在 [查詢編輯器] 中選取 [ **從您** 的訂用帳戶發行] (\* >script.asaql) 並依照指示進行。

    ![在 Visual Studio Code 中發佈至 Azure](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. 遵循將已 [部署的 Azure 串流分析作業匯出至專案指示](./stream-analytics-vs-tools.md#export-jobs-to-a-project)。

2. 在 \* [查詢編輯器] 中開啟 >script.asaql 檔案，選取腳本編輯器中的 [ **提交至 Azure** ]，然後依照指示將作業發佈到另一個區域，或使用新的名稱來備份。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Visual Studio Code 建立串流分析作業](quick-create-visual-studio-code.md)
* [快速入門：使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)
* [使用 Azure Pipelines 來部署具有 CI/CD 的 Azure 串流分析作業](stream-analytics-tools-visual-studio-cicd-vsts.md)