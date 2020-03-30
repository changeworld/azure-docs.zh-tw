---
title: 安裝 Azure Data Lake Tools for Visual Studio
description: 本文說明如何安裝 Azure Data Lake Tools for Visual Studio。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914057"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio

瞭解如何使用視覺化工作室創建 Azure 資料湖分析帳戶。 您可以在[U-SQL](data-lake-analytics-u-sql-get-started.md)中定義作業，並將作業提交到資料湖分析服務。 有關資料湖分析的詳細資訊，請參閱[Azure 資料湖分析概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>Prerequisites

* **Visual Studio**：支援 Express 以外的所有版本。

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1 版或更新版本。 使用 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 來進行安裝。
* **資料湖分析**帳戶。 如需建立帳戶，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>為視覺化工作室 2017 或 2019 年視覺化工作室安裝 Azure 資料湖工具

Visual Studio 2017 15.3 或更高版本支援用於視覺化工作室的 Azure 資料湖工具。 該工具是**資料存儲和處理**以及**Azure 開發**工作負荷的一部分。 安裝 Visual Studio 時，請啟用其中一個工作負載。

啟用**資料儲存和處理**工作負載，如下所示：

![啟用資料存儲和處理工作負載](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

啟用 **Azure 開發**工作負載，如下所示：

![選擇 Azure 開發工作負荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>安裝 Azure Data Lake Tools for Visual Studio 2013 與 2015

下載並安裝[微軟 Azure 資料湖和視覺化工作室的流分析工具](https://aka.ms/adltoolsvs)。 安裝後，Visual Studio 有以下更改：

* **伺服器資源管理器** > **Azure**節點包含**資料湖分析**節點。
* [工具]**** 功能表包含 [Data Lake]**** 項目。

## <a name="next-steps"></a>後續步驟

* 要記錄診斷資訊，請參閱[訪問 Azure 資料湖分析的診斷日誌](data-lake-analytics-diagnostic-logs.md)。
* 若要了解更複雜的查詢，請參閱 [使用 Azure Data Lake Analytics 來分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
* 要使用頂點執行視圖，請參閱[在視覺化工作室使用資料湖工具中的頂點執行視圖](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
