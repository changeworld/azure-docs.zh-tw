---
title: Azure Data Lake Analytics 中的 U-SQL 認知功能
description: 瞭解如何在 SQL-DMO 中使用認知功能的智慧。 此程式碼範例可協助您開始使用。
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/05/2018
ms.openlocfilehash: 35973f3453daf61ee18b04b68e2fee4dd2e52402
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87125474"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>開始使用 U-SQL 的辨識功能

## <a name="overview"></a>概觀
U-SQL 的辨識功能讓開發人員可以在其公司的巨量資料程式中使用 put 智慧。 

下列使用認知功能的範例可供使用：
* 影像：偵測 [臉部](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 影像：偵測 [表情](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 影像：偵測 [物件 (標記) ](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 映射： [OCR (光學字元辨識) ](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 文字：[關鍵片語擷取與情感分析](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>在 U-SQL 中註冊認知擴充功能
開始之前，請遵循本文章中的步驟，在 U-SQL 中註冊認知擴充功能：[在 U-SQL 中註冊認知擴充功能](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)。

## <a name="next-steps"></a>接下來的步驟
* [U-SQL/認知範例](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
