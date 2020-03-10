---
title: 持續整合及部署
description: 適用於資料倉儲的企業級資料庫 DevOps 體驗，搭配使用 Azure Pipelines 進行持續整合及部署的內建支援。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb28afc84c423c5c5321a8f948e82f82031888a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195935"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>資料倉儲的持續整合和部署

此簡易教學課程會概述如何將您的 SQL Server Data tools (SSDT) 資料庫專案與 Azure DevOps 整合，並運用 Azure Pipelines 來設定持續整合及部署。 本教學課程是為資料倉儲建置持續整合和部署管線的第二個步驟。 

## <a name="before-you-begin"></a>開始之前

- 請檢閱[原始檔控制整合教學課程](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- 設定及連線至 Azure DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>與 Visual Studio 組建持續整合

1. 瀏覽至 Azure Pipelines 並建立新的建置管線。

      ![新增管線](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "新增管線")

2. 選取您的原始程式碼存放庫 (Azure Repos Git)，然後選取 .NET 桌面應用程式範本。

      ![管線設定](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "管線設定") 

3. 編輯您的 YAML 檔案，以使用適當的代理程式集區。 YAML 檔案應該會看起來像這樣：

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

此時，您會有一個簡單的環境，而原始檔控制存放庫主要分支的任何簽入，都應該會在資料庫專案中觸發成功的 Visual Studio 組建。 若要驗證自動化是否以端對端的方式運作，您可以在本機資料庫專案中進行變更，並將該變更簽入至您的主要分支。


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>使用 Azure SQL 資料倉儲 (或資料庫) 部署工作進行持續部署

1. 使用 [Azure SQL Database 部署工作](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops)加入新的工作，並填寫必要欄位，即可連線到您的目標資料倉儲。 當此工作執行時，從先前建置程序產生的 DACPAC 就會部署到目標資料倉儲。 您也可以使用 [Azure SQL 倉儲部署工作](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)。 

      ![部署工作](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "部署工作")

2. 如果您使用自我裝載式代理程式，請確定您已將環境變數設定為使用 SQL 資料倉儲適用的 SqlPackage.exe。 路徑應該會看起來像這樣：

      ![環境變數](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "環境變數")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   執行及驗證您的管線。 您可以在本機進行變更，並將變更簽入原始檔控制，以產生自動建置和部署。

## <a name="next-steps"></a>後續步驟

- 探索 [SQL 分析 MPP 架構](massively-parallel-processing-mpp-architecture.md)
- 快速[建立 SQL 集區](create-data-warehouse-portal.md)
- [載入範例資料](sql-data-warehouse-load-sample-databases.md)
- 探索[影片](/azure/sql-data-warehouse/sql-data-warehouse-videos)
