---
title: 創建映射資料流程
description: 如何創建 Azure 資料工廠映射資料流程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 2eb455ba6fa40538bfa03018be47232066036c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930433"
---
# <a name="create-azure-data-factory-data-flow"></a>建立 Azure Data Factory 資料流程



對應 ADF 中的資料流程可以大規模轉換資料，完全不需要撰寫任何程式碼。 您可以建構一系列轉換，以便在資料流設計工具中設計資料轉換作業。 先進行不限次數的來源轉換，然後進行資料轉換步驟。 接著，使用接收完成資料流程，以便將結果置於目的地中。

首先從 Azure 門戶創建新的 V2 資料工廠。 建立新的中心之後，按一下「作者與監視器」磚以啟動 Data Factory 使用者介面。

![資料流程選項](media/data-flow/v2portal.png "資料流程創建")

您在 Data Factory UI 中時，可以使用範例資料流程。 可以從 ADF 範本資源庫中取得這些範例。 在 ADF 中，建立「範本管線」，並從範本資源庫中選取資料流程類別。

![資料流程選項](media/data-flow/template.png "資料流程創建")

系統會提示您輸入 Azure Blob 儲存體帳戶資訊。

![資料流程選項](media/data-flow/template2.png "資料流程創建 2")

[這些範例所使用的資料可以在這裡找到](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata)。 下載範例資料，並將檔案儲存在您的 Azure Blob 儲存體帳戶上，以便您執行範例。

## <a name="create-new-data-flow"></a>創建新的資料流程

使用 ADF UI 中的"添加符號"按鈕創建資料流程。

![資料流程選項](media/data-flow/newresource.png "新資源")

## <a name="next-steps"></a>後續步驟

開始使用[源轉換](data-flow-source.md)構建資料轉換。
