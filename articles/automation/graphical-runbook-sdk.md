---
title: 使用 Azure 自動化圖形執行簿 SDK
description: 本文介紹如何使用 Azure 自動化圖形運行手冊 SDK。
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682913"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>使用 Azure 自動化圖形執行簿 SDK

[圖形運行簿](automation-graphical-authoring-intro.md)有助於管理基礎 Windows PowerShell 或 PowerShell 工作流代碼的複雜性。 Microsoft Azure 自動化圖形創作 SDK 使開發人員能夠創建和編輯圖形運行簿,以便與 Azure 自動化一起使用。 本文介紹了從代碼創建圖形 Runbook 的基本步驟。

## <a name="prerequisites"></a>Prerequisites

將`Microsoft.Azure.Automation.GraphicalRunbook.Model`包導入專案。

## <a name="create-a-runbook-object-instance"></a>建立 Runbook 物件執行個體

請參考 `Orchestrator.GraphRunbook.Model` 組件，並建立 `Orchestrator.GraphRunbook.Model.GraphRunbook` 類別的執行個體：

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>新增 Runbook 參數

具現化 `Orchestrator.GraphRunbook.Model.Parameter` 物件，並將其新增至 Runbook：

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>新增活動和連結

將適當類型的活動具現化，並將其新增至 Runbook：

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

活動由`Orchestrator.GraphRunbook.Model`命名空間中的以下類實現。

|類別  |活動  |
|---------|---------|
|CommandActivity     | 叫用 PowerShell 命令 (cmdlet、函式等)。        |
|InvokeRunbookActivity     | 叫用另一個 Runbook 內嵌。        |
|JunctionActivity     | 等候所有內送分支完成。        |
|WorkflowScriptActivity     | 在 Runbook 的內容中執行 PowerShell 或 PowerShell 工作流程程式碼 (取決於 Runbook 類型) 的區塊。 這是功能強大的工具，但請勿過度使用：UI 會將這個指令碼區塊顯示為文字；執行引擎會將提供的區塊視為黑箱，且不會嘗試分析其內容，除了基本的語法檢查之外。 如果您只需要叫用單一 PowerShell 命令，建議使用 CommandActivity。        |

> [!NOTE]
> 不要從提供的類派生您自己的活動。 Azure 自動化不能將 Runbook 與自定義活動類型一起使用。

您必須提供`CommandActivity``InvokeRunbookActivity`和 參數作為值描述符,而不是直接值。 值描述符指定如何生成實際參數值。 目前提供下列值描述項：


|描述項  |定義  |
|---------|---------|
|ConstantValueDescriptor     | 是指硬式編碼的常數值。        |
|RunbookParameterValueDescriptor     | 是指依名稱而定的 Runbook 參數。        |
|ActivityOutputValueDescriptor     | 是指上游活動輸出，可讓一個活動「訂閱」另一個活動所產生的資料。        |
|AutomationVariableValueDescriptor     | 是指依名稱而定的自動化變數資產。         |
|AutomationCredentialValueDescriptor     | 是指依名稱而定的自動化憑證資產。        |
|AutomationConnectionValueDescriptor     | 是指依名稱而定的自動化連線資產。        |
|PowerShellExpressionValueDescriptor     | 指定自由格式 PowerShell 運算式，在即將叫用活動之前才會加以評估。  <br/>這是功能強大的工具，但請勿過度使用：UI 會將這個運算式顯示為文字；執行引擎會將提供的區塊視為黑箱，且不會嘗試分析其內容，除了基本的語法檢查之外。 如果可能的話，最好使用更明確的值描述項。      |

> [!NOTE]
> 不要從提供的類派生自己的值描述符。 Azure 自動化不能使用具有自定義值描述符類型的 Runbook。

將連線活動的連結具現化，並將其新增至 Runbook：

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>將 Runbook 儲存到檔案

使用 `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` 將 Runbook 序列化為字串：

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

您可以將此字串儲存到具有 **.graphrunbook**副檔名的檔案。 相應的 Runbook 可以導入到 Azure 自動化中。
序列化格式可能會在`Orchestrator.GraphRunbook.Model.dll`的未來版本中變更 。 我們保證回溯相容性：利用較舊版 `Orchestrator.GraphRunbook.Model.dll` 序列化的任何 Runbook 都可由新版本還原序列化。 不保證往後相容性：以較新版本序列化的 Runbook 可能無法由較舊版本還原序列化。

## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure 自動化中的圖形執行手冊的更多資訊,請參閱[圖形創作簡介](automation-graphical-authoring-intro.md)。