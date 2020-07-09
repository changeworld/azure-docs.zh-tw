---
title: 安裝 Azure Data Lake Tools for Visual Studio
description: 本文說明如何安裝 Azure Data Lake Tools for Visual Studio。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d153ba1d00c0e43a5809503e047fb66713b7fb7c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104923"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio

瞭解如何使用 Visual Studio 建立 Azure Data Lake Analytics 帳戶。 您可以在 [ [U-SQL](data-lake-analytics-u-sql-get-started.md) ] 中定義作業，並將作業提交至 Data Lake Analytics 服務。 如需 Data Lake Analytics 的詳細資訊，請參閱[Azure Data Lake Analytics 總覽](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>必要條件

* **Visual Studio**：支援 Express 以外的所有版本。

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1 版或更新版本。 使用 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 來進行安裝。
* **Data Lake Analytics**帳戶。 如需建立帳戶，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>安裝 Azure Data Lake Tools for Visual Studio 2017 或 Visual Studio 2019

Visual Studio 2017 15.3 或更新版本中支援 Azure Data Lake Tools for Visual Studio。 此工具是**資料儲存和處理**和**Azure 開發**工作負載的一部分。 安裝 Visual Studio 時，請啟用其中一個工作負載。

啟用**資料儲存和處理**工作負載，如下所示：

![啟用資料儲存和處理工作負載](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

啟用 **Azure 開發**工作負載，如下所示：

![選取 Azure 開發工作負載](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>安裝 Azure Data Lake Tools for Visual Studio 2013 與 2015

下載並安裝[適用于 Visual Studio 的 Microsoft Azure Data Lake 和串流分析工具](https://aka.ms/adltoolsvs)。 安裝之後，Visual Studio 會進行下列變更：

* **伺服器總管**的  >  **Azure**節點包含**Data Lake Analytics**節點。
* [工具]**** 功能表包含 [Data Lake]**** 項目。

## <a name="next-steps"></a>後續步驟

* 若要記錄診斷資訊，請參閱[存取 Azure Data Lake Analytics 的診斷記錄](data-lake-analytics-diagnostic-logs.md)。
* 若要了解更複雜的查詢，請參閱 [使用 Azure Data Lake Analytics 來分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
* 若要使用頂點執行視圖，請參閱[在適用于 Visual Studio 的 Data Lake 工具中使用頂點執行視圖](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
