---
title: 自訂沉浸式讀取器按鈕
titleSuffix: Azure Cognitive Services
description: 本文將說明如何自訂啟動沉浸式讀取器的按鈕。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946300"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>如何自訂沉浸式讀取器按鈕

本文示範如何自訂啟動沉浸式讀取器的按鈕，以符合您應用程式的需求。

## <a name="add-the-immersive-reader-button"></a>新增沉浸式讀取器按鈕

沉浸式讀取器 SDK 會為啟動沉浸式讀取器的按鈕提供預設樣式。 使用`immersive-reader-button`類別屬性來啟用此樣式。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>自訂按鈕樣式

使用`data-button-style`屬性來設定按鈕的樣式。 允許的值為`icon`、 `text`和`iconAndText`。 預設值是 `icon`。

### <a name="icon-button"></a>圖示按鈕

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

這會呈現下列內容：

![圖示按鈕](./media/button-icon.png)

### <a name="text-button"></a>文字按鈕

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

這會呈現下列內容：

![圖示按鈕](./media/button-text.png)

### <a name="icon-and-text-button"></a>圖示和文字按鈕

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

這會呈現下列內容：

![圖示按鈕](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>自訂按鈕文字

使用`data-locale`屬性設定按鈕的語言和替代文字。 預設語言為英文。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>自訂圖示的大小

您可以使用`data-icon-px-size`屬性來設定沉浸式讀取器圖示的大小。 這會設定圖示的大小（以圖元為單位）。 預設大小為20px。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)