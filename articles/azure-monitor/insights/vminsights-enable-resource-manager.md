---
title: 使用 Resource Manager 範本啟用適用於 VM 的 Azure 監視器
description: 本文說明如何使用 Azure PowerShell 或 Azure Resource Manager 範本，啟用一或多個 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: cee7072f9bc844fb1f89168de3547dc726472b67
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695904"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>使用 Resource Manager 範本啟用適用於 VM 的 Azure 監視器
本文說明如何使用 Resource Manager 範本來啟用虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。 此程式可用於下列各項：

- Azure 虛擬機器
- Azure 虛擬機器擴展集
- 與 Azure Arc 連線的混合式虛擬機器

## <a name="prerequisites"></a>必要條件

- [建立並設定 Log Analytics 工作區](vminsights-configure-workspace.md)。 
- 請參閱 [支援的作業系統](vminsights-enable-overview.md#supported-operating-systems) ，以確保支援您所啟用之虛擬機器或虛擬機器擴展集的作業系統。 

## <a name="resource-manager-templates"></a>Resource Manager 範本

我們已建立範例 Azure Resource Manager 範本，讓您將虛擬機器和虛擬機器擴展集上架。 這些範本包括您可以用來在現有資源上啟用監視的案例，以及建立已啟用監視的新資源。

>[!NOTE]
>範本必須部署在與要啟用的虛擬機器或虛擬機器擴展集相同的資源群組中。


您可以從我們的 GitHub 存放庫 [下載](https://aka.ms/VmInsightsARMTemplates) ( .zip) 封存檔中提供 Azure Resource Manager 範本。 檔案的內容包括使用範本和參數檔案來代表每個部署案例的資料夾。 在執行之前，請修改參數檔案，並指定所需的值。 

下載檔案包含下列適用于不同案例的範本：

- 如果虛擬機器已存在， **ExistingVmOnboarding** 範本會啟用適用於 VM 的 Azure 監視器。
- **NewVmOnboarding** 範本會建立虛擬機器，並啟用適用於 VM 的 Azure 監視器來監視它。
- 如果虛擬機器擴展集已存在， **ExistingVmssOnboarding** 範本會啟用適用於 VM 的 Azure 監視器。
- **NewVmssOnboarding** 範本會建立虛擬機器擴展集，並可讓適用於 VM 的 Azure 監視器監視它們。
- **ConfigureWorkspace** 範本會設定您的 Log Analytics 工作區以支援適用於 VM 的 Azure 監視器，方法是啟用解決方案和集合的 Linux 和 Windows 作業系統效能計數器。

>[!NOTE]
>如果虛擬機器擴展集已存在，且升級原則設定為 [ **手動**]，則在執行 **ExistingVmssOnboarding** Azure Resource Manager 範本之後，預設不會啟用實例的適用於 VM 的 Azure 監視器。 您必須手動升級實例。

## <a name="deploy-templates"></a>部署範本
您可以使用 [Resource Manager 範本的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md) 來部署範本，包括使用 POWERSHELL 和 CLI 的下列範例。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>後續步驟

現在已為您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱 [View AZURE VM performance](vminsights-performance.md)。
