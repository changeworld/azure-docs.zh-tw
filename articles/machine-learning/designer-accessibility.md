---
title: 在設計器中使用協助工具（預覽版）
titleSuffix: Azure Machine Learning
description: 瞭解設計器中提供的鍵盤快速鍵和螢幕閱讀器協助工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366189"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>使用鍵盤使用 Azure 機器學習設計器（預覽版）

瞭解如何使用鍵盤和螢幕閱讀器使用 Azure 機器學習設計器。 有關 Azure 門戶中所有位置工作的鍵盤快速鍵的清單，請參閱[Azure 門戶中的鍵盤快速鍵](../azure-portal/azure-portal-keyboard-shortcuts.md)

此工作流已過與[講述人和](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) [JAWS](https://www.freedomscientific.com/products/software/jaws/)一起測試，但它應與其他標準螢幕閱讀器配合使用。

## <a name="navigate-the-pipeline-graph"></a>導航管道圖

管道圖作為嵌套清單組織。 外部清單是一個模組清單，它描述了管道圖中的所有模組。 內部清單是一個連接清單，它描述特定模組的所有連接。  

1. 在模組清單中，使用方向鍵切換模組。
1. 使用選項卡打開目的模組的連接清單。
1. 使用方向鍵在模組的連接埠之間切換。
1. 使用"G"轉到目的模組。

## <a name="edit-the-pipeline-graph"></a>編輯管道圖

### <a name="add-a-module-to-the-graph"></a>向圖形添加模組

1. 使用 Ctrl_F6 將焦點從畫布切換到模組樹。
1. 使用標準樹狀檢視控制項在模組樹中查找所需的模組。

### <a name="edit-a-module"></a>編輯模組

要將模組連接到其他模組，請進行：

1. 在模組清單中定位模組時，使用 Ctrl + Shift + H 打開連接協助程式。
1. 編輯模組的連接埠。

要調整模組屬性：

1. 定位模組時，使用 Ctrl + Shift + E 打開模組屬性。
1. 編輯模組屬性。

## <a name="navigation-shortcuts"></a>導航快捷方式

| 按鍵輸入 | 描述 |
|-|-|
| Ctrl + F6 | 在畫布和模組樹之間切換焦點 |
| Ctrl = F1   | 聚焦模組樹中的節點時打開資訊卡 |
| Ctrl = 移位 = H | 當焦點位於節點上時打開連接説明器 |
| Ctrl = 移位 = E | 焦點位於節點上時打開模組屬性 |
| Ctrl = G | 如果管道運行失敗，將焦點移動到第一個失敗節點 |

## <a name="action-shortcuts"></a>操作快捷方式

使用以下快捷方式與訪問金鑰。 有關訪問金鑰的詳細資訊，請參閱https://en.wikipedia.org/wiki/Access_key。

| 按鍵輸入 | 動作 |
|-|-|
| 便捷鍵 = R | 執行 |
| 便捷鍵 = P | 發佈 |
| 訪問金鑰 = C | 複製 |
| 便捷鍵 = D | 部署 |
| 便捷鍵 = I | 創建/更新推理管道 |
| 訪問金鑰 = B | 創建/更新批次處理推理管道 |
| 便捷鍵 = K | 打開"創建推理管道"下拉清單 |
| 便捷鍵 = U | 打開"更新推理管道"下拉清單 |
| 便捷鍵 = M | 打開更多（...） 下拉清單 |

## <a name="next-steps"></a>後續步驟

- [開啟高對比或變更佈景主題](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [微軟的協助工具相關工具](https://www.microsoft.com/accessibility)
