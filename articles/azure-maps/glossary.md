---
title: Azure 地圖服務詞彙 | Microsoft Docs
description: 與 Azure 地圖服務、位置型服務和 GIS 相關之常用詞彙的詞彙。
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657039"
---
# <a name="glossary"></a>字彙

下列清單描述與 Azure 地圖服務服務搭配使用的常見字組。

## <a name="a"></a>A

<a name="address-validation"></a>**位址驗證**：驗證位址是否存在的程式。

<a name="advanced-routing"></a>**Advanced routing**：使用道路路線資料執行前進作業的服務集合;例如，計算可到達的範圍（等時線）、距離矩陣，以及批次路由要求。

<a name="aerial-imagery"></a>**空中影像**：請參閱[衛星影像](#satellite-imagery)。 

<a name="along-a-route-search"></a>**沿著路由搜尋**：在指定的繞道時間內尋找資料的空間查詢，或與路由路徑的距離。

<a name="altitude"></a>**高度**：指向參考介面上某個點的高度或垂直提高許可權。 高度的測量是以指定的參考數據為基礎，例如平均海平面。 請參閱＜相對高度＞。

<a name="ambiguous"></a>不**明確**：當物件可以針對指定的屬性適當地指派兩個或多個值時，資料分類中存在不確定性的狀態。 例如，當地理編碼「CA」時，會傳回兩個不明確的結果：「加拿大」和「加州」。 「CA」是國家/地區和州碼，分別代表「加拿大」和「加州」。 

<a name="annotation"></a>**批註**：顯示在地圖上以提供資訊給使用者的文字或圖形。 註釋可以識別或描述特定的地圖實體、提供地圖上某個區域的一般資訊，或是提供有關地圖本身的資訊。

<a name="antimeridian"></a>**Antimeridian**：也稱為 180<sup>th</sup>經線。 這是-180 度和180度的經度符合點。 這與地球上的質數經線相反。

<a name="application-programming-interface-api"></a>**應用程式發展介面（API）** ：可讓開發人員建立應用程式的規格。

<a name="api-key"></a>**API 金鑰**：請參閱[共用金鑰驗證](#shared-key-authentication)。

<a name="area-of-interest-aoi"></a>**感利率的區域（AOI）** ：用來定義地圖或資料庫生產環境之焦點區域的範圍。

<a name="asset-tracking"></a>**資產追蹤**：追蹤資產位置的程式，例如人員、車輛或其他物件。

<a name="asynchronous-request"></a>**非同步要求**：此 HTTP 要求會開啟連接，並對傳回非同步要求之識別碼的伺服器提出要求，然後關閉連接。 伺服器會持續處理要求，而使用者可以使用該識別碼來查詢其狀態。 當要求處理完成之後，使用者便可以下載回應。 這種類型的要求會用於長時間執行的進程。

<a name="autocomplete"></a>**自動完成**：應用程式中的一項功能，可預測使用者所輸入之單字的其餘部分。 

<a name="autosuggest"></a>**自動建議**：應用程式中的一項功能，可預測使用者所輸入內容的邏輯可能性。

<a name="azure-location-based-services-lbs"></a>**Azure 位置服務（磅）** ：在預覽期間，Azure 地圖服務的舊名稱。

<a name="azure-active-directory"></a>**Azure Active Directory （Azure AD）** ： Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 Azure 地圖服務的 Azure AD 整合目前可供所有 Azure 地圖服務 Api 預覽。 Azure AD 支援以角色為基礎的存取控制（RBAC），以允許更細緻的 Azure 地圖服務資源存取權。 若要深入瞭解 Azure 地圖服務 Azure AD 整合，請參閱[Azure 地圖服務和 Azure AD](azure-maps-authentication.md)和[管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)。

<a name="azure-maps-key"></a>**Azure 地圖服務金鑰**：請參閱[共用金鑰驗證](#shared-key-authentication)。

## <a name="b"></a>B

<a name="base-map"></a>**基本地圖**：地圖應用程式的部分，會顯示背景參考資訊，例如道路、地標和政治界限。

<a name="batch-request"></a>**批次要求**：將多個要求結合成單一要求的程式。

<a name="bearing"></a>**軸承**：點相對於另一個點的水準方向。 這會表示為相對於北部的角度，以順時針方向從0度到360度。 

<a name="boundary"></a>**界限**：分隔相鄰政治實體（例如國家/地區、區域和屬性）的線條或多邊形。 界線為可能或可能不會依循實體特徵 (例如河流、山或牆) 的線條。

<a name="bounds"></a>**界限**：請參閱周[框](#bounding-box)方塊。

<a name="bounding-box"></a>周**框**方塊：用來代表地圖上矩形區域的座標集合。 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**：已註冊之土地和屬性的記錄。 另請參閱[地塊](#parcel)。

<a name="camera"></a>**相機**：在互動式地圖控制項的內容中，相機定義 [地圖] 欄位。 相機的視口是根據數個地圖參數來決定：置中、縮放層級、音調、軸承。 

<a name="centroid"></a>**距心**：功能的幾何中心。 線條的距心將會是該線條的中間點，而多邊形的距心則會是其區域的中心點。

<a name="choropleth-map"></a>**分級著色圖地圖**：主題地圖，其中的區域會與統計變數的測量比例成正比。 此統計變數會顯示在地圖上。 例如，依據每個美國州份相對於其他州的人口，以各種色彩標示其界限。

<a name="concave-hull"></a>**凹**形輪廓：表示在指定資料集中包含所有圖形的可能凹幾何的形狀。 所產生的形狀類似於將資料包覆在塑膠膜中並對它加熱，這會導致間距較長之兩點間的線條朝內向其他資料點縮緊。

<a name="consumption-model"></a>**耗用量模型**：定義車輛耗用燃料或電力之速率的資訊。 另請參閱[耗用模型文件](consumption-model.md)。

<a name="control"></a>**控制項**：由圖形化使用者介面組成的獨立或可重複使用元件，可定義一組介面的行為。 以地圖控制項為例，其通常是使用者介面載入互動式地圖的部分。

<a name="convex-hull"></a>**凸**殼：凸殼是一種圖形，代表將所有圖形封裝在指定資料集中的最小凸幾何。 所產生的形狀類似於以橡皮筋纏住資料集。

<a name="coordinate"></a>**座標**：包含用來代表地圖上位置的經度和緯度值。

<a name="coordinate-system"></a>**座標系統**：用來定義兩個或三個維度中之點位置的參考架構。

<a name="country-code"></a>**國家（地區）代碼**：以 ISO 標準為基礎之國家/地區的唯一識別碼。 ISO2 為國家/地區的二字元代碼 (例如 US)，而 ISO3 則代表三字元代碼 (例如 USA)。

<a name="country-subdivision"></a>**國家**（地區）細分：國家/地區的第一層子分支，通常稱為州或省。

<a name="country-secondary-subdivision"></a>國家（地區）**次要細分**：國家/地區的第二層細分，通常稱為「縣市」。

<a name="country-tertiary-subdivision"></a>**國家**/地區第三細分：國家/區域的第三層細分，通常是命名區域，例如 ward。

<a name="cross-street"></a>**交叉街道**：兩個或更多街道相交的點。

<a name="cylindrical-projection"></a>**圓柱投射**：將 spheroid 或球面的點轉換成正切函數或正割圓柱的投射。 圓柱接著會由上而下切開，並壓扁為平面。

## <a name="d"></a>D

<a name="datum"></a>**Datum**：測量系統的參考規格、介面上的座標位置系統（水準基準）或表面上或下方的高度（垂直基準）。

<a name="dbf-file"></a>**DBF file**：與形狀檔（.shp）結合使用的資料庫檔案格式。

<a name="degree-minutes-seconds-dms"></a>**度數分鐘數（DMS）** ：用來描述緯度和經度的測量單位。 一度為圓形 1/360<sup></sup> 的大小。 一度會進一步分割成 60 分鐘，而一分鐘則會分割成 60 秒。

<a name="delaunay-triangulation"></a>**Delaunay 三角**化：用來從點資料集建立連續、不重迭三角形網格的技術。 每個三角形結構內都不會包含資料集中的點。

<a name="demographics"></a>**人口**統計：人類人口的統計特性（例如年齡、出生率和收入）。

<a name="destination"></a>**目的地**：使用者前往的結束點或位置。

<a name="digital-elevation-model-dem"></a>**數位高度提升模型（DEM）** ：與介面相關的提高許可權值資料集，會使用一般的基準，以固定間隔的區域來捕捉。 DEM 通常是用來代表地勢。

<a name="dijkstra's-algorithm"></a>**Dijkstra 的演算法**：一種演算法，可檢查網路的連線能力，以找出兩個點之間的最短路徑。

<a name="distance-matrix"></a>**距離矩陣**：包含一組來源與目的地之間的旅遊時間和距離資訊的矩陣。 

## <a name="e"></a>E

<a name="elevation"></a>提高**許可權**：某個點的垂直距離，或高於或低於參考介面或 datum 的物件。 一般而言，參考介面是平均海洋層級。 相對高度通常指的是土地的垂直高度。

<a name="envelope"></a>**信封**：請參閱周[框](#bounding-box)方塊。

<a name="extended-postal-code"></a>**擴充的郵遞區號**：可能包含額外資訊的郵遞區號。 例如，在美國，郵遞區號有五位數。 但擴充郵遞區號（也稱為 zip + 4）包含四個額外的數位。 這些額外的數位是用來識別五位數傳遞區域內的地理區段，例如 city 組塊、公寓群組或郵局盒。 瞭解地理區段有助於有效率地進行郵件排序和傳遞。

<a name="extent"></a>**範圍**：請參閱周[框](#bounding-box)方塊。

## <a name="f"></a>F

<a name="federated-authentication"></a>同盟**驗證**：一種驗證方法，允許跨多個 web 和行動裝置應用程式使用單一登入/驗證機制。 

<a name="feature"></a>**功能**：結合 geometry 與其他中繼資料資訊的物件。 

<a name="feature-collection"></a>**功能集合**：功能物件的集合。

<a name="find-along-route"></a>**沿著路線尋找**：一個空間查詢，它會尋找位於指定繞道時間內的資料，或與路由路徑之間的距離。

<a name="find-nearby"></a>**尋找鄰近**的：從某個點搜尋固定直線距離（如「魚尾紋飛行」）的空間查詢。

<a name="fleet-management"></a>**車隊管理**：商業車輛的管理，例如汽車、卡車、出貨和飛機。 車隊管理可以包含數種功能，例如動產融資、維護、車載資通訊 (追蹤與診斷)，以及駕駛員、速度、燃料及健康與安全管理。 車隊管理是一種由依賴業務運輸的公司所使用的流程。 公司想要將風險降至最低，並降低其整體運輸和員工成本，同時確保符合政府法規。

<a name="free-flow-speed"></a>**免費流程速度**：理想狀況下的免費流程速度。 通常為規定的速度限制。

<a name="free-form-address"></a>**自由格式位址**：以單行文字表示的完整位址。

<a name="fuzzy-search"></a>**模糊搜尋**：採用可自由格式的文字字串（可能是位址或感興趣的點）的搜尋。 

## <a name="g"></a>G

<a name="geocode"></a>**地理編碼**：已轉換成座標的位址或位置，可以用來在地圖上顯示該位置。 

<a name="geocoding"></a>**地理編碼**：也稱為正向地理編碼，這是將位置資料的位址轉換成座標的過程。 

<a name="geodesic-path"></a>**測地線路徑**：曲線表面上兩個點之間的最短路徑。 在 Azure 地圖服務上呈現時，基於麥卡托投影的原因，此路徑會以曲線的形式呈現。

<a name="geofence"></a>**地理柵欄**：定義的地理區域，可在裝置進入或存在區域時用來觸發事件。

<a name="geojson"></a>**GeoJSON**：是常用的 JSON 檔案格式，用於儲存地理向量資料，例如點、線條和多邊形。 **注意**：Azure 地圖服務使用的是 GeoJSON 的擴充版本，如[這裡所述](extend-geojson.md)。

<a name="geometry"></a>**Geometry**：代表空間物件，例如點、線條或多邊形。

<a name="geometrycollection"></a>**GeometryCollection**： geometry 物件的集合。

<a name="geopol"></a>**GeoPol**：指的是地緣政治性敏感性資料，例如有爭議的邊界和地點名稱。

<a name="georeference"></a>**Georeference**：將地理資料或影像對齊已知座標系統的程式。 此程序可能會對資料進行移位、旋轉、縮放或扭曲。

<a name="georss"></a>**GeoRSS**：用來將空間資料新增至 RSS 摘要的 XML 擴充功能。

<a name="gis"></a>**GIS**：「地理資訊系統」的縮寫。 這是經常用來描述地圖產業的字眼。

<a name="gml"></a>**GML**：也稱為「地理標記語言」。 可用來儲存空間資料的 XML 檔案擴充功能。

<a name="gps"></a>**GPS**（也稱為全球定位系統）是用來判斷地球上的裝置位置的衛星系統。 環繞地球移動的衛星會傳輸訊號，讓位於地球上任何位置的 GPS 接收器都能透過三邊測量計算出自己的位置。

<a name="gpx"></a> **.Gpx**：也稱為 gps 交換格式，是通常從 GPS 裝置建立的 XML 檔案格式。  

<a name="great-circle-distance"></a>**大圓距離**：球體表面上兩個點之間的最短距離。

<a name="greenwich-mean-time-gmt"></a>**格林威治標準時間（GMT）** ：經線的時間，這是在英國的皇家天文臺執行。

<a name="guid"></a>**GUID**：全域唯一識別碼。 用來唯一識別介面、類別、類型程式庫、元件類別或記錄的字串。

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine 公式**：一般的方程式，用來計算球面上兩個點之間的大圓距離。

<a name="hd-maps"></a>**HD 地圖**服務（也稱為「高定義圖」）包含高精確度的道路網路資訊，例如「非持續駕駛」所需的 lane 標記、告示和方向燈。

<a name="heading"></a>**標題**：某個專案指向或面向的方向。 另請參閱[方位](#heading)。

<a name="heatmap"></a>**熱度圖**：一種資料視覺效果，其中的一系列色彩代表特定區域中的點密度。 另請參閱＜主題地圖＞。

<a name="hybrid-imagery"></a>**混合式圖像**：具有道路資料和標籤的衛星或空中影像會在其上重迭。

## <a name="i"></a>I

<a name="iana"></a>**IANA**：網際網路指派的號碼授權單位的縮略字。 監督全域 IP 位址配置的非營利組織。

<a name="isochrone"></a>**Isochrone**： Isochrone 會定義可在指定的時間內，從給定位置的任何方向傳輸運輸模式的區域。 另請參閱[可抵達範圍](#reachable-range)。

<a name="isodistance"></a>**Isodistance**：假設有一個位置，isochrone 會定義可讓使用者在指定的距離內出差的區域，以取得任何方向的運輸模式。 另請參閱[可抵達範圍](#reachable-range)。

## <a name="k"></a>K

<a name="kml"></a>**KML**：也稱為鑰匙孔標記語言，是用來儲存地理向量資料的通用 XML 檔案格式，例如點、線條和多邊形。 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**： Multispectral，地球軌道由 NASA 所開發的衛星，可收集土地的影像。 這種影像用於許多產業，例如農業、林業和一套。

<a name="latitude"></a>**緯度**：以北或南方向從赤道角度測量的角度距離。

<a name="level-of-detail"></a>**詳細資料層級**：請參閱縮放層級。

<a name="lidar"></a>**Lidar**：光線偵測和範圍的縮寫。 這個遠端感應技術會使用雷射來測量反射面的距離。

<a name="linear-interpolation"></a>**線性插補**：使用已知值之間的線性距離估計未知值。

<a name="linestring"></a>**LineString**：用來代表線條的幾何。 也稱為聚合線條。 

<a name="localization"></a>**當地語系化**：支援不同的語言和文化特性。

<a name="logistics"></a>**物流**：以協調的方式移動人員、車輛、用品或資產的程式。

<a name="longitude"></a>**經度**：從東部或西部方向的質數經線中，以度為單位的角度距離。

## <a name="m"></a>M

<a name="map-tile"></a>**地圖**底圖：表示地圖畫布之分割的矩形影像。 如需詳細資訊，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件。

<a name="marker"></a>**標記**：也稱為釘選或圖釘，這是代表地圖上點位置的圖示。

<a name="mercator-projection"></a>**Mercator 投影**：一種圓柱圖投影，這是一種做為海裡用途的標準地圖投影，因為它能夠將常數的直線（稱為恒向線）當做可節省經線角度的直線線段。 與地球表面的真實佈局相比，所有平面的地圖投影都會扭曲地圖形狀或大小。 麥卡托投影會放大遠離赤道的區域，因此當您接近兩極時，地圖上較小的區域看起來將會比較大。 

<a name="multilinestring"></a>**MultiLineString**：代表 LineString 物件集合的幾何。 

<a name="multipoint"></a>**MultiPoint**：表示 Point 物件集合的幾何。

<a name="multipolygon"></a>**MultiPolygon**：代表多邊形物件集合的幾何。 例如，若要顯示夏威夷的界限，每個島都會以多邊形框住。 因此，夏威夷的界限會是 MultiPolygon。

<a name="municipality"></a>**Municipality**： city 或城鎮。 

<a name="municipality-subdivision"></a>**Municipality 細分**： Municipality 的子分區，例如鄰近地區或區功能變數名稱稱，例如 "市中心"。

## <a name="n"></a>N

<a name="navigation-bar"></a>**導覽**列：地圖上用來調整縮放層級、音調、旋轉和切換基底地圖圖層的控制項集合。

<a name="nearby-search"></a>**鄰近搜尋**：可從某個點搜尋固定直線距離（如「魚尾紋飛行」）的空間查詢。

<a name="neutral-ground-truth"></a>**中性的事實**：地圖會以它所代表之區域的官方語言，以及在本機腳本（如果有的話）來呈現標籤。

## <a name="o"></a>O

<a name="origin"></a>**來源**：使用者所在的起始點或位置。

## <a name="p"></a>P

<a name="panning"></a>**移動流覽：在**維持固定縮放層級的情況下，以任何方向移動地圖的進程。

<a name="parcel"></a>**包裹**：土地或屬性界限的繪圖。

<a name="pitch"></a>**音調**：地圖相對於垂直的傾斜量，其中0會在地圖上直接向下尋找。

<a name="point"></a>**Point**：代表地圖上單一位置的幾何。 

<a name="points-of-interest-poi"></a>**感點數（POI）** ：業務、地標或共同的常見位置。

<a name="polygon"></a>**多邊形**：代表地圖上某個區域的實心幾何。 

<a name="polyline"></a>[**折線**]：用來代表線條的幾何。 也稱為 LineString。 

<a name="position"></a>**Position**：某個點的經度、緯度及高度（x、y、z 座標）。

<a name="post-code"></a>**Post 代碼**：請參閱[郵遞區號](#postal-code)。

<a name="postal-code"></a>**郵遞區號**：一系列以特定格式的字母或數位（或兩者）。 國家/地區的郵政服務會使用郵遞區號來將地理區域分割成區域，以簡化郵件的傳遞。

<a name="primary-key"></a>**主要金鑰**：為 Azure 地圖服務共用金鑰驗證所提供的兩個訂用帳戶金鑰中的第一個。 請參閱[共用金鑰驗證](#shared-key-authentication)。

<a name="prime-meridian"></a>**質數經線**：代表0度經度的一條經度。 一般來說，在 westerly 方向上移動時，經度值會減少到180度，而在 easterly 方向到-180-度時則會增加。 

<a name="prj"></a>**PRJ**：通常伴隨形狀檔檔案的文字檔，其中包含資料集所處投射座標系統的相關資訊。

<a name="projection"></a>**投射**：以地圖投影為基礎的投影座標系統，例如橫向 Mercator、Albers 等面積和 Robinson。 它們能將地球球面的地圖投影到 2D 笛卡兒座標平面。 投影的座標系統有時會被稱為「地圖投影」。

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**： quadtree 並排顯示系統內磚的基底4位址索引。 如需詳細資訊，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件。

<a name="quadtree"></a>**Quadtree**：每個節點剛好有四個子系的資料結構。 Azure 地圖服務中使用的磚系統會使用 quadtree 結構，如此一來，當使用者放大一個層級時，每個地圖底圖就會分成四個 subtiles。  如需詳細資訊，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件。

<a name="queries-per-second-qps"></a>**每秒查詢數（QPS）** ：可在一秒內對服務或平臺發出的查詢或要求數目。 

## <a name="r"></a>R

<a name="radial-search"></a>星形**搜尋**：可從某個點搜尋固定直線距離（如「魚尾紋飛行」）的空間查詢。 

<a name="raster-data"></a>**點陣資料**：組織成資料列和資料行（或格線）的儲存格（或圖元）矩陣，其中每個資料格都包含代表資訊的值，例如溫度。 點陣包含數位空照圖相片、來自衛星的影像、數位圖片，以及掃瞄的地圖。

<a name="raster-layer"></a>**點陣圖層**：包含點陣影像的磚圖層。

<a name="reachable-range"></a>可連線的**範圍**：可連線的範圍會定義在指定的時間或距離內，可以從位置的任何方向移動的運輸模式。 另請參閱[等時線](#isochrone)和[等距線](#isodistance)。

<a name="remote-sensing"></a>**遠端檢測**：從距離收集和解讀感應器資料的程式。

<a name="rest-service"></a>**Rest 服務**：縮寫 REST 代表具像狀態傳輸。 REST 服務是一種 URL 型 Web 服務，其仰賴基本 Web 技術來進行通訊，最常見的方式為 HTTP GET 和 POST 要求。 與傳統的 SOAP 型服務相比，這些類型的服務通常較為快速，體積也較小。

<a name="reverse-geocode"></a>**反向地理編碼**：採用座標和判斷地圖上代表之位址的進程。

<a name="reproject"></a>**Reproject**：請參閱[轉換](#transformation)。

<a name="rest-service"></a>**REST 服務**：具像狀態傳輸的縮寫。 用來在非集中式分散環境中的對等之間交換資訊的架構。 REST 可讓不同電腦上的程式與作業系統或平臺分開通訊。 服務可以將超文字傳輸通訊協定（HTTP）要求傳送至統一資源定位器（URL），並取回資料。

<a name="route"></a>**Route**：兩個或多個位置之間的路徑，可能也會包含其他資訊，例如沿著路由進行之導航點的指示。

<a name="requests-per-second-rps"></a>**每秒要求數（RPS）** ：請參閱[每秒查詢數（QPS）](#queries-per-second-qps)。 

<a name="rss"></a>**RSS**：真正簡單的新聞訂閱、資源描述架構（RDF）網站摘要或豐富網站摘要的縮寫，視來源而定。 它是用來共用不同網站上內容的簡易、結構化 XML 格式。 RSS 文件會包含重要的中繼資料元素，例如作者、日期、標題、簡短描述，以及超文字連結。 此資訊能協助使用者 (或 RSS 發行者服務) 決定有哪些內容值得他們做出進一步的調查。

## <a name="s"></a>S

<a name="satellite-imagery"></a>**衛星圖像**：由平面和衛星直接向下捕捉的影像。

<a name="secondary-key"></a>**次要金鑰**：為 Azure 地圖服務共用金鑰驗證所提供的兩個訂用帳戶金鑰的第二個。 請參閱[共用金鑰驗證](#shared-key-authentication)。

<a name="shapefile-shp"></a>**形狀檔（.shp）** ：也稱為 ESRI 形狀檔，是用來儲存地理特徵之位置、形狀和屬性的向量資料儲存格式。 形狀檔會以相關檔案集合的形式儲存。

<a name="shared-key-authentication"></a>**共用金鑰驗證**：共用金鑰驗證會依賴每個要求傳遞 Azure 地圖服務帳戶所產生的金鑰，以 Azure 地圖服務。 這些金鑰通常稱為「訂用帳戶金鑰」。 建議定期重新產生金鑰以確保安全性。 系統會提供兩個金鑰，以便您在重新產生另一個金鑰時，使用一個索引鍵來維護連接。 在重新產生金鑰時，必須對所有存取此帳戶的應用程式進行更新，使其使用新的金鑰。 若要深入瞭解 Azure 地圖服務驗證，請參閱[Azure 地圖服務和 Azure AD](azure-maps-authentication.md)並[在 Azure 地圖服務中管理驗證](how-to-manage-authentication.md)。

<a name="software-development-kit-sdk"></a>**軟體發展工具組（SDK）** ：說明文件、範例程式碼和範例應用程式的集合，可協助開發人員使用 API 來建立應用程式。

<a name="spherical-mercator-projection"></a>**球面 Mercator 投影**：請參閱[Web Mercator](#web-mercator)。 

<a name="spatial-query"></a>**空間查詢**：對執行空間作業的服務所提出的要求。 例如輻射式搜尋或沿路線搜尋。

<a name="spatial-reference"></a>**空間參考**：以座標為基礎的本機、區域或全域系統，用來精確地找出地理實體。 它能定義用來將地圖座標與真實世界位置相關聯的座標系統。 空間參考可確保來自不同層或來源的空間資料可以進行整合，以進行精確的觀看或分析。 Azure 地圖服務會使用 [EPSG:3857](https://epsg.io/3857) \(英文\) 座標參考系統，並針對輸入幾何資料使用 WGS 84。

<a name="sql-spatial"></a>**Sql 空間**：指的是內建于 sql Azure 和 SQL Server 2008 和更新版本的空間功能。 此空間功能也會以 .NET 程式庫的形式提供，並可獨立於 SQL Server 使用。 如需詳細資訊，請參閱[空間資料 (SQL Server) 文件](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)。

<a name="subscription-key"></a>**訂**用帳戶金鑰：請參閱[共用金鑰驗證](#shared-key-authentication)。

<a name="synchronous-request"></a>**同步要求**： HTTP 要求會開啟連線並等候回應。 瀏覽器會限制可從某個頁面提出之並行 HTTP 要求的數目。 如果同時進行多個長時間執行的同步要求，則可以達到此限制。 要求將會延遲，直到其中一個其他要求完成為止。

## <a name="t"></a>T

<a name="telematics"></a>**車載資訊系統**：透過電信裝置傳送、接收和儲存資訊，並結合對遠端物件的控制。 

<a name="temporal-data"></a>**時態性資料**：特別參考時間或日期的資料。 時態性資料指的可能是離散事件 (例如閃電)、移動物件 (例如火車)，或是重複觀察 (例如流量感應器的計數)。

<a name="terrain"></a>**地形**：具有特定特性（例如 sandy 地形或 mountainous 地形）的土地區域。

<a name="thematic-maps"></a>**主題 maps**：主題地圖是一個簡單的地圖，用來反映地理區域的主題。 這種對應類型的常見案例是根據某些資料計量，將系統管理區域（例如國家/地區）設為色彩。

<a name="tile-layer"></a>**磚圖層**：將地圖底圖（矩形區段）組合成連續圖層來顯示的圖層。 圖格可能是點陣影像圖格或向量圖格。 通常會預先轉譯點陣磚圖層，並將它們儲存為伺服器上的影像。 點陣磚圖層可能會使用大型的儲存空間。 向量磚圖層會在用戶端應用程式中以近乎即時的方式呈現。 因此，向量磚圖層的伺服器端儲存需求較小。

<a name="time-zone"></a>**時區**：全球的一個區域，遵循統一的標準時間來進行法律、商業和社會用途。 時區通常會遵循國家/地區及其子分割的界限。

<a name="transaction"></a>**Transaction**： Azure 地圖服務使用交易式授權模型，其中：

- 每要求 15 個地圖或流量圖格，便會建立一個交易。
- Azure 地圖服務中的其中一個服務，會針對每個 API 呼叫建立一個交易。 搜尋和路由是 Azure 地圖服務服務的範例。

<a name="transformation"></a>**轉換**：在不同的地理座標系統之間轉換資料的程式。 例如，您可能會有一些在英國擷取的資料，這些資料會以 OSGB 1936 地理座標系統為基礎。 Azure 地圖服務會使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) \(英文\) 座標參考系統變體。 因此，若要正確顯示資料，Azure 地圖服務便需要將座標從其中一個系統轉換成另一個系統。

<a name="traveling-salesmen-problem-tsp"></a>**旅行 Salesmen problem 問題（TSP）** ：一項 Hamiltonian 線路問題，其中銷售人員必須尋找最有效率的方式來流覽一系列的停止，然後回到開始位置。  

<a name="trilateration"></a>**Trilateration**：測量所有三個點之間的距離，以判斷地球表面上某個點的位置（關於兩個其他點）。

<a name="turn-by-turn-navigation"></a>**輪流導覽**：此應用程式會在使用者接近下一個稍微解釋一下時，為路線中的每個步驟提供路線指示。

## <a name="v"></a>V

<a name="vector-data"></a>**向量資料**：以點、線條或多邊形表示的座標型資料。

<a name="vector-tile"></a>**向量磚**：用來儲存地理空間向量資料的開放式資料規格，其使用與地圖控制項相同的磚系統。 另請參閱[圖格圖層](#tile-layer)。

<a name="vehicle-routing-problem-vrp"></a>**車輛路由問題（VRP）** ：一類問題，其中會計算一組車輛的已排序路由，同時將其視為一組條件約束。 這些條件約束可能包括傳遞時間視窗、多個路由容量和行進持續時間限制。

<a name="voronoi-diagram"></a>**Voronoi 圖**：將空間分割成區域或資料格，以圍住一組幾何物件，通常是點特徵。 這些資料格 (或多邊形) 必須滿足德勞內三角的準則。 與集合中任何其他物件相比，區域內所有位置與其所環繞之物件的距離都較為接近。 沃羅諾伊圖表通常是用來勾勒出受地理特徵影響之周遭區域的輪廓。 

## <a name="w"></a>W

<a name="waypoint"></a>**導航點**：導航點是由經度和緯度定義的指定地理位置，用於導覽用途。 它通常用來代表某人用來於路線上行進的點。

<a name="waypoint-optimization"></a>**導航點優化**：重新排列一組之導航點的程式，以將通過所有提供之導航點所需的行進時間或距離降至最低。 視優化的複雜度而定，這項優化通常稱為「[旅遊 Salesmen problem 問題](#traveling-salesmen-problem-tsp)」或「[車輛路線](#vehicle-routing-problem-vrp)」的問題。

<a name="web-map-service-wms"></a>**Web 地圖服務（wms）** ： WMS 是開放地理聯盟（OGC）標準，可定義以影像為基礎的地圖服務。 WMS 服務能針對地圖內的特定區域以隨選方式提供地圖影像。 影像會包含預先轉譯的符號學，並可能轉譯為數種具名樣式的其中一種 (若服務已定義)。

<a name="web-mercator"></a>**Web Mercator**：也稱為球形 Mercator 投射。 這是 Mercator 投射的輕微變化，主要用於 Web 架構的對應程式。 它所使用的公式，與標準麥卡托投影用於較小規模地圖的公式相同。 不過，Web Mercator 會使用所有規模的球面公式，但大規模的 Mercator 地圖通常會使用投影的橢圓體形式。 這項差異是以全球規模 imperceptible，但它會使當地區域的對應與真正的橢圓體 Mercator 對應有些許的偏差。

<a name="wgs84"></a>**WGS84**：用來將空間座標關聯至地圖表面上位置的常數集合。 WGS84 數據是大部分線上地圖提供者和 GPS 裝置所使用的標準數據。 Azure 地圖服務會使用 WGS84 的 [EPSG:3857](https://epsg.io/3857) \(英文\) 座標參考系統變體。

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z 座標**：請參閱[高度](#altitude)。 

<a name="zip-code"></a>**郵遞區號**：請參閱[郵遞區號](#postal-code)。

<a name="Zoom level"></a>**縮放層級**：指定詳細資料的層級，以及地圖的可見程度。 將所有方法縮放至層級0時，會經常顯示完整的世界地圖。 但是，地圖會顯示有限的詳細資料，例如國家/地區名稱、框線和海洋名稱。 放大到層級 17 時，地圖將會顯示涵蓋數個城市街區的區域，並提供詳細的道路資訊。 在 azure 地圖服務中，最高的縮放層級為22。 如需詳細資訊，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件。

