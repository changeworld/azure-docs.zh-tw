---
title: 快速入門：使用 Azure PowerShell 建立 Synapse 工作區
description: 遵循本指南中的步驟，使用 Azure PowerShell 建立 Azure Synapse 工作區。
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0537d2353d6b372ed19127101c488b872bbc5224
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218853"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure Synapse 工作區

Azure PowerShell 是一組 Cmdlet，可直接從 PowerShell 管理 Azure 資源。 您可以在瀏覽器中使用它搭配 Azure Cloud Shell。 您也可以將其安裝在 macOS、Linux 或 Windows 上。

在本快速入門中，您會了解如何使用 Azure PowerShell 建立 Synapse 工作區。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Data Lake Storage Gen2 儲存體帳戶](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Azure Synapse 工作區必須能夠讀取和寫入選取的 ADLS Gen2 帳戶。 對於您連結為主要儲存體帳戶的任何儲存體帳戶，您必須在建立儲存體帳戶時啟用 [階層式命名空間]，如[建立儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account)所述。

如果您選擇使用 Cloud Shell，請參閱 [Azure Cloud Shell 的概觀](../cloud-shell/overview.md)以取得詳細資訊。

### <a name="install-the-azure-powershell-module-locally"></a>在本機安裝 Azure PowerShell 模組

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

如需關於使用 Azure PowerShell 進行驗證的詳細資訊，請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps)。

### <a name="install-the-azure-synapse-powershell-module"></a>安裝 Azure Synapse PowerShell 模組

> [!IMPORTANT]
> **Az.Synapse** PowerShell 模組處於預覽狀態，因此您必須使用 `Install-Module` Cmdlet 來將其分開安裝。 此 PowerShell 模組正式推出後，便會成為未來 Az PowerShell 模組版本的一部分，且預設可從 Azure Cloud Shell 內使用。

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>使用 Azure PowerShell 建立 Azure Synapse 工作區

1. 定義必要的環境變數，以建立 Azure Synapse 工作區的資源。

   |        變數名稱        |                                                 描述                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | 現有 ADLS Gen2 儲存體帳戶的名稱。                                                           |
   | StorageAccountResourceGroup | 現有 ADLS Gen2 儲存體帳戶資源群組的名稱。                                             |
   | FileShareName               | 現有儲存體檔案系統的名稱。                                                                  |
   | SynapseResourceGroup        | 為您的 Azure Synapse 資源群組選擇新的名稱。                                                    |
   | 區域                      | 選擇其中一個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/geographies/#overview)。 |
   | SynapseWorkspaceName        | 為新的 Azure Synapse 工作區選擇唯一的名稱。                                                  |
   | SqlUser                     | 選擇新使用者名稱的值。                                                                          |
   | SqlPassword                 | 選擇安全的密碼。                                                                                   |
   | ClientIP                    | 您正在從中執行 PowerShell 的系統所擁有的公用 IP 位址。                                             |
   |                             |                                                                                                             |

1. 建立資源群組作為 Azure Synapse 工作區的容器：

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. 擷取 ADLS Gen 2 儲存體帳戶金鑰：

   ```azurepowershell-interactive
   $StorageAccountKey = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName |
     Select-Object -First 1 -ExpandProperty Value
    ```

1. 擷取 ADLS Gen 2 儲存體端點 URL：

   ```azurepowershell-interactive
   $StorageEndpointUrl = (Get-AzStorageAccount -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName).PrimaryEndpoints.Dfs
   ```

1. (選擇性) 您一律可以查看 ADLS Gen2 的儲存體帳戶金鑰和端點為何：

   ```azurepowershell-interactive
   Write-Output "Storage Account Key: $StorageAccountKey"
   Write-Output "Storage Endpoint URL: $StorageEndpointUrl"
   ```

1. 建立 Azure Synapse 工作區：

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. 取得 Azure Synapse 工作區的 Web 和開發 URL：

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. 建立防火牆規則，以允許從您的電腦存取 Azure Synapse 工作區：

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. 開啟儲存在環境變數 `WorkspaceWeb` 中的 Azure Synapse 工作區 Web URL 位址，以存取您的工作區：

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse 工作區 Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>清除資源

請遵循下列步驟來刪除 Azure Synapse 工作區。

> [!WARNING]
> 刪除 Azure Synapse 工作區將會移除分析引擎和儲存在包含 SQL 集區和工作區繼資料之資料庫中的資料。 您無法再連線到 SQL 或 Apache Spark 端點。 系統將刪除所有程式碼成品 (查詢、筆記本、作業定義和管線)。 刪除工作區 **不會** 影響連結到工作區之 Data Lake Store Gen2 中的資料。

如果您不需要本文所建立的 Azure Synapse 工作區，則可以執行下列範例來將其刪除。

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>後續步驟

接下來，您可以[建立 SQL 集區](quickstart-create-sql-pool-studio.md)或 [建立 Apache Spark 集區](quickstart-create-apache-spark-pool-studio.md)，以開始分析和探索您的資料。