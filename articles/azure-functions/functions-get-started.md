---
title: 開始使用 Azure Functions
description: 執行處理 Azure Functions 的首要步驟。
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975003"
---
# <a name="getting-started-with-azure-functions"></a>開始使用 Azure Functions

## <a name="introduction"></a>簡介

[Azure Functions](./functions-overview.md) 可讓您將系統的邏輯實作為可供使用的程式碼區塊。 這些程式碼區塊稱為「函式」。

請利用下列資源開始使用。

::: zone pivot="programming-language-csharp"

| 動作 | 資源 |
| --- | --- |
| **建立您的第一個函式** | 使用下列其中一項工具：<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[命令列](./create-first-function-cli-csharp.md) |
| **查看執行中的函式** | <li>[Azure 範例瀏覽器](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Azure 社群程式庫](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **探索互動式教學課程**| <li>[針對商務案例選擇最佳的 Azure 無伺服器技術](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[結構完善的架構 - 效能效率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用觸發程序執行 Azure 函式](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>如需[互動式教學課程的完整清單](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)，請參閱 Microsoft Learn。|
| **深入了解** | <li>了解函式如何[自動增加或減少](./functions-scale.md)執行個體以符合需求<li>探索可用的不同[部署方法](./functions-deployment-technologies.md)<li>利用內建的[監視工具](./functions-monitoring.md)來分析您的函式<li>閱讀 [C# 語言參考](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| 動作 | 資源 |
| --- | --- |
| **建立您的第一個函式** | 使用下列其中一項工具：<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[使用終端機/命令提示字元的 Java/Maven 函式](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **查看執行中的函式** | <li>[Azure 範例瀏覽器](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Azure 社群程式庫](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **探索互動式教學課程**| <li>[針對商務案例選擇最佳的 Azure 無伺服器技術](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[結構完善的架構 - 效能效率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用適用於 Azure Functions 的 Maven 外掛程式來開發應用程式](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>如需[互動式教學課程的完整清單](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)，請參閱 Microsoft Learn。|
| **深入了解** | <li>了解函式如何[自動增加或減少](./functions-scale.md)執行個體以符合需求<li>探索可用的不同[部署方法](./functions-deployment-technologies.md)<li>利用內建的[監視工具](./functions-monitoring.md)來分析您的函式<li>閱讀 [Java 語言參考](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| 動作 | 資源 |
| --- | --- |
| **建立您的第一個函式** | 使用下列其中一項工具：<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js 終端機/命令提示字元](./create-first-function-cli-java.md) |
| **查看執行中的函式** | <li>[Azure 範例瀏覽器](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Azure 社群程式庫](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **探索互動式教學課程** | <li>[針對商務案例選擇最佳的 Azure 無伺服器技術](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[結構完善的架構 - 效能效率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 建置無伺服器 API](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 建立無伺服器邏輯](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[使用 Azure Functions 將 Node.js 和 Express API 重構為無伺服器 API](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>如需[互動式教學課程的完整清單](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)，請參閱 Microsoft Learn。|
| **深入了解** | <li>了解函式如何[自動增加或減少](./functions-scale.md)執行個體以符合需求<li>探索可用的不同[部署方法](./functions-deployment-technologies.md)<li>利用內建的[監視工具](./functions-monitoring.md)來分析您的函式<li>閱讀 [JavaScript](./functions-reference-node.md) 或 [TypeScript](./functions-reference-node.md#typescript) 語言參考|
::: zone-end

::: zone pivot="programming-language-powershell"
| 動作 | 資源 |
| --- | --- |
| **建立您的第一個函式** | <li>使用 [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **查看執行中的函式** | <li>[Azure 範例瀏覽器](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Azure 社群程式庫](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **探索互動式教學課程** | <li>[針對商務案例選擇最佳的 Azure 無伺服器技術](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[結構完善的架構 - 效能效率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 建置無伺服器 API](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 建立無伺服器邏輯](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[使用觸發程序執行 Azure 函式](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>如需[互動式教學課程的完整清單](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)，請參閱 Microsoft Learn。|
| **深入了解** | <li>了解函式如何[自動增加或減少](./functions-scale.md)執行個體以符合需求<li>探索可用的不同[部署方法](./functions-deployment-technologies.md)<li>利用內建的[監視工具](./functions-monitoring.md)來分析您的函式<li>閱讀 [PowerShell 語言參考](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| 動作 | 資源 |
| --- | --- |
| **建立您的第一個函式** | 使用下列其中一項工具：<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[終端機/命令提示字元](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **查看執行中的函式** | <li>[Azure 範例瀏覽器](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Azure 社群程式庫](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **探索互動式教學課程** | <li>[針對商務案例選擇最佳的 Azure 無伺服器技術](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[結構完善的架構 - 效能效率](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 建置無伺服器 API](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 建立無伺服器邏輯](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>如需[互動式教學課程的完整清單](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)，請參閱 Microsoft Learn。|
| **深入了解** | <li>了解函式如何[自動增加或減少](./functions-scale.md)執行個體以符合需求<li>探索可用的不同[部署方法](./functions-deployment-technologies.md)<li>利用內建的[監視工具](./functions-monitoring.md)來分析您的函式<li>閱讀 [Python 語言參考](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Azure Functions 應用程式的剖析](./functions-reference.md)
