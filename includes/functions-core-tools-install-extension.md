---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75564668"
---
## <a name="register-extensions"></a>註冊延伸模組

除了 HTTP 和計時器觸發程式以外，執行時間2.x 版和更新版本中的函式系結會實作為擴充封裝。 在2.x 版和之後的 Azure Functions 執行時間中，您必須明確地為函式中使用的系結類型註冊擴充功能。 例外狀況為 HTTP 系結和計時器觸發程式，這不需要延伸模組。

您可以選擇個別安裝系結延伸模組，也可以將延伸模組配套參考新增至主機. json 專案檔。 延伸模組配套會移除在使用多個系結類型時，發生套件相容性問題的機會。 這是註冊系結延伸模組的建議方法。 延伸模組配套也會移除安裝 .NET Core 2.x SDK 的需求。 

### <a name="extension-bundles"></a>延伸模組配套

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

若要深入瞭解，請參閱[註冊 Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles)系結延伸模組。 在將系結新增至函式. json 檔案之前，您應該先將擴充功能配套新增至主機。

### <a name="register-individual-extensions"></a>註冊個別的延伸模組

如果您需要安裝不在配套中的延伸模組，可以針對特定系結手動註冊個別的延伸模組套件。 

> [!NOTE]
> 若要使用`func extensions install`手動註冊擴充功能，您必須安裝 .net CORE 2.x SDK。

在更新 function.json** 檔案以包含函式所需的所有繫結後，請在專案資料夾中執行下列命令。

```bash
func extensions install
```

此命令會讀取 function.json** 檔案，查看您需要哪些套件，加以安裝，然後重建擴充專案。 它會在目前版本中新增任何新繫結，但不會更新現有的繫結。 在安裝新的繫結時，使用 `--force` 選項將現有繫結更新為最新版本。
