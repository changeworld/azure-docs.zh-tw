---
title: 重新設定 Azure-SSIS 整合執行階段
description: 了解如何在佈建 Azure Data Factory 中的 Azure SSIS 整合執行階段後重新設定。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: f609cfb0945d79cfa8ae21b786a5761b92b9dabb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324618"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>重新設定 Azure-SSIS 整合執行階段

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何重新設定現有的 Azure-SSIS 整合執行階段。 若要了解如何在 Azure Data Factory 建立 Azure-SSIS 整合執行階段 (IR)，請瀏覽[建立 Azure SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)，。  

## <a name="data-factory-ui"></a>Data Factory UI 
您可以使用 Data Factory UI 來停止、編輯/重新設定或刪除 Azure-SSIS IR。 

1. 在 Data Factory 首頁上選取 [**作者 & 監視器**] 圖格，以開啟 DATA FACTORY 的 UI。
2. 在 [**首頁** **]、** [**編輯**] 和 [**監視**] 中樞底下選取 [**管理**中樞]，以顯示 [連線] 窗格。

### <a name="to-reconfigure-an-azure-ssis-ir"></a>重新設定 Azure-SSIS IR
在 [管理] 中樞的 [連線] 窗格上，切換至 [整合執行階段] 頁面，然後選取 [重新整理]。 

   ![[連線] 窗格](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   您可以透過選取 Azure-SSIS IR 的名稱來加以編輯/重新設定。 您也可以選取相關按鈕來監視/啟動/停止/刪除 Azure-SSIS IR、自動產生具有執行 SSIS 套件活動的 ADF 管線，以在 Azure-SSIS IR 上執行，以及檢視 Azure-SSIS IR 的 JSON 程式碼/承載。  只有當 Azure-SSIS IR 停止時，才能加以編輯/刪除。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

佈建並啟動 Azure SSIS 整合執行階段的執行個體之後，您可以執行一連串`Stop` - `Set` - `Start` PowerShell Cmdlet 來重新設定。 例如，下列 PowerShell 指令碼會將配置給 Azure-SSIS 整合執行階段執行個體的節點數目變更為五。

### <a name="reconfigure-an-azure-ssis-ir"></a>重新設定 Azure-SSIS IR

1. 首先，使用[AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Cmdlet 來停止 Azure SSIS 整合執行時間。 此命令會釋出其所有節點並停止計費。

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. 接下來，使用 AzDataFactoryV2IntegrationRuntime Cmdlet 來重新[設定](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime)AZURE SSIS IR。 下列範例命令可將 Azure-SSIS 整合執行階段相應放大為五個節點。

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. 然後，使用[AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) Cmdlet 來啟動 Azure SSIS 整合執行時間。 此命令會配置其所有節點以執行 SSIS 套件。   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>刪除 Azure-SSIS IR
1. 首先，列出資料處理站下所有現有的 Azure SSIS IR。

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. 接著，停止資料處理站下所有現有的 Azure SSIS IR。

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. 然後，逐一移除資料處理站下所有現有的 Azure SSIS IR。

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. 最後，移除您的資料處理站。

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. 如果您已建立新的資源群組，請移除此資源群組。

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>後續步驟
如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列主題： 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文提供建立 Azure SSIS IR 的逐步指示，並使用 Azure SQL Database 來裝載 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 這篇文章會詳述教學課程，並提供使用 Azure SQL 受控執行個體以及將 IR 加入虛擬網路的指示。 
- [將 Azure-SSIS IR 加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。 這篇文章提供將 Azure SSIS IR 加入至 Azure 虛擬網路的概念資訊。 它也提供使用 Azure 入口網站來設定虛擬網路，好讓 Azure SSIS IR 可加入虛擬網路的步驟。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。
