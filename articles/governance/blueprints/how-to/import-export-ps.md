---
title: 使用 PowerShell 匯入和匯出藍圖
description: 瞭解如何使用您的藍圖定義做為程式碼。 共用、原始檔控制，並使用 export 和 import 命令來管理它們。
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686844"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>使用 PowerShell 匯入和匯出藍圖定義

Azure 藍圖可以透過 Azure 入口網站完全管理。 隨著組織繼續使用 Azure 藍圖，他們應該開始思考藍圖定義做為受控碼。 這個概念通常稱為基礎結構即程式碼（IaC）。 將您的藍圖定義視為程式碼，除了 Azure 入口網站所提供的功能之外，還提供其他優點。 這些優點包括：

- 共用藍圖定義
- 備份您的藍圖定義
- 在不同的租使用者或訂用帳戶中重複使用藍圖定義
- 將藍圖定義放在原始檔控制中
  - 測試環境中藍圖定義的自動化測試
  - 支援持續整合與持續部署（CI/CD）管線

無論您的原因為何，以程式碼來管理您的藍圖定義都有其優點。 本文說明如何在[Az. 藍圖](https://powershellgallery.com/packages/Az.Blueprint/)模組`Export-AzBlueprintWithArtifact`中使用`Import-AzBlueprintWithArtifact`和命令。

## <a name="prerequisites"></a>先決條件

本文假設您對 Azure 藍圖進行了中等程度的工作知識。 如果您尚未這麼做，請執行下列文章：

- [在入口網站中建立藍圖](../create-blueprint-portal.md)
- 閱讀[部署階段](../concepts/deployment-stages.md)和[藍圖生命週期的](../concepts/lifecycle.md)相關資訊
- 使用 PowerShell[建立](../create-blueprint-powershell.md)和[管理](./manage-assignments-ps.md)藍圖定義和指派

如果尚未安裝 **Az.Blueprint** 模組，請依照[新增 Az.Blueprint 模組](./manage-assignments-ps.md#add-the-azblueprint-module)中的指示，從 PowerShell 資源庫安裝並驗證它。

## <a name="folder-structure-of-a-blueprint-definition"></a>藍圖定義的資料夾結構

在查看匯出和匯入藍圖之前，讓我們來看看組成藍圖定義的檔案如何結構化。 藍圖定義應該儲存在自己的資料夾中。

> [!IMPORTANT]
> 如果未將任何值傳遞至**Name** `Import-AzBlueprintWithArtifact` Cmdlet 的 name 參數，則會使用藍圖定義儲存所在的資料夾名稱。

除了藍圖定義（必須命名為`blueprint.json`），也是藍圖定義組成的構件。 每個成品都必須位於名為`artifacts`的子資料夾中。
將您的藍圖定義結構放在一起，如同資料夾中的 JSON 檔案，其外觀如下：

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>匯出您的藍圖定義

匯出藍圖定義的步驟相當簡單。 匯出藍圖定義有助於共用、備份或放入原始檔控制。

- **藍圖**[必要]
  - 指定藍圖定義
  - 使用`Get-AzBlueprint`取得參考物件
- **OutputPath** [必要]
  - 指定要儲存藍圖定義 JSON 檔案的路徑
  - 輸出檔案位於具有藍圖定義名稱的子資料夾中
- **版本**（選擇性）
  - 如果**藍圖**參考物件包含一個以上版本的參考，則指定要輸出的版本。

1. 取得藍圖定義的參考，以從表示為`{subId}`的訂用帳戶匯出：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用`Export-AzBlueprintWithArtifact` Cmdlet 來匯出指定的藍圖定義：

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>匯入您的藍圖定義

一旦您有匯出的[藍圖定義](#export-your-blueprint-definition)，或在[必要的資料夾結構](#folder-structure-of-a-blueprint-definition)中有手動建立的藍圖定義之後，您就可以將該藍圖定義匯入至不同的管理群組或訂用帳戶。

如需內建藍圖定義的範例，請參閱[Azure 藍圖 GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)存放庫。

- **名稱**[必要]
  - 指定新藍圖定義的名稱
- **InputPath** [必要]
  - 指定建立藍圖定義的來源路徑
  - 必須符合[所需的資料夾結構](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** （選擇性）
  - 用來儲存藍圖定義的管理群組識別碼（如果不是目前的內容預設）
  - 必須指定**ManagementGroupId**或**SubscriptionId**
- **SubscriptionId** （選擇性）
  - 用來儲存藍圖定義的訂用帳戶識別碼（如果不是目前的內容預設）
  - 必須指定**ManagementGroupId**或**SubscriptionId**

1. 使用`Import-AzBlueprintWithArtifact` Cmdlet 匯入指定的藍圖定義：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

一旦匯入藍圖定義，請[使用 PowerShell 加以指派](./manage-assignments-ps.md#create-blueprint-assignments)。

如需建立 advanced 藍圖定義的詳細資訊，請參閱下列文章：

- 使用[靜態和動態參數](../concepts/parameters.md)。
- 自訂[藍圖排序次序](../concepts/sequencing-order.md)。
- 使用[藍圖資源鎖定](../concepts/resource-locking.md)來保護部署。
- 將藍圖當做程式[代碼來管理](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。