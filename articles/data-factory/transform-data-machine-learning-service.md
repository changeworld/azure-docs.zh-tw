---
title: 執行 Azure 機器學習導管
description: 瞭解如何在 Azure 數據工廠管道中運行 Azure 機器學習管道。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418995"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>在 Azure 資料工廠中執行 Azure 機器學習管道

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 數據工廠管道中運行 Azure 機器學習管道作為步驟。 機器學習執行管道活動支援批處理預測方案,例如識別可能的貸款預設值、確定情緒和分析客戶行為模式。

以下視頻包含 6 分鐘的介紹和演示此功能。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>語法

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
NAME | 管線中的活動名稱 | String | 是
type | 活動類型為"AzureML 執行管道" | String | 是
linkedServiceName | 連結到 Azure 機器學習服務 | 連結服務參考 | 是
毫升里線Id | 已發布的 Azure 機器學習導管的 ID | 字串 (或含有字串之 resultType 的運算式) | 是
實驗名稱 | 執行機器學習管執行的歷史實驗名稱 | 字串 (或含有字串之 resultType 的運算式) | 否
mlPipeline 參數 | 鍵,要傳遞給已發佈的 Azure 機器學習管道終結點的值對。 鍵必須與已發布的機器學習導管中定義的導管參數的名稱符合 | 具有鍵值對的物件(或具有結果類型物件的運算式) | 否
毫升家長魯尼 | 父 Azure 機器學習導管執行 ID | 字串 (或含有字串之 resultType 的運算式) | 否
繼續步排失敗 | 如果步驟失敗,是否繼續執行機器學習管道中的其他步驟 | boolean | 否

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料：

* [執行資料串流活動](control-flow-execute-data-flow-activity.md)
* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [豬活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [火花活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
