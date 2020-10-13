---
title: 使用 Azure 原則大規模部署 Azure 監視器
description: 使用 Azure 原則大規模部署 Azure 監視器功能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: 4ec7cd2b0f573a9a74f82546da2367edcf721539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441454"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>使用 Azure 原則大規模部署 Azure 監視器
雖然某些 Azure 監視器功能設定為一次或有限的次數，但有些則必須針對您要監視的每個資源重複。 本文說明使用 Azure 原則來大規模執行 Azure 監視器的方法，以確保針對您的所有 Azure 資源一致且正確地設定監視。

例如，您需要為所有現有的 Azure 資源和您建立的每個新資源建立診斷設定。 您也必須在每次建立虛擬機器時，安裝並設定代理程式。 您可以使用 PowerShell 或 CLI 之類的方法來執行這些動作，因為這些方法適用于 Azure 監視器的所有功能。 使用 Azure 原則時，您可以使用邏輯，在每次建立或修改資源時都會自動執行適當的設定。


## <a name="azure-policy"></a>Azure 原則
本節提供 [Azure 原則](../governance/policy/overview.md) 的簡介，讓您可以在整個 Azure 訂用帳戶或管理群組中，輕鬆地評定及強制執行組織標準。 如需完整的詳細資料，請參閱 [Azure 原則檔](../governance/policy/overview.md) 。

您可以使用 Azure 原則指定所建立之任何資源的設定需求，並識別不符合規範的資源、封鎖建立資源，或新增必要的設定。 其運作方式是攔截呼叫以建立新的資源，或修改現有的資源。 如果不符合原則定義中所預期的屬性、將其標示為不符合規範，或部署相關的資源，則會以拒絕要求的方式回應此類效果。 您可以使用 **deployIfNotExists** 或 **修改** 原則定義來補救現有的資源。

Azure 原則包含下表中的物件。 如需每個物件的詳細說明，請參閱 [Azure 原則物件](../governance/policy/overview.md#azure-policy-objects) 。

| 項目 | 描述 |
|:---|:---|
| 原則定義 | 描述資源合規性條件以及符合條件時所要採取的效果。 這可能是特定類型的所有資源，或僅符合特定屬性的資源。 效果可能只是將資源標示為符合規範，或部署相關的資源。 原則定義是使用 JSON 所撰寫，如 [Azure 原則定義結構](../governance/policy/concepts/definition-structure.md)中所述。 [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)中會說明效果。
| 原則方案 | 應一起套用的一組原則定義。 例如，您可能會有一個原則定義，可將資源記錄傳送至 Log Analytics 工作區，另一個則會將資源記錄傳送至事件中樞。 建立包含這兩種原則定義的計畫，並將該計畫套用至資源，而不是個別的原則定義。 方案是使用 JSON 撰寫，如 [Azure 原則計畫結構](../governance/policy/concepts/initiative-definition-structure.md)中所述。 |
| 指派 | 原則定義或方案在指派給某個範圍之前都不會生效。 例如，將原則指派給資源群組，以將其套用至在該資源中建立的所有資源，或將其套用至訂用帳戶，以將其套用至該訂用帳戶中的所有資源。  如需詳細資訊，請參閱 [Azure 原則指派結構](../governance/policy/concepts/assignment-structure.md)。 |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>適用於 Azure 監視器的內建原則定義
Azure 原則包含數個與 Azure 監視器相關的預建定義。 您可以將這些原則定義指派給現有的訂用帳戶，或使用它們作為基礎來建立您自己的自訂定義。 如需 **監視** 類別內內建政治的完整清單，請參閱 Azure 監視器的 [Azure 原則內建原則定義](samples/policy-samples.md)。

若要查看與監視相關的內建原則定義，請執行下列動作：

1. 移至 Azure 入口網站中的 **Azure 原則** 。
2. 選取 [ **定義**]。
3. 在 [ **類型**] 中，選取 [ *內建* ] 和 [ **類別**]，然後選取 [ *監視*]。

  ![Azure 入口網站中 [Azure 原則定義] 頁面的螢幕擷取畫面，其中顯示監視類別和內建類型的原則定義清單。](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>診斷設定
[診斷設定](platform/diagnostic-settings.md) 會將資源記錄和計量從 Azure 資源收集到多個位置，通常是 log Analytics 工作區，可讓您使用 [記錄查詢](log-query/log-query-overview.md) 和 [記錄警示](platform/alerts-log.md)來分析資料。 使用原則，在您每次建立資源時自動建立診斷設定。

每個 Azure 資源類型都有一組唯一的類別，必須列在診斷設定中。 因此，每個資源類型都需要個別的原則定義。 有些資源類型具有內建原則定義，您無須修改即可指派這些定義。 針對其他資源類型，您必須建立自訂定義。

### <a name="built-in-policy-definitions-for-azure-monitor"></a>適用於 Azure 監視器的內建原則定義
每個資源類型都有兩個內建原則定義，一個是傳送至 Log Analytics 工作區，另一個用於事件中樞。 如果您只需要一個位置，請為該資源類型指派該原則。 如果您同時需要這兩個原則定義，請為資源指派這兩個原則定義。

例如，下圖顯示 Data Lake Analytics 的內建診斷設定原則定義。

  ![[Azure 原則定義] 頁面的部分螢幕擷取畫面，其中顯示 Data Lake Analytics 的兩個內建診斷設定原則定義。](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>自訂原則定義
針對沒有內建原則的資源類型，您必須建立自訂原則定義。 您可以藉由複製現有的內建原則，然後針對您的資源類型進行修改，在 Azure 入口網站中手動完成這項作業。 不過，使用 PowerShell 資源庫中的腳本，以程式設計方式建立原則會更有效率。

腳本 [AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) 會建立特定資源類型的原則檔案，您可以使用 POWERSHELL 或 CLI 來進行安裝。 使用下列程式來建立診斷設定的自訂原則定義。


1. 確定您已安裝 [Azure PowerShell](/powershell/azure/install-az-ps) 。
2. 使用下列命令來安裝腳本：
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 使用參數來執行腳本，以指定要將記錄傳送至何處。 系統會提示您指定訂用帳戶和資源類型。 例如，若要建立傳送至 Log Analytics 工作區和事件中樞的原則定義，請使用下列命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 或者，您可以在命令中指定訂用帳戶和資源類型。 例如，若要建立一個原則定義，以傳送至 Log Analytics 工作區和適用于 Azure SQL Server 資料庫的事件中樞，請使用下列命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 腳本會為每個原則定義建立個別的資料夾，每個都包含三個名為 azurepolicy.json、azurepolicy.rules.json、azurepolicy.parameters.js的檔案。 如果您想要以手動方式在 Azure 入口網站中建立原則，您可以複製並貼上 azurepolicy.js的內容，因為它包含整個原則定義。 透過 PowerShell 或 CLI 使用其他兩個檔案，從命令列建立原則定義。

    下列範例示範如何從 PowerShell 和 CLI 安裝原則定義。 每個都包含用來指定 **監視** 類別的中繼資料，以使用內建原則定義將新的原則定義群組在一起。

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>方案
與其建立每個原則定義的指派，常見的策略是建立包含原則定義的方案，以建立每個 Azure 服務的診斷設定。 依據您管理環境的方式，在方案與管理群組、訂用帳戶或資源群組之間建立指派。 此策略提供下列優點：

- 為方案建立單一指派，而不是針對每個資源類型建立多個指派。 針對多個監視群組、訂用帳戶或資源群組，請使用相同的方案。
- 當您需要新增新的資源類型或目的地時，請修改此計畫。 例如，您的初始需求可能只會將資料傳送到 Log Analytics 工作區，但稍後您會想要新增事件中樞。 修改方案，而不是建立新的指派。

如需建立方案的詳細資訊，請參閱 [建立和指派計畫定義](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) 。 請考量下列建議事項：

- 將 **類別** 設定為 [ **監視** ]，以將相關的內建和自訂原則定義群組在一起。
- 請使用常見的計畫參數，而不是針對方案中包含的原則定義指定 Log Analytics 工作區和事件中樞的詳細資料。 這可讓您輕鬆地為所有原則定義指定通用值，並視需要變更該值。

![計畫定義](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>指派 
根據您要監視的資源範圍，將計畫指派給 Azure 管理群組、訂用帳戶或資源群組。 如果您的組織有多個訂用帳戶， [管理群組](../governance/management-groups/overview.md) 特別適用于範圍原則。

![螢幕擷取畫面： Azure 入口網站中 Log Analytics 工作區的 [指派計畫] 區段中，[基本] 索引標籤的 [基本] 索引標籤設定。](media/deploy-scale/initiative-assignment.png)

您可以使用計畫參數，針對方案中的所有原則定義指定工作區或任何其他詳細資料。 

![計畫參數](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>修復
此計畫會在每個虛擬機器建立時套用。 [補救](../governance/policy/how-to/remediate-resources.md)工作會將計畫中的原則定義部署到現有的資源，如此一來，您就可以針對任何已建立的資源建立診斷設定。 當您使用 Azure 入口網站建立指派時，可以選擇同時建立補救工作。 如需補救的詳細資訊，請參閱 [使用 Azure 原則補救不符合規範的資源](../governance/policy/how-to/remediate-resources.md) 。

![計畫補救](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md) 是 Azure 監視器中用來監視虛擬機器的主要工具。 啟用適用於 VM 的 Azure 監視器會安裝 Log Analytics 代理程式和 Dependency agent。 請使用 Azure 原則，以確保每個虛擬機器在建立時都已設定，而不是手動執行這些工作。

> [!NOTE]
> 適用於 VM 的 Azure 監視器包含稱為 **適用於 VM 的 Azure 監視器原則涵蓋範圍** 的功能，可讓您在環境中探索及補救不符合規範的 vm。 您可以使用這項功能，而不是直接使用適用于 Azure Vm 的 Azure 原則以及與 Azure Arc 連線的混合式虛擬機器。針對 Azure 虛擬機器擴展集，您必須使用 Azure 原則建立指派。
 

適用於 VM 的 Azure 監視器包含下列內建的計畫，可安裝兩個代理程式來啟用完整的監視。 

|名稱 |描述 |
|:---|:---|
|啟用適用於 VM 的 Azure 監視器 | 在與 Azure Arc 連線的 Azure Vm 和混合式 Vm 上安裝 Log Analytics 代理程式和 Dependency agent。 |
|啟用虛擬機器擴展集的 Azure 監視器 | 在 Azure 虛擬機器擴展集上安裝 Log Analytics 代理程式和 Dependency agent。 |


### <a name="virtual-machines"></a>虛擬機器
適用於 VM 的 Azure 監視器包含的功能可讓您檢查每個領域中的虛擬機器數目，以判斷是否已套用此計畫，而不是使用 Azure 原則介面建立這些計畫的指派。 然後，您可以使用該介面來設定工作區並建立任何必要的指派。

如需此程式的詳細資訊，請參閱 [使用 Azure 原則啟用適用於 VM 的 Azure 監視器](./insights/vminsights-enable-policy.md)。

![適用於 VM 的 Azure 監視器原則](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集
若要使用 Azure 原則來啟用虛擬機器擴展集的監視，請根據您要監視的資源範圍，將 **虛擬機器擴展集計畫的啟用 Azure 監視器** 指派給 Azure 管理群組、訂用帳戶或資源群組。 如果您的組織有多個訂用帳戶， [管理群組](../governance/management-groups/overview.md) 特別適用于範圍原則。

![Azure 入口網站中 [指派計畫] 頁面的螢幕擷取畫面。 方案定義會設定為啟用虛擬機器擴展集的 Azure 監視器。](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

選取將傳送資料的工作區。 此工作區必須安裝 *VMInsights* 解決方案，如中所述 []() 。

![選取工作區](media/deploy-scale/virtual-machine-scale-set-workspace.png)

如果您有需要指派此原則的現有虛擬機器擴展集，請建立補救工作。

![補救工作](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics 代理程式
您可能會有想要安裝 Log Analytics 代理程式，但不是相依性代理程式的案例。 代理程式沒有內建的方案，但您可以根據適用於 VM 的 Azure 監視器提供的內建原則定義來建立您自己的方案。

> [!NOTE]
> 不需要自行部署相依性代理程式，因為它需要 Log Analytics 代理程式將其資料傳遞至 Azure 監視器。


|名稱 |描述 |
|-----|------------|
|Audit Log Analytics 代理程式部署-未列出 VM 映射 (OS)  |如果 VM 映射 (OS) 未在清單中定義且未安裝代理程式，請將 Vm 報告為不相容。 |
|部署適用于 Linux Vm 的 Log Analytics 代理程式 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Linux Vm 的 Log Analytics 代理程式。 |
|部署適用于 Windows Vm 的 Log Analytics 代理程式 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Windows Vm 的 Log Analytics 代理程式。 |
| [預覽]：您的 Linux Azure Arc 電腦上應安裝 Log Analytics 代理程式 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，則會將混合式 Azure Arc 電腦報告為 Linux Vm 的不相容。 |
| [預覽]： Log Analytics 代理程式應該安裝在 Windows Azure Arc 電腦上 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，則會將混合式 Azure Arc 電腦報告為不相容的 Windows Vm。 |
| [預覽]：將 Log Analytics 代理程式部署到 Linux Azure Arc 機 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Linux 混合式 Azure Arc 機的 Log Analytics 代理程式。 |
| [預覽]：將 Log Analytics 代理程式部署至 Windows Azure Arc 機 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Windows 混合式 Azure Arc 機的 Log Analytics 代理程式。 |
|在虛擬機器擴展集中審核相依性代理程式部署-未列出 VM 映射 (OS)  |如果 VM 映射 (OS) 未在清單中定義且未安裝代理程式，請將虛擬機器擴展集報告為不相容。 |
|在虛擬機器擴展集中審核記錄分析代理程式部署-未列出 VM 映射 (OS)  |如果 VM 映射 (OS) 未在清單中定義且未安裝代理程式，請將虛擬機器擴展集報告為不相容。 |
|為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Linux 虛擬機器擴展集的 Log Analytics 代理程式。 |
|為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射 (OS) 定義于清單中，而且未安裝代理程式，請部署適用于 Windows 虛擬機器擴展集的 Log Analytics 代理程式。 |


## <a name="next-steps"></a>後續步驟

- 閱讀 [Azure 原則](../governance/policy/overview.md)的詳細資訊。
- 深入瞭解 [診斷設定](platform/diagnostic-settings.md)。