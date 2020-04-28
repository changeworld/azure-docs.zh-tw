---
title: 使用 Geo AI
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278423"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>使用地區人工智慧資料科學虛擬機器

使用地區人工智慧 (AI) 資料科學虛擬機器 (VM) 來擷取要分析的資料、執行資料整理 (data wrangling)，以及為取用地理空間資訊的 AI 應用程式建立模型。 在您布建 Geo AI 資料科學 VM 並透過 ArcGIS 帳戶登入 ArcGIS Pro 之後，您就可以開始與 ArcGIS desktop 和 ArcGIs online 互動。 您也可以從 Python 介面和在異地資料科學 VM 上預先設定的 R 語言橋接器來存取 ArcGIS。 若要建立豐富的 AI 應用程式，請將地理資料科學 VM 與機器學習和深度學習架構，以及其他可用的資料科學軟體結合。  


## <a name="configuration-details"></a>組態詳細資料

Python 程式庫[arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)（用來與 ArcGIS 互動）會安裝在位於之資料科學 VM 的全域根 conda 環境中```c:\anaconda```。

- 如果您是在命令提示字元中執行 Python， ```activate```請執行以啟用至 Conda 根 Python 環境。
- 如果您使用的是 IDE 或 Jupyter 筆記本，可以選取環境或核心，以確定您是在正確的 conda 環境中。

ArcGIS 的 R 橋接器會安裝為位於的主要 Microsoft Machine Learning Server 獨立實例中名為[arcgisbinding](https://github.com/R-ArcGIS/r-bridge)的 r 程式庫```C:\Program Files\Microsoft\ML Server\R_SERVER```。 Visual Studio、RStudio 和 Jupyter 已預先設定為使用此 R 環境，並可存取```arcgisbinding``` R 程式庫。 


## <a name="geo-ai-data-science-vm-samples"></a>地區 AI 資料科學 VM 範例

除了來自基底資料科學 VM 的機器學習和深度學習架構範例以外，一組地理空間範例也會提供做為 Geo AI 資料科學 VM 的一部分。 這些範例可協助您使用地理空間資料和 ArcGIS 軟體，快速開始開發 AI 應用程式：


1. 使用[python 開始使用地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：簡介範例示範如何透過 python 介面使用地理空間資料來 ArcGIS，由[arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)程式庫提供。 它也會示範如何結合傳統機器學習服務與地理空間資料，然後在 ArcGIS 的地圖上將結果視覺化。

2. [開始使用 r 的地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：示範如何使用 r 介面來 ArcGIS [arcgisbinding](https://github.com/R-ArcGIS/r-bridge)程式庫所提供的地理空間資料。 

3. [像素層級土地使用分類](https://github.com/Azure/pixel_level_land_classification)：此教學課程說明如何建立可輸入空拍影像、並傳回地表標籤的深度類神經網路模型。 土地標籤的範例包括*重型*和*水*。 模型會針對影像中的每個像素傳回這樣的標籤。 


## <a name="next-steps"></a>後續步驟

此處可找到其他使用資料科學 VM 的範例：

* [範例](dsvm-samples-and-walkthroughs.md)