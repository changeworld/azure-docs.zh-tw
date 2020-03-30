---
title: 使用視覺化工作室代碼創建範本
description: 瞭解如何安裝和使用視覺化工作室代碼和 Azure 資源管理器工具擴展。
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273627"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>使用視覺化工作室代碼創建 Azure 資源管理器範本

視覺化工作室代碼是一個羽量級的多平臺開源編輯器。 Azure 資源管理器範本工具擴展是資源管理器範本開發外掛程式。 該擴展增加了對範本的語言支援，為您提供對內感知、語法突出顯示、內聯説明和許多其他語言函數。 它們共同提供了推薦的範本開發體驗。

## <a name="install-visual-studio-code"></a>安裝 Visual Studio Code

視覺化工作室代碼支援 MacOS、Windows 和 Linux。  它可以從[視覺工作室代碼](https://code.visualstudio.com/)安裝。

## <a name="install-resource-manager-tools-extension"></a>安裝 Resource Manager Tools 擴充功能

1. 開啟 Visual Studio Code。
1. 從左側**功能表中選擇"擴展**"。 或者，從 **"查看"** 功能表中，選擇 **"擴展**"以打開"擴展"窗格。

    ![安裝視覺化工作室代碼資源管理器工具擴展](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. 搜索**資源管理器**。
1. 選擇 **"在** **Azure 資源管理器工具**下安裝"。

## <a name="the-extension-features"></a>擴展功能

### <a name="colorization-for-template-language-expressions"></a>範本語言運算式的著色

參數、變數、函數、名稱和運算式均進行顏色編碼，如以下螢幕截圖所示：

![視覺化工作室代碼資源管理器工具擴展著色](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

範本大綱視圖便於流覽大型範本。

### <a name="intellisense"></a>Intellisense

資源管理器範本擴展知道函數名稱、參數、變數和引用的可能完成。 鍵入時，將彈出"IntelliSense"建議。 如果繼續鍵入字元，則成員清單（變數、方法等）將篩選為僅包括包含鍵入字元的成員。 按 **"選項卡"****或"輸入**"插入所選成員。

- 內建函數名稱

    ![視覺化工作室代碼資源管理器工具擴展感知函數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- 參數引用

    ![視覺化工作室代碼資源管理器工具擴展無意義參數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- 變數引用

    ![視覺化工作室代碼資源管理器工具擴展感知變數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- 資源組（）屬性

    ![視覺化工作室代碼資源管理器工具擴展感知函數](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

此外，Intellisense 還適用于訂閱（） 屬性和對作為物件的變數的引用的屬性。

### <a name="signature-help-for-function-parameters"></a>函數參數的簽名説明

將滑鼠懸停在函數名稱上時，擴展將顯示函數參數的簽名説明。

![視覺化工作室代碼資源管理器工具擴展簽名功能](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>轉到變數和參數引用的定義

您可以使用**Ctrl+Click**跳轉到定義，或者使用螢幕截圖所示的內容功能表：Visual ![Studio 代碼資源管理器工具擴展到定義](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

您可以使用**Ctrl_Alt_Click**將定義打開到側面。

### <a name="peek-for-variable-and-parameter-definitions"></a>查看變數和參數定義

要打開偷看編輯器，請使用內容功能表，如上一張螢幕截圖所示。

以下螢幕截圖顯示了預覽編輯器：

![視覺化工作室代碼資源管理器工具擴展預覽編輯器](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>查找變數和參數的所有引用

要查找所有引用，請使用內容功能表，如上一螢幕截圖所示。

以下螢幕截圖顯示了引用如何突出顯示：

![視覺化工作室代碼資源管理器工具擴展查找所有引用](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>重命名變數和參數的所有引用

要重命名變數和參數的所有引用，請使用內容功能表，如上一螢幕截圖所示。

### <a name="hover-for-parameter-description"></a>懸停以進行參數描述

![視覺化工作室代碼資源管理器工具擴展懸停定義](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>括號對稱

一旦游標靠近其中一個括弧，匹配的括弧就會突出顯示。 按一下大括弧時，匹配的大括弧也會突出顯示，如以下螢幕截圖所示：

![視覺化工作室代碼資源管理器工具擴展大括弧匹配](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>顯示錯誤和警告

擴展正在識別的錯誤包括：

- 未定義的參數引用
- 未定義的變數引用
- 無法識別的函數名稱
- 變數定義中的引用（）函數用法
- 函數中的參數數不正確

警告包括：

- 未使用的參數
- 未使用的變數

## <a name="next-steps"></a>後續步驟

- 要瞭解如何創建 Azure 資源管理器範本，請參閱[教程：創建和部署第一個 Azure 資源管理器範本](template-tutorial-create-first-template.md)。
- 要使用視覺化工作室代碼快速入門，請參閱[快速入門：使用視覺化工作室代碼創建 Azure 資源管理器範本](quickstart-create-templates-use-visual-studio-code.md)
