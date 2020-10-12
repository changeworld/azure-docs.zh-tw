---
title: 找不到資源錯誤
description: 描述如何在找不到資源時解決錯誤。 部署 Azure Resource Manager 範本或採取管理動作時，可能會發生此錯誤。
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 224af4ce0fe5053201f25d8207f4ca8cdc73e638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84667942"
---
# <a name="resolve-resource-not-found-errors"></a>解決找不到資源錯誤

本文說明在操作期間找不到資源時所看到的錯誤。 通常，您會在部署資源時看到此錯誤。 當您執行管理工作時，也會看到此錯誤，Azure Resource Manager 找不到必要的資源。 例如，如果您嘗試將標記新增至不存在的資源，就會收到此錯誤。

## <a name="symptom"></a>徵狀

有兩個錯誤碼指出找不到資源。 **NotFound**錯誤會傳回類似以下的結果：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**ResourceNotFound**錯誤會傳回類似以下的結果：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

Resource Manager 需要取得資源的屬性，但在您的訂用帳戶中找不到資源。

## <a name="solution-1---check-resource-properties"></a>解決方案 1-檢查資源屬性

當您在執行管理工作時收到此錯誤時，請檢查您為資源提供的值。 要檢查的三個值為：

* 資源名稱
* 資源群組名稱
* 訂用帳戶

如果您是使用 PowerShell 或 Azure CLI，請檢查您是否正在包含資源的訂用帳戶中執行命令。 您可以使用 [設定 set-azcoNtext](/powershell/module/Az.Accounts/Set-AzContext) 或 [az 帳戶集](/cli/azure/account#az-account-set)來變更訂用帳戶。 許多命令也提供訂用帳戶參數，可讓您指定與目前內容不同的訂用帳戶。

如果您在驗證屬性時遇到問題，請登入 [入口網站](https://portal.azure.com)。 尋找您嘗試使用的資源，並檢查資源名稱、資源群組和訂用帳戶。

## <a name="solution-2---set-dependencies"></a>解決方案 2-設定相依性

如果您在部署範本時收到這個錯誤，您可能需要新增相依性。 如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。 如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素。 例如，在部署 Web 應用程式時，App Service 方案必須存在。 如果您未指定 Web 應用程式相依於 App Service 方案，Resource Manager 就會同時建立這兩個資源。 您會收到錯誤，指出找不到 App Service 方案資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。 您會設定 Web 應用程式的相依性，以避免此錯誤。

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

2. 從歷程記錄中選取部署，然後選取 [事件]****。

   ![選取部署事件](./media/error-not-found/select-deployment-events.png)

3. 檢查每個資源的事件順序。 注意每個作業的狀態。 例如，下列映像顯示平行部署的三個儲存體帳戶。 請注意，三個儲存體帳戶會同時啟動。

   ![平行部署](./media/error-not-found/deployment-events-parallel.png)

   下圖顯示非平行部署的三個儲存體帳戶。 第二個儲存體帳戶相依於第一個儲存體帳戶，而第三個儲存體帳戶相依於第二個儲存體帳戶。 第一個儲存體帳戶會在下一個儲存體帳戶啟動之前啟動、接受及完成。

   ![連續部署](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>解決方案 3-取得外部資源

部署範本，而且您需要取得存在於不同訂用帳戶或資源群組中的資源時，請使用 [resourceId 函數](template-functions-resource.md#resourceid)。 此函數會傳回以取得資源的完整名稱。

ResourceId 函數中的訂用帳戶和資源群組參數都是選擇性的。 如果未提供，則會預設為目前的訂用帳戶和資源群組。 使用不同資源群組或訂用帳戶中的資源時，請務必提供這些值。

下列範例會取得存在於不同資源群組中之資源的資源識別碼。

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>解決方案 4-從資源取得受控識別

如果您要部署的資源會隱含地建立 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md)，您必須等到該資源部署之後，才能抓取受控識別的值。 如果您將受控識別名稱傳遞給 [參考](template-functions-resource.md#reference) 函式，Resource Manager 會在部署資源和身分識別之前嘗試解析參考。 請改為將身分識別套用的資源名稱傳遞給。 這種方法可確保在 Resource Manager 解析參考函式之前，會先部署資源和受控識別。

在參考函式中，使用 `Full` 來取得所有屬性（包括受控識別）。

模式為：

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> 請勿使用模式：
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> 您的範本將會失敗。

例如，若要取得適用于虛擬機器之受控識別的主體識別碼，請使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

或者，若要取得適用于虛擬機器擴展集之受控識別的租使用者識別碼，請使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>解決方案 5-檢查函數

部署範本時，請尋找使用 [參考](template-functions-resource.md#reference) 或 [listKeys](template-functions-resource.md#listkeys) 函數的運算式。 您提供的值會根據資源是否位於相同範本、資源群組及訂用帳戶而有所不同。 確認您要為您的案例提供必要的參數值。 如果資源位於不同資源群組中，請提供完整資源識別碼。 例如，若要參考另一個資源群組的儲存體帳戶，請使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```