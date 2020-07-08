---
title: Azure Data Factory 的 Azure PowerShell 範例
description: Azure PowerShell 範例：協助您建立和管理 Data Factory 的指令碼。
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: c640adb0758c8dfd9dcdae3e26fd023afd5499f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85252265"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>適用於 Azure Data Factory 的 Azure PowerShell 範例

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

下表包含適用於 Azure Data Factory 之範例 Azure PowerShell 指令碼的連結。

| |  |
|---|---|
|**複製資料**||
|[在 Azure Blob 儲存體中將 Blob 從某個資料夾複製到另一個資料夾](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會將 Blob 從 Azure Blob 儲存體中的某個資料夾，複製到相同 Blob 儲存體中的另一個資料夾。 |
|[將資料從 SQL Server 複製到 Azure Blob 儲存體](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 腳本會將資料從 SQL Server 資料庫複製到 Azure blob 儲存體。 |
|[大量複製](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此範例 PowerShell 腳本會將 Azure SQL Database 資料庫中的多個資料表中的資料複製到 Azure SQL 資料倉儲。 |
|[增量複製](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 這個範例 PowerShell 指令碼會在初次將資料從來源完整複製到接收器之後，只將來源資料存放區中新的或已更新的記錄，載入到接收資料存放區。 |
|**轉換資料**||
|[使用 Spark 叢集轉換資料](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼可藉由在 Spark 叢集上執行程式來轉換資料。 |
|**將 SSIS 套件隨即轉移至 Azure**||
|[建立 Azure-SSIS 整合執行階段](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 指令碼會佈建 Azure-SSIS 整合執行階段，以在 Azure 中執行 SQL Server Integration Services (SSIS) 套件。 |



