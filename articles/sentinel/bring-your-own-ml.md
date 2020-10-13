---
title: 將您自己的 ML 帶入 Azure Sentinel |Microsoft Docs
description: 本文說明如何建立及使用您自己的機器學習演算法，以 Azure Sentinel 中的資料分析。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 17c0ba7306ab4cc51fe8bbe3709d5b6bc85fa487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342689"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>將您自己的 Machine Learning (ML) 帶入 Azure Sentinel

Machine Learning (ML) 是 Azure Sentinel 的其中一個主要結構性支援，以及其中一個設定的主要屬性。 Azure Sentinel 在數個體驗中提供 ML：內建的 [融合](fusion.md) 相互關聯引擎和 Jupyter 筆記本，以及新推出的組建專屬 ML (BYO ml) 平臺。 

ML 偵測模型可以適應個別的環境，以及變更使用者行為，以減少誤報，並找出傳統方法找不到的威脅。 許多安全性組織都瞭解 ML 的安全性價值，但其中不太多都有許多專業人員擁有安全性和 ML 的專業知識。 我們設計了在此提供的架構，讓安全性組織和專業人員能夠在 ML 旅程中與我們一起成長。 如果組織剛開始採用 ML，或沒有必要的專業知識，就能從 Azure Sentinel 的內建 ML 功能獲得顯著的保護價值。

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="機器學習架構":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>什麼是自備 Machine Learning (BYO-ML) 平臺？

針對具有 ML 資源且想要為其獨特商務需求建立自訂 ML 模型的組織，我們提供 **BYO ml 平臺**。 平臺會利用[Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/)環境，並 Jupyter 筆記本來產生 ML 環境。 它提供下列元件：

- BYO ML 封裝，其中包含可協助您存取資料的程式庫，並將結果推送回 Log Analytics (LA) ，因此您可以將結果與偵測、調查和搜尋整合。 

- 供您自訂以符合組織中特定安全性問題的 ML 演算法範本。 

- 定型模型和排程模型計分的範例筆記本。 

除此之外，您可以攜帶自己的 ML 模型，以及/或您自己的 Spark 環境，以與 Azure Sentinel 整合。

使用 BYO ML 平臺，您可以快速開始建立自己的 ML 模型： 

- 具有範例資料的筆記本可協助您取得端對端的實際操作體驗，而不需擔心如何處理生產資料。

- 與 Spark 環境整合的封裝可降低管理基礎結構的挑戰和摩擦。

- 程式庫支援資料移動。 訓練和評分筆記本會示範端對端體驗，並以範本的形式提供給您，以適應您的環境。

### <a name="use-cases"></a>使用案例
 
BYO ML 平臺和套件可大幅減少您建立自己的 ML 偵測所需的時間和精力，並可充分利用 Azure Sentinel 中解決特定安全性問題的能力。 平臺支援下列使用案例：

**將 ML 演算法定型以取得自訂模型：** 您可以採用現有的 ML 演算法 (由 Microsoft 或使用者群體) 共用，並輕鬆地在您自己的資料上定型，以取得更適合您的資料和環境的自訂 ML 模型。

**修改 ML 演算法範本以取得自訂模型：** 您可以修改 Microsoft 所共用的 ML 演算法範本 (，或由使用者社區) ，然後在您自己的資料上定型修改過的演算法，以衍生符合您特定問題的自訂模型。

**建立您自己的模型：** 使用 Azure Sentinel 的 BYO ML 平臺和公用程式，從頭開始建立您自己的模型。

**整合您的 Databricks/Spark 環境：** 將您現有的 Databricks/Spark 環境整合至 Azure Sentinel，並使用 BYO ML 程式庫和範本來為其獨特的情況建立 ML 模型。

匯**入您自己的 ML 模型：** 您可以匯入自己的 ML 模型，並使用 BYO ML 平臺和公用程式，將它們與 Azure Sentinel 整合。

**共用 ML 演算法：** 共用適用于社區的 ML 演算法來採用和調整。

**使用 ML 強化 SecOps：** 使用您自己的自訂 ML 模型和結果來搜尋、偵測、調查和回應。

本文說明 BYO ML 平臺的元件，以及如何利用平臺和異常資源存取演算法，以 Azure Sentinel 提供自訂的 ML 偵測。

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 環境

[Apache Spark™](http://spark.apache.org/) 藉由提供統一的架構來建立資料管線，讓您能夠更快地簡化大型資料。 Azure Databricks 藉由提供以 Spark 為基礎的零管理雲端平臺來進一步採用此方法。 我們建議您針對 BYO ML 平臺使用 Databricks，讓您可以專注于尋找對您的業務造成立即影響的答案，而不是處理資料管線和平臺問題。
如果您已經有 Databricks 或任何其他 Spark 環境，而且想要使用現有的安裝程式，則 BYO ML 套件也會正常運作。 

## <a name="byo-ml-package"></a>BYO-ML 套件

BYO ML 套件包含最佳做法，並可在 ML 前端進行安全性的研究。 在此封裝中，我們會針對安全性問題提供下列公用程式、筆記本和演算法範本清單。

| 檔案名稱 | 描述 |
| --------- | ----------- |
| azure_sentinel_utilities. .whl | 包含從 Azure 讀取 blob 並寫入 Log Analytics 的公用程式。 |
| AnomalousRASampleData | 筆記本會示範如何在 Sentinel 中使用異常資源存取模型，並產生定型和測試範例資料。 |
| AnomalousRATraining. .ipynb | 用來定型演算法、建立和儲存模型的筆記本。 |
| AnomalousRAScoring. .ipynb | 用來排程要執行之模型的筆記本、將結果視覺化，然後將分數寫回 Azure Sentinel。 |
|

我們所提供的第一個 ML 演算法範本適用于 [異常的資源存取偵測](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)。 它是以共同作業篩選演算法為基礎，並且使用 Windows 檔案共用存取記錄進行定型， (具有事件識別碼 5140) 的安全性事件。 在記錄檔中，此模型所需的重要資訊是存取的使用者和資源的配對。 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>範例逐步解說：異常檔案共用存取偵測 

現在您已熟悉 BYO ML 平臺的重要元件，以下範例示範如何使用平臺和元件來提供自訂的 ML 偵測。

### <a name="setup-the-databricksspark-environment"></a>設定 Databricks/Spark 環境

如果您還沒有 Databricks 環境，您將需要設定它。 如需相關指示，請參閱 [Databricks 快速入門](https://docs.microsoft.com/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) 檔。

### <a name="auto-export-instruction"></a>自動匯出指令

若要根據您在 Sentinel 中的資料建立自訂 ML 模型，您必須將資料從 Log Analytics 匯出至 Blob 儲存體或事件中樞資源，讓 ML 模型可以從 Databricks 存取它。 瞭解如何將 [資料內嵌至 Azure Sentinel](connect-data-sources.md)。

在此範例中，您需要有 Azure blob 儲存體中檔案共用存取記錄的定型資料。 資料的格式記載于筆記本和程式庫中。

您可以使用 [Azure 命令列介面 (CLI) ](https://docs.microsoft.com/cli/azure/monitor/log-analytics)，自動將您的資料從 Log Analytics 匯出。 

您必須將 Log Analytics 工作區、儲存體帳戶和 EventHub 資源中的「參與者」角色指派給「 **參與者** 」角色，才能執行命令。 

以下是設定自動匯出的範例命令集：

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>匯出自訂資料

針對 Log Analytics 自動匯出不支援的自訂資料，您可以使用邏輯應用程式或其他解決方案來移動您的資料。 您可以參考將 [Log Analytics 資料匯出至 Blob 存放區](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) 的 blog 和腳本。

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>與 Azure Sentinel 外部的資料相互關聯

您也可以將資料從 Azure Sentinel 外部帶入 blob 儲存體或事件中樞，並將它們與 Sentinel 資料相互關聯，以建立您的 ML 模型。 
 
### <a name="copy-and-install-the-related-packages"></a>複製並安裝相關套件

從上述 Azure Sentinel GitHub 存放庫將 BYO ML 套件複製到您的 Databricks 環境。 然後開啟筆記本，並遵循筆記本中的指示，在您的叢集上安裝所需的程式庫。

### <a name="model-training-and-scoring"></a>模型定型和評分

遵循兩個筆記本中的指示，根據您自己的環境和資源來變更設定，並遵循步驟來定型和建立模型，然後排程模型以對傳入檔案共用存取記錄進行評分。

### <a name="write-results-to-log-analytics"></a>將結果寫入 Log Analytics

一旦您已排定評分，您就可以使用評分筆記本中的模組，將分數結果寫入與 Azure Sentinel 實例相關聯的 Log Analytics 工作區。

### <a name="check-results-in-azure-sentinel"></a>檢查 Azure Sentinel 中的結果

若要查看您的評分結果以及相關記錄的詳細資料，請返回您的 Azure Sentinel 入口網站。 在 **記錄** > 自訂記錄檔中，您會在 **AnomalousResourceAccessResult_CL** 資料表 (或您自己的自訂資料表名稱) 中看到結果。 您可以使用這些結果來增強您的調查和搜尋體驗。

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="機器學習架構":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>使用 ML 結果建立自訂分析規則

一旦您確認 ML 結果是在自訂記錄資料表中，且您對分數的精確度感到滿意，就可以根據結果建立偵測。 從 Azure Sentinel 入口網站移至 [ **分析** ]，並 [建立新的偵測規則](tutorial-detect-threats-custom.md)。 以下範例顯示用來建立偵測的查詢。

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="機器學習架構":::

### <a name="view-and-respond-to-incidents"></a>查看和回應事件
當您根據 ML 結果設定分析規則之後，如果您在查詢中設定的閾值超過閾值，系統就會產生事件，並在 Azure Sentinel 的 [ **事件** ] 頁面上呈現。 

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何使用 Azure Sentinel 的 BYO ML 平臺，來建立或匯入您自己的機器學習演算法，以分析資料並偵測到威脅。

- 請參閱 [Azure Sentinel Blog](https://aka.ms/azuresentinelblog)中有關機器學習和其他許多相關主題的文章。
