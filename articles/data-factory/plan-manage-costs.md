---
title: 規劃和管理 Azure Data Factory 的成本
description: 本文說明如何規劃和管理 Azure Data Factory 的成本
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: 6271b83749c078b64c168a27f887ed7055e37d96
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678911"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>規劃和管理 Azure Data Factory 的成本

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 是針對雲端規模而建立的無伺服器和彈性資料整合服務。  這表示不需要規劃尖峰負載所需的固定大小計算作業，而能視需要指定每項作業需配置多少資源，如此能以更利於擴充的方式設計 ETL 流程。 此外，ADF 是以耗用量為基礎的方案計費，這表示您只需支付使用的部分。

本文說明如何規劃和管理 Azure Data Factory 的成本。

*   首先，在 ETL 專案的一開始會執行概念證明，並使用單個管線耗用量搭配定價計算機來預估成本。
*   將管線部署到生產環境之後，您可以使用成本管理功能來設定預算和監視成本。 您也可以檢視預測的成本，並找出費用的趨勢。
*   此外，您可以檢視每個管線的耗用量和每個活動的耗用量資訊，以瞭解哪些管線和活動需要最多成本，並找出可降低成本的候選項目。

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>使用管線和活動執行耗用量和定價計算機來預估成本

您可以使用 [ADF 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=data-factory)估計在 Azure Data Factory 中執行 ETL 工作負載的成本。  若要使用計算機，必須輸入詳細資料，例如活動執行數目、資料整合單位時數、用於資料流程的計算類型、核心計數、執行個體計數、執行持續時間等等。

定價計算機的常見問題之一，就是輸入時該使用哪些值。  在概念證明階段中，您可以使用範例資料集來進行試用版的執行，以瞭解各種 ADF 計量的耗用量。  然後，根據範例資料集的耗用量，您可以推估出完整資料集和運算化排程的耗用量。

> [!NOTE]
> 下列範例中使用的價格為假設，並非表示實際定價。

例如，假設您需要每日從 AWS S3 移動 1 TB 的資料到 Azure Data Lake Gen2。  您可以執行移動 100 GB 資料的 POC，以測量資料的內嵌輸送量，並瞭解相對應的計費耗用量。

以下是範例複製活動執行詳細資料 (實際的里程會根據特定資料集、網路速度、S3 帳戶的輸出限制、ADLS Gen2 的輸入限制，以及其他因素而有所不同)。

![S3 複製執行](media/plan-manage-costs/s3-copy-run-details.png)

藉由利用[管線執行層級的耗用量監視](#monitor-consumption-at-pipeline-run-level)，您可以看到對應的資料移動計量耗用量數量：

![S3 複製管線耗用量](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

因此，整個月每日移動 1 TB 所需的 DIU 時數總計為：

1.2667 (DIU-小時) * (1 TB/100 GB) * 30 (一個月中的天數) = 380 DIU 小時

現在，您可以將 30 個活動執行和 380 DIU 小時插入 ADF 定價計算機，以取得每月計量的預估：

![S3 複製定價計算機](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>使用預算和成本警示

您可以建立[預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)來管理成本，以及建立警示，以便在出現異常消費和超支風險時自動通知利害關係人。  警示是以支出為基礎 (相較於預算和成本閾值)。  建立預算時，可以在訂閱層級進行，也可以新增額外的篩選器 (例如資源識別碼和計量名稱) 更為精細地執行。  但您無法建立處理站內個別管線的預算。

## <a name="monitor-costs-at-factory-level"></a>監視工廠層級的成本

當您開始使用 Azure Data Factory 時，您可以在 Azure 入口網站的 [成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md) 窗格中看到產生的成本。

1. 若要檢視[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md)，請開啟 [成本管理 + 計費] 視窗，從功能表中選取 [成本管理]，然後選取 [開啟成本分析]。
2. 預設的視圖會顯示目前月份的累積成本。  您可以切換到不同的時間範圍和不同的資料細微性，例如每日或每月。
3. 若要減少單一服務 (例如 Azure Data Factory) 的成本，請選取 [新增篩選]，然後選取 [服務名稱]。  然後從清單中選擇 [Azure Data Factory v2]。
4. 您可以新增其他篩選準則，以便分析特定處理站執行個體和特定 ADF 計量資料細微性的成本。

   ![成本分析](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>監視管線執行層級的耗用量

根據您在管線中的活動類型、要移動和轉換的資料量，以及轉換的複雜度，執行管線會在 Azure Data Factory 中旋轉不同的計費計量。

您可以在 Azure Data Factory 使用者體驗中，針對個別管線執行的不同計量，查看其耗用量量。 若要開啟監視體驗，請在 [Azure 入口網站](https://portal.azure.com/)的 [資料處理站] 刀鋒視窗中選取 [監視器和管理] 磚。 如果您已經在 ADF UX 中，請按一下左側提要欄位上的 [監視器] 圖示。 預設監視視圖是管線執行的清單。

按一下管線名稱旁邊的 [耗用量] 按鈕，會出現快顯視窗，其中顯示管線執行作業在管線內所有活動上匯總的耗用量。

![管線執行耗用量](media/plan-manage-costs/pipeline-run-consumption.png)

![管線耗用量詳細資料](media/plan-manage-costs/pipeline-consumption-details.png)

[管線執行耗用量] 檢視會顯示每個 ADF 計量針對特定管線執行所耗用的數量，但不會顯示實際費用，因為您所支付的費用取決於您擁有的 Azure 帳戶類型，以及所使用的貨幣類型。  若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](../cost-management-billing/costs/understand-cost-mgt-data.md)。

## <a name="monitor-consumption-at-activity-run-level"></a>監視活動執行層級的耗用量
一旦您瞭解管線執行層級的匯總耗用量，在某些情況下，您需要進一步向下切入並找出管線中最耗費成本的活動。

若要檢視活動執行層級的耗用量，請前往您的資料處理站 [建立者和監視器] UI。 從 [監視器] 索引標籤中，您會看到管線執行清單，按一下 [管線名稱] 連結，即可存取管線執行中的活動執行清單。  按一下活動名稱旁邊的 [輸出] 按鈕，然後在 JSON 輸出中尋找 **billableDuration** 屬性：

以下是來自複製活動執行的範例：

![複製輸出](media/plan-manage-costs/copy-output.png)

以下是來自對應資料流程活動執行的範例：

![資料流程](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入瞭解定價在 Azure Data Factory 中的運作方式：

- [Azure Data Factory 定價頁面](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [透過範例瞭解 Azure Data Factory](./pricing-concepts.md)
- [Azure Data Factory 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=data-factory)