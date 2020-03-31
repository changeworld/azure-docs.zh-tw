---
title: 使用 PowerShell 導入和匯出藍圖
description: 瞭解如何將藍圖定義用作代碼。 使用匯出和導入命令共用、原始程式碼管理並管理它們。
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873194"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>使用 PowerShell 的導入和匯出藍圖定義

Azure 藍圖可以通過 Azure 門戶完全管理。 隨著組織在使用藍圖方面的進步，他們應該開始將藍圖定義視為託管代碼。 這個概念通常被稱為基礎結構作為代碼 （IaC）。 將藍圖定義視為代碼提供了 Azure 門戶提供的其他優勢。 這些優勢包括：

- 共用藍圖定義
- 備份藍圖定義
- 重用不同租戶或訂閱中的藍圖定義
- 將藍圖定義置於原始程式碼管理中
  - 測試環境中藍圖定義的自動測試
  - 支援持續集成和持續部署 （CI/CD） 管道

無論您的理由是什麼，將藍圖定義管理為代碼都有其好處。 本文演示如何在[Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) `Export-AzBlueprintWithArtifact`模組中使用`Import-AzBlueprintWithArtifact`和 命令。

## <a name="prerequisites"></a>Prerequisites

本文假定 Azure 藍圖具有中等的工作知識。 如果您尚未這樣做，則執行以下文章：

- [在入口網站中建立藍圖](../create-blueprint-portal.md)
- 閱讀有關[部署階段](../concepts/deployment-stages.md)和[藍圖生命週期](../concepts/lifecycle.md)
- 使用 PowerShell[創建](../create-blueprint-powershell.md)[和管理](./manage-assignments-ps.md)藍圖定義和分配

如果尚未安裝 **Az.Blueprint** 模組，請依照[新增 Az.Blueprint 模組](./manage-assignments-ps.md#add-the-azblueprint-module)中的指示，從 PowerShell 資源庫安裝並驗證它。

## <a name="folder-structure-of-a-blueprint-definition"></a>藍圖定義的資料夾結構

在查看匯出和導入藍圖之前，我們來看看構成藍圖定義的檔的構造方式。 藍圖定義應存儲在其自己的資料夾中。

> [!IMPORTANT]
> 如果未將任何值傳遞給`Import-AzBlueprintWithArtifact`Cmdlet 的名稱參數，則將使用藍圖定義中存儲的資料夾的名稱。 **Name**

與必須命名的`blueprint.json`藍圖定義一起，是藍圖定義由工件組成的。 每個專案必須位於名為`artifacts`的子資料夾中。
綜合起來，藍圖定義的結構作為資料夾中的 JSON 檔應如下所示：

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

## <a name="export-your-blueprint-definition"></a>匯出藍圖定義

匯出藍圖定義的步驟非常簡單。 匯出藍圖定義可用於共用、備份或放入原始程式碼管理。

- **藍圖**[必需]
  - 指定藍圖定義
  - 用於`Get-AzBlueprint`獲取引用物件
- **輸出路徑**[必需]
  - 指定將藍圖定義 JSON 檔保存到
  - 輸出檔案位於具有藍圖定義名稱的子資料夾中
- **版本**（可選）
  - 如果**藍圖**引用物件包含對多個版本的引用，則指定要輸出的版本。

1. 獲取對藍圖定義的引用，以便從表示為`{subId}`的訂閱匯出：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用`Export-AzBlueprintWithArtifact`Cmdlet 匯出指定的藍圖定義：

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>導入藍圖定義

在[所需的資料夾結構](#folder-structure-of-a-blueprint-definition)中具有[匯出的藍圖定義](#export-your-blueprint-definition)或手動創建的藍圖定義後，可以將該藍圖定義導入到不同的管理組或訂閱。

有關內置藍圖定義的示例，請參閱 Azure 藍圖[GitHub 存儲庫](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins)。

- **名稱**[必需]
  - 指定新藍圖定義的名稱
- **輸入路徑**[必需]
  - 指定從
  - 必須匹配[所需的資料夾結構](#folder-structure-of-a-blueprint-definition)
- **管理組 Id（** 可選）
  - 管理組 ID，用於將藍圖定義保存到當前上下文預設值（如果不是當前上下文預設）
  - 必須指定**管理組 Id**或**訂閱 Id**
- **訂閱 Id（** 可選）
  - 將藍圖定義保存到當前上下文預設值（如果不是當前上下文預設值）的訂閱 ID
  - 必須指定**管理組 Id**或**訂閱 Id**

1. 使用`Import-AzBlueprintWithArtifact`Cmdlet 導入指定的藍圖定義：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

導入藍圖定義後，使用[PowerShell 分配它](./manage-assignments-ps.md#create-blueprint-assignments)。

有關創建高級藍圖定義的資訊，請參閱以下文章：

- 使用[靜態和動態參數](../concepts/parameters.md)。
- 自訂[藍圖排序次序](../concepts/sequencing-order.md)。
- 使用[藍圖資源鎖定](../concepts/resource-locking.md)來保護部署。
- [將藍圖管理為代碼](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。