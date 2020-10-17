---
title: Azure 認知服務安全性
titleSuffix: Azure Cognitive Services
description: 瞭解認知服務使用方式的各種安全性考慮。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: ceaa04fdf8776d4fab1db4cfb1b3df4298f28de9
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152291"
---
# <a name="azure-cognitive-services-security"></a>Azure 認知服務安全性

開發任何和所有應用程式時，應將安全性視為最優先的優先考慮。 在啟用人工智慧的應用程式驗證之後，安全性更重要。 本文概述 Azure 認知服務安全性的各個層面，例如使用傳輸層安全性、驗證、安全地設定機密資料，以及客戶加密箱客戶資料存取。

## <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

所有透過 HTTP 公開的認知服務端點都會強制使用 TLS 1.2。 使用強制執行的安全性通訊協定，嘗試呼叫認知服務端點的取用者應遵守這些指導方針：

* 用戶端作業系統 (作業系統) 必須支援 TLS 1。2
* 用來進行 HTTP 呼叫的語言 (和平臺) 需要在要求中指定 TLS 1。2
  * 視語言和平臺而定，指定 TLS 是以隱含或明確的方式進行

針對 .NET 使用者，請考慮<a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">傳輸層安全性最佳作法 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。

## <a name="authentication"></a>驗證

討論驗證時，有幾個常見的誤解。 驗證和授權通常會彼此混淆。 身分識別也是安全性中的主要元件。 身分識別是關於<a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">主體 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>的資訊集合。 身分識別提供者 (IdP) 提供驗證服務的身分識別。 驗證是驗證使用者身分識別的動作。 授權是指定身分識別之資源的存取權限和許可權規格。 許多認知服務供應專案，包括 Azure 角色型存取控制 (Azure RBAC) 。 Azure RBAC 可用來簡化與手動管理主體相關的部分工作。 如需詳細資訊，請參閱 azure [資源的 azure 角色型存取控制](../role-based-access-control/overview.md)。

如需有關使用訂用帳戶金鑰進行驗證、存取權杖和 Azure Active Directory (AAD) 的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">驗證對 Azure <span class="docon docon-navigate-external x-hidden-focus"></span> 認知服務的要求</a>。

## <a name="environment-variables-and-application-configuration"></a>環境變數和應用程式設定

環境變數是儲存在特定環境中的成對名稱/值。 針對敏感性資料使用硬式編碼的值，更安全的替代方法是使用環境變數。 硬式編碼的值並不安全，應予以避免。

> [!CAUTION]
> 請勿 **針對機密資料使用硬** 式編碼的值，這會導致嚴重的安全性弱點。

> [!NOTE]
> 雖然環境變數是以純文字儲存，但是會隔離到環境。 如果環境遭到入侵，也就是環境中的變數。

### <a name="set-environment-variable"></a>設定環境變數

若要設定環境變數，請使用下列其中一個命令，其中 `ENVIRONMENT_VARIABLE_KEY` 是命名的索引鍵，而 `value` 是儲存在環境變數中的值。

# <a name="command-line"></a>[命令列](#tab/command-line)

建立並指派已保存的環境變數（指定值）。

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

在新的 **命令提示**字元實例中，讀取環境變數。

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

建立並指派已保存的環境變數（指定值）。

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

在 **Windows PowerShell**的新實例中，讀取環境變數。

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

建立並指派已保存的環境變數（指定值）。

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

在 **Bash**的新實例中，讀取環境變數。

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 設定環境變數之後，請重新開機您的整合式開發環境 (IDE) ，以確保可使用新加入的環境變數。

### <a name="get-environment-variable"></a>取得環境變數

若要取得環境變數，必須將它讀入記憶體中。 根據您所使用的語言，請考慮下列程式碼片段。 這些程式碼片段示範如何取得環境變數 `ENVIRONMENT_VARIABLE_KEY` ，並將其指派給名為的變數 `value` 。

# <a name="c"></a>[C#](#tab/csharp)

如需詳細資訊， <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

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

如需詳細資訊， <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

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

如需詳細資訊， <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

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

如需詳細資訊， <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

如需詳細資訊， <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

如需詳細資訊， <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>請參閱。

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>客戶加密箱

[適用于 Microsoft Azure 的客戶加密箱](../security/fundamentals/customer-lockbox-overview.md) 會提供介面供客戶審核，並核准或拒絕客戶資料存取要求。 在 Microsoft 工程師必須於支援要求期間存取客戶資料的情況下，便會使用此功能。 如需有關如何起始、追蹤和儲存客戶加密箱要求的詳細資訊，請參閱 [客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)。 

客戶加密箱適用于此認知服務：

* Translator

針對下列服務，Microsoft 工程師不會存取 E0 層中的任何客戶資料： 

* Language Understanding
* 臉部
* 內容仲裁
* 個人化工具

> [!IMPORTANT]
> 若為 **表單辨識器**，Microsoft 工程師將不會在2020年7月10日後建立的資源中存取任何客戶資料。

若要要求使用 E0 SKU 的能力，請填寫並提交此 [要求表單](https://aka.ms/cogsvc-cmk)。 大約需要3-5 個工作天的時間，才會收到要求的狀態。 視需求而定，您可能會被放入佇列中，並在可用的空間獲得核准。 核准搭配 LUIS 使用 E0 SKU 之後，您必須從 Azure 入口網站建立新的資源，並選取 [E0] 作為定價層。 使用者將無法從 F0 升級至新的 E0 SKU。

語音服務目前不支援客戶加密箱。 不過，您可以使用「攜帶您自己的儲存體」 (BYOS) 來儲存客戶資料，讓您可以達成客戶加密箱的類似資料控制。 請記住，語音服務資料會保留，並在建立語音資源的區域中處理。 這適用于待用資料和傳輸中的資料。 使用自訂功能（例如自訂語音和自訂語音）時，所有客戶資料會在您的 BYOS (的相同區域中傳送、儲存和處理（如果使用) 和語音服務資源）。

> [!IMPORTANT]
> Microsoft 不 **會** 使用客戶資料來改善其語音模型。 此外，如果停用端點記錄且未使用任何自訂，則不會儲存任何客戶資料。 

## <a name="next-steps"></a>後續步驟

* 探索各種 [認知服務](welcome.md)
* 深入瞭解 [認知服務虛擬網路](cognitive-services-virtual-networks.md)
