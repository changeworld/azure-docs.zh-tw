---
title: Azure Functions 中支援的語言
description: 瞭解支援哪些語言（GA），哪些是預覽版本，以及將函式開發擴充至其他語言的方式。
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 91a6ea886c3828678771b24d69bb7987af1fb105
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994896"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions 中支援的語言

本文會說明針對可搭配 Azure Functions 使用之語言所提供的支援等級。 它也會說明使用原本不支援的語言來建立函式的策略。

## <a name="levels-of-support"></a>支援等級

支援的層級有兩種：

* **正式推出 (GA)** - 完整支援且已核准用於生產環境。
* **預覽** - 尚未支援，但預期未來會正式推出。

## <a name="languages-by-runtime-version"></a>依執行階段版本的語言 

有[三個版本的 Azure Functions 運行](functions-versions.md)時間可供使用。 以下表格說明每個執行階段版本支援哪些語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers-preview"></a>自訂處理常式（預覽） 

自訂處理常式是輕量的 web 伺服器，可接收來自 Azure Functions 主機的事件。 任何支援 HTTP 基本物件的語言都可以執行自訂處理常式。 這表示自訂處理常式可以用來以未正式支援的語言建立函式。 若要深入瞭解，請參閱[Azure Functions 自訂處理常式（預覽）](functions-custom-handlers.md)。

## <a name="language-extensibility"></a>語言擴充性

從2.x 版開始，執行時間是為了提供[語言](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)擴充性而設計的。 2.x 執行階段中的 JavaScript 和 Java 語言是使用此擴充性來建置。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何以支援的語言開發函數，請參閱下列資源：

+ [C # 類別庫開發人員參考](functions-dotnet-class-library.md)
+ [C # 腳本開發人員參考](functions-reference-csharp.md)
+ [JAVA 開發人員參考](functions-reference-java.md)
+ [JavaScript 開發人員參考](functions-reference-node.md)
+ [PowerShell 開發人員參考](functions-reference-powershell.md)
+ [Python 開發人員參考](functions-reference-python.md)
+ [TypeScript 開發人員參考](functions-reference-node.md#typescript)
