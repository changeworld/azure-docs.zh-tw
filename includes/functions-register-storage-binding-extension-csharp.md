---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78201029"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>註冊繫結延伸模組

除了 HTTP 和計時器觸發程序之外，繫結皆會以擴充套件的形式實作。 在終端機視窗中執行下列 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令來將儲存體套件新增至您的專案。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

現在，您可以將儲存體輸出繫結新增至您的專案。  
::: zone-end  