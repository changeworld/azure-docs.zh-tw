---
title: 快速入門：使用 Terraform 進行新原則指派
description: 在本快速入門中，使用 Terraform 和 HCL 語法來建立原則指派，以識別不符合規範的資源。
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93104898"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>快速入門：使用 Terraform 建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。
本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將會成功識別出未使用受控磁碟的虛擬機器。 它們「不符合」原則指派的規範。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 在您環境中設定的 [Terraform](https://www.terraform.io/) 0.12.0 版或更高版本。
  如需指示，請參閱[使用 Azure Cloud Shell 設定 Terraform](/azure/developer/terraform/get-started-cloud-shell)。
- 此快速入門需要您執行 Azure CLI 2.13.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>建立 Terraform 組態、變數和輸出檔案

在本快速入門中，您會建立一個原則指派，並且指派 **稽核沒有受控磁碟的虛擬機器** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

首先，設定 Terraform 組態、變數和輸出檔案。 Azure 原則的 Terraform 資源會使用 [Azure 提供者](https://www.terraform.io/docs/providers/azurerm/index.html)。

1. 建立名為 `policy-assignment` 的新資料夾，並將目錄變更到其中。

1. 以下列程式碼建立 `main.tf`：

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. 以下列程式碼建立 `variables.tf`：

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   範圍會決定在哪些資源或資源群組上強制執行原則指派。 此範圍可包含管理群組到個別資源。 請務必將 `{scope}` 取代為下列其中一個模式：

   - 訂用帳戶：`/subscriptions/{subscriptionId}`
   - 資源群組：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - 資源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. 以下列程式碼建立 `output.tf`：

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>初始化 Terraform 並建立計畫

接下來，初始化 Terraform 以下載所需的提供者，然後建立計畫。

1. 執行 [terraform init](https://www.terraform.io/docs/commands/init.html) 命令。 此命令會下載在 Terraform 組態中建立 Azure 資源所需的 Azure 模組。

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="執行 terraform init 命令的螢幕擷取畫面，其中顯示下載 azurerm 模組和成功訊息。":::

1. 使用適用於 Terraform 的 [Azure CLI](/cli/azure/) 進行驗證。 如需詳細資訊，請參閱 [Azure 提供者：使用 Azure CLI 進行驗證](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html)。

   ```bash
   az login
   ```

1. 使用 [terraform plan](https://www.terraform.io/docs/commands/plan.html) 命令和 **out** 參數來建立執行計畫。

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="執行 terraform plan 命令和 out 參數，以顯示即將建立的 Azure 資源的螢幕擷取畫面。":::

   > [!NOTE]
   > 如需保存執行計畫和安全性的詳細資訊，請參閱 [Terraform 計畫：安全性警告](https://www.terraform.io/docs/commands/plan.html#security-warning)。

## <a name="apply-the-terraform-execution-plan"></a>套用 Terraform 執行計畫

最後，套用執行計畫。

執行 [terraform apply](https://www.terraform.io/docs/commands/apply.html) 命令並指定已建立的 `assignment.tfplan`。

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="執行 terraform apply 命令和所產生資源建立的螢幕擷取畫面。":::

出現「套用完成！ 資源：已新增 1 個，已變更 0 個，已終結 0 個。」 訊息，現在已建立原則指派。 因為我們定義了 `outputs.tf` 檔案，所以也會傳回 _assignment\_id_ 。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

若要檢視這個新指派下不符合規範的資源，請使用 `terraform apply` 傳回的 _assignment\_id_ 。 藉此，執行下列命令，以取得不合規資源的資源識別碼，而這些識別碼會輸出到 JSON 檔案中：

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

您的結果類似下列範例：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

結果類似於您通常在 Azure 入口網站檢視中看到列在 [不符合規範的資源] 之下的內容。

## <a name="clean-up-resources"></a>清除資源

若要移除所建立的指派，請使用 Azure CLI 或以 `terraform destroy` 反轉 Terraform 執行計畫。

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以驗證新資源是相容的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)
