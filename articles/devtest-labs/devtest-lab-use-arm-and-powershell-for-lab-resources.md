---
title: 使用 Azure Resource Manager 範本建立或修改實驗室
description: 了解如何使用 Powershell 及 Azure Resource Manager 範本，在 DevTest 實驗室中自動建立或修改實驗室
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87272635"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>使用 PowerShell 及 Azure Resource Manager 範本自動建立或修改實驗室

DevTest 實驗室提供許多 Azure Resource Manager 範本和 PowerShell 指令碼，可以協助您快速及自動建立新的實驗室，或修改現有的實驗室，並進行資源部署。

本文循序指導您使用這些範本及指令碼，來自動化實驗室的建立、修改及部署。 本文也將說明，您將可以在哪裡找到如何使用 PowerShell 來執行 DevTest 實驗室中的若干一般工作的相關資訊。

## <a name="step-1-gather-your-templates-and-scripts"></a>步驟 1︰收集您的範本和指令碼
您可以在我們的公用[GitHub 存放庫](https://github.com/Azure/azure-devtestlab)中找到預先製作的[Azure Resource Manager 範本](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)和[PowerShell 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)。 直接使用，或針對您的需求進行自訂，並儲存在您自己的[私人 Git 存放庫](devtest-lab-add-artifact-repo.md)。

## <a name="step-2-modify-your-azure-resource-manager-template"></a>步驟 2：修改您的 Azure Resource Manager 範本
如果您以前從未建立過範本，您可以遵循[建立第一個 Azure Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)中的步驟。

此外，[建立 Azure Resource Manager 範本的最佳做法](../azure-resource-manager/templates/template-best-practices.md)提供許多指導方針和建議，可以協助您建立可靠又容易使用的 Azure Resource Manager 範本。 一般而言，您會使用所提供的其中一個方法或範例的某種變化，再針對您的需要來修改範本。

## <a name="step-3-deploy-resources-with-powershell"></a>步驟 3︰ 使用 PowerShell 部署資源
自訂您的範本和指令碼之後，請依照下列步驟，[使用 Resource Manager 範本和 Azure PowerShell 部署資源](../azure-resource-manager/templates/deploy-powershell.md)。 本文提供有關使用 Azure PowerShell 和 Azure Resource Manager 範本，將您的資源部署至 Azure 的一般資訊。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>您可以使用 PowerShell 在 DevTest 實驗室中執行的一般工作
有許多其他一般工作，您可以使用 PowerShell 來自動執行。 下列文件各節說明執行這些工作所需的步驟。

* [使用 PowerShell 從 VHD 檔案建立自訂映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [使用 PowerShell 將 VHD 檔案上傳到實驗室的儲存體帳戶](devtest-lab-upload-vhd-using-powershell.md)
* [使用 PowerShell 將外部使用者新增至實驗室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [使用 PowerShell 建立實驗室自訂角色](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>後續步驟
* 了解如何建立您將儲存自訂範本或指令碼的[私人 Git 存放庫](devtest-lab-add-artifact-repo.md)。
* 探索[Azure 快速入門範本資源庫中的 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates)。
