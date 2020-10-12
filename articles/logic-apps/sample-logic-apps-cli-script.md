---
title: Azure CLI 腳本範例-建立邏輯應用程式
description: 透過 Azure CLI 中的 Logic Apps 擴充功能建立邏輯應用程式的範例腳本。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/30/2020
ms.openlocfilehash: ceb4a3356ef78d2129f76bd11f555a9ca5206d51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87505831"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI 腳本範例-建立邏輯應用程式

此腳本會透過 [Azure CLI Logic Apps 延伸](/cli/azure/ext/logic/logic?view=azure-cli-latest)模組建立範例邏輯應用程式， (`az logic`) 。 如需透過 Azure CLI 建立和管理邏輯應用程式的詳細指南，請參閱 [Azure CLI 的 Logic Apps 快速入門](quickstart-logic-apps-azure-cli.md)。

> [!WARNING]
> Azure CLI Logic Apps 擴充功能目前為「實驗性」擴充功能，不在客戶支援涵蓋範圍內。 請小心使用此 CLI 擴充功能，特別是當您選擇在生產環境中使用擴充功能時。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在您的本機電腦上安裝 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 在您的電腦上安裝 [Logic Apps Azure CLI 擴充功能](/cli/azure/azure-cli-extensions-list?view=azure-cli-latest)。 若要安裝此擴充功能，請使用下列命令：`az extension add --name logic`
* 邏輯應用程式的 [工作流程定義](quickstart-logic-apps-azure-cli.md#workflow-definition) 。 此 JSON 檔案必須遵循 [工作流程定義語言架構](logic-apps-workflow-definition-language.md)。
* 在與邏輯應用程式相同的資源群組中，透過支援的 [Logic Apps 連接器](../connectors/apis-list.md) ，對電子郵件帳戶的 API 連線。 此範例使用 [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) 連接器，但您也可以使用其他連接器，例如 [Outlook.com](../connectors/connectors-create-api-outlook.md)。

### <a name="prerequisite-check"></a>先決條件檢查

開始之前，請先驗證您的環境：

* 登入 Azure 入口網站，並執行 `az login` 檢查您的訂用帳戶是否有效。

* 執行 `az --version`，在終端機或命令視窗中檢查您的 Azure CLI 版本。 如需最新版本，請參閱[最新版本資訊](/cli/azure/release-notes-azure-cli?tabs=azure-cli&view=azure-cli-latest)。

  * 如果您沒有最新版本，請遵循[適用於您作業系統或平台的安裝指南](/cli/azure/install-azure-cli?view=azure-cli-latest)，更新您的安裝。

### <a name="sample-workflow-explanation"></a>範例工作流程說明

此範例工作流程定義檔會建立與 [Azure 入口網站的 Logic Apps 快速入門](quickstart-create-first-logic-app-workflow.md)相同的基本邏輯應用程式。 

此範例工作流程： 

1. 指定 `$schema` 邏輯應用程式的架構。

1. 在觸發程式清單中定義邏輯應用程式的觸發程式 `triggers` 。 觸發程式會 `recurrence` 每3小時重複 () 。 `When_a_feed_item_is_published`針對指定的 rss 摘要 () 的 () 發佈新的摘要專案時，就會觸發這些動作 `feedUrl` 。

1. 在動作清單中定義邏輯應用程式的動作 `actions` 。 此動作會傳送電子郵件 (`Send_an_email_(V2)`) 到 Microsoft 365，其中包含 RSS 摘要專案的詳細資料，如 [主體] 區段中所指定 (`body`) 動作輸入 (`inputs`) 。

## <a name="sample-workflow-definition"></a>範例工作流程定義

執行範例腳本之前，您必須先建立範例 [工作流程定義](#prerequisites)。

1. `testDefinition.json`在您的電腦上建立 JSON 檔案。 

1. 將下列內容複寫到 JSON 檔案中： 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. 使用您自己的資訊來更新預留位置值：

    1. 取代 () 的預留位置電子郵件地址 `"To": "test@example.com"` 。 您必須使用與 Logic Apps 連接器相容的電子郵件地址。 如需詳細資訊，請參閱 [必要條件](#prerequisites)。

    1. 如果您使用的是與 Office 365 Outlook 連接器不同的電子郵件連接器，請更換其他連接器詳細資料。

    1. `00000000-0000-0000-0000-000000000000`以您自己的訂用帳戶值取代 () 的連接識別碼 (`connectionId` 和) 下的預留位置訂 `id` `$connections` 用 (帳戶值。

1. 儲存您的變更。

## <a name="sample-script"></a>範例指令碼

> [!NOTE]
> 此範例是針對 shell 所撰寫 `bash` 。 如果您想要在另一個 shell （例如 Windows PowerShell 或命令提示字元）中執行此範例，您可能需要對腳本進行修改。

執行此範例腳本之前，請執行下列命令以連線到 Azure：

```azurecli-interactive

az login

```

接下來，流覽至您在其中建立工作流程定義的目錄。 例如，如果您在桌面上建立了工作流程定義 JSON 檔案：

```azurecli

cd ~/Desktop

```

然後，執行此腳本來建立邏輯應用程式。 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>清除部署

當您使用完範例腳本之後，請執行下列命令來移除資源群組及其所有的嵌套資源，包括邏輯應用程式。

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>指令碼說明

此範例腳本會使用下列命令來建立新的資源群組和邏輯應用程式。

| Command | 注意 |
| ------- | ----- |
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | 建立用來儲存邏輯應用程式資源的資源群組。 |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-create) | 根據參數中定義的工作流程建立邏輯應用程式 `--definition` 。 |
| [`az group delete`](/cli/azure/vm/extension?view=azure-cli-latest) | 刪除資源群組及其所有的嵌套資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure/?view=azure-cli-latest)。

您可以在 [Microsoft 的程式碼範例瀏覽器](/samples/browse/?products=azure-logic-apps)中找到其他 Logic Apps CLI 指令碼範例。
