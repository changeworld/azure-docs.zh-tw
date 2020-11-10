---
title: 使用 Azure CLI 來建立和管理邏輯應用程式
description: 使用 Azure CLI 來建立邏輯應用程式，然後使用 list、show (get)、update 和 delete 之類的作業來管理邏輯應用程式。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 10/28/2020
ms.openlocfilehash: 0d7f455e748a52595839cc509720bf7ad5b9b617
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099057"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>快速入門：使用 Azure CLI 來建立和管理邏輯應用程式

本快速入門說明如何使用 [Azure CLI Logic Apps 擴充功能](/cli/azure/ext/logic/logic) (`az logic`) 來建立和管理邏輯應用程式。 從命令列中，您可以使用邏輯應用程式工作流程定義的 JSON 檔案來建立邏輯應用程式。 然後，您可以從命令列執行 `list`、`show` (`get`)、`update` 和 `delete` 之類的作業來管理邏輯應用程式。

> [!WARNING]
> Azure CLI Logic Apps 擴充功能目前為「實驗性」擴充功能，不在客戶支援涵蓋範圍內。 請小心使用此 CLI 擴充功能，特別是當您選擇在生產環境中使用擴充功能時。

如果您不熟悉 Logic Apps，也可以了解如何[透過 Azure 入口網站](quickstart-create-first-logic-app-workflow.md)、 [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)以及在 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) 中建立第一個邏輯應用程式。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在您的本機電腦上安裝 [Azure CLI](/cli/azure/install-azure-cli)。
* 在您的電腦上安裝 [Logic Apps Azure CLI 擴充功能](/cli/azure/azure-cli-extensions-list)。 若要安裝此擴充功能，請使用下列命令：`az extension add --name logic`
* 要在其中建立邏輯應用程式的 [Azure 資源群組](#example---create-resource-group)。

### <a name="prerequisite-check"></a>先決條件檢查

開始之前，請先驗證您的環境：

* 登入 Azure 入口網站，並執行 `az login` 檢查您的訂用帳戶是否有效。
* 執行 `az --version`，在終端機或命令視窗中檢查您的 Azure CLI 版本。 如需最新版本，請參閱[最新版本資訊](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果您沒有最新版本，請遵循[適用於您作業系統或平台的安裝指南](/cli/azure/install-azure-cli)，更新您的安裝。

### <a name="example---create-resource-group"></a>範例 - 建立資源群組

如果您的邏輯應用程式還沒有資源群組，請使用 `az group create` 命令來建立群組。 例如，下列命令會在 `westus` 位置建立名為 `testResourceGroup` 的資源群組。

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

當您的資源群組成功建立時，輸出會將 `provisioningState` 顯示為 `Succeeded`：

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>工作流程定義

在使用 Azure CLI 來[建立新的邏輯應用程式](#create-logic-apps-from-cli)或[更新現有的邏輯應用程式](#update-logic-apps-from-cli)之前，您需要邏輯應用程式的工作流程定義。 在 Azure 入口網站中，您可以從 [設計工具] 檢視切換至 [程式碼] 檢視，以 JSON 格式來檢視邏輯應用程式的基礎工作流程定義。

當執行命令來建立或更新邏輯應用程式時，會將您的工作流程定義上傳為必要參數 (`--definition`)。 您必須建立工作流程定義，作為遵循[工作流程定義語言結構描述](./logic-apps-workflow-definition-language.md)的 JSON 檔案。

## <a name="create-logic-apps-from-cli"></a>從 CLI 建立邏輯應用程式

您可以使用 [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) 命令搭配定義的 JSON 檔案，從 Azure CLI 建立邏輯應用程式工作流程。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

您的命令必須包含下列[必要參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters)：

| 參數 | 值 | 說明 |
| --------- | ----- | ----------- |
| 工作流程定義 | `--definition` | 具有邏輯應用程式[工作流程定義](#workflow-definition)的 JSON 檔案。 |
| Location | `--location -l` | 邏輯應用程式所在的 Azure 區域。 |
| 名稱 | `--name -n` | 邏輯應用程式的名稱。 名稱只能包含字母、數字、連字號 (`-`)、底線 (`_`)、括弧(`()`) 和句點 (`.`)。 名稱在整個 Azure 中也必須是唯一的。 |
| 資源群組名稱 | `--resource-group -g` | 要在其中建立邏輯應用程式的 [Azure 資源群組](../azure-resource-manager/management/overview.md)。 如果您的邏輯應用程式還沒有資源群組，請在開始之前先[建立資源群組](#example---create-resource-group)。 |

您也可以包含其他[選擇性參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters)，來設定邏輯應用程式的存取控制、端點、整合帳戶、整合服務環境、狀態，以及資源標籤。

### <a name="example---create-logic-app"></a>範例 - 建立邏輯應用程式

在此範例中，會在位置 `westus` 的資源群組 `testResourceGroup` 中建立名為 `testLogicApp` 的工作流程。 JSON 檔案 `testDefinition.json` 包含工作流程定義。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

當您的工作流程成功建立時，CLI 會顯示新工作流程定義的 JSON 程式碼。 如果您的工作流程建立失敗，請參閱[可能的錯誤清單](#errors)。

## <a name="update-logic-apps-from-cli"></a>從 CLI 更新邏輯應用程式

您也可以使用 [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) 命令，從 Azure CLI 更新邏輯應用程式的工作流程。

您的命令必須包含與您[建立邏輯應用程式](#create-logic-apps-from-cli)時相同的[必要參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters)。 您也可以新增與建立邏輯應用程式時相同的[選擇性參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters)。

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>範例 - 更新邏輯應用程式

在此範例中，[在上一節中建立的範例工作流程](#example---create-logic-app)會更新為使用不同的 JSON 定義檔 `newTestDefinition.json`，並以描述值新增兩個資源標籤 `testTag1` 和 `testTag2`。

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

當您的工作流程成功更新時，CLI 會顯示邏輯應用程式的已更新工作流程定義。 如果您的更新失敗，請參閱[可能的錯誤清單](#errors)。

## <a name="delete-logic-apps-from-cli"></a>從 CLI 刪除邏輯應用程式

您可以使用 [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) 命令，從 Azure CLI 刪除邏輯應用程式的工作流程。

您的命令必須包含下列[必要參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters)：

| 參數 | 值 | 描述 |
| --------- | ----- | ----------- |
| 名稱 | `--name -n` | 邏輯應用程式的名稱。 |
| 資源群組名稱 | `-resource-group -g` | 邏輯應用程式所在的資源群組。 |

您也可以包含[選擇性參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters)，以略過確認提示 (`--yes -y`)。

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLI 接著會提示您確認刪除邏輯應用程式。 您可以使用選擇性參數 `--yes -y` 搭配命令來略過確認提示。

```azurecli

Are you sure you want to perform this operation? (y/n):

```

您可以[在 CLI 中列出邏輯應用程式](#list-logic-apps-in-cli)，或在 Azure 入口網站中檢視邏輯應用程式，來確認邏輯應用程式的刪除作業。

### <a name="example---delete-logic-app"></a>範例 - 刪除邏輯應用程式

在此範例中，會刪除[在上一節中建立的範例工作流程](#example---create-logic-app)。

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

在您使用 `y` 來回應確認提示之後，即會刪除邏輯應用程式。

## <a name="show-logic-apps-in-cli"></a>在 CLI 中顯示邏輯應用程式

您可以使用 [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) 命令來取得特定的邏輯應用程式工作流程。

```azurecli

az logic workflow show --name
                       --resource-group

```

您的命令必須包含下列[必要參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)

| 參數 | 值 | 描述 |
| --------- | ----- | ----------- |
| 名稱 | `--name -n` | 邏輯應用程式的名稱。 |
| 資源群組名稱 | `--resource-group -g` | 邏輯應用程式所在的資源群組名稱。 |

### <a name="example---get-logic-app"></a>範例 - 取得邏輯應用程式

在此範例中，資源群組 `testResourceGroup` 中的邏輯應用程式 `testLogicApp` 會搭配完整記錄傳回，以進行偵錯。

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>在 CLI 中列出邏輯應用程式

您可以使用 [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) 命令，依訂用帳戶列出邏輯應用程式。 此命令會傳回邏輯應用程式工作流程的 JSON 程式碼。

您可以依下列[選擇性參數](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters)篩選結果：

| 參數 | 值 | 說明 |
| --------- | ----- | ----------- |
| 資源群組名稱 | `--resource-group -g` | 您想要依其篩選結果的資源群組名稱。 |
| 項目數 | `--top` | 結果中包含的項目數。 |
| Filter | `--filter` | 您要在清單上使用的篩選類型。 您可以依狀態 (`State`)、觸發程序 (`Trigger`) 和所參考資源 (`ReferencedResourceId`) 的識別碼進行篩選。 |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>範例 - 列出邏輯應用程式

在此範例中，資源群組 `testResourceGroup` 中所有已啟用的工作流程都會以 ASCII 資料表格式傳回。

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Errors

下列錯誤指出未安裝 Azure Logic Apps CLI 擴充功能。 遵循必要條件中的步驟，在您的電腦上[安裝 Logic Apps 擴充功能](#prerequisites)。

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

下列錯誤可能指出上傳工作流程定義的檔案路徑不正確。

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>全域參數

您可以使用下列選擇性的全域 Azure CLI 參數搭配 `az logic` 命令：

| 參數 | 值 | 說明 |
| --------- | ----- | ----------- |
| 輸出格式 | `--output -o` | 變更預設 JSON 的[輸出格式](/cli/azure/format-output-azure-cli)。 |
| 僅顯示錯誤 | `--only-show-errors` | 隱藏警告，只顯示錯誤。 |
| 「詳細資訊」 | `--verbose` | 顯示詳細資訊記錄。 |
| 偵錯 | `--debug` | 顯示所有的偵錯記錄。 |
| 說明訊息 | `--help -h` | 顯示說明對話方塊。 |
| 查詢 | `--query` | 設定 JSON 輸出的 JMESPath 查詢字串。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure/)。

您可以在 [Microsoft 的程式碼範例瀏覽器](/samples/browse/?products=azure-logic-apps)中找到其他 Logic Apps CLI 指令碼範例。

接下來，您可以使用範例指令碼和工作流程定義，透過 Azure CLI 建立範例應用程式邏輯。

> [!div class="nextstepaction"]
> [使用範例指令碼建立邏輯應用程式](sample-logic-apps-cli-script.md)。
