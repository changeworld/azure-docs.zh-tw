---
title: 使用 Visual Studio Code 建立範本
description: 瞭解如何安裝及使用 Visual Studio Code 和 Azure Resource Manager 工具擴充功能。
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273627"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>使用 Visual Studio Code 建立 Azure Resource Manager 範本

Visual Studio Code 是輕量、多平臺、開放原始碼編輯器。 Azure Resource Manager 範本工具擴充功能是用於 Resource Manager 範本開發的外掛程式。 延伸模組會新增範本的語言支援，以提供 intellisense、語法醒目提示、內嵌說明，以及許多其他語言功能。 它們一起提供建議的範本開發體驗。

## <a name="install-visual-studio-code"></a>安裝 Visual Studio Code

Visual studio Code 支援 MacOS、Windows 和 Linux。  您可以從[Visual Studio Code](https://code.visualstudio.com/)安裝此檔案。

## <a name="install-resource-manager-tools-extension"></a>安裝 Resource Manager Tools 擴充功能

1. 開啟 Visual Studio Code。
1. 從左側功能表中選取 [**擴充**功能]。 或者，從 [ **View** ] 功能表選取 [**擴充**功能] 以開啟 [擴充功能] 窗格。

    ![安裝 Visual Studio Code Resource Manager 工具擴充功能](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. 搜尋**Resource Manager**。
1. 在 [ **Azure Resource Manager 工具**] 下選取 [**安裝**]。

## <a name="the-extension-features"></a>延伸模組功能

### <a name="colorization-for-template-language-expressions"></a>範本語言運算式的顏色標示

參數、變數、函式、名稱和運算式會以色彩標示，如下列螢幕擷取畫面所示：

![Visual Studio Code Resource Manager 工具擴充功能顏色標示](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

範本大綱視圖可讓您輕鬆流覽大型範本。

### <a name="intellisense"></a>Intellisense

Resource Manager 範本延伸模組知道函數名稱、參數、變數和參考的可能完成。 IntelliSense 建議會在您輸入時彈出。 如果您繼續輸入字元，則會篩選成員清單（變數、方法等等），只包含包含您具類型字元的成員。 按**tab**或**Enter**會插入選取的成員。

- 內建函數名稱

    ![Visual Studio Code Resource Manager 工具擴充功能 intellisense 函式](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- 參數參考

    ![Visual Studio Code Resource Manager 工具擴充功能 intellisense 參數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- 變數參考

    ![Visual Studio Code Resource Manager 工具擴充功能 intellisense 變數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup （）屬性

    ![Visual Studio Code Resource Manager 工具擴充功能 intellisense 函式](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

此外，intellisense 也適用于訂用帳戶（）屬性，以及屬於物件之變數的參考屬性。

### <a name="signature-help-for-function-parameters"></a>函數參數的簽章說明

當滑鼠停留在函式名稱上時，延伸模組會顯示函數參數的簽章說明。

![Visual Studio Code Resource Manager 工具擴充功能簽名函式](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>移至變數和參數參考的定義

您可以使用**Ctrl + 按一下**來跳至定義，或使用內容功能表（如螢幕擷取畫面： ![Visual Studio Code Resource Manager 工具延伸模組 [移至定義] 中所示）](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

您可以使用**Ctrl + Alt + 按一下**來開啟這一端的定義。

### <a name="peek-for-variable-and-parameter-definitions"></a>查看變數和參數定義

若要開啟 [查看編輯器]，請使用內容功能表，如先前的螢幕擷取畫面所示。

下列螢幕擷取畫面顯示 [查看編輯器]：

![Visual Studio Code Resource Manager 工具延伸模組查看編輯器](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>尋找變數和參數的所有參考

若要尋找所有參考，請使用內容功能表，如先前的螢幕擷取畫面所示。

下列螢幕擷取畫面顯示如何反白顯示參考：

![Visual Studio Code Resource Manager 工具延伸模組尋找所有參考](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>為變數和參數重新命名所有參考

若要為變數和參數重新命名所有參考，請使用內容功能表，如先前的螢幕擷取畫面所示。

### <a name="hover-for-parameter-description"></a>滑鼠停留以取得參數描述

![Visual Studio Code Resource Manager 工具延伸模組暫留定義](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>括號對稱

一旦游標接近其中一處，就會反白顯示相符的括弧。 當您按一下大括弧時，相符的大括弧也會反白顯示，如下列螢幕擷取畫面所示：

![Visual Studio Code Resource Manager 工具擴充括弧對應](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>顯示錯誤和警告

延伸模組所識別的錯誤包括：

- 未定義的參數參考
- 未定義的變數參考
- 無法辨識的函式名稱
- reference （）變數定義中的函數用法
- 函數中的引數數目不正確

警告包括：

- 未使用的參數
- 未使用的變數

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立 Azure Resource Manager 範本，請參閱[教學課程：建立及部署您的第一個 Azure Resource Manager 範本](template-tutorial-create-first-template.md)。
- 若要使用 Visual Studio Code 進行快速入門，請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](quickstart-create-templates-use-visual-studio-code.md)
