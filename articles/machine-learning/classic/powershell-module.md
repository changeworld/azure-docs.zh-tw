---
title: ML Studio (傳統) ： PowerShell 模組-Azure
description: 使用 PowerShell 來建立和管理 Azure Machine Learning Studio (傳統) 工作區、實驗、web 服務等。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: f66363ec8b64dea1a076f81f4fc89bafe5ca4151
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322327"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>適用于 Azure Machine Learning Studio (傳統) 的 PowerShell 模組

**適用於：** ![適用於。](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)   ![不適用於。 ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


您可以使用 PowerShell 模組，以程式設計方式管理您的 Studio (傳統) 資源和資產，例如工作區、資料集和 web 服務。

您可以使用三個 PowerShell 模組，與 Studio (傳統) 資源互動：

* 2018 年發行的 [Azure PowerShell Az](#az-rm) (包含 AzureRM 的所有功能)，雖然使用不同的 Cmdlet 名稱
* [AzureRM](#az-rm) 發行于2016中，由 PowerShell Az 取代
* 2016 年發行的 [Azure Machine Learning Studio 傳統模組](#classic)

雖然這些 PowerShell 模組有一些相似之處，但每個模組都是針對特定案例所設計。 本文說明 PowerShell 模組之間的差異，並協助您決定要選擇哪一個模組。  

檢查以下[支援表格](#support-table)，以查看每個模組所支援的資源。 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az 和 AzureRM

Az 現在是預計用來與 Azure 互動的 PowerShell 模組，而且包含 AzureRM 所有先前的功能。 AzureRM 將持續收到錯誤 (bug) 修正，但不會收到任何新的 Cmdlet 或功能。  Az 和 AzureRM 兩者都會管理使用 **Azure Resource Manager** 部署模型部署的解決方案。 這些資源包括 Studio (傳統) 工作區和 Studio (傳統) 「新的」 web 服務。 

PowerShell 傳統可以隨 Az 或 AzureRM 一起安裝，以涵蓋「新」和「傳統」資源類型。 不過，不建議同時安裝 Az 和 AzureRM。 若要在 Az 與 AzureRM 之間做決定，Microsoft 建議將 Az 用於所有未來部署。  深入瞭解 Az 與 AzureRM 以及 [Azure PowerShell Az 簡介](/powershell/azure/new-azureps-module-az)中的遷移路徑。

若要開始使用 Az，請遵循 [Azure Az 安裝指示](/powershell/azure/install-az-ps)。

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell 傳統

Studio (傳統) [PowerShell 傳統模組](https://aka.ms/amlps) 可讓您管理使用 **傳統部署模型** 部署的資源。 這些資源包括 Studio (傳統) 使用者資產、「傳統」 web 服務和「傳統」 web 服務端點。

不過，Microsoft 建議您針對所有未來的資源使用 Resource Manager 部署模型，以簡化資源的部署和管理。 如果您要深入了解部署模型，請參閱 [Azure Resource Manager 與傳統部署](../../azure-resource-manager/management/deployment-models.md)一文。

若要開始使用 PowerShell 傳統模組，請從 GitHub 下載[發行套件](https://github.com/hning86/azuremlps/releases)並遵循[安裝指示](https://github.com/hning86/azuremlps/blob/master/README.md)。 指示說明如何解除封鎖已下載/解壓縮的 DLL，然後再匯入 PowerShell 環境。

PowerShell 傳統可以隨 Az 或 AzureRM 一起安裝，以涵蓋「新」和「傳統」資源類型。

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell 支援表格


| Task | **Az** |  **PowerShell 傳統** |
| --- | --- | --- |
| 建立/刪除工作區 | [Resource Manager 範本](./deploy-with-resource-manager-template.md) |  |
| 管理工作區承諾用量方案 | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| 管理工作區使用者 |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 管理 Web 服務 | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br> ( 新的 web 服務) | [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> ( "傳統" web 服務)  |
| 管理 web 服務端點/金鑰 |  [AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| 管理使用者資料集/定型模型| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 管理使用者實驗 |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 管理自訂模組 | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>後續步驟
請參閱下列 PowerShell 模組的完整檔：
* [PowerShell 傳統](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)