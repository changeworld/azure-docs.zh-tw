---
title: TexConv - 紋理轉換工具
description: TexConv 指令列工具的使用者手冊
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680020"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - 紋理轉換工具

TexConv 是一種命令列工具,用於將 PNG、TGA、JPEG 和 DDS 等典型輸入格式的紋理處理為最佳化的格式,以便進行執行時消耗。
雖然最常見的方案是將單個輸入檔`A.xxx`轉換為優化的`B.yyy`格式 ,但該工具有許多其他選項可供高級使用。

## <a name="command-line-help"></a>命令列說明

使用`--help`參數運行 TexConv.exe 將列出所有可用的選項。 此外,TexConv 在執行時列印使用的選項,以幫助瞭解它在做什麼。 有關詳細資訊,請參閱此輸出。

## <a name="general-usage"></a>一般用法

TexConv 始終**只生成一個輸出**檔。 它可以使用**多個輸入**檔來組裝輸出。 對於程式集,它還需要一個**通道映射**,它告訴它從哪個通道(*紅色、綠色、藍色*或*Alpha)* 從哪個通道獲取輸入檔,並將其移動到輸出圖像的哪個通道中。

最直接的命令列是:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`指定輸出檔案與格式
- `-in0`指定第一個輸入影像
- `-rgba`告訴輸出影像應使用所有四個通道,並且它們應從輸入影像中取 1:1

## <a name="multiple-input-files"></a>多個輸入檔案

要從多個輸入檔案組載輸出,請使用有增加數量`-in`的選項指定每個輸入檔案:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

When assembling a cubemap from 2D textures, `-bottom`one `-front` `-back` can `-px` `-nx`also `-py` `-ny`use `-pz` `-right` `-nz`, `-left`, `-top`, , , or , , , , , .

要將這些輸入映射到輸出檔,需要適當的通道映射。

## <a name="channel-mappings"></a>通道對應

通道對應選擇指定從哪個輸入填充給定的輸出通道。 您可以單獨指定每個通道的輸入,如下所示:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

在這裡,輸出的RGB通道將使用第一個輸入圖像填充,但紅色和藍色將被交換。 輸出的 Alpha 通道將填充來自第二個輸入圖像的紅色通道的值。

分別指定每個通道的映射提供了最大的靈活性。 為方便起見,可以使用「旋轉」運算符編寫相同的文章:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>輸出頻道

提供以下通道對應選項:

- `-r``-g``-b`,、、、、、`-a`這些指定單色道分配
- `-rg`:指定紅色和綠色通道分配。
- `-rgb`:指定紅色、綠色和藍色通道分配。
- `-rgba`:指定所有四個通道分配。

僅提及 R、RG 或 RGB 通道,指示 TexConv 分別創建僅具有 1 個、2 個或 3 個通道的輸出檔。

### <a name="input-swizzling"></a>輸入旋轉

當說明哪個輸入紋理應填充哪個輸出通道時,可以旋轉輸入:

- `-rgba in0`等效於`-rgba in0.rgba`
- `-rgba in0.bgra`會旋轉輸入色版
- `-rgb in0.rrr`將紅色頻道複製到所有通道

也可以用黑色或白色填充通道:

- `-rgb in0 -a white`將建立 4 個通道輸出紋理,但將 alpha 設定為完全不透明
- `-rg black -b white`建立一個完整藍色的紋理

## <a name="common-options"></a>一般選項

下面列出了最有趣的選項。 更多選項由`TexConv --help`列出。

### <a name="output-type"></a>輸出類型

- `-type 2D`:輸出將是常規的 2D 圖像。
- `-type Cubemap`: 輸出將是立方體貼圖圖像。 僅適用於 DDS 輸出檔。 指定此選項後,可以從 6 個常規 2D 輸入圖像中組裝立方體貼圖。

### <a name="image-compression"></a>影像壓縮

- `-compression none`:輸出圖像將解壓縮。
- `-compression medium`:如果支援,輸出映射將使用壓縮而不犧牲太多品質。
- `-compression high`:如果支援,輸出映射將使用壓縮和犧牲質量來選擇較小的檔。

### <a name="mipmaps"></a>米普映射

預設情況下,當輸出格式支援時,TexConv 會生成 mipmap。

- `-mipmaps none`:不會生成 Mipmap。
- `-mipmaps Linear`: 如果支援,將使用框篩選器產生 mipmap。

### <a name="usage-srgb--gamma-correction"></a>用法(sRGB / 伽瑪校正)

該`-usage`選項指定輸出的用途,從而告訴 TexConv 是否對輸入和輸出檔應用伽馬校正。 使用率僅影響RGB通道。 Alpha 通道始終被視為包含"線性"值。 如果未指定用法,"自動"模式將嘗試從第一個輸入圖像的格式和檔名中檢測使用方式。 例如,單通道和雙通道輸出格式始終為線性格式。 檢查輸出以查看 TexConv 做出什麼決定。

- `-usage Linear`: 輸出影像包含不表示顏色的值。 金屬和粗糙紋理以及各種蒙版通常都是這種情況。

- `-usage Color`: 輸出圖像表示顏色,如漫反射/阿爾貝多貼圖。 sRGB 標誌將在輸出 DDS 標頭中設置。

- `-usage HDR`:輸出檔應使用超過 8 位元/ 像素進行編碼。 因此,所有值都存儲在線性空間中。 對於 HDR 紋理,數據是表示顏色還是其他數據並不重要。

- `-usage NormalMap`:輸出圖像表示切線空間法線貼圖。 值將規範化,mipmap計算將稍加優化。

- `-usage NormalMap_Inverted`:輸出是切面法線貼圖,Y 指向與輸入相反的方向。

### <a name="image-rescaling"></a>影像重新縮放

- `-minRes 64`:指定輸出的最小解析度。 如果輸入圖像較小,它將升級。
- `-maxRes 1024`:指定輸出的最大解析度。 如果輸入圖像較大,它將縮小縮放。
- `-downscale 1`:如果大於 0,則輸入圖像將在 N 倍的解析度下減半。 使用此選項可應用整體品質降低。

## <a name="examples"></a>範例

### <a name="convert-a-color-texture"></a>轉換顏色紋理

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>轉換法線地圖

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>建立 HDR 多資料集圖

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

HDR 立方體映射的一個很好的來源[是hdrihaven.com。](https://hdrihaven.com/hdris/)

### <a name="bake-multiple-images-into-one"></a>將多個影像烘焙為一個

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>擷取單一版

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
