---
title: 配置檔案以進行轉換
description: 有關如何最好將檔案放入輸入容器中的建議。
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 13c4b841fe2d0b62472628d9382b5f6ee3d1fa6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318070"
---
# <a name="laying-out-files-for-conversion"></a>配置檔案以進行轉換

為了正確處理資產，轉換服務必須能夠找到所有輸入檔。
這些是由主要資產檔案所轉換，通常是由資產檔案內的路徑所參考的其他檔案。
轉換資產的要求會提供兩個參數，決定轉換服務找到這些檔案的方式： `input.folderPath` (是選擇性的) 和 `input.inputAssetPath` 。
它們會完整記載在 [轉換 REST API](conversion-rest-api.md) 頁面中。
為了設定檔案，要注意的重要事項是，會 `folderPath` 決定在處理資產時，可供轉換服務使用的一組完整檔案。

## <a name="placing-files-so-they-can-be-found"></a>放置檔案以便找到它們

當來源資產利用外部檔案時，這些檔案的路徑會儲存在資產中。
轉換服務必須在檔案系統中解讀不同于資產原始檔案系統的路徑。
如果路徑儲存為相對路徑，且來源資產與其參考的檔案之間的相對位置沒有變更，則轉換服務很容易就能找到參考的檔案。

> [!Note]
> 建議您將檔案放在輸入容器中，使檔案的相對位置與建立資產時相同。

> [!Note]
> 偏好建立附有相對路徑的資產。
> 針對 [3Ds max 設定教材](../../tutorials/modeling/3dsmax-material-setup.md) 的教學課程，提供了3Ds 的最大範例，說明如何確保資產使用相對路徑。

## <a name="finding-textures"></a>尋找材質

由於有許多方式可以產生資產，轉換服務必須有彈性。
尤其是，它必須處理資產中的路徑和紋理的位置無法精確地相符的情況。
例如，當產生的資產包含絕對路徑時，因為這些路徑永遠不會符合轉換服務所使用的檔案系統。
為了處理這種情況，我們會使用最佳的方法來尋找材質。

找出紋理的演算法如下：指定儲存在資產中的路徑時，尋找最長的子路徑尾碼，當作為來源資產位置的相對路徑時，會以現有的檔案為目標。
如果沒有這類的子路徑 (包括整個路徑) 以檔案為目標，則會將材質視為遺失。

請考慮下列假設檔案系統： 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
如果 myAsset 參考的材質具有相對路徑 `..\Textures\MyAssetTextures\myTexture.png` ，則轉換服務將會使用 File E。事實上，它可以使用 A、C 和 E 的任何檔案（如果存在的話），因為它是以最長的後置字元找到，所以慣用的是 file E。
因為不 `Textures\myTexture.png` 是預存路徑之任何尾碼的一部分，所以永遠不會使用檔案 B 和 D。
如果資產改為包含路徑 `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` 或 `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` ，則轉換服務會找出檔案 A 和 C （如果有的話）， (在) 上偏好 C。 不過，這個方法找不到電子郵件，而且必須重新放置檔案。
您可以藉由移動資產旁的材質資料夾來修正此問題。

> [!Note]
> 如果找不到材質，可能的因應措施是確保資產是包含紋理的部分子樹的同級。

## <a name="next-steps"></a>後續步驟

- [模型轉換](model-conversion.md)
