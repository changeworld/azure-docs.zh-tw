---
title: 找不到資源的錯誤
description: 描述當使用 Azure Resource Manager 範本部署時，找不到資源時，如何解決錯誤。
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773254"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>解決找不到 Azure 資源的錯誤

本文描述在部署期間找不到資源時可能會看到的錯誤。

## <a name="symptom"></a>徵狀

當範本包含無法解析的資源名稱時，您會收到類似以下的錯誤：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果您對無法解析的資源使用 [reference](template-functions-resource.md#reference) 或 [listKeys](template-functions-resource.md#listkeys) 函式，便會收到下列錯誤：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

Resource Manager 需要擷取資源的屬性，但是無法識別您訂用帳戶中的資源。

## <a name="solution-1---set-dependencies"></a>解決方案 1：設定相依性

如果您正嘗試在範本中部署遺漏的資源，請檢查是否需要新增相依性。 如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。 如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素。 例如，在部署 Web 應用程式時，App Service 方案必須存在。 如果您未指定 Web 應用程式相依於 App Service 方案，Resource Manager 就會同時建立這兩個資源。 您會收到錯誤，指出找不到 App Service 方案資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。 您會設定 Web 應用程式的相依性，以避免此錯誤。

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

但是，您要避免設定不需要的相依性。 當您有不必要的相依性時，會阻止未彼此相依的資源以平行方式部署，因而延長部署的時間。 此外，您可以建立封鎖部署的循環相依性。 在同一個範本中部署所參考的資源且依其名稱 (而非資源識別碼) 加以參考時，[reference](template-functions-resource.md#reference) 函式和 [list*](template-functions-resource.md#list) 函式會在該資源上建立隱含的相依性。 因此，您的相依性可能會比 **dependsOn** 屬性中指定的相依性還多。 [resourceId](template-functions-resource.md#resourceid) 函式不會建立隱含的相依性或驗證資源存在。 依資源的資源識別碼參考資源時，[reference](template-functions-resource.md#reference) 函式和 [list*](template-functions-resource.md#list) 函式不會建立隱含的相依性。 若要建立隱含的相依性，請針對部署於相同範本的資源，傳遞資源的名稱。

當您看到相依性問題時，應深入了解資源部署的順序。 若要檢視部署作業的順序︰

1. 選取資源群組的部署歷程記錄。

   ![選取部署歷程記錄](./media/error-not-found/select-deployment.png)

2. 從歷程記錄中選取部署，然後選取 [事件]。

   ![選取部署事件](./media/error-not-found/select-deployment-events.png)

3. 檢查每個資源的事件順序。 注意每個作業的狀態。 例如，下列映像顯示平行部署的三個儲存體帳戶。 請注意，三個儲存體帳戶會同時啟動。

   ![平行部署](./media/error-not-found/deployment-events-parallel.png)

   下圖顯示非平行部署的三個儲存體帳戶。 第二個儲存體帳戶相依於第一個儲存體帳戶，而第三個儲存體帳戶相依於第二個儲存體帳戶。 第一個儲存體帳戶會在下一個儲存體帳戶啟動之前啟動、接受及完成。

   ![連續部署](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>解決方案 2：從不同的資源群組取得資源

當資源存在於與部署目標不同的資源群組時，使用 [resourceId 函式](template-functions-resource.md#resourceid)以取得資源的完整名稱。

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>解決方案 3：檢查參考函式

尋找包含 [reference](template-functions-resource.md#reference) 函式的運算式。 您提供的值會根據資源是否位於相同範本、資源群組及訂用帳戶而有所不同。 再次確認您會為案例提供必要的參數值。 如果資源位於不同資源群組中，請提供完整資源識別碼。 例如，若要參考另一個資源群組的儲存體帳戶，請使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>解決方案 4-從資源取得受控識別

如果您要部署隱含建立[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)的資源，您必須等到該資源部署之後，再抓取受控識別上的值。 如果您將受控識別名稱傳遞給[reference](template-functions-resource.md#reference)函式，Resource Manager 會在部署資源和身分識別之前，先嘗試解析參考。 請改為傳遞要套用身分識別的資源名稱。 這種方法可確保在 Resource Manager 解析參考函式之前，會先部署資源和受控識別。

在 reference 函式中，使用 `Full` 來取得所有屬性，包括受控識別。

例如，若要取得適用于虛擬機器擴展集之受控識別的租使用者識別碼，請使用：

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```