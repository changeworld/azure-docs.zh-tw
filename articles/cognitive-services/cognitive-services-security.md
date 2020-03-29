---
title: 安全性
titleSuffix: Azure Cognitive Services
description: 瞭解認知服務使用的各種安全注意事項。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131525"
---
# <a name="azure-cognitive-services-security"></a>Azure 認知服務安全性

在開發任何和所有應用程式時，安全性應被視為重中之重。 隨著啟用人工智慧的應用程式的建立，安全性就顯得尤為重要。 在本文中，概述了 Azure 認知服務安全性的各個方面，例如使用傳輸層安全性、身份驗證和安全配置敏感性資料。

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

通過 HTTP 公開的所有認知服務終結點都強制實施 TLS 1.2。 使用強制安全協定，嘗試調用認知服務終結點的消費者應遵守以下準則：

* 用戶端作業系統 （OS） 需要支援 TLS 1.2
* 用於進行 HTTP 調用的語言（和平臺）需要指定 TLS 1.2 作為請求的一部分
  * 根據語言和平臺的不同，指定 TLS 是隱式或顯式完成的

對於 .NET 使用者，請考慮<a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">傳輸層安全最佳實踐<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="authentication"></a>驗證

在討論身份驗證時，有幾個常見的誤解。 身份驗證和授權經常相互混淆。 身份也是安全性的重要組成部分。 標識是有關<a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">主體<span class="docon docon-navigate-external x-hidden-focus"></span></a>的資訊的集合。 標識提供程式 （IdP） 向身份驗證服務提供標識。 身份驗證是驗證使用者身份的行為。 授權是指定給定標識的資源存取權限和許可權的規範。 一些認知服務產品包括基於角色的存取控制 （RBAC）。 RBAC 可用於簡化手動管理主體所涉及的一些儀式。 有關詳細資訊，請參閱[Azure 資源的基於角色的存取控制](../role-based-access-control/overview.md)。

有關使用訂閱金鑰、訪問權杖和 Azure 活動目錄 （AAD） 進行身份驗證的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">對 Azure<span class="docon docon-navigate-external x-hidden-focus"></span>認知服務的身份驗證請求</a>。

## <a name="environment-variables-and-application-configuration"></a>環境變數和應用程式佈建

環境變數是名稱值對，存儲在特定環境中。 對敏感性資料使用硬編碼值的一個更安全的替代方法是使用環境變數。 硬編碼值不安全，應避免使用。

> [!CAUTION]
> **不要**對敏感性資料使用硬編碼值，這樣做是一個主要的安全性漏洞。

> [!NOTE]
> 當環境變數以純文字形式存儲時，它們被隔離到環境中。 如果環境遭到破壞，則環境中的變數也會受到影響。

### <a name="set-environment-variable"></a>設置環境變數

要設置環境變數，請使用以下命令之一 - 其中`ENVIRONMENT_VARIABLE_KEY`是命名鍵，`value`是存儲在環境變數中的值。

# <a name="command-line"></a>[命令列](#tab/command-line)

創建和分配持久化環境變數（給定值）。

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

在**命令提示符**的新實例中，讀取環境變數。

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[電源外殼](#tab/powershell)

創建和分配持久化環境變數（給定值）。

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

在**Windows PowerShell**的新實例中，讀取環境變數。

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

創建和分配持久化環境變數（給定值）。

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

在**Bash**的新實例中，讀取環境變數。

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 設置環境變數後，重新開機整合式開發環境 （IDE），以確保新添加的環境變數可用。

### <a name="get-environment-variable"></a>獲取環境變數

要獲取環境變數，必須將其讀取到記憶體中。 根據所使用的語言，請考慮以下程式碼片段。 這些程式碼片段演示如何獲取環境變數給定`ENVIRONMENT_VARIABLE_KEY`並分配給名為 的`value`變數。

# <a name="c"></a>[C#](#tab/csharp)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

有關詳細資訊，請參閱<a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[JAVA](#tab/java)

有關詳細資訊，請參閱<a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

有關詳細資訊，請參閱<a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

有關詳細資訊，請參閱<a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[目標C](#tab/objective-c)

有關詳細資訊，請參閱<a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment`<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>後續步驟

* 探索各種[認知服務](welcome.md)
* 瞭解有關[認知服務虛擬網路的更多資訊](cognitive-services-virtual-networks.md)
