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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75564668"
---
## <a name="register-extensions"></a>註冊延伸模組

除了 HTTP 和計時器觸發器外，執行階段版本 2.x 和更高版本的函數綁定作為擴展包實現。 在 Azure 函數運行時的版本 2.x 及以後，必須顯式註冊函數中使用的綁定類型的擴展。 例外情況是 HTTP 綁定和計時器觸發器，它們不需要擴展。

您可以選擇單獨安裝綁定副檔名，也可以向 host.json 專案檔案添加擴展包引用。 擴展包消除了在使用多種綁定類型時發生包相容性問題的可能性。 它是註冊綁定擴展的建議方法。 擴展包還消除了安裝 .NET Core 2.x SDK 的要求。 

### <a name="extension-bundles"></a>擴展捆綁包

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

要瞭解更多資訊，請參閱[註冊 Azure 函數綁定擴展](../articles/azure-functions/functions-bindings-register.md#extension-bundles)。 在將綁定添加到函數.json 檔之前，應向 host.json 添加擴展包。

### <a name="register-individual-extensions"></a>註冊單個擴展

如果需要安裝不在捆綁包中的擴展，可以手動註冊特定綁定的單個擴展包。 

> [!NOTE]
> 要使用`func extensions install`手動註冊擴展，必須安裝 .NET Core 2.x SDK。

在更新 function.json** 檔案以包含函式所需的所有繫結後，請在專案資料夾中執行下列命令。

```bash
func extensions install
```

此命令會讀取 function.json** 檔案，查看您需要哪些套件，加以安裝，然後重建擴充專案。 它會在目前版本中新增任何新繫結，但不會更新現有的繫結。 在安裝新的繫結時，使用 `--force` 選項將現有繫結更新為最新版本。
