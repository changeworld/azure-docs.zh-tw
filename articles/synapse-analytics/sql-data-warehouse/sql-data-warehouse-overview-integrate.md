---
title: 建置整合式解決方案
description: 與 Azure Synapse Analytics 中的專用 SQL 集區整合的解決方案工具和合作夥伴。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9f92128266c41912868f6ab74abaa2d374c6d236
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324492"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>將其他服務與 Azure Synapse Analytics 中的專用 SQL 集區整合。

Azure Synapse Analytics 內專用的 SQL 集區功能，可讓使用者與 Azure 中的許多其他服務整合。 使用 Synapse SQL，您可以透過其專用的 SQL 集區資源來建立資料倉儲，然後使用數個額外的服務，其中包括：

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure 串流分析

如需有關在 Azure 之間整合服務的詳細資訊，請參閱[整合合作夥伴](sql-data-warehouse-partner-data-integration.md)一文。

## <a name="power-bi"></a>Power BI

Power BI 整合可讓您結合資料倉儲的計算能力以及 Power BI 的動態報告和視覺效果。 目前 Power BI 整合包括：

* **Direct Connect** ：對使用專用 SQL 集區布建的資料倉儲進行邏輯下推的更先進連接。 下推可提供更快速且更大規模的分析。
* **在 Power BI 中開啟** ：[在 Power BI 中開啟] 按鈕會傳遞執行個體資訊給 Power BI，提供簡化的連線方式。

如需詳細資訊，請參閱[與 Power BI 整合](sql-data-warehouse-get-started-visualize-with-power-bi.md)或 [Power BI 文件](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)。

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory 提供使用者一個受控平台，以建立複雜的擷取和載入管線。 專用的 SQL 集區與 Azure Data Factory 整合包括：

* **預存程序** ：協調預存程序的執行。
* **複製** ：使用 ADF 將資料移至專用的 SQL 集區。 這項作業可以在幕後使用 ADF 的標準資料移動機制或 PolyBase。

如需詳細資訊，請參閱[與 Azure Data Factory 整合](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning 是完全受控的分析服務，可讓您使用一組大型預測工具建立複雜模型。 專用的 SQL 集區支援作為這些模型的來源和目的地，並具有下列功能：

* **讀取資料：** 針對專用的 SQL 集區使用 T-sql 大規模驅動模型。
* **寫入資料：** 將任何模型的變更認可回專用的 SQL 集區。

如需詳細資訊，請參閱[與 Azure Machine Learning 整合](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 串流分析

Azure 串流分析是複雜、完全受控的基礎結構，可處理和取用產生自 Azure 事件中樞的事件資料。  與專用 SQL 集區整合可讓串流資料與關聯式資料進行有效的處理和儲存，讓分析更深入、更先進。  

* **作業輸出：** 將串流分析作業的輸出直接傳送到專用的 SQL 集區。

如需詳細資訊，請參閱[與 Azure 串流分析整合](sql-data-warehouse-integrate-azure-stream-analytics.md)。
