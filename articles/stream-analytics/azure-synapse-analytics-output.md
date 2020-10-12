---
title: Azure 串流分析的 Azure Synapse Analytics 輸出
description: 本文說明 Azure 串流分析的 Azure Synapse Analytics 作為輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 04c315f593b90204faaeaec562c18e9e4be301d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90881901"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure 串流分析的 Azure Synapse Analytics 輸出

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (先前為 SQL 資料倉儲) 是一種無限制的分析服務，可將企業資料倉儲和巨量資料分析整合在一起。 

Azure 串流分析作業可以輸出至 Azure Synapse Analytics 中的 SQL 集區資料表，而且可以處理高達 200 MB/秒的輸送量速率。這可針對報告和儀表板管理等工作負載，支援最嚴苛的即時分析和忙碌路徑資料處理需求。  

SQL 集區資料表必須先存在，您才能將其當做輸出新增至您的串流分析作業。 資料表結構描述必須符合作業輸出中的欄位及其類型。 

若要使用 Azure Synapse 作為輸出，您必須確定已設定儲存體帳戶。 瀏覽到儲存體帳戶設定以設定儲存體帳戶。 僅允許支援資料表的儲存體帳戶類型：一般用途 V2 和一般用途 V1。 僅選取標準層。 不支援進階層。

## <a name="output-configuration"></a>輸出設定

下表列出屬性名稱及其描述以建立 Azure Synapse Analytics 輸出。

|屬性名稱|描述|
|-|-|
|輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
|資料庫 |您要傳送輸出的 SQL 集區名稱。 |
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