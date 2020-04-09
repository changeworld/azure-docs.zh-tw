---
title: 持續整合及部署
description: 企業級資料庫 DevOps 體驗,用於數據倉庫,內置支援使用 Azure 管道進行持續整合和部署。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c5a326c07c8c2b0e5482361060c25c06d25643c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874138"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>持續整合及部署資料主目錄

此簡易教學課程會概述如何將您的 SQL Server Data tools (SSDT) 資料庫專案與 Azure DevOps 整合，並運用 Azure Pipelines 來設定持續整合及部署。 本教程是構建數據倉庫的持續集成和部署管道的第二步。

## <a name="before-you-begin"></a>開始之前

- 請檢閱[原始檔控制整合教學課程](sql-data-warehouse-source-control-integration.md)

- 設定及連線至 Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>與 Visual Studio 組建持續整合

1. 瀏覽至 Azure Pipelines 並建立新的建置管線。

      ![新增管線](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "新增管線")

2. 選取您的原始程式碼存放庫 (Azure Repos Git)，然後選取 .NET 桌面應用程式範本。

      ![管線設定](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "管線設定")

3. 編輯您的 YAML 檔案，以使用適當的代理程式集區。 YAML 檔案應該會看起來像這樣：

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

此時，您會有一個簡單的環境，而原始檔控制存放庫主要分支的任何簽入，都應該會在資料庫專案中觸發成功的 Visual Studio 組建。 若要驗證自動化是否以端對端的方式運作，您可以在本機資料庫專案中進行變更，並將該變更簽入至您的主要分支。

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>使用 Azure SQL 資料倉儲 (或資料庫) 部署工作進行持續部署

1. 使用 [Azure SQL Database 部署工作](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-sqldb?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)加入新的工作，並填寫必要欄位，即可連線到您的目標資料倉儲。 當此工作執行時，從先前建置程序產生的 DACPAC 就會部署到目標資料倉儲。 您也可以使用 [Azure SQL 倉儲部署工作](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)。

      ![部署工作](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "部署工作")

2. 如果您使用自我裝載式代理程式，請確定您已將環境變數設定為使用 SQL 資料倉儲適用的 SqlPackage.exe。 路徑應該會看起來像這樣：

      ![環境變數](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "環境變數")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   執行及驗證您的管線。 您可以在本機進行變更，並將變更簽入原始檔控制，以產生自動建置和部署。

## <a name="next-steps"></a>後續步驟

- 探索[突觸 SQL 池 MPP 體系結構](massively-parallel-processing-mpp-architecture.md)
- 快速[建立 SQL 集區](create-data-warehouse-portal.md)
- [載入範例資料](load-data-from-azure-blob-storage-using-polybase.md)
- 探索[影片](sql-data-warehouse-videos.md)
