---
title: 快速入門：使用 JavaScript 進行新原則指派
description: 在本快速入門中，您會使用 JavaScript 建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347974"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>快速入門：使用 JavaScript 建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 在本快速入門中，您會建立原則指派，以識別未使用受控磁碟的虛擬機器。 完成時，您會識別出「不符合規範」__ 的虛擬機器。

JavaScript 程式庫可用來從命令列或在指令碼中管理 Azure 資源。 本指南說明如何使用 JavaScript 程式庫建立原則指派。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- **Node.js**：需要 [Node.js](https://nodejs.org/) 12 版或更新版本。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>新增原則程式庫

若要讓 JavaScript 能夠與 Azure 原則搭配使用，必須新增程式庫。 這些程式庫適用於可以使用 JavaScript 的任何位置，包括 [Windows 10 上的 Bash](/windows/wsl/install-win10)。

1. 執行下列命令以設定新的 Node.js 專案。

   ```bash
   npm init -y
   ```

1. 新增 yargs 程式庫的參考。

   ```bash
   npm install yargs
   ```

1. 新增 Azure 原則程式庫的參考。

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. 新增 Azure 驗證程式庫的參考。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 確認 _package.json_ `@azure/arm-policy` 的版本是 **3.1.0** 或更高版本、`@azure/arm-policyinsights` 的版本是 **3.2.0** 或更高版本，且 `@azure/ms-rest-nodeauth` 的版本是 **3.0.5** 或更高版本。

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派**稽核沒有受控磁碟的虛擬機器** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

1. 建立名為 _policyAssignment.js_ 的新檔案，並輸入下列程式碼。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. 在終端機中輸入下列命令：

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

上述命令會使用下列資訊：

- **subID** - 驗證內容的訂用帳戶識別碼。 請務必將 `{subscriptionId}` 取代為您的訂用帳戶。
- **名稱** - 原則指派物件的唯一名稱。 上述範例使用 _audit-vm-manageddisks_。
- **displayName** - 原則指派的顯示名稱。 在此案例中，您會使用_稽核沒有受控磁碟指派的虛擬機器_。
- **policyDefID** - 原則定義路徑，這是您用來建立指派的根基。 在此案例中，即為原則定義_稽核沒有受控磁碟的虛擬機器_的 ID。
- **description** - 原則用途或為何指派給此範圍的更深入說明。
- **scope** - 範圍會決定在哪些資源或資源群組上強制執行原則指派。 此範圍可包含管理群組到個別資源。 請務必將 `{scope}` 取代為下列其中一個模式：
  - 管理群組：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 訂用帳戶：`/subscriptions/{subscriptionId}`
  - 資源群組：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 資源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

現在您已建立原則指派，您可以識別不符合規範的資源。

1. 建立名為 _policyState.js_ 的新檔案，並輸入下列程式碼。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. 在終端機中輸入下列命令：

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

將 `{subscriptionId}` 取代為您想要查看特定原則指派之合規性結果的訂用帳戶；該指派的名稱為 'audit-vm-manageddisks'，是我們在先前的步驟中建立的。 如需其他範圍和資料摘要方式的清單，請參閱 [PolicyStates*](/javascript/api/@azure/arm-policyinsights/) 方法。

您的結果類似下列範例：

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

結果會符合原則指派在 Azure 入口網站檢視中的 [資源合規性]**** 索引標籤所顯示的內容。

## <a name="clean-up-resources"></a>清除資源

- 刪除原則指派 透過入口網站_稽核沒有受控磁碟指派的 VM_。 原則定義是內建的，因此沒有任何定義可供移除。

- 如果您想要從應用程式中移除已安裝的程式庫，請執行下列命令。

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則定義，以驗證新資源是符合規範的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)