---
title: 安全性
titleSuffix: Azure Cognitive Services
description: 深入瞭解認知服務使用方式的各種安全性考慮。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: dapine
ms.openlocfilehash: fa0ad8c7f75a977e1a39ff6ffd6fee08d977f57a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202001"
---
# <a name="azure-cognitive-services-security"></a>Azure 認知服務安全性

開發任何和所有應用程式時，應將安全性視為最高優先順序。 有了人工智慧功能的應用程式萌芽，安全性就更為重要。 本文概述了 Azure 認知服務安全性的各個層面，例如使用傳輸層安全性、驗證、安全地設定敏感性資料，以及客戶加密箱客戶資料存取。

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

所有透過 HTTP 公開的認知服務端點都會強制使用 TLS 1.2。 透過強制執行的安全性通訊協定，嘗試呼叫認知服務端點的取用者應遵循下列指導方針：

* 用戶端作業系統（OS）必須支援 TLS 1。2
* 用來進行 HTTP 呼叫的語言（和平臺）必須指定 TLS 1.2 做為要求的一部分。
  * 視語言和平臺而定，指定 TLS 是隱含或明確地完成

針對 .NET 使用者，請考慮<a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">傳輸層安全性最佳做法 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。

## <a name="authentication"></a>驗證

在討論驗證時，有幾個常見的誤解。 驗證和授權通常會相互混淆。 身分識別也是安全性的主要元件。 身分識別是<a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">主體 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>相關資訊的集合。 身分識別提供者（IdP）提供驗證服務的身分識別。 驗證是確認使用者身分識別的動作。 授權是指定身分識別之資源的存取權和許可權的規格。 其中幾項認知服務供應專案包含角色型存取控制（RBAC）。 RBAC 可用來簡化與手動管理主體有關的部分儀式。 如需詳細資訊，請參閱[適用于 Azure 資源的角色型存取控制](../role-based-access-control/overview.md)。

如需使用訂用帳戶金鑰、存取權杖和 Azure Active Directory （AAD）進行驗證的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">向 Azure 認知服務 <span class="docon docon-navigate-external x-hidden-focus"></span> 驗證要求</a>。

## <a name="environment-variables-and-application-configuration"></a>環境變數和應用程式設定

環境變數是名稱/值組，儲存在特定環境中。 針對機密資料使用硬式編碼值，更安全的替代方法是使用環境變數。 硬式編碼的值不安全，應予以避免。

> [!CAUTION]
> 請勿**針對機密資料使用硬**式編碼的值，這是一個主要的安全性弱點。

> [!NOTE]
> 雖然環境變數是以純文字儲存，但它們會隔離到環境中。 如果環境遭到入侵，也就是環境的變數。

### <a name="set-environment-variable"></a>設定環境變數

若要設定環境變數，請使用下列其中一個命令-，其中是已命名的索引 `ENVIRONMENT_VARIABLE_KEY` 鍵，而 `value` 是儲存在環境變數中的值。

# <a name="command-line"></a>[命令列](#tab/command-line)

根據指定的值，建立並指派保存的環境變數。

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

在**命令提示**字元的新實例中，讀取環境變數。

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

根據指定的值，建立並指派保存的環境變數。

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

根據指定的值，建立並指派保存的環境變數。

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
> 設定環境變數之後，請重新開機您的整合式開發環境（IDE），以確保可以使用新加入的環境變數。

### <a name="get-environment-variable"></a>取得環境變數

若要取得環境變數，則必須將它讀入記憶體中。 視您使用的語言而定，請考慮下列程式碼片段。 這些程式碼片段示範如何取得指定的環境變數 `ENVIRONMENT_VARIABLE_KEY` ，並將指派給名為的變數 `value` 。

# <a name="c"></a>[C#](#tab/csharp)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

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

# <a name="c"></a>[C + +](#tab/cpp)

如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

如需詳細資訊，請參閱 <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

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

如需詳細資訊，請參閱 <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

如需詳細資訊，請參閱 <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

如需詳細資訊，請參閱 <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` <span class="docon docon-navigate-external x-hidden-focus"></span></a> \(英文\)。

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>客戶加密箱

[Microsoft Azure 的客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)會提供介面供客戶審查，並核准或拒絕客戶資料存取要求。 在 Microsoft 工程師必須在支援要求期間存取客戶資料的情況下，才會使用此功能。 如需如何起始、追蹤及儲存客戶加密箱要求以供日後審查和審核的詳細資訊，請參閱[客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)。 

客戶加密箱適用于此認知服務：

* 轉譯程式

針對 Language Understanding，Microsoft 工程師將不會存取 E0 SKU 中的任何客戶資料。 若要要求能夠使用 E0 SKU，請填寫並提交 [LUIS 服務要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會放在佇列中，並已核准為可用空間。 一旦核准搭配 LUIS 使用 E0 SKU，您就必須從 Azure 入口網站建立新的 Language Understanding 資源，然後選取 [E0] 作為 [定價層]。 使用者將無法從 F0 升級至新的 E0 SKU。

語音服務目前不支援客戶加密箱。 不過，您可以使用 BYOS 來儲存客戶資料，讓您可以達成類似的資料控制項來[客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)。 請記住，語音服務資料會保留，並在語音資源建立所在的區域中進行處理。 這適用于待用資料和傳輸中的資料。 當您使用自訂功能（例如自訂語音和自訂語音）時，所有客戶資料都會在您的 BYOS （如果使用）和語音服務資源所在的相同區域中進行傳輸、儲存及處理。

> [!IMPORTANT]
> Microsoft 不**會**使用客戶資料來改善其語音模型。 此外，如果已停用端點記錄且未使用任何自訂專案，則不會儲存任何客戶資料。 

## <a name="next-steps"></a>後續步驟

* 探索各種[認知服務](welcome.md)
* 深入瞭解[認知服務虛擬網路](cognitive-services-virtual-networks.md)
