---
title: 什麼是 Azure SQL 資料庫邊緣？ | Microsoft Docs
description: 瞭解 Azure SQL 資料庫邊緣
keywords: sql 資料庫邊緣簡介，什麼是 sql 資料庫邊緣，sql 資料庫邊緣概述
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246699"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>什麼是 Azure SQL 資料庫邊緣預覽？

Azure SQL 資料庫邊緣預覽是一個優化的關係資料庫引擎，適用于 IoT 和 IoT 邊緣部署。 它提供了為 IoT 應用程式和解決方案創建高效能資料存儲和處理層的功能。 Azure SQL 資料庫邊緣提供了流、處理和分析關係和非關係（如 JSON、圖形和時間序列資料）的功能，這使得它成為各種現代 IoT 應用程式的正確選擇。

Azure SQL 資料庫邊緣基於最新版本的 Microsoft [SQL Server 資料庫引擎](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)構建，它提供了業界領先的性能、安全性和查詢處理功能。 由於 Azure SQL 資料庫邊緣構建在 SQL 伺服器和 Azure SQL 資料庫的同一引擎上，它提供了相同的 T-SQL 程式設計表面積，使應用程式或解決方案的開發更輕鬆、更快速，同時使應用程式物聯網邊緣設備、資料中心和雲之間的可攜性。

## <a name="deployment-models"></a>部署模型

Azure SQL 資料庫邊緣在 Azure 應用商店中可用，可以作為[Azure IoT 邊緣的](../iot-edge/about-iot-edge.md)模組進行部署。 有關詳細資訊，請參閱部署[Azure SQL 資料庫邊緣](deploy-portal.md)。<br>

![SQL 資料庫邊緣概覽圖](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL 資料庫邊緣的版本

SQL 資料庫邊緣提供三個不同的版本或軟體計畫。 這些版本具有相同的功能集，並且僅在其使用許可權及其支援的 cpu/記憶體量方面有所不同。

   |**規劃**  |**描述**  |
   |---------|---------|
   |Azure SQL 資料庫邊緣開發人員  |  開發僅 SKU，每個 SQL 資料庫邊緣容器限制為最多 4 個內核和 32 GB 記憶體  |
   |Azure SQL Database Edge    |  生產 sKU，每個 SQL 資料庫邊緣容器限制為最多 8 個內核和 64 GB 記憶體。 |

## <a name="pricing-and-availability"></a>定價和可用性

Azure SQL 資料庫當前處於預覽狀態。 有關定價和可用性的詳細資訊，請參閱 Azure [SQL 資料庫邊緣](https://azure.microsoft.com/services/sql-database-edge/)。

> [!IMPORTANT]
> 要瞭解 Azure SQL 資料庫邊緣和 SQL Server 之間的功能差異，以及不同 Azure SQL 資料庫邊緣選項之間的差異，請參閱[SQL 資料庫邊緣資料庫功能](https://azure.microsoft.com/services/sql-database-edge/)。

## <a name="streaming-capabilities"></a>流式處理功能  

Azure SQL 資料庫邊緣提供內置的流功能，用於即時分析和複雜事件處理。 流式處理功能使用與[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)相同的構造構建，並提供與[IoT 邊緣上的 Azure 流分析](../stream-analytics/stream-analytics-edge.md)類似的功能。

Azure SQL 資料庫邊緣的流式處理引擎專為低延遲、恢復性、高效使用頻寬和合規性而設計。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>機器學習和人工智慧功能

Azure SQL 資料庫邊緣通過集成開放格式 ONNX（開放神經網路交換）運行時提供內置的機器學習和分析功能，從而允許在不同框架之間交換深度學習和神經網路模型。 有關 ONNX 的詳細資訊，請參閱[此處](https://onnx.ai/)。 ONNX 運行時提供了使用您選擇的語言或工具開發模型的靈活性，然後可以轉換為 ONNX 格式，在 SQL 資料庫邊緣內執行。 有關詳細資訊，請參閱[SQL 資料庫邊緣中的使用 ONNX 的機器學習和人工智慧](onnx-overview.md)。

## <a name="working-with-azure-sql-database-edge"></a>使用 Azure SQL 資料庫邊緣

Azure SQL 資料庫邊緣使開發和維護應用程式變得更加輕鬆和高效。 使用者可以使用所有熟悉的工具和技能來構建出色的應用和解決方案，以滿足其 IoT Edge 需求。 使用者可以使用以下工具在 SQL 資料庫邊緣進行開發：

- [Azure 門戶](https://portal.azure.com/)- 用於管理所有 Azure 服務的基於 Web 的應用程式。
- [SQL 伺服器管理工作室](/sql/ssms/download-sql-server-management-studio-ssms/)- 一個免費的、可下載的用戶端應用程式，用於管理從 SQL Server 到 SQL 資料庫的任何 SQL 基礎結構。
- [Visual Studio 中的 SQL Server 資料工具](/sql/ssdt/download-sql-server-data-tools-ssdt/)- 用於開發 SQL Server 關係資料庫、SQL 資料庫、整合服務包、分析服務資料模型和報表服務報告的免費、可下載的用戶端應用程式。
- [Azure 資料工作室](/sql/azure-data-studio/what-is/)- 一種免費、可下載的跨平臺資料庫工具，適用于資料專業人員，使用 Windows、MacOS 和 Linux 上的 Microsoft 本地和雲資料平臺系列。
- [視覺化工作室代碼](https://code.visualstudio.com/docs)- 適用于 Windows、macOS 和 Linux 的免費、可下載的開原始程式碼編輯器。 它支援擴展，包括用於查詢 Microsoft SQL Server、Azure SQL 資料庫和 Azure SQL 資料倉儲的[mssql 擴展](https://aka.ms/mssql-marketplace)。


## <a name="next-steps"></a>後續步驟

- 有關定價和可用性的詳細資訊，請參閱[Azure SQL 資料庫邊緣](https://azure.microsoft.com/services/sql-database-edge/)。
- 請求為訂閱啟用 Azure SQL 資料庫邊緣。
- 要開始，請參閱以下內容：
  - [通過 Azure 門戶部署 SQL 資料庫邊緣](deploy-portal.md)
  - [具有 SQL 資料庫邊緣的機器學習和人工智慧](onnx-overview.md)
