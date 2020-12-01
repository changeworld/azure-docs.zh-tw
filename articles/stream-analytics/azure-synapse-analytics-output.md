---
title: Azure 串流分析的 Azure Synapse Analytics 輸出
description: 本文說明 Azure 串流分析的 Azure Synapse Analytics 作為輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 63d03559b2a30020ed2e8bd1cd030855b8fb20b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445097"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure 串流分析的 Azure Synapse Analytics 輸出

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) 是一種無限制的分析服務，可將企業資料倉儲和大型資料分析整合在一起。 

Azure 串流分析作業可以輸出至 Azure Synapse Analytics 中的專用 SQL 集區資料表，且最多可處理 200MB/秒的輸送量速率。這可為工作負載（例如報告和儀表板）支援最嚴苛的即時分析和經常性路徑資料處理需求。  

專用的 SQL 集區資料表必須先存在，您才能將其新增為串流分析作業的輸出。 資料表結構描述必須符合作業輸出中的欄位及其類型。 

若要使用 Azure Synapse 作為輸出，您必須確定已設定儲存體帳戶。 瀏覽到儲存體帳戶設定以設定儲存體帳戶。 僅允許支援資料表的儲存體帳戶類型：一般用途 V2 和一般用途 V1。 僅選取標準層。 不支援進階層。

## <a name="output-configuration"></a>輸出設定

下表列出屬性名稱及其描述以建立 Azure Synapse Analytics 輸出。

|屬性名稱|描述|
|-|-|
|輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
|資料庫 |您要將輸出傳送至其中的專用 SQL 集區名稱。 |
|伺服器名稱 |Azure Synapse 伺服器名稱。  |
|使用者名稱 |具有資料庫寫入存取權的使用者名稱。 串流分析只支援 SQL 驗證。 |
|密碼 |連線到資料庫的密碼。 |
|Table  | 要在其中寫入輸出的資料表名稱。 表格名稱會區分大小寫。 這個資料表的結構描述應該完全符合您作業輸出所產生的欄位數目及其類型。|

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)