---
title: Azure Functions 中支援的語言
description: 了解支援哪些語言 (GA) 以及哪些語言仍在實驗性或預覽版階段。
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942251"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions 中支援的語言

本文會說明針對可搭配 Azure Functions 使用之語言所提供的支援等級。

## <a name="levels-of-support"></a>支援等級

支援等級有三個：

* **正式推出 (GA)** - 完整支援且已核准用於生產環境。
* **預覽** - 尚未支援，但預期未來會正式推出。
* **實驗性** - 不支援，且未來可能會放棄，不保證最後會提供預覽或正式推出。

## <a name="languages-by-runtime-version"></a>依執行階段版本的語言 

有[三個版本的 Azure Functions 運行](functions-versions.md)時間可供使用。 以下表格說明每個執行階段版本支援哪些語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>實驗性語言

1.x 版的實驗性語言並未妥善調整，因此不支援所有繫結。

請勿針對您所依賴的任何項目使用實驗性功能，因為那些功能並未受到正式支援。 請勿針對實驗性語言相關的問題建立支援案例。 

較新的執行階段版本不支援實驗性語言。 只有在生產環境可支援新語言時，才會新增該語言的支援。 

### <a name="language-extensibility"></a>語言擴充性

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
