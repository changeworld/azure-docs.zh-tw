---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: f102a5dd5b7dccba6643176d06d17a2a65171c90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904057"
---
## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 版或更新版本。
::: zone-end  
::: zone pivot="programming-language-python"
+ 對應至已安裝 Python 版本的 Azure Functions Core Tools 版本：

   | Python 版本 | Core Tools 版本 |
   | -------------- | ------------------ |
   | Python 3.8     | [3.x 版](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [版本 2.7.1846 或更新版本](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 版或更新版本。 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)、作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 位元)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 位元)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 位元)](https://www.python.org/downloads/release/python-368/)(由 Azure Functions 支援)。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 第 8 或第 11 版。 

+ [Apache Maven](https://maven.apache.org) 3.0 版或更高版本。

::: zone-end
::: zone pivot="programming-language-other"
+ 您所用語言的開發工具。 本教學課程使用 [R 程式設計語言](https://www.r-project.org/)作為範例。
::: zone-end