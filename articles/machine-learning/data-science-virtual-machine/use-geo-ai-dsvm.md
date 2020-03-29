---
title: 使用地理 AI
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何使用地區 AI 資料科學虛擬機器來分析資料，並依據地理空間資料建立模型。
keywords: 深度學習、AI、資料科學工具、資料科學虛擬機器、地理空間分析
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278423"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>使用地區人工智慧資料科學虛擬機器

使用地區人工智慧 (AI) 資料科學虛擬機器 (VM) 來擷取要分析的資料、執行資料整理 (data wrangling)，以及為取用地理空間資訊的 AI 應用程式建立模型。 預配 Geo AI 資料科學 VM 並通過 ArcGIS 帳戶登錄到 ArcGIS Pro 後，即可開始連線與 ArcGIS 桌面和 ArcGI 交互。 您還可以從 Python 介面和在地理資料科學 VM 上預配置的 R 語言橋接器訪問 ArcGIS。 要構建豐富的 AI 應用程式，請將地理資料科學 VM 與機器學習和深度學習框架以及其上可用的其他資料科學軟體相結合。  


## <a name="configuration-details"></a>組態詳細資料

Python 庫[arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)用於與 ArcGIS 介面，安裝在 資料科學 VM 的全域根環境中，該環境位於 。 ```c:\anaconda```

- 如果在命令提示符下運行 Python，請運行```activate```以啟動 conda 根 Python 環境。
- 如果您使用的是 IDE 或 Jupyter 筆記本，則可以選擇環境或內核，以確保您處於正確的 conda 環境中。

R 橋到 ArcGIS 作為 R 庫安裝，稱為[arcgisbinding，](https://github.com/R-ArcGIS/r-bridge)位於 的主 Microsoft 機器學習伺服器獨立```C:\Program Files\Microsoft\ML Server\R_SERVER```實例中。 Visual Studio、RStudio 和 Jupyter 已預先配置為使用此 R 環境，並將有權訪問```arcgisbinding```R 庫。 


## <a name="geo-ai-data-science-vm-samples"></a>地區 AI 資料科學 VM 範例

除了來自基礎資料科學 VM 的機器學習和基於深度學習框架的樣本外，還提供一組地理空間樣本作為地理 AI 資料科學 VM 的一部分。 這些示例可以説明您通過使用地理空間資料和 ArcGIS 軟體快速啟動 AI 應用程式的開發：


1. [使用 Python 開始進行地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)[：Arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)庫提供了一個介紹性示例，演示如何通過 Python 介面與 ArcGIS 處理地理空間資料。 它還演示如何將傳統機器學習與地理空間資料相結合，然後在 ArcGIS 中的地圖上視覺化結果。

2. [開始使用 R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)： 開始地理空間分析 ：一個介紹性示例，演示如何使用[由 Arcgis 綁定](https://github.com/R-ArcGIS/r-bridge)庫提供的與 ArcGIS 的 R 介面處理地理空間資料。 

3. [像素層級土地使用分類](https://github.com/Azure/pixel_level_land_classification)：此教學課程說明如何建立可輸入空拍影像、並傳回地表標籤的深度類神經網路模型。 土地覆蓋標籤的例子有*森林**和水。* 模型會針對影像中的每個像素傳回這樣的標籤。 


## <a name="next-steps"></a>後續步驟

此處可找到其他使用資料科學 VM 的範例：

* [樣品](dsvm-samples-and-walkthroughs.md)