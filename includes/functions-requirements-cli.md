---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673141"
---
## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 版或更新的 2.x 版。
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 和 3.7 需要 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 版或更新的 2.x 版。 Python 3.8 需要 [版本 3.x](../articles/azure-functions/functions-run-local.md#v2) 的 Core Tools。
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 版或更新版本。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)、作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 位元)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 位元)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 位元)](https://www.python.org/downloads/release/python-368/)(由 Azure Functions 支援)。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks) 第 8 版。

+ [Apache Maven](https://maven.apache.org) 3.0 版或更高版本。

> [!IMPORTANT]
> JAVA_HOME 環境變數必須設定為 JDK 的安裝位置，才能完成本快速入門。
::: zone-end