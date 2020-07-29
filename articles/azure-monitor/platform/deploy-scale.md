---
title: 部署 Azure 監視器
description: 使用 Azure 原則大規模部署 Azure 監視器功能。
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 043edae04c6de5d42849cf43b947b9646f12f489
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317400"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>使用 Azure 原則大規模部署 Azure 監視器
雖然部分 Azure 監視器功能只會設定一次或有限的次數，但有些則必須針對您想要監視的每個資源重複。 本文說明使用 Azure 原則來大規模執行 Azure 監視器的方法，以確保一致且正確地設定所有 Azure 資源的監視。

例如，您必須針對所有現有的 Azure 資源，以及您所建立的每個新資源建立診斷設定。 您也必須在每次建立虛擬機器時，安裝並設定代理程式。 您可以使用 PowerShell 或 CLI 之類的方法來執行這些動作，因為這些方法適用于 Azure 監視器的所有功能。 使用 Azure 原則，您可以將邏輯備妥，每次建立或修改資源時，都會自動執行適當的設定。


## <a name="azure-policy"></a>Azure 原則
本節提供[Azure 原則](../../governance/policy/overview.md)的簡介，讓您以最輕鬆的方式，在整個 Azure 訂用帳戶或管理群組中評估及強制執行組織標準。 如需完整詳細資料，請參閱[Azure 原則檔](../../governance/policy/overview.md)。

使用 Azure 原則您可以指定所建立資源的設定需求，並識別不符合規範的資源、封鎖建立資源，或新增所需的設定。 其運作方式是攔截呼叫以建立新的資源，或修改現有的資源。 如果不符合原則定義中預期的屬性、將其標示為不符合規範，或部署相關資源，則它可以回應為拒絕要求的結果。 您可以使用**deployIfNotExists**或**修改**原則定義來補救現有的資源。

Azure 原則包含下表中的物件。 如需各項的詳細說明，請參閱[Azure 原則物件](../../governance/policy/overview.md#azure-policy-objects)。

| 項目 | 說明 |
|:---|:---|
| 原則定義 | 描述資源合規性條件，以及符合條件時所要採取的效果。 這可能是特定類型的所有資源，或僅符合特定屬性的資源。 其效果可能只是將資源標示為符合規範，或部署相關資源。 原則定義是使用 JSON 來撰寫，如[Azure 原則定義結構](../../governance/policy/concepts/definition-structure.md)中所述。 [瞭解 Azure 原則效果](../../governance/policy/concepts/effects.md)中會描述效果。
| 原則計畫 | 應該一起套用的原則定義群組。 例如，您可能會有一個原則定義，可將資源記錄傳送到 Log Analytics 工作區，另一個則會將資源記錄傳送至事件中樞。 建立一個包含原則定義的方案，並將該計畫套用至資源，而不是個別的原則定義。 計畫是使用 JSON 來撰寫，如[Azure 原則計畫結構](../../governance/policy/concepts/initiative-definition-structure.md)中所述。 |
| 指派 | 原則定義或計畫在指派給範圍之前不會生效。 例如，將原則指派給資源群組，將它套用至在該資源中建立的所有資源，或將它套用至訂用帳戶，以將它套用至該訂用帳戶中的所有資源。  如需詳細資訊，請參閱[Azure 原則指派結構](../../governance/policy/concepts/assignment-structure.md)。 |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>適用於 Azure 監視器的內建原則定義
Azure 原則包含數個與 Azure 監視器相關的預先建立定義。 您可以將這些原則定義指派給現有的訂用帳戶，或使用它們作為基礎，以建立您自己的自訂定義。 如需 [**監視**] 類別中內建政治的完整清單，請參閱[Azure 監視器的 Azure 原則內建原則定義](../samples/policy-samples.md)。

若要查看與監視相關的內建原則定義，請執行下列動作：

1. 移至 Azure 入口網站中的**Azure 原則**。
2. 選取 [**定義**]。
3. 針對 [**類型**]，選取 [*內建*]，針對 [**類別**] 選取 [*監視*]。

  ![內建原則定義](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>診斷設定
[診斷設定](./diagnostic-settings.md)會將資源記錄和計量從 Azure 資源收集到多個位置，通常是在 log Analytics 工作區，可讓您使用[記錄查詢](../log-query/log-query-overview.md)和[記錄警示](alerts-log.md)來分析資料。 每當您建立資源時，請使用原則自動建立診斷設定。

每個 Azure 資源類型都有一組唯一的類別，必須列在 [診斷] 設定中。 因此，每個資源類型都需要個別的原則定義。 某些資源類型具有內建原則定義，您可以在不修改的情況下指派。 針對其他資源類型，您必須建立自訂定義。

### <a name="built-in-policy-definitions-for-azure-monitor"></a>適用於 Azure 監視器的內建原則定義
每個資源類型有兩個內建原則定義，一個用於傳送至 Log Analytics 工作區，另一個用於事件中樞。 如果您只需要一個位置，請為該資源類型指派該原則。 如果您同時需要這兩個，請指派資源的原則定義。

例如，下圖顯示 Data Lake Analytics 的內建診斷設定原則定義。

  ![內建原則定義](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>自訂原則定義
對於沒有內建原則的資源類型，您必須建立自訂原則定義。 您可以在 Azure 入口網站中手動執行此動作，方法是複製現有的內建原則，然後修改您的資源類型。 不過，使用 PowerShell 資源庫中的腳本以程式設計方式建立原則會更有效率。

[AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy)的腳本會建立特定資源類型的原則檔，您可以使用 POWERSHELL 或 CLI 來進行安裝。 使用下列程式來建立診斷設定的自訂原則定義。


1. 請確定您已安裝[Azure PowerShell](/powershell/azure/install-az-ps) 。
2. 使用下列命令安裝腳本：
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. 使用參數來執行腳本，以指定要將記錄檔傳送到何處。 系統會提示您指定訂用帳戶和資源類型。 例如，若要建立傳送至 Log Analytics 工作區和事件中樞的原則定義，請使用下列命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. 或者，您可以在命令中指定訂用帳戶和資源類型。 例如，若要建立原則定義以傳送至 Log Analytics 工作區，並將 Azure SQL Server 資料庫的事件中樞，請使用下列命令。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. 此腳本會針對每個原則定義建立個別的資料夾，每個都包含三個名為 azurepolicy.js的檔案，azurepolicy.rules.js開啟，azurepolicy.parameters.js開啟。 如果您想要在 Azure 入口網站中手動建立原則，您可以複製並貼上 azurepolicy.js的內容，因為它包含整個原則定義。 使用其他兩個檔案搭配 PowerShell 或 CLI，從命令列建立原則定義。

    下列範例示範如何從 PowerShell 和 CLI 安裝原則定義。 每個都包含中繼資料來指定**監視**的類別，以使用內建原則定義將新的原則定義群組在一起。

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>方案
與其為每個原則定義建立指派，常見的策略是建立一個包含原則定義的方案，以建立每個 Azure 服務的診斷設定。 根據您管理環境的方式，在計畫和管理群組、訂用帳戶或資源群組之間建立指派。 此策略提供下列優點：

- 為此計畫建立單一指派，而不是每個資源類型的多個指派。 針對多個監視群組、訂用帳戶或資源群組使用相同的方案。
- 當您需要加入新的資源類型或目的地時，請修改此計畫。 例如，您的初始需求可能是僅將資料傳送至 Log Analytics 工作區，但稍後您想要新增事件中樞。 修改計畫，而不是建立新的指派。

如需建立計畫的詳細資訊，請參閱[建立及指派計畫定義](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)。 請考量下列建議事項：

- 將 [**類別**] 設定為 [**監視**]，使用相關的內建和自訂原則定義將它分組。
- 請使用一般的計畫參數，而不是指定 Log Analytics 工作區的詳細資料，以及計畫中所包含原則定義的事件中樞。 這可讓您輕鬆地指定所有原則定義的通用值，並在必要時變更該值。

![計畫定義](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>指派 
視您要監視的資源範圍而定，將方案指派給 Azure 管理群組、訂用帳戶或資源群組。 如果您的組織有多個訂用帳戶，[管理群組](../../governance/management-groups/overview.md)特別適合用於範圍設定原則。

![方案指派](media/deploy-scale/initiative-assignment.png)

藉由使用計畫參數，您可以針對計畫中的所有原則定義，指定工作區或任何其他詳細資料。 

![計畫參數](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>修復
此計畫會在每個虛擬機器建立時套用至該應用程式。 [補救](../../governance/policy/how-to/remediate-resources.md)工作會將計畫中的原則定義部署至現有的資源，因此您可以為已建立的任何資源建立診斷設定。 當您使用 Azure 入口網站建立指派時，可以選擇同時建立補救工作。 如需補救的詳細資料，請參閱[使用 Azure 原則補救不符合規範的資源](../../governance/policy/how-to/remediate-resources.md)。

![計畫補救](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms-and-virtual-machine-agents"></a>適用於 VM 的 Azure 監視器和虛擬機器代理程式
[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)是監視虛擬機器和虛擬機器擴展集 Azure 監視器中的主要工具。 若要啟用適用於 VM 的 Azure 監視器您必須在每個用戶端上安裝 Log Analytics 代理程式和 Dependency agent。 您也可以自行安裝 Log Analytics 代理程式，以支援其他監視案例。 請使用 Azure 原則，確保每個虛擬機器在建立時都已設定，而不是手動執行這些工作。

> [!NOTE]
> 適用於 VM 的 Azure 監視器包含稱為**適用於 VM 的 Azure 監視器原則涵蓋範圍**的功能，可讓您在環境中探索及補救不相容的 vm。 您可以使用這項功能，而不是直接使用適用于 Azure Vm 的 Azure 原則，以及與 Azure Arc 連線的混合式虛擬機器。針對 Azure 虛擬機器擴展集，您必須使用 Azure 原則建立指派。
 

適用於 VM 的 Azure 監視器包含下列內建計畫，可安裝這兩個代理程式來啟用完整監視。 

|Name |說明 |
|:---|:---|
|啟用適用於 VM 的 Azure 監視器 | 在 Azure Vm 和與 Azure Arc 連線的混合式 Vm 上安裝 Log Analytics 代理程式和相依性代理程式。 |
|啟用虛擬機器擴展集的 Azure 監視器 | 在 Azure 虛擬機器擴展集上安裝 Log Analytics 代理程式和相依性代理程式。 |


### <a name="virtual-machines"></a>虛擬機器
適用於 VM 的 Azure 監視器包含的功能可讓您檢查每個範圍中的虛擬機器數目，以判斷是否已套用此計畫，而不是使用 Azure 原則介面來建立這些計畫的指派。 接著，您可以使用該介面來設定工作區，並建立任何必要的指派。

如需此程式的詳細資訊，請參閱[使用 Azure 原則啟用適用於 VM 的 Azure 監視器](../insights/vminsights-enable-at-scale-policy.md)。

![適用於 VM 的 Azure 監視器原則](../platform/media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集
若要使用 Azure 原則來啟用虛擬機器擴展集的監視，請根據您要監視的資源範圍，將 [**啟用虛擬機器擴展集的 Azure 監視器**] 方案指派給 Azure 管理群組、訂用帳戶或資源群組。 如果您的組織有多個訂用帳戶，[管理群組](../../governance/management-groups/overview.md)特別適合用於範圍設定原則。

![方案指派](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

選取要將資料傳送至其中的工作區。 此工作區必須安裝*VMInsights*解決方案，如中所述 []() 。

![選取工作區](media/deploy-scale/virtual-machine-scale-set-workspace.png)

如果您有需要指派此原則的現有虛擬機器擴展集，請建立補救工作。

![補救工作](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics 代理程式
在某些情況下，您可能會想要安裝 Log Analytics 代理程式，而不是 dependency agent。 只有代理程式沒有內建方案，但是您可以根據適用於 VM 的 Azure 監視器提供的內建原則定義來建立自己的計畫。

> [!NOTE]
> 不需要自行部署相依性代理程式，因為它會要求 Log Analytics 代理程式將其資料傳遞給 Azure 監視器。


|Name |說明 |
|-----|------------|
|Audit Log Analytics 代理程式部署–未列出的 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將 Vm 報告為不相容。 |
|部署適用於 Linux VM 的 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux Vm 的 Log Analytics 代理程式。 |
|部署適用於 Windows VM 的 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows Vm 的 Log Analytics 代理程式。 |
| [預覽]： Log Analytics 代理程式應該安裝在您的 Linux Azure Arc 機器上 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請將混合式 Azure Arc 機器報告為 Linux Vm 不相容。 |
| [預覽]： Log Analytics 代理程式應該安裝在您的 Windows Azure Arc 機器上 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請將混合式 Azure Arc 機器回報為 Windows Vm 不符合規範。 |
| [預覽]：將 Log Analytics 代理程式部署至 Linux Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 混合式 Azure Arc 機器的 Log Analytics 代理程式。 |
| [預覽]：將 Log Analytics 代理程式部署至 Windows Azure Arc 機器 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows 混合式 Azure Arc 機器的 Log Analytics 代理程式。 |
|在虛擬機器擴展集中進行 Audit Dependency agent 部署-未列出 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將虛擬機器擴展集報告為不相容。 |
|在虛擬機器擴展集中進行 Audit Log Analytics 代理程式部署-未列出 VM 映射（OS） |如果 VM 映射（OS）未在清單中定義且未安裝代理程式，則將虛擬機器擴展集報告為不相容。 |
|為 Linux 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Linux 虛擬機器擴展集的 Log Analytics 代理程式。 |
|為 Windows 虛擬機器擴展集部署 Log Analytics 代理程式 |如果 VM 映射（OS）是在清單中定義且未安裝代理程式，請部署適用于 Windows 虛擬機器擴展集的 Log Analytics 代理程式。 |


## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 原則](../../governance/policy/overview.md)。
- 深入瞭解[診斷設定](diagnostic-settings.md)。

