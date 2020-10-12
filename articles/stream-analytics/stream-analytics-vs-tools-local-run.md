---
title: 在 Visual Studio 的本機測試 Azure 串流分析查詢
description: 本文說明如何使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試查詢。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: aa1d8ff0743399d96436f33426ebc4c4ab4a29f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045632"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>使用 Visual Studio 在本機測試串流分析查詢

您可以使用 Azure 串流分析工具進行 Visual Studio，利用範例資料或 [即時資料](stream-analytics-live-data-local-testing.md)在本機測試您的串流分析作業。 

使用此[快速入門](stream-analytics-quick-create-vs.md)了解如何使用 Visual Studio 建立串流分析工作。

## <a name="test-your-query"></a>測試查詢

在 Azure 串流分析專案中，按兩下 **Script.asaql** 以在編輯器中開啟指令碼。 您可以編譯查詢以查看是否有任何語法錯誤。 查詢編輯器支援 IntelliSense、語法著色和錯誤標記。

![查詢編輯器](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>新增本機輸入

若要對本機靜態資料驗證您的查詢，請以滑鼠右鍵按一下輸入並選取 [新增本機輸入]****。
   
![新增本機輸入](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
在快顯視窗中，從本機路徑選取範例資料並 [儲存]****。
   
![新增本機輸入](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
系統會自動在您的輸入資料夾新增名為 **local_EntryStream.json** 的檔案。
   
![本機輸入資料夾檔案清單](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
在查詢編輯器中選取 [在本機執行]****。 或者，您可以按 F5。
   
![在本機執行](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
可以直接從 Visual Studio 以表格格式檢視輸出。

![表格格式的輸出](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

您可以從主控台輸出中尋找輸出路徑。 按任何鍵以開啟結果資料夾。
   
![本機執行](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
檢查本機資料夾中的結果。
   
![本機資料夾結果](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>範例輸入
您也可以從您的輸入來源將範例輸入資料收集到本機檔案。 以滑鼠右鍵按一下輸入設定檔，然後選取 [範例資料]****。 

![取樣資料](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

您只能對來自事件中樞或 IoT 中樞的資料流取樣。 不支援其他輸入來源。 在快顯對話方塊中，填入用來儲存範例資料的本機路徑並選取 [範例]****。

![範例資料設定](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
您可以在 [ **輸出** ] 視窗中看到進度。 

![範例資料輸出](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>接下來的步驟

* [快速入門：使用 Visual Studio 建立串流分析工作](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具 (預覽) 在本機測試即時資料](stream-analytics-live-data-local-testing.md)
* [教學課程：使用 Azure DevOps 部署 CI/CD 的 Azure 串流分析作業](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [使用串流分析工具持續進行整合及開發](stream-analytics-tools-for-visual-studio-cicd.md)
