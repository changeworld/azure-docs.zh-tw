---
title: TexConv-紋理轉換工具
description: TexConv 命令列工具的使用者手冊
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680020"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-紋理轉換工具

TexConv 是一種命令列工具，用來處理從一般輸入格式（例如 PNG、TGA、JPEG 和 DDS）到優化格式的材質，以供執行時間耗用量之用。
雖然最常見的案例是將單一輸入檔轉換 `A.xxx` 成優化格式，但 `B.yyy` 此工具還有許多額外的選項可供 advanced 使用。

## <a name="command-line-help"></a>命令列說明

使用參數執行 TexConv.exe `--help` 會列出所有可用的選項。 此外，TexConv 會在執行時列印已使用的選項，以協助您瞭解它的作用。 如需詳細資訊，請參閱此輸出。

## <a name="general-usage"></a>一般使用方式

TexConv 一律會產生 **剛好一個輸出** 檔。 它可能會使用 **多個輸入** 檔來組合輸出。 針對元件，它也需要 **通道對應**，它會告訴它哪個通道 (*紅色、綠色、藍色* 或 *Alpha*) 從哪個輸入檔中取出，然後將它移到輸出影像的哪個通道中。

最直接的命令列如下：

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` 指定輸出檔和格式
- `-in0` 指定第一個輸入影像
- `-rgba` 告知輸出影像應該使用全部四個通道，而且應該從輸入映射取得1:1

## <a name="multiple-input-files"></a>多個輸入檔案

若要從多個輸入檔組合輸出，請使用具有遞增數位的選項來指定每個輸入檔 `-in` ：

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

從2d 材質組合立方體貼圖時，您也可以使用 `-right` 、、 `-left` `-top` 、 `-bottom` 、 `-front` 、或、、、 `-back` `-px` `-nx` `-py` `-ny` 、 `-pz` 、 `-nz` 。

若要將這些輸入對應至輸出檔案，則需要適當的通道對應。

## <a name="channel-mappings"></a>通道對應

通道對應選項會指定要從中填滿指定輸出通道的輸入。 您可以針對每個通道個別指定輸入，如下所示：

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

在這裡，輸出的 RGB 通道會使用第一個輸入影像填滿，但紅色和藍色將會被交換。 輸出的 Alpha 色板會填滿第二個輸入影像的紅色通道值。

分別指定每個通道的對應，可提供最大的彈性。 為了方便起見，可以使用 "swizzling" 運算子撰寫相同的：

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>輸出通道

可用的通道對應選項如下：

- `-r`、 `-g` 、 `-b` 、 `-a` ：這些會指定單一通道指派
- `-rg` ：指定紅色和綠色通道指派。
- `-rgb` ：指定紅色、綠色和藍色通道指派。
- `-rgba` ：指定全部四個通道指派。

只提及 R、RG 或 RGB 通道，會指示 TexConv 建立僅包含1、2或3個通道的輸出檔案。

### <a name="input-swizzling"></a>輸入 swizzling

當您指出哪一個輸入材質應填滿哪個輸出通道時，可以 swizzle 輸入：

- `-rgba in0` 相當於 `-rgba in0.rgba`
- `-rgba in0.bgra` 將 swizzle 輸入通道
- `-rgb in0.rrr` 會將紅色通道複製到所有通道

其中一個可能也會以黑色或白色填滿頻道：

- `-rgb in0 -a white` 將建立4個通道輸出紋理，但將 Alpha 設定為完全不透明
- `-rg black -b white` 會建立一個完全藍色的材質

## <a name="common-options"></a>一般選項

最有趣的選項如下所示。 列出更多選項 `TexConv --help` 。

### <a name="output-type"></a>輸出類型

- `-type 2D` ：輸出將會是一般的2D 影像。
- `-type Cubemap` ：輸出將會是立方體貼圖影像。 僅支援 DDS 輸出檔。 指定此項時，您可以將立方體貼圖從6個標準2D 輸入映射組合。

### <a name="image-compression"></a>影像壓縮

- `-compression none` ：輸出影像將會解壓縮。
- `-compression medium` ：如果支援，輸出影像將會使用壓縮，而不會犧牲太多品質。
- `-compression high` ：如果支援，輸出影像將會使用壓縮並犧牲品質，以促進較小的檔案。

### <a name="mipmaps"></a>Mipmap

根據預設，TexConv 會在輸出格式支援時產生 mipmap。

- `-mipmaps none` ：將不會產生 mipmap。
- `-mipmaps Linear` ：如果支援，將會使用 box 篩選來產生 mipmap。

### <a name="usage-srgb--gamma-correction"></a>使用方式 (sRGB/gamma 更正) 

`-usage`選項會指定輸出的目的，並藉此告知 TexConv 是否要將 gamma 修正套用至輸入檔和輸出檔。 使用方式只會影響 RGB 通道。 Alpha 色板一律會被視為包含「線性」值。 如果未指定 usage，' auto ' 模式會嘗試從第一個輸入映射的格式和檔案名偵測使用方式。 比方說，單一和雙重通道輸出格式一律是線性的。 檢查輸出以查看 TexConv 進行的決策。

- `-usage Linear` ：輸出影像包含不代表色彩的值。 這通常是材質和粗糙度材質的情況，以及所有種類的遮罩。

- `-usage Color` ：輸出影像代表色彩，例如漫射/>albedo 地圖。 在輸出 DDS 標頭中會設定 sRGB 旗標。

- `-usage HDR` ：輸出檔案應針對編碼使用超過8個位的每個圖元。 因此，所有值都會儲存線上性空間中。 對於 HDR 紋理而言，資料是否代表色彩或其他資料並不重要。

- `-usage NormalMap` ：輸出影像代表正切空間的一般地圖。 值將會正規化，而且 mipmap 計算會稍微優化。

- `-usage NormalMap_Inverted` ：輸出是正切空間的一般地圖，Y 指向相對於輸入的方向。

### <a name="image-rescaling"></a>影像縮放

- `-minRes 64` ：指定輸出的最小解析度。 如果輸入影像較小，則會取得放大。
- `-maxRes 1024` ：指定輸出的最大解析度。 如果輸入影像較大，則會取得 downscaled。
- `-downscale 1` ：如果大於0，則輸入影像將會在解決 N 次中減半。 您可以使用此應用程式來降低整體品質。

## <a name="examples"></a>範例

### <a name="convert-a-color-texture"></a>轉換彩色材質

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>轉換一般地圖

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>建立 HDR 立方體貼圖

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

[Hdrihaven.com](https://hdrihaven.com/hdris/)是 HDR cubemaps 的絕佳來源。

### <a name="bake-multiple-images-into-one"></a>將多個影像製作成一個

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>將單一通道解壓縮

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
