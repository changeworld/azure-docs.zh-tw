---
title: 使用 PowerShell 匯入及匯出藍圖
description: 瞭解如何將藍圖定義用作代碼。 使用匯出和導入命令共用、原始程式碼管理並管理它們。
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686844"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>使用 PowerShell 匯入及匯出藍圖定義

Azure 藍圖可以通過 Azure 門戶完全管理。 隨著組織在 Azure 藍圖的使用方面取得進展,他們應該開始將藍圖定義視為託管代碼。 這個概念通常被稱為基礎結構作為代碼 (IaC)。 將藍圖定義視為代碼提供了 Azure 門戶提供的其他優勢。 這些優勢包括:

- 共用藍圖定義
- 備份藍圖定義
- 重用不同租戶或訂閱中的藍圖定義
- 將藍圖定義置於源碼管理中
  - 測試環境中藍圖定義的自動測試
  - 支援持續整合及持續部署 (CI/CD) 導管

無論您的理由是什麼,將藍圖定義管理為代碼都有其好處。 本文演示如何在[Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/)`Export-AzBlueprintWithArtifact`模組`Import-AzBlueprintWithArtifact`中使用 和 命令。

## <a name="prerequisites"></a>Prerequisites

本文假定 Azure 藍圖具有中等的工作知識。 如果您尚未這樣做,則執行以下文章:

- [在入口網站中建立藍圖](../create-blueprint-portal.md)
- 閱讀有關[部署階段](../concepts/deployment-stages.md)與[藍圖生命週期](../concepts/lifecycle.md)
- 使用 PowerShell[建立](../create-blueprint-powershell.md)[及管理](./manage-assignments-ps.md)藍圖定義及分配

如果尚未安裝 **Az.Blueprint** 模組，請依照[新增 Az.Blueprint 模組](./manage-assignments-ps.md#add-the-azblueprint-module)中的指示，從 PowerShell 資源庫安裝並驗證它。

## <a name="folder-structure-of-a-blueprint-definition"></a>藍圖定義的資料夾結構

在查看匯出和導入藍圖之前,我們來看看構成藍圖定義的文件的構造方式。 藍圖定義應存儲在其自己的資料夾中。

> [!IMPORTANT]
> 如果未將任何值傳遞給`Import-AzBlueprintWithArtifact`cmdlet 的名稱參數,則將使用藍圖定義中儲存的資料夾的**Name**名稱。

與必須命名的`blueprint.json`藍圖定義一起,是藍圖定義由工件組成的。 每個項目必須位於名為`artifacts`的子資料夾中。
綜合起來,藍圖定義的結構作為資料夾中的 JSON 檔應如下所示:

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
  - 取得`Get-AzBlueprint`參考物件
- **輸出路徑**[必需]
  - 指定將藍圖定義 JSON 檔案儲存到
  - 輸出檔案位於具有藍圖定義名稱的子資料夾中
- **版本**(選擇性的)
  - 如果**藍圖**引用物件包含對多個版本的引用,則指定要輸出的版本。

1. 取得對藍圖定義的引用,以便從表示為`{subId}`的訂閱匯出:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用`Export-AzBlueprintWithArtifact`cmdlet 匯出指定的藍圖定義:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>匯入藍圖定義

在[所需的資料夾結構](#folder-structure-of-a-blueprint-definition)中具有[匯出的藍圖定義](#export-your-blueprint-definition)或手動創建的藍圖定義後,可以將該藍圖定義導入到不同的管理組或訂閱。

有關內建藍圖定義的範例,請參閱 Azure 藍圖[GitHub 儲存函式庫](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)。

- **名稱**[必需]
  - 指定新藍圖定義的名稱
- **輸入路徑**[必需]
  - 指定從
  - 必須符合[需要的資料夾結構](#folder-structure-of-a-blueprint-definition)
- **管理群組代碼(** 選擇性的)
  - 管理群組 ID,用於將藍圖定義儲存到目前上下文預設值(如果不是目前上下文預設)
  - 必須指定**管理群組 Id**或**訂閱 Id**
- **訂閱 Id(** 選擇性的 )
  - 將藍圖定義儲存到目前上下文預設值(如果不是目前上下文預設值)的訂閱 ID
  - 必須指定**管理群組 Id**或**訂閱 Id**

1. 使用`Import-AzBlueprintWithArtifact`cmdlet 匯入指定的藍圖定義:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

匯入藍圖定義後,使用[PowerShell 分配它](./manage-assignments-ps.md#create-blueprint-assignments)。

有關創建高級藍圖定義的資訊,請參閱以下文章:

- [使用 靜態與動態參數](../concepts/parameters.md)。
- 自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 使用[藍圖資源鎖定](../concepts/resource-locking.md)來保護部署。
- [將藍圖管理為代碼](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。