---
title: 創建&查詢 Azure 資料湖分析 - Azure 門戶
description: 使用 Azure 入口網站來建立 Azure Data Lake Analytics 帳戶和提交 U-SQL 作業。
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315766"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>使用 Azure 入口網站開始使用 Azure Data Lake Analytics
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

本文說明如何使用 Azure 入口網站建立 Azure Data Lake Analytics 帳戶、在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定義作業，以及將作業提交至 Data Lake Analytics 服務。

## <a name="prerequisites"></a>Prerequisites

在開始本教程之前，必須具有 Azure**訂閱**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-data-lake-analytics-account"></a>建立 Data Lake Analytics 帳戶

現在，您會同時建立 Data Lake Analytics 和 Azure Data Lake Storage Gen1 帳戶。  這個步驟很簡單，只需要大約 60 秒即可完成。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 按一下"**創建資源** >  **資料 + 分析** > **資料湖分析**"。
3. 選取下列項目的值︰
   * **名稱**：替 Data Lake Analytics 帳戶命名 (只允許小寫字母和數字)。
   * **訂用帳戶**：選擇用於分析帳戶的 Azure 訂用帳戶。
   * **資源組**. 選取現有的 Azure 資源群組，或建立一個新的群組。
   * **位置**。 為 Data Lake Analytics 帳戶選取 Azure 資料中心。
   * **Data Lake Storage Gen1**：請依照指示建立新的 Data Lake Storage Gen1 帳戶，或選取現有的帳戶。 
4. (選擇性) 選取 Data Lake Analytics 帳戶的定價層。
5. 按一下 **[建立]**。 


## <a name="your-first-u-sql-script"></a>您的第一個 U-SQL 指令碼

下列文字是很簡單的 U-SQL 指令碼。 該指令碼會定義指令碼內的小型資料集，然後將該資料集寫至預設的 Data Lake Storage Gen1 帳戶，作為名為 `/data.csv` 的檔案。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>提交 U-SQL 作業

1. 從 Data Lake Analytics 帳戶中，選取 [新增作業]****。
2. 貼上前述 U-SQL 指令碼的文字。 為作業命名。 
3. 選取 [提交]**** 按鈕以啟動作業。   
4. 監視作業的 [狀態]****，並等候作業狀態變更為 [成功]****。
5. 選擇"**資料**"選項卡，然後選擇 **"輸出"** 選項卡。選擇命名的`data.csv`輸出檔案並查看輸出資料。

## <a name="see-also"></a>另請參閱

* 若要開始開發 U-SQL 應用程式，請參閱 [使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，請參閱 [開始使用 Azure Data Lake Analytics U-SQL 語言](data-lake-analytics-u-sql-get-started.md)。
* 針對管理工作，請參閱 [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
