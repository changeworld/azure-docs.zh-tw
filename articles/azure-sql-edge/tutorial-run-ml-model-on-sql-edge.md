---
title: 使用 ONNX 在 Azure SQL Edge 上部署 ML 模型
description: 在此 Azure SQL Edge 教學課程三部曲的第三部分中，您會在 SQL Edge 上執行 ONNX 機器學習模型，以預測鐵礦中的雜質含量。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422190"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>使用 ONNX 在 Azure SQL Edge 上部署 ML 模型 

在此教學課程三部曲的第三部分中，若要在 Azure SQL Edge 中預測鐵礦中的雜質含量，您必須：

1. 使用 Azure Data Studio 連線到 Azure SQL Edge 執行個體中的 SQL Database。
2. 使用 Azure SQL Edge 中的 ONNX 來預測鐵礦中的雜質含量。

## <a name="key-components"></a>重要元件

1. 解決方案在傳送至 Edge 中樞的每個訊息之間使用預設的 500 毫秒。 這可以在 **Program.cs** 檔案中變更 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. 解決方案產生了具有下列屬性的訊息。 依據需求新增或移除屬性。 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>連線到 Azure SQL Edge 執行個體中的 SQL Database，以定型、部署及測試 ML 模型

1. 開啟 Azure Data Studio。

2. 在 **歡迎使用** 索引標籤中，使用下列詳細資料開始新的連線：

   |_欄位_|_ReplTest1_|
   |-------|-------|
   |連線類型| Microsoft SQL Server|
   |伺服器|在此示範中建立的 VM 中提到的公用 IP 位址|
   |使用者名稱|sa|
   |密碼|建立 Azure SQL Edge 執行個體時所使用的強式密碼|
   |資料庫|預設|
   |伺服器群組|預設|
   |名稱 (選用)|提供選用的名稱|

3. 按一下 [連接] 

4. 在 [檔案] 區段中，從您在其中複製專案檔的資料夾開啟 **/DeploymentScripts/MiningProcess_ONNX.jpynb**。

5. 將核心設定為 Python 3。


## <a name="next-steps"></a>後續步驟

如需在 Azure SQL Edge 中使用 ONNX 模型的詳細資訊，請參閱[在 SQL Edge 中使用 ONNX 的機器學習和 AI](onnx-overview.md)。