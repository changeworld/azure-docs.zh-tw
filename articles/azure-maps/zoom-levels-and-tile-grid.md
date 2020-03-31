---
title: 縮放級別和磁貼網格 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 地圖中的縮放級別和切片網格。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 01/22/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: d58c9f6940dceefdc25211f4540b34522aec935d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530286"
---
# <a name="zoom-levels-and-tile-grid"></a>縮放層級和圖格格線

Azure 地圖服務使用麥卡托圓球投影座標系統 (EPSG：3857)。 投影是用於將球形球體轉換為平面貼圖的數學模型。 球面墨卡托投影將地圖拉伸到兩極處以創建方形地圖。 此投影顯著扭曲了地圖的比例和麵積，但有兩個重要屬性超過了此失真：

- 它是一個順從投影，這意味著它保留了相對較小的物件的形狀。 在顯示航空圖像時，保留小物體的形狀尤為重要。 例如，我們希望避免扭曲建築物的形狀。 方形建築應顯示為方形，而不是矩形。
- 這是圓柱形投影。 南北總是上下顛倒，西邊和東總是左右。 

為了優化地圖檢索和顯示的性能，地圖分為方形切片。 Azure 地圖 SDK 使用的大小為 512 x 512 圖元的切片進行道路地圖，而用於影像的圖元大小為 256 x 256 圖元。 Azure 貼圖為 23 個縮放級別（編號為 0 到 22）提供柵格和向量切片。 在縮放層級 0，整個世界剛好放進單一圖格裡：

<center>

![世界地圖磁貼](./media/zoom-levels-and-tile-grid/world0.png)</center>

縮放層級 1 使用四個圖格來呈現世界：2 x 2 個正方形

<center>

![2x2 地圖磁貼佈局](media/zoom-levels-and-tile-grid/map-2x2-tile-layout.png)</center>

每個附加縮放級別四重除前一個的切片，創建 2<sup>縮放</sup>x 2<sup>變焦</sup>的網格。 縮放層級 22 是 格線 2<sup>22</sup> x 2<sup>22</sup>，或 4,194,304 x 4,194,304 個圖格 (總計 17,592,186,044,416 個圖格)。

Azure 地圖 Web 和 Android 的互動式地圖控制項支援 25 個縮放級別，編號為 0 到 24。 儘管道路資料僅在磁貼可用時在放大級別可用。

下表提供了切片大小為 512 圖元平方的縮放級別的值的完整清單：

|縮放層級|公尺/像素|公尺/圖格邊|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

## <a name="pixel-coordinates"></a>圖元座標

在選擇投影和縮放以在每個縮放級別使用後，我們可以將地理座標轉換為圖元座標。 特定縮放級別的世界地圖圖像的完整圖元寬度和高度計算為：

```javascript
var mapWidth = tileSize * Math.pow(2, zoom);

var mapHeight = mapWidth;
```

由於地圖寬度和高度在每個縮放級別不同，因此圖元座標也不同。 地圖左上角的圖元始終具有圖元座標 （0， 0）。 地圖右下角的圖元具有圖元座標 *（寬度-1，高度-1），* 或引用上一節中的方程 *（tileSize 2 \* <sup>縮放</sup>+1，平鋪\*大小 2<sup>縮放</sup>+1）。* 例如，在級別 2 中使用 512 方形切片時，圖元座標的範圍範圍為 （0，0） 到 （2047， 2047），如下所示：

<center>

![顯示圖元尺寸的地圖](media/zoom-levels-and-tile-grid/map-width-height.png)

</center>

給定緯度和經度（以度為單位）以及詳細程度，圖元 XY 座標的計算方式如下：

```javascript
var sinLatitude = Math.sin(latitude * Math.PI/180);

var pixelX = ((longitude + 180) / 360) * tileSize * Math.pow(2, zoom);

var pixelY = (0.5 – Math.log((1 + sinLatitude) / (1 – sinLatitude)) / (4 * Math.PI)) * tileSize * Math.pow(2, zoom);
```

假定緯度和經度值位於 WGS 84 基準線上。 即使 Azure 地圖使用球形投影，但將所有地理座標轉換為公共基準線也非常重要。 WGS 84 是所選基準。 假定經度值的範圍為 -180 度到 +180 度，緯度值必須剪切到 -85.05112878 到 85.05112878。 遵循這些值可避免極點處的奇點，並確保投影的地圖是方形。

## <a name="tile-coordinates"></a>平鋪座標

為了優化地圖檢索和顯示的性能，渲染的地圖被剪切成切片。 圖元數和切片數在每個縮放級別上有所不同：

```javascript
var numberOfTilesWide = Math.pow(2, zoom);

var numberOfTilesHigh = numberOfTilesWide;
```

每個切片的 XY 座標範圍從左上角的 （0， 0） 到右下角*的 （2<sup>縮放</sup>+1， 2<sup>縮放</sup>+1）。* 例如，在縮放級別 2 時，切片座標範圍從 （0， 0） 到 （7， 7）， 如下所示：

<center>

![瓷磚座標地圖](media/zoom-levels-and-tile-grid/map-tiles-x-y-coordinates-7x7.png)</center>

給定一對圖元 XY 座標，您可以輕鬆地確定包含該圖元的切片的切片 XY 座標：

```javascript
var tileX = Math.floor(pixelX / tileSize);

var tileY = Math.floor(pixelY / tileSize);
```

切片由縮放級別調用。 x 和 y 座標組應于該縮放級別的網格上的磁貼位置。

確定要使用的縮放級別時，請記住每個位置在其磁貼上的固定位置。 因此，顯示給定區域所需的切片數取決於世界地圖上縮放網格的具體位置。 例如，如果兩個點相距 900 公尺，「可能」** 只需要在縮放層級 17 用三個圖格來顯示這兩點之間的路線。 不過，如果西邊的點是在其圖格中的右邊，而東邊的點是在其圖格中的左邊，則可能需要四個圖格：

<center>

![縮放比例示範](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png)</center>

一旦決定縮放層級，就可以計算 x 和 y 值。 每個縮放網格中的左上角磁貼為 x=0，y=0;右下角的磁貼位於 x=2<sup>縮放-1</sup>，y=2<sup>縮放-1</sup>。

以下是縮放層級 1 的縮放格線：

<center>

![縮放層級 1 的縮放格線](media/zoom-levels-and-tile-grid/api_x_y.png)</center>

## <a name="quadkey-indices"></a>四鍵索引

某些映射平臺使用`quadkey`索引命名約定，將切片 ZY 座標合併到稱為`quadtree`鍵的單維字串中，或者`quadkeys`簡稱為單維字串。 每個`quadkey`磁貼都在特定的詳細級別上標識單個磁貼，它可以用作常見資料庫 B 樹索引中的鍵。 Azure 地圖 SDK 支援疊加使用`quadkey`命名約定的切片圖層，以及[添加切片圖層](map-add-tile-layer.md)文檔中記錄的其他命名約定。

> [!NOTE]
> 命名`quadkeys`約定僅適用于一個或多個縮放級別的縮放級別。 Azure 地圖 SDK 支援縮放級別 0，它是適用于全世界的單個地圖磁貼。 

要將切片座標轉換為`quadkey`，Y 和 X 座標的位是交錯的，結果被解釋為基-4 數（維護前置字元為零）並轉換為字串。 例如，在級別 3 處給定的切片 XY 座標 （3， 5），`quadkey`確定如下：

```
tileX = 3 = 011 (base 2)

tileY = 5 = 1012 (base 2)

quadkey = 100111 (base 2) = 213 (base 4) = "213"
```

`Qquadkeys`有幾個有趣的屬性。 首先，（`quadkey`數位數） 的長度等於相應磁貼的縮放級別。 其次，`quadkey`任何磁貼的從其父磁貼`quadkey`（上一級別的包含磁貼）開始。 如下例所示，磁貼 2 是磁貼 20 到 23 的父項：

<center>

![四鍵磁貼金字塔](media/zoom-levels-and-tile-grid/quadkey-tile-pyramid.png)</center>

最後，`quadkeys`提供一維索引鍵，該鍵通常保留 XY 空間中切片的鄰近度。 換句話說，具有附近 XY 座標的兩個切片`quadkeys`通常具有相對接近的。 這對優化資料庫性能非常重要，因為連續的磁貼通常以組形式請求，因此最好將這些切片保留在同一磁片塊上，以便最大限度地減少磁片讀取的數量。

## <a name="tile-math-source-code"></a>切片數學原始程式碼

以下示例代碼說明了如何實現本文檔中描述的函數。 這些函數可以根據需要輕鬆轉換為其他程式設計語言。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Text;

namespace AzureMaps
{
    /// <summary>
    /// Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857)
    /// </summary>
    public static class TileMath
    {
        //Earth radius in meters.
        private const double EarthRadius = 6378137;

        private const double MinLatitude = -85.05112878;
        private const double MaxLatitude = 85.05112878;
        private const double MinLongitude = -180;
        private const double MaxLongitude = 180;

        /// <summary>
        /// Clips a number to the specified minimum and maximum values.
        /// </summary>
        /// <param name="n">The number to clip.</param>
        /// <param name="minValue">Minimum allowable value.</param>
        /// <param name="maxValue">Maximum allowable value.</param>
        /// <returns>The clipped value.</returns>
        private static double Clip(double n, double minValue, double maxValue)
        {
            return Math.Min(Math.Max(n, minValue), maxValue);
        }

        /// <summary>
        /// Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
        /// </summary>
        /// <param name="zoom">Zoom Level to calculate width at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Width and height of the map in pixels</returns>
        public static double MapSize(double zoom, int tileSize)
        {
            return Math.Ceiling(tileSize * Math.Pow(2, zoom));
        }

        /// <summary>
        /// Calculates the Ground resolution at a specific degree of latitude in meters per pixel.
        /// </summary>
        /// <param name="latitude">Degree of latitude to calculate resolution at</param>
        /// <param name="zoom">Zoom level to calculate resolution at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Ground resolution in meters per pixels</returns>
        public static double GroundResolution(double latitude, double zoom, int tileSize)
        {
            latitude = Clip(latitude, MinLatitude, MaxLatitude);
            return Math.Cos(latitude * Math.PI / 180) * 2 * Math.PI * EarthRadius / MapSize(zoom, tileSize);
        }

        /// <summary>
        /// Determines the map scale at a specified latitude, level of detail, and screen resolution.
        /// </summary>
        /// <param name="latitude">Latitude (in degrees) at which to measure the map scale.</param>
        /// <param name="zoom">Level of detail, from 1 (lowest detail) to 23 (highest detail).</param>
        /// <param name="screenDpi">Resolution of the screen, in dots per inch.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>The map scale, expressed as the denominator N of the ratio 1 : N.</returns>
        public static double MapScale(double latitude, double zoom, int screenDpi, int tileSize)
        {
            return GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /// <summary>
        /// Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level. 
        /// Global Pixel coordinates are relative to the top left corner of the map (90, -180)
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A position value in the format [longitude, latitude].</returns>
        public static double[] GlobalPixelToPosition(double[] pixel, double zoom, int tileSize)
        {
            var mapSize = MapSize(zoom, tileSize);

            var x = (Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return new double[] {
                360 * x,    //Longitude
                90 - 360 * Math.Atan(Math.Exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            };
        }

        /// <summary>
        /// Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param> 
        /// <returns>A global pixel coordinate.</returns>
        public static double[] PositionToGlobalPixel(double[] position, int zoom, int tileSize)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = MapSize(zoom, tileSize);

            return new double[] {
                 Clip(x * mapSize + 0.5, 0, mapSize - 1),
                 Clip(y * mapSize + 0.5, 0, mapSize - 1)
            };
        }

        /// <summary>
        /// Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        public static void GlobalPixelToTileXY(double[] pixel, int tileSize, out int tileX, out int tileY)
        {
            tileX = (int)(pixel[0] / tileSize);
            tileY = (int)(pixel[1] / tileSize);
        }

        /// <summary>
        /// Performs a scale transform on a global pixel value from one zoom level to another.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="oldZoom">The zoom level in which the input global pixel value is from.</param>  
        /// <returns>A scale pixel coordinate.</returns>
        public static double[] ScaleGlobalPixel(double[] pixel, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            return new double[] { pixel[0] * scale, pixel[1] * scale };
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="pixels">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static double[][] ScaleGlobalPixels(double[][] pixels, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            var output = new System.Collections.Generic.List<double[]>();
            foreach (var p in pixels)
            {
                output.Add(new double[] { p[0] * scale, p[1] * scale });
            }

            return output.ToArray();
        }

        /// <summary>
        /// Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="pixelX">Output parameter receiving the X coordinate of the point, in pixels.</param>  
        /// <param name="pixelY">Output parameter receiving the Y coordinate of the point, in pixels.</param>  
        public static double[] TileXYToGlobalPixel(int tileX, int tileY, int tileSize)
        {
            return new double[] { tileX * tileSize, tileY * tileSize };
        }

        /// <summary>
        /// Converts tile XY coordinates into a quadkey at a specified level of detail.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="zoom">Zoom level</param>
        /// <returns>A string containing the quadkey.</returns>
        public static string TileXYToQuadKey(int tileX, int tileY, int zoom)
        {
            var quadKey = new StringBuilder();
            for (int i = zoom; i > 0; i--)
            {
                char digit = '0';
                int mask = 1 << (i - 1);
                if ((tileX & mask) != 0)
                {
                    digit++;
                }
                if ((tileY & mask) != 0)
                {
                    digit++;
                    digit++;
                }
                quadKey.Append(digit);
            }
            return quadKey.ToString();
        }

        /// <summary>
        /// Converts a quadkey into tile XY coordinates.
        /// </summary>
        /// <param name="quadKey">Quadkey of the tile.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level.</param>
        public static void QuadKeyToTileXY(string quadKey, out int tileX, out int tileY, out int zoom)
        {
            tileX = tileY = 0;
            zoom = quadKey.Length;
            for (int i = zoom; i > 0; i--)
            {
                int mask = 1 << (i - 1);
                switch (quadKey[zoom - i])
                {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw new ArgumentException("Invalid QuadKey digit sequence.");
                }
            }
        }

        /// <summary>
        /// Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X position.</param>
        /// <param name="tileY">Output parameter receiving the tile Y position.</param>
        public static void PositionToTileXY(double[] position, int zoom, int tileSize, out int tileX, out int tileY)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = MapSize(zoom, tileSize);
            tileX = (int)Math.Floor(Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize);
            tileY = (int)Math.Floor(Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a specified viewport.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="width">The width of the map viewport in pixels.</param>
        /// <param name="height">The height of the map viewport in pixels.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings that are within the specified viewport.</returns>
        public static string[] GetQuadkeysInView(double[] position, int zoom, int width, int height, int tileSize)
        {
            var p = PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = GlobalPixelToPosition(new double[] { left, top }, zoom, tileSize);
            var br = GlobalPixelToPosition(new double[] { right, bottom }, zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = new double[] { tl[0], br[1], br[0], tl[1] };

            return GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="zoom">Zoom level to calculate tiles for.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings.</returns>
        public static string[] GetQuadkeysInBoundingBox(double[] bounds, int zoom, int tileSize)
        {
            var keys = new System.Collections.Generic.List<string>();

            if (bounds != null && bounds.Length >= 4)
            {
                PositionToTileXY(new double[] { bounds[3], bounds[0] }, zoom, tileSize, out int tlX, out int tlY);
                PositionToTileXY(new double[] { bounds[1], bounds[2] }, zoom, tileSize, out int brX, out int brY);

                for (int x = tlX; x <= brX; x++)
                {
                    for (int y = tlY; y <= brY; y++)
                    {
                        keys.Add(TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys.ToArray();
        }

        /// <summary>
        /// Calculates the bounding box of a tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate</param>
        /// <param name="tileY">Tile Y coordinate</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].</returns>
        public static double[] TileXYToBoundingBox(int tileX, int tileY, double zoom, int tileSize)
        {
            //Top left corner pixel coordinates
            var x1 = (double)(tileX * tileSize);
            var y1 = (double)(tileY * tileSize);

            //Bottom right corner pixel coordinates
            var x2 = (double)(x1 + tileSize);
            var y2 = (double)(y1 + tileSize);

            var nw = GlobalPixelToPosition(new double[] { x1, y1 }, zoom, tileSize);
            var se = GlobalPixelToPosition(new double[] { x2, y2 }, zoom, tileSize);

            return new double[] { nw[0], se[1], se[0], nw[1] };
        }

        /// <summary>
        /// Calculates the best map view (center, zoom) for a bounding box on a map.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="mapWidth">Map width in pixels.</param>
        /// <param name="mapHeight">Map height in pixels.</param>
        /// <param name="padding">Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="latitude">Output parameter receiving the center latitude coordinate.</param>
        /// <param name="longitude">Output parameter receiving the center longitude coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level</param>
        public static void BestMapView(double[] bounds, double mapWidth, double mapHeight, int padding, int tileSize, out double centerLat, out double centerLon, out double zoom)
        {
            if (bounds == null || bounds.Length < 4)
            {
                centerLat = 0;
                centerLon = 0;
                zoom = 1;
                return;
            }

            double boundsDeltaX;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0])
            {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else
            {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.Log((Math.Sin(bounds[1] * Math.PI / 180) + 1) / Math.Cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.Log((Math.Sin(bounds[3] * Math.PI / 180) + 1) / Math.Cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.Atan(Math.Sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.Log(Math.Tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.Log(Math.Tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.Max(resolutionHorizontal, resolutionVertical);

            zoom = Math.Log(360 / (resolution * tileSize), 2);
        }
    }
}
```

#### <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
module AzureMaps {

    /** Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857) */
    export class TileMath {
        //Earth radius in meters.
        private static EarthRadius = 6378137;

        private static MinLatitude = -85.05112878;
        private static MaxLatitude = 85.05112878;
        private static MinLongitude = -180;
        private static MaxLongitude = 180;

        /**
         * Clips a number to the specified minimum and maximum values.
         * @param n The number to clip.
         * @param minValue Minimum allowable value.
         * @param maxValue Maximum allowable value.
         * @returns The clipped value.
         */
        private static Clip(n: number, minValue: number, maxValue: number): number {
            return Math.min(Math.max(n, minValue), maxValue);
        }

        /**
         * Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
         * @param zoom Zoom Level to calculate width at.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Width and height of the map in pixels.
         */
        public static MapSize(zoom: number, tileSize: number): number {
            return Math.ceil(tileSize * Math.pow(2, zoom));
        }

        /**
         * Calculates the Ground resolution at a specific degree of latitude in the meters per pixel.
         * @param latitude Degree of latitude to calculate resolution at.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Ground resolution in meters per pixels.
         */
        public static GroundResolution(latitude: number, zoom: number, tileSize: number): number {
            latitude = this.Clip(latitude, this.MinLatitude, this.MaxLatitude);
            return Math.cos(latitude * Math.PI / 180) * 2 * Math.PI * this.EarthRadius / this.MapSize(zoom, tileSize);
        }

        /**
         * Determines the map scale at a specified latitude, level of detail, and screen resolution.
         * @param latitude Latitude (in degrees) at which to measure the map scale.
         * @param zoom Zoom level.
         * @param screenDpi Resolution of the screen, in dots per inch.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The map scale, expressed as the denominator N of the ratio 1 : N.
         */
        public static MapScale(latitude: number, zoom: number, screenDpi: number, tileSize: number): number {
            return this.GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /**
         * Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level.
         * Global Pixel coordinates are relative to the top left corner of the map (90, -180).
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A position value in the format [longitude, latitude].
         */
        public static GlobalPixelToPosition(pixel: number[], zoom: number, tileSize: number): number[] {
            var mapSize = this.MapSize(zoom, tileSize);

            var x = (this.Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (this.Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return [
                360 * x,    //Longitude
                90 - 360 * Math.atan(Math.exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            ];
        }

        /**
         * Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A pixel coordinate 
         */
        public static PositionToGlobalPixel(position: number[], zoom: number, tileSize: number): number[] {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = this.MapSize(zoom, tileSize);

            return [
                this.Clip(x * mapSize + 0.5, 0, mapSize - 1),
                this.Clip(y * mapSize + 0.5, 0, mapSize - 1)
            ];
        }

        /**
         * Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tile XY coordinates.
         */
        public static GlobalPixelToTileXY(pixel: number[], tileSize: number): { tileX: number, tileY: number } {
            return {
                tileX: Math.round(pixel[0] / tileSize),
                tileY: Math.round(pixel[1] / tileSize)
            };
        }

        /**
         * Performs a scale transform on a global pixel value from one zoom level to another.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param oldZoom The zoom level in which the input global pixel value is from.
         * @param newZoom The new zoom level in which the output global pixel value should be aligned with.
         */
        public static ScaleGlobalPixel(pixel: number[], oldZoom: number, newZoom: number): number[] {
            var scale = Math.pow(2, oldZoom - newZoom);

            return [pixel[0] * scale, pixel[1] * scale];
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="points">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static ScaleGlobalPixels(pixels: number[][], oldZoom: number, newZoom: number): number[][] {
            var scale = Math.pow(2, oldZoom - newZoom);

            var output: number[][] = [];
            for (var i = 0, len = pixels.length; i < len; i++) {
                output.push([pixels[i][0] * scale, pixels[i][1] * scale]);
            }

            return output;
        }

        /**
         * Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Pixel coordinates in the format of [x, y].
         */
        public static TileXYToGlobalPixel(tileX, tileY, tileSize): number[] {
            return [tileX * tileSize, tileY * tileSize];
        }

        /**
         * Converts tile XY coordinates into a quadkey at a specified level of detail.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @returns A string containing the quadkey.
         */
        public static TileXYToQuadKey(tileX: number, tileY: number, zoom: number): string {
            var quadKey: number[] = [];
            for (var i = zoom; i > 0; i--) {
                var digit = 0;
                var mask = 1 << (i - 1);

                if ((tileX & mask) != 0) {
                    digit++;
                }

                if ((tileY & mask) != 0) {
                    digit += 2
                }

                quadKey.push(digit);
            }
            return quadKey.join('');
        }

        /**
         * Converts a quadkey into tile XY coordinates.
         * @param quadKey Quadkey of the tile.
         * @returns Tile XY cocorindates and zoom level for the specified quadkey.
         */
        public static QuadKeyToTileXY(quadKey: string): { tileX: number, tileY: number, zoom: number } {
            var tileX = 0;
            var tileY = 0;
            var zoom = quadKey.length;

            for (var i = zoom; i > 0; i--) {
                var mask = 1 << (i - 1);
                switch (quadKey[zoom - i]) {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw "Invalid QuadKey digit sequence.";
                }
            }

            return {
                tileX: tileX,
                tileY: tileY,
                zoom: zoom
            };
        }

        /**
         * Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tiel XY coordinates.
         */
        public static PositionToTileXY(position: number[], zoom: number, tileSize: number): { tileX: number, tileY: number } {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = this.MapSize(zoom, tileSize);

            return {
                tileX: Math.floor(this.Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize),
                tileY: Math.floor(this.Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize)
            };
        }

        /**
         * Calculates the tile quadkey strings that are within a specified viewport.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param width The width of the map viewport in pixels.
         * @param height The height of the map viewport in pixels.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings that are within the specified viewport.
         */
        public static GetQuadkeysInView(position: number[], zoom: number, width: number, height: number, tileSize: number): string[] {
            var p = this.PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = this.GlobalPixelToPosition([left, top], zoom, tileSize);
            var br = this.GlobalPixelToPosition([right, bottom], zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = [tl[0], br[1], br[0], tl[1]];

            return this.GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /**
         * Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north].
         * @param zoom Zoom level to calculate tiles for.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings.
         */
        public static GetQuadkeysInBoundingBox(bounds: number[], zoom: number, tileSize: number): string[] {
            var keys: string[] = [];

            if (bounds != null && bounds.length >= 4) {
                var tl = this.PositionToTileXY([bounds[0], bounds[3]], zoom, tileSize);
                var br = this.PositionToTileXY([bounds[2], bounds[1]], zoom, tileSize);

                for (var x = tl[0]; x <= br[0]; x++) {
                    for (var y = tl[1]; y <= br[1]; y++) {
                        keys.push(this.TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys;
        }

        /**
         * Calculates the bounding box of a tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].
         */
        public static TileXYToBoundingBox(tileX: number, tileY: number, zoom: number, tileSize: number): number[] {
            //Top left corner pixel coordinates
            var x1 = tileX * tileSize;
            var y1 = tileY * tileSize;

            //Bottom right corner pixel coordinates
            var x2 = x1 + tileSize;
            var y2 = y1 + tileSize;

            var nw = this.GlobalPixelToPosition([x1, y1], zoom, tileSize);
            var se = this.GlobalPixelToPosition([x2, y2], zoom, tileSize);

            return [nw[0], se[1], se[0], nw[1]];
        }

        /**
         * Calculates the best map view (center, zoom) for a bounding box on a map.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north]. 
         * @param mapWidth Map width in pixels.
         * @param mapHeight Map height in pixels.
         * @param padding Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The center and zoom level to best position the map view over the provided bounding box.
         */
        public static BestMapView(bounds: number[], mapWidth: number, mapHeight: number, padding: number, tileSize: number): { center: number[], zoom: number } {
            if (bounds == null || bounds.length < 4) {
                return {
                    center: [0, 0],
                    zoom: 1
                };
            }

            var boundsDeltaX: number;
            var centerLat: number;
            var centerLon: number;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0]) {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.log((Math.sin(bounds[1] * Math.PI / 180) + 1) / Math.cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.log((Math.sin(bounds[3] * Math.PI / 180) + 1) / Math.cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.atan(Math.sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.log(Math.tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.log(Math.tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.max(resolutionHorizontal, resolutionVertical);

            var zoom = Math.log2(360 / (resolution * tileSize));

            return {
                center: [centerLon, centerLat],
                zoom: zoom
            };
        }
    }
}
```

* * *

> [!NOTE]
> Azure 地圖 SDK 中的互動式地圖控制項具有用於在地理空間位置和視口圖元之間轉換的説明器函數。 
> - [Web SDK：映射圖元和位置計算](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#pixelstopositions-pixel---)

## <a name="next-steps"></a>後續步驟

直接從 Azure 地圖 REST 服務訪問地圖切片：

> [!div class="nextstepaction"]
> [獲取地圖磁貼](https://docs.microsoft.com/rest/api/maps/render/getmaptile)

> [!div class="nextstepaction"]
> [獲取流量磁貼](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)

> [!div class="nextstepaction"]
> [獲取交通事件磁貼](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)

瞭解有關地理空間概念的更多內容：

> [!div class="nextstepaction"]
> [Azure 地圖詞彙表](glossary.md)
