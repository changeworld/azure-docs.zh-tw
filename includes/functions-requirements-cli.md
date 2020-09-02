---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944498"
---
## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 版或更新的 2.x 版。
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
+ [Java Developer Kit](https://aka.ms/azure-jdks) 第 8 版。 

    > [!IMPORTANT]
    > + Java 11 的函數支援目前處於預覽狀態，而且 Maven 原型預設會建立 Java 8 部署。 如果想要改在 Java 11 上執行函數應用程式，您必須將 pom.xml 檔案手動更新為 Java 11 的值。 若要深入了解，請參閱 [Java 版本](../articles/azure-functions/functions-reference-java.md#java-versions)。 
    > + `JAVA_HOME` 環境變數必須設定為正確 JDK 版本的安裝位置，才能完成本快速入門。

+ [Apache Maven](https://maven.apache.org) 3.0 版或更高版本。

::: zone-end
