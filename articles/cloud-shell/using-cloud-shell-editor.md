---
title: 使用 Azure Cloud Shell 編輯器 | Microsoft Docs
description: Azure Cloud Shell 編輯器使用方式概觀。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "60199186"
---
# <a name="using-the-azure-cloud-shell-editor"></a>使用 Azure Cloud Shell 編輯器

Azure Cloud Shell 包含從開放原始碼 [Monaco Editor](https://github.com/Microsoft/monaco-editor) \(英文\) 建置的整合式檔案編輯器。 Cloud Shell 編輯器支援語言醒目提示、命令選擇區，以及檔案總管等功能。

![Cloud Shell 編輯器](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>開啟編輯器

針對簡易的檔案建立和編輯，請在 Cloud Shell 終端機中執行 `code .` 來啟動編輯器。 這項動作會開啟編輯器，並在終端機中設為您目前正在使用的工作目錄。

若要直接開啟檔案以進行快速編輯，請執行 `code <filename>` 來開啟編輯器，而不透過檔案總管。

若要透過 UI 按鈕來開啟編輯器，從工具列按一下 `{}` 編輯器圖示。 這樣會開啟編輯器而且檔案總管預設會進入 `/home/<user>` 目錄。

## <a name="closing-the-editor"></a>關閉編輯器

若要關閉編輯器，請開啟編輯器右上角的`...` 動作面板窗格，然後選取 `Close editor`。

![關閉編輯器](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>命令選擇區

若要啟動命令選擇區，當焦點是設定在編輯器上時，使用 `F1` 鍵。 透過動作面板也可以開啟命令選擇區。

![命令選擇區](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>對 Monaco Editor 做出貢獻

Cloud Shell 編輯器中的語言反白顯示支援，是透過 [Monaco Editor](https://github.com/Microsoft/monaco-editor) 使用之 Monarch 語法所定義的上游功能實現的。 若要了解如何做出貢獻，請閱讀 [Monaco 參與者指南](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md) \(英文\)。

## <a name="next-steps"></a>後續步驟
[試用 Cloud Shell](quickstart.md) 
 中 Bash 的快速入門[查看整合式 Cloud Shell 工具的完整清單](features.md)