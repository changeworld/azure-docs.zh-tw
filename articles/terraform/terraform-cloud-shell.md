---
title: 教學課程 - 設定適用於 Terraform 的 Azure Cloud Shell
description: 在本教學課程中，您會使用 Terraform 與 Azure Cloud Shell 來簡化驗證與範本設定。
keywords: azure devops terraform 雲端 shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945341"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>教學課程：設定適用於 Terraform 的 Azure Cloud Shell

Terraform 適用於 macOS、Windows 或 Linux 中的 Bash 命令列。 在 [Azure Cloud Shell](/azure/cloud-shell/overview) 的 Bash 體驗中執行您的 Terraform 設定，具有一些獨特優點。 本教學課程將說明如何使用 Cloud Shell 來撰寫部署至 Azure 的 Terraform 指令碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>自動認證設定

Terraform 已安裝且立即可在 Cloud Shell 中使用。 不使用任何額外的設定登入 Cloud Shell 來管理結礎結構時，使用 Azure 驗證 Terraform 指令碼。 自動驗證會略過兩個手動程序：
- 建立 Azure Active Directory 服務主體
- 設定 Azure Terraform 提供者變數


## <a name="use-modules-and-providers"></a>使用模組和提供者

Azure Terraform 模組需要認證才能存取和修改 Azure 資源。 若要在 Cloud Shell 中使用 Terraform 模組，請新增下列程式碼：


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

使用任何 `terraform` CLI 命令時，Cloud Shell 會透過環境變數傳遞 `azurerm` 提供者的必要值。

## <a name="other-cloud-shell-developer-tools"></a>其他 Cloud Shell 開發人員工具

檔案和殼層狀態會保存在 Cloud Shell 工作階段之間的 Azure 儲存體中。 使用 [Azure 儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer)，將檔案從本機電腦複製並上傳至 Cloud Shell。

Azure CLI 隨附於 Cloud Shell，是在完成 `terraform apply` 或 `terraform destroy` 後測試設定以及檢查工作的適用工具。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用模組登錄建立小型 VM 叢集](terraform-create-vm-cluster-module.md)