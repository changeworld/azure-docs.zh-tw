---
title: 建立對應資料流程
description: 如何建立 Azure Data Factory 對應資料流程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: eaf36cc2690b3c0f8922c05432b3197b4ff30d9a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026049"
---
# <a name="create-azure-data-factory-data-flow"></a>建立 Azure Data Factory 資料流程

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

對應 ADF 中的資料流程可以大規模轉換資料，完全不需要撰寫任何程式碼。 您可以建構一系列轉換，以便在資料流設計工具中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 接著，使用接收完成資料流程，以便將結果置於目的地中。

先從 Azure 入口網站建立新的 V2 Data Factory 開始著手。 建立新的中心之後，按一下「作者與監視器」磚以啟動 Data Factory 使用者介面。

![螢幕擷取畫面顯示 [新增資料處理站] 窗格，其中已選取 [版本] 的 V2。](media/data-flow/v2portal.png "資料流程建立")

您在 Data Factory UI 中時，可以使用範例資料流程。 可以從 ADF 範本資源庫中取得這些範例。 在 ADF 中，建立「範本管線」，並從範本資源庫中選取資料流程類別。

![螢幕擷取畫面顯示 [資料流程] 索引標籤，其中包含 [使用選取的資料流程轉換資料]。](media/data-flow/template.png "資料流程建立")

系統會提示您輸入 Azure Blob 儲存體帳戶資訊。

![螢幕擷取畫面顯示使用 [資料流程] 窗格來輸入使用者輸入的轉換資料。](media/data-flow/template2.png "資料流程建立2")

[這些範例所使用的資料可以在這裡找到](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 下載範例資料，並將檔案儲存在您的 Azure Blob 儲存體帳戶上，以便您執行範例。

## <a name="create-new-data-flow"></a>建立新的資料流程

使用 ADF UI 中的 [建立資源] [加號] 按鈕來建立資料流程。

![螢幕擷取畫面顯示從 [Factory 資源] 功能表選取的資料流程。](media/data-flow/newresource.png "新增資源")

## <a name="next-steps"></a>後續步驟

使用 [來源轉換](data-flow-source.md)開始建立您的資料轉換。
