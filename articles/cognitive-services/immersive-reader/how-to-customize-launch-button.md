---
title: 自訂沉浸式閱讀器按鈕
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何自訂啟動沉浸式閱讀器的按鈕。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946300"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>如何自訂沉浸式閱讀器按鈕

本文演示如何自訂啟動沉浸式閱讀器的按鈕，以滿足應用程式的需求。

## <a name="add-the-immersive-reader-button"></a>添加沉浸式閱讀器按鈕

沉浸式讀取器 SDK 為啟動沉浸式閱讀器的按鈕提供預設樣式。 使用`immersive-reader-button`類屬性啟用此樣式。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>自訂按鈕樣式

使用`data-button-style`屬性設置按鈕的樣式。 允許的值是`icon`，`text`和`iconAndText`。 預設值是 `icon`。

### <a name="icon-button"></a>圖示按鈕

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

這呈現以下內容：

![圖示按鈕](./media/button-icon.png)

### <a name="text-button"></a>文本按鈕

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

這呈現以下內容：

![圖示按鈕](./media/button-text.png)

### <a name="icon-and-text-button"></a>圖示和文本按鈕

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

這呈現以下內容：

![圖示按鈕](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>自訂按鈕文本

使用`data-locale`屬性配置按鈕的語言和 alt 文本。 預設語言為英文。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>自訂圖示的大小

可以使用`data-icon-px-size`屬性配置沉浸式讀取器圖示的大小。 這將設置以圖元為單位的圖示大小。 預設大小為 20px。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)