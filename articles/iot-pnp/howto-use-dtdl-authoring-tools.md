---
title: 使用工具來撰寫和驗證 DTDL 模型 |Microsoft Docs
description: 安裝 Visual Studio Code 或 Visual Studio 2019 的 DTDL 編輯器，並用它來撰寫 IoT 隨插即用模型。
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 59ea0258d6262f652e9ac563bbda5ec89c4c5819
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579957"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>安裝和使用 DTDL authoring tools

[數位 Twins 定義語言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 模型是 JSON 檔案。 您可以使用擴充功能來 Visual Studio 程式碼或 Visual Studio 2019，以撰寫並驗證這些模型檔案。

## <a name="install-and-use-the-vs-code-extension"></a>安裝和使用 VS Code 擴充功能

VS Code 的 DTDL 延伸模組會新增下列 DTDL 撰寫功能：

- DTDL v2 語法驗證。
- Intellisense （包括自動完成）可協助您使用語言語法。
- 從命令選擇區建立介面的能力。

若要安裝 DTDL 延伸模組，請移至 [ [DTDL 編輯器](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)] 以 Visual Studio Code。 您也可以在 VS Code 的 [擴充功能] 視圖中搜尋 **DTDL** 。

當您已安裝擴充功能時，請使用它來協助您在 VS code 中撰寫 DTDL 模型檔案：

- 此延伸模組會在 DTDL 模型檔案中提供語法驗證，並反白顯示錯誤，如下列螢幕擷取畫面所示：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="VS Code 中的模型驗證":::

- 當您編輯 DTDL 模型時，請使用 intellisense 和自動完成：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="VS Code 中的模型驗證":::

- 建立新的 DTDL 介面。 下列命令會建立具有新介面的 JSON 檔案。 此介面包含範例遙測、屬性和命令定義。

## <a name="install-and-use-the-visual-studio-extension"></a>安裝和使用 Visual Studio 擴充功能

Visual Studio 2019 的 DTDL 延伸模組新增下列 DTDL 撰寫功能：

- DTDL v2 語法驗證。
- Intellisense （包括自動完成）可協助您使用語言語法。

若要安裝 DTDL 延伸模組，請移至 [VS 2019 的 DTDL Language 支援](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)。 您也可以在 Visual Studio 的 [**管理擴充**功能] 中搜尋**DTDL** 。

當您已安裝擴充功能時，請使用它來協助您在 Visual Studio 中撰寫 DTDL 模型檔案：

- 此延伸模組會在 DTDL 模型檔案中提供語法驗證，並反白顯示錯誤，如下列螢幕擷取畫面所示：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="VS Code 中的模型驗證":::

- 當您編輯 DTDL 模型時，請使用 intellisense 和自動完成：

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="VS Code 中的模型驗證":::

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何使用 Visual Studio Code 的 DTDL 延伸模組，以及 Visual Studio 2019 來撰寫和驗證 DTDL 模型檔案。 建議的下一個步驟是瞭解如何搭配使用 [Azure IoT explorer 與您的模型和裝置](./howto-use-iot-explorer.md)。
