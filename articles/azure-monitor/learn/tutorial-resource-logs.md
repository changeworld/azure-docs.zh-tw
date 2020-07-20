---
title: 從 Azure 資源收集資源記錄，並使用 Azure 監視器進行分析
description: 將資源記錄從 Azure 資源收集到 Log Analytics 工作區以透過記錄查詢進行分析的診斷設定教學課程。
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269198"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>教學課程：從 Azure 資源收集資源記錄並加以分析

資源記錄可讓您深入了解 Azure 資源的詳細作業，並且可用來監視其健康情況和可用性。 Azure 資源會自動產生資源記錄，但您必須設定要收集記錄的位置。 本教學課程會引導您完成建立診斷設定的程序，讓您可以收集 Azure 訂用帳戶中資源的資源記錄，並使用記錄查詢進行分析。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 監視器中建立 Log Analytics 工作區
> * 建立診斷設定以收集資源記錄 
> * 建立簡單的記錄查詢來分析記錄


## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要可監視的 Azure 資源。 您可以使用 Azure 訂用帳戶中支援診斷設定的任何資源。 若要判斷資源是否支援診斷設定，請移至 Azure 入口網站中的功能表，並確認功能表的 [監視]  區段中有 [診斷設定]  選項。


## <a name="log-in-to-azure"></a>登入 Azure
在 [https://portal.azure.com](https://portal.azure.com) 上登入 Azure 入口網站。


## <a name="create-a-workspace"></a>建立工作區
Azure 監視器中的 Log Analytics 工作區會從各種來源收集記錄資料並編製索引，而且允許使用功能強大的查詢語言進行進階分析。 必須有 Log Analytics 工作區，您才能建立診斷設定來將資料傳送到其中。 您可以使用 Azure 訂用帳戶中的現有工作區，或是使用下列程序建立一個。 

> [!NOTE]
> 雖然您可以在 [Azure 監視器]  功能表的 Log Analytics 工作區中使用資料，但需透過 [Log Analytics 工作區]  功能表建立和管理工作區。

1. 從 [所有服務]  中選取 [Log Analytics 工作區]  。
2. 按一下畫面頂端的 [新增]  ，並提供工作區的下列詳細資料：
   - **Log Analytics 工作區**：新工作區的名稱。 此名稱在所有 Azure 監視器訂用帳戶中必須是全域唯一的。
   - 訂用帳戶  ：選取要儲存工作區的訂用帳戶。 此訂用帳戶不必是受監視資源所在的訂用帳戶。
   - **資源群組**：選取現有的資源群組，或按一下 [新建]  來建立新群組。 此資源群組不必是受監視資源所在的資源群組。
   - **位置**：選取 Azure 區域或建立一個新的位置。 此位置不必是受監視資源所在的位置。
   - **定價層**：選取 [隨用隨付]  作為定價層。 您可以在之後變更此定價層。 按一下 [Log Analytics 定價]  連結，以深入了解不同的定價層。

    ![新增工作區](media/tutorial-resource-logs/new-workspace.png)

3. 按一下 [確定]  以建立工作區。

## <a name="create-a-diagnostic-setting"></a>建立診斷設定
[診斷設定](../platform/diagnostic-settings.md)會定義特定資源的資源記錄應傳送至何處。 單一診斷設定可以有多個[目的地](../platform/diagnostic-settings.md#destinations)，但本教學課程只會使用 Log Analytics 工作區。

1. 在資源功能表的 [監視]  區段中，選取 [診斷設定]  。
2. 您應該會看到「未定義診斷設定」的訊息。 按一下「新增診斷設定」  。

    ![診斷設定](media/tutorial-resource-logs/diagnostic-settings.png)

3. 每個診斷設定都有三個基本部分：
 
   - **Name**：名稱不會有顯著的影響，使用對您而言有描述性的名稱即可。
   - **目的地**：一個或多個要傳送記錄的目的地。 所有 Azure 服務都會共用一組相同的三個可能目的地。 每個診斷設定可以定義一個或多個目的地，但不能有多個特定類型的目的地。 
   - **類別**：要傳送到每個目的地的記錄類別。 每個 Azure 服務的類別集合都會有所不同。

4. 選取 [傳送至 Log Analytics 工作區]  ，然後選取您建立的工作區。
5. 選取您想要收集的類別。 請參閱每個服務的文件，以了解其可用類別的定義。

    ![診斷設定](media/tutorial-resource-logs/diagnostic-setting.png)

6. 按一下 [儲存]  來儲存這些診斷設定。

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>使用記錄查詢來取出記錄
您可使用以 Kusto 查詢語言 (KQL) 撰寫的記錄查詢，從 Log Analytics 工作區中取出資料。 Azure 監視器中的深入解析和解決方案會提供記錄查詢來取出特定服務的資料，但您可以透過 Log Analytics，直接在 Azure 入口網站中使用記錄查詢及其結果。 

1. 在資源功能表的 [監視]  區段中，選取 [記錄]  。
2. Log Analytics 會以空的查詢視窗開啟，並將範圍設定為您的資源。 任何查詢都只會包含來自該資源的記錄。

    > [!NOTE]
    > 如果您從 Azure 監視器功能表開啟記錄，則範圍會設定為 Log Analytics 工作區。 在此情況下，任何查詢都會包含工作區中的所有記錄。
   
    ![記錄](media/tutorial-resource-logs/logs.png)

4. 範例中顯示的服務會將資源記錄寫入 **AzureDiagnostics** 資料表，但其他服務可能會寫入其他資料表。 如需不同 Azure 服務所使用的資料表，請參閱 [Azure 資源記錄的支援服務、結構描述和類別](../platform/diagnostic-logs-schema.md)。

    > [!NOTE]
    > 有多個服務會將資源記錄寫入 AzureDiagnostics 資料表。 如果您從 Azure 監視器功能表啟動 Log Analytics，則需要新增具有 `ResourceProvider` 資料行的 `where` 語句來指定特定服務。 當您從資源功能表啟動 Log Analytics 時，範圍會設定為僅限此資源的記錄，因此不需要此資料行。 請參閱範例查詢的服務文件。


5. 輸入查詢，然後按一下 [執行]  來檢查結果。 
6. 如需撰寫記錄查詢的教學課程，請參閱[開始使用 Azure 監視器中的記錄查詢](../log-query/get-started-queries.md)。

    ![記錄檔查詢](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>後續步驟
現在您已了解如何將資源記錄收集到 Log Analytics 工作區，接著請完成撰寫記錄查詢來分析此資料的教學課程。

> [!div class="nextstepaction"]
> [開始使用 Azure 監視器中的查詢](../log-query/get-started-queries.md)
