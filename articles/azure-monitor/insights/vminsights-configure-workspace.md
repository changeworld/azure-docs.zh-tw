---
title: 設定適用於 VM 的 Azure 監視器的 Log Analytics 工作區
description: 描述如何建立及設定適用於 VM 的 Azure 監視器所使用的 Log Analytics 工作區。
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 40ff7bfe713f401a23dea0b3660be1b7728a1380
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828552"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>設定適用於 VM 的 Azure 監視器的 Log Analytics 工作區
適用於 VM 的 Azure 監視器會從 Azure 監視器中的一或多個 Log Analytics 工作區收集其資料。 在上架代理程式之前，您必須先建立並設定工作區。 本文說明工作區的需求，並將其設定為適用於 VM 的 Azure 監視器。

## <a name="overview"></a>概觀
單一訂用帳戶可以使用任意數目的工作區，視您的需求而定，工作區的唯一需求是它位於支援的位置，並使用*VMInsights*解決方案進行設定。

設定好工作區之後，您可以使用任何可用的選項，在 VM 和 VMMS 上安裝必要的代理程式，並指定工作區來傳送其資料。 適用於 VM 的 Azure 監視器會從其訂用帳戶中任何已設定的工作區收集資料。

> [!NOTE]
> 當您使用 Azure 入口網站在單一 VM 或 VMMS 上啟用適用於 VM 的 Azure 監視器時，您可以選擇選取現有的工作區，或建立一個新的。 *VMInsights*解決方案將會安裝在此工作區中（如果尚未安裝）。 然後，您可以將此工作區用於其他代理程式。


## <a name="create-log-analytics-workspace"></a>建立 Log Analytics 工作區

>[!NOTE]
>本節中所述的資訊也適用于[服務對應解決方案](service-map.md)。 

從**Log analytics 工作區**功能表，存取 Azure 入口網站中的 log analytics 工作區。

[![記錄 Anlytics 工作區](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

您可以使用下列任何方法來建立新的 Log Analytics 工作區。 如需判斷您應該在環境中使用的工作區數目，以及如何設計其存取策略的指引，請參閱[設計您的 Azure 監視器記錄部署](../platform/design-logs-deployment.md)。


* [Azure 入口網站](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

## <a name="supported-regions"></a>支援區域

適用於 VM 的 Azure 監視器支援下欄區域中的 Log Analytics 工作區，但您可以監視任何區域中的虛擬機器。 虛擬機器本身不限於 Log Analytics 工作區支援的區域。

- 美國中西部
- 美國西部
- 美國西部 2
- 美國中南部
- 美國東部
- 美國東部 2
- 美國中部
- 美國中北部
- US Gov Az
- US Gov Va
- 加拿大中部
- 英國南部
- 北歐
- 西歐
- 東亞
- 東南亞
- 印度中部
- 日本東部
- 澳大利亞東部
- 澳大利亞東南部

## <a name="role-based-access-control"></a>角色型存取控制
若要啟用和存取適用於 VM 的 Azure 監視器中的功能，您必須在工作區中擁有[Log Analytics 參與者角色](../platform/manage-access.md#manage-access-using-azure-permissions)。 若要查看效能、健康情況和對應資料，您必須擁有 Azure VM 的 [[監視讀取](../platform/roles-permissions-security.md#built-in-monitoring-roles)者] 角色。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。

## <a name="add-vminsights-solution-to-workspace"></a>將 VMInsights 解決方案新增至工作區
您必須先安裝*VMInsights*解決方案，Log Analytics 工作區才能與適用於 VM 的 Azure 監視器搭配使用。 下列各節將說明用於設定工作區的方法。

> [!NOTE]
> 當您將*VMInsights*解決方案新增至工作區時，所有連線到該工作區的現有虛擬機器將會開始將資料傳送至 InsightsMetrics。 在您將 Dependency Agent 新增至連線到工作區的現有虛擬機器之前，不會收集其他資料類型的資料。

### <a name="azure-portal"></a>Azure 入口網站
有三個選項可讓您從 Azure 入口網站設定現有的工作區。

若要設定單一工作區，請選取**其他上線選項**，然後**設定工作區**。 選取訂用帳戶和工作區，然後按一下 [**設定**]。

[![設定工作區](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

若要設定多個工作區，請在 Azure 入口網站中 [**監視**] 功能表的 [**虛擬機器**] 功能表中選取 [**工作區**設定] 索引標籤。 設定篩選值，以顯示現有工作區的清單。 選取要啟用的每個工作區旁的方塊，然後按一下 [**設定已選取**]。

[![工作區設定](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


當您使用 Azure 入口網站在單一 VM 或 VMMS 上啟用適用於 VM 的 Azure 監視器時，您可以選擇選取現有的工作區，或建立一個新的。 *VMInsights*解決方案將會安裝在此工作區中（如果尚未安裝）。 然後，您可以將此工作區用於其他代理程式。

[![在入口網站中啟用單一 VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager 範本
適用於 VM 的 Azure 監視器的 Azure Resource Manager 範本提供于封存檔 ( .zip) ，您可以[從我們的 GitHub 存放庫下載](https://aka.ms/VmInsightsARMTemplates)該檔。 這包括名為**ConfigureWorkspace**的範本，它會設定適用於 VM 的 Azure 監視器的 Log Analytics 工作區。 您可以使用任何標準方法來部署此範本，包括下列範例 PowerShell 和 CLI 命令： 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>後續步驟
- 請參閱將代理程式上[架至適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)，將代理程式連接到適用於 VM 的 Azure 監視器。
- 請參閱[Azure 監視器 (Preview) 中的目標監視解決方案](solution-targeting.md)，以限制從解決方案傳送到工作區的資料量。