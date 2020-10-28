---
title: 自訂沉浸式閱讀程式按鈕
titleSuffix: Azure Cognitive Services
description: 本文將示範如何自訂啟動沈浸式閱讀程式的按鈕。
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 6d6757af6619edd41709a4b8f06ef615cd35bf30
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636573"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>如何自訂沈浸式閱讀程式按鈕

本文示範如何自訂啟動沈浸式閱讀程式的按鈕，以符合您應用程式的需求。

## <a name="add-the-immersive-reader-button"></a>新增沈浸式閱讀程式按鈕

沈浸式閱讀程式 SDK 提供啟動沈浸式閱讀程式之按鈕的預設樣式。 您 `immersive-reader-button` 可以使用類別屬性來啟用這個樣式。

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>自訂按鈕樣式

您 `data-button-style` 可以使用屬性來設定按鈕的樣式。 允許的值為 `icon` 、 `text` 和 `iconAndText` 。 預設值為 `icon`。

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

使用屬性設定按鈕的語言和替代文字 `data-locale` 。 預設語言為英文。

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>自訂圖示的大小

您可以使用屬性來設定沈浸式閱讀程式圖示的大小 `data-icon-px-size` 。 這會設定圖示的大小（以圖元為單位）。 預設大小為20px。

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)