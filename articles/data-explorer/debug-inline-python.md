---
title: 使用 VS 代碼調試 Kusto 查詢語言內聯 Python - Azure 資料資源管理器
description: 瞭解如何使用 VS 代碼調試 Kusto 查詢語言 （KQL） 內聯 Python。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444460"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>使用 VS 代碼調試庫斯托查詢語言內聯 Python

Azure 資料資源管理器支援使用[python（） 外掛程式](/azure/kusto/query/pythonplugin)運行嵌入庫斯托查詢語言中的 Python 代碼。 外掛程式運行時託管在沙箱中，這是一個隔離和安全的 Python 環境。 python（） 外掛程式功能擴展了 Kusto 查詢語言本機功能與 OSS Python 包的巨大存檔。 此擴展使您能夠運行高級演算法，如機器學習、人工智慧、統計和時間序列作為查詢的一部分。

Kusto 查詢語言工具對於開發和調試 Python 演算法不方便。 因此，在您最喜愛的 Python 整合式開發環境中開發演算法，例如 Jupyter、PyCharm、VS 或 VS 代碼。 演算法完成後，複製並粘貼到 KQL 中。 為了改進和簡化此工作流，Azure 資料資源管理器支援 Kusto 資源管理器或 Web UI 用戶端與 VS 代碼之間的集成，用於創作和調試 KQL 內聯 Python 代碼。 

> [!NOTE]
> 此工作流只能用於調試相對較小的輸入表（最多 MB）。 因此，您可能需要限制調試的輸入。  如果需要處理大型表，請使用 限制它進行調試`| take``| sample``where rand() < 0.x`。

## <a name="prerequisites"></a>Prerequisites

1. 安裝 Python [Anaconda 分發](https://www.anaconda.com/distribution/#download-section)。 在**高級選項**中，選擇**將 Anaconda 添加到我的 PATH 環境變數**。
2. 安裝[視覺化工作室代碼](https://code.visualstudio.com/Download)
3. 安裝[Visual Studio 代碼的 Python 擴展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

## <a name="run-your-query-in-your-client-application"></a>在用戶端應用程式中執行查詢

1. 在用戶端應用程式中，在包含內聯 Python 的查詢首碼`set query_python_debug;`
1. 執行查詢。
    * Kusto 資源管理器：VS 代碼自動啟動與*debug_python.py*腳本。
    * 庫斯托 Web UI： 
        1. 下載並保存*debug_python.py*debug_python.py，df.txt，和*kargs.txt。* *df.txt* 在視窗中，選擇"**允許**"。 **將檔保存在**選定的目錄中。 

            ![Web UI 下載內聯 python 檔](media/debug-inline-python/webui-inline-python.png)

        1. 按右鍵*debug_python.py，* 然後打開 VS 代碼。 
        *debug_python.py*腳本包含內聯 Python 代碼，來自 KQL 查詢，由範本代碼預定，用於初始化*df.txt*的輸入資料框和來自*kargs.txt*的參數字典。    
            
1. 在 VS 代碼中，啟動 VS 代碼調試器：**調試** > **啟動調試 （F5），** 選擇**Python**配置。 調試器將啟動並自動中中斷點以調試內聯代碼。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS 代碼中的內聯 Python 調試如何工作？

1. 在伺服器中解析和執行查詢，直到達到所需的`| evaluate python()`子句。
1. 調用 Python 沙箱，但它不是運行代碼，而是序列化輸入表、參數字典和代碼，並將它們發送回用戶端。
1. 這三個物件保存在三個檔中 *：df.txt、kargs.txt*和*debug_python.py*在選定的目錄 （Web UI） 或用戶端 %TEMP% 目錄 （Kusto 資源管理器） 中。 *kargs.txt*
1. VS 代碼啟動，預載入*debug_python.py*檔，該檔包含首碼代碼，用於從各自的檔中初始化 df 和 Kargs，然後是嵌入在 KQL 查詢中的 Python 腳本。

## <a name="query-example"></a>查詢範例

1. 在用戶端應用程式中運行以下 KQL 查詢：

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    請參閱生成的表：

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 使用`set query_python_debug;`在用戶端應用程式中運行相同的 KQL 查詢：

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS 代碼啟動：

    ![啟動 VS 代碼](media/debug-inline-python/launch-vs-code.png)

1. VS 代碼在調試主控台中調試並列印"結果"資料幀：

    ![VS 代碼調試](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 沙箱映射和本地安裝之間可能存在差異。 [通過查詢外掛程式，檢查特定包的沙箱圖像](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)。
