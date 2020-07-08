---
title: 使用設計工具中的協助工具功能 (預覽)
titleSuffix: Azure Machine Learning
description: 了解設計工具中提供的鍵盤快速鍵和螢幕助讀程式協助工具功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: af676f128796c762b330d986836ea825a36cb246
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646304"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>使用鍵盤以使用 Azure Machine Learning 設計工具 (預覽)

了解如何使用鍵盤和螢幕助讀程式以使用 Azure Machine Learning 設計工具。 如需在 Azure 入口網站中隨處可用的鍵盤快速鍵清單，請參閱[Azure 入口網站的鍵盤快速鍵](../azure-portal/azure-portal-keyboard-shortcuts.md)

此工作流程已經過[朗讀程式](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator)和 [JAWS](https://www.freedomscientific.com/products/software/jaws/) 測試，但該流程應該與其他標準螢幕助讀程式搭配使用。

## <a name="navigate-the-pipeline-graph"></a>瀏覽管線圖表

管線圖表會組織成巢狀清單。 外部清單是模組清單，其描述管線圖表中的所有模組。 內部清單是連線清單，描述特定模組的所有連線。  

1. 在模組清單中，使用方向鍵以切換模組。
1. 使用索引標籤開啟目標模組的連線清單。
1. 使用方向鍵在模組的連線連接埠之間切換。
1. 使用「G」前往目標模組。

## <a name="edit-the-pipeline-graph"></a>編輯管線圖表

### <a name="add-a-module-to-the-graph"></a>將模組新增至圖表

1. 使用 Ctrl+F6，將焦點從畫布切換至模組樹狀結構。
1. 使用標準的樹狀檢視控制項，在模組樹狀結構中尋找所需的模組。

### <a name="edit-a-module"></a>編輯模組

若要將模組連線到另一個模組：

1. 將模組清單中的模組設為目標時，請使用 Ctrl + Shift + H 開啟連線協助程式。
1. 編輯模組的連線連接埠。

若要調整模組屬性：

1. 將模組設為目標時，請使用 Ctrl + Shift + E 開啟模組屬性。
1. 編輯模組屬性。

## <a name="navigation-shortcuts"></a>導覽捷徑

| 按鍵 | 描述 |
|-|-|
| Ctrl + F6 | 在畫布與模組樹狀結構之間切換焦點 |
| Ctrl + F1   | 將焦點放在模組樹狀結構中的節點時，開啟資訊卡 |
| Ctrl + Shift + H | 當焦點在節點上時，開啟連線協助程式 |
| Ctrl + Shift + E | 當焦點在節點上時，開啟模組屬性 |
| Ctrl + G | 如果管線執行失敗，將焦點移至第一個失敗的節點 |

## <a name="action-shortcuts"></a>動作捷徑

將以下捷徑與便捷鍵搭配使用。 如需便捷鍵的詳細資訊，請參閱 https://en.wikipedia.org/wiki/Access_key 。

| 按鍵 | 動作 |
|-|-|
| 便捷鍵 + R | 執行 |
| 便捷鍵 + P | 發佈 |
| 便捷鍵 + C | 複製 |
| 便捷鍵 + D | 部署 |
| 便捷鍵 + I | 建立/更新推斷管線 |
| 便捷鍵 + B | 建立/更新批次推斷管線 |
| 便捷鍵 + K | 開啟 [建立推斷管線] 下拉式清單 |
| 便捷鍵 + U | 開啟 [更新推斷管線] 下拉式清單 |
| 便捷鍵 + M | 開啟 [更多(...)] 下拉式清單 |

## <a name="next-steps"></a>後續步驟

- [開啟高對比或變更佈景主題](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft 的協助工具相關工具](https://www.microsoft.com/accessibility)
