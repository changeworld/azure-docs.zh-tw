---
title: Azure Data Lake Analytics 中的 U-SQL 認知功能
description: 瞭解如何在 SQL-DMO 中使用認知功能的智慧。 此程式碼範例可協助您開始使用。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/05/2018
ms.openlocfilehash: 19d947b8b595107c76c1201d05e4d5ade3d7a092
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220052"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>開始使用 U-SQL 的辨識功能

## <a name="overview"></a>總覽
U-SQL 的辨識功能讓開發人員可以在其公司的巨量資料程式中使用 put 智慧。 

下列使用認知功能的範例可供使用：
* 影像：偵測 [臉部](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 影像：偵測 [表情](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 影像：偵測 [物件 (標記) ](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 映射： [OCR (光學字元辨識) ](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 文字：[關鍵片語擷取與情感分析](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>在 U-SQL 中註冊認知擴充功能
開始之前，請遵循本文章中的步驟，在 U-SQL 中註冊認知擴充功能：[在 U-SQL 中註冊認知擴充功能](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)。

## <a name="next-steps"></a>後續步驟
* [U-SQL/認知範例](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](./data-lake-analytics-u-sql-get-started.md)