---
title: 粗略 Relocalization
description: 瞭解如何使用粗略的 relocalization 來尋找您附近的錨點。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76844389"
---
# <a name="coarse-relocalization"></a>粗略地重新當地語系化

粗略的 relocalization 是一項功能，可提供問題的初始解答：*我的裝置現在在何處，以及應該觀察的內容為何？* 回應不是精確的，而是採用下列格式：*您接近這些錨點; 請嘗試找出其中一個*。

粗略 relocalization 的運作方式是將各種裝置感應器讀數與建立和查詢錨點產生關聯。 針對戶外案例，感應器資料通常是裝置的 GPS （全球定位系統）位置。 當 GPS 無法使用或不可靠（例如室內）時，感應器資料是由 WiFi 存取點和範圍內的藍牙信號所組成。 所有收集的感應器資料都有助於維護空間索引。這是 Azure 空間錨點所使用的，以快速判斷在您的裝置大約100計量內的錨點。

粗略 relocalization 所啟用的快速查閱錨點可簡化世界級集合（例如數百萬個地理位置分散）錨點所支援的應用程式開發。 錨點管理的複雜性全都隱藏起來，讓您更專注于絕佳的應用程式邏輯。 所有的錨點繁重動作都是由 Azure 空間錨點在幕後進行。

## <a name="collected-sensor-data"></a>收集的感應器資料

您可以傳送至錨點服務的感應器資料為下列其中一項：

* GPS 位置：緯度、經度、海拔高度。
* 範圍內 WiFi 存取點的信號強度。
* 範圍內的藍牙信號信號強度。

一般來說，您的應用程式必須取得裝置特定的許可權，才能存取 GPS、WiFi 或 BLE 資料。 此外，某些平臺上的某些感應器資料無法透過設計來使用。 為因應這些情況，感應器資料的集合是選擇性的，而且預設會關閉。

## <a name="set-up-the-sensor-data-collection"></a>設定感應器資料收集

讓我們先建立感應器指紋提供者，並讓會話知道它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

接下來，您必須決定您想要使用哪些感應器來進行粗略的 relocalization。 這是您正在開發的應用程式特有的決策，但下表中的建議應該提供您一個不錯的起點：


|             | 室內 | 戶外 |
|-------------|---------|----------|
| GPS         | 關閉 | 開啟 |
| WiFi        | 開啟 | On （選擇性） |
| BLE 信標 | On （選擇性加上注意事項，請參閱下文） | 關閉 |


### <a name="enabling-gps"></a>啟用 GPS

假設您的應用程式已經有存取裝置 GPS 位置的許可權，您可以設定 Azure 空間錨點來使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

在您的應用程式中使用 GPS 時，請記住，硬體提供的讀數通常是：

* 非同步和低頻率（小於 1 Hz）。
* 不可靠/有雜訊（平均 7-m 標準差）。

一般而言，裝置 OS 和 Azure 空間錨點會進行一些篩選，並在原始 GPS 信號上進行推斷，以嘗試減輕這些問題。 這種額外的處理需要額外的時間來進行聚合，因此若要獲得最佳結果，您應該嘗試：

* 儘早在您的應用程式中建立一個感應器指紋提供者
* 讓感應器指紋提供者在多個會話之間保持運作
* 在多個會話之間共用感應器指紋提供者

如果您打算在錨點會話外使用感應器指紋提供者，請務必先將它啟動，然後再要求感應器預估值。 例如，下列程式碼會負責即時更新您裝置在地圖上的位置：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>啟用 WiFi

假設您的應用程式已經有存取裝置 WiFi 狀態的許可權，您可以設定 Azure 空間錨點來使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

在您的應用程式中使用 WiFi 時，請記住，硬體提供的讀數通常是：

* 非同步和低頻率（小於 0.1 Hz）。
* 可能會在 OS 層級進行節流。
* 不可靠/雜訊（平均 3-dBm 標準差）。

Azure 空間錨點會嘗試在會話期間建立已篩選的 WiFi 信號強度對應，以嘗試減輕這些問題。 為了獲得最佳結果，您應該嘗試：

* 請先建立會話，再放置第一個錨點。
* 盡可能讓會話保持運作狀態（也就是在一個會話中建立所有錨點和查詢）。

### <a name="enabling-bluetooth-beacons"></a>啟用藍牙信號

假設您的應用程式已經有權存取裝置的藍牙狀態，您可以設定 Azure 空間錨點來使用它：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

信號通常是多用途的裝置，其中包括 Uuid 和 MAC 位址，都可以設定。 這種彈性對於 Azure 空間錨點可能會造成問題，因為它會考慮以其 Uuid 唯一識別的指標。 無法確保這種唯一性，很可能會造成空間 wormholes。 為了獲得最佳結果，您應該：

* 將唯一的 Uuid 指派給您的指標。
* 部署它們-通常是一般模式，例如方格。
* 將唯一的指標 Uuid 清單傳遞給感應器指紋提供者：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 空間錨點只會追蹤位於已知的「指標鄰近性 Uuid」清單中的藍牙信號。 已設計成具有允許清單之 Uuid 的惡意信號，仍然可能會對服務的品質造成負面影響。 基於這個理由，您應該只在可以控制其部署的策劃空間中使用信號。

## <a name="querying-with-sensor-data"></a>使用感應器資料查詢

建立具有相關聯感應器資料的錨點之後，您就可以開始使用裝置所報告的感應器讀數來抓取它們。 您不再需要為服務提供已知錨點的清單，而只是讓服務知道裝置的位置，如其上架感應器所報告。 Azure 空間錨點接著會找出靠近您裝置的錨點集合，並嘗試以視覺化方式比對。

若要讓查詢使用感應器資料，請先建立「接近裝置」準則：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters`參數可控制我們要探索錨定圖表以取得內容的距離。 假設您已在每個計量的常數密度中，以錨點填入一些空間。 此外，您裝置上的相機會觀察到單一錨點，而且服務已成功找到它。 您很可能會想要抓取您放在附近的所有錨點，而不是您目前觀察到的單一錨點。 假設您放置的錨點已連接在圖形中，則服務可以遵循圖形中的邊緣，為您抓取所有附近的錨點。 完成的圖形遍歷量是由所控制 `DistanceInMeters` ; 您會將所有錨點都連接到您所找到的，這會比更接近 `DistanceInMeters` 。

請記住，的大型值可能會對 `MaxResultCount` 效能造成負面影響。 將它設定為您應用程式的合理值。

最後，您必須告訴會話使用以感應器為基礎的查詢：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>預期的結果

取用者等級的 GPS 裝置通常不精確。 [Zandenbergen 和 Barbeau （2011）][6]的研究會報告行動電話的中間值精確度，其輔助 GPS （gps）約為7計量-相當於要忽略的大數值！ 為了考慮這些度量錯誤，服務會將錨點視為 GPS 空間中的機率分佈。 因此，錨點現在是最可能的空間區域（也就是，具有超過95% 的信賴度）包含其真實、不明的 GPS 位置。

使用 GPS 進行查詢時，會套用相同的推理。 裝置會在其真實、不明的 GPS 位置上，以另一個空間信賴區域表示。 探索附近的錨點會轉譯成隻尋找信賴區域*接近*裝置信賴區域的錨點，如下圖所示：

![使用 GPS 選擇錨點候選人](media/coarse-reloc-gps-separation-distance.png)

GPS 信號的精確度（在錨點建立和查詢期間）對一組傳回的錨點會有很大的影響。 相反地，以 WiFi/指標為基礎的查詢會將所有具有至少一個存取點/信號的錨點，視為與查詢共通的。 就這一點而言，根據 WiFi/信號的查詢結果主要取決於存取點/信號的實體範圍和環境障礙物。

下錶針對每個感應器類型估計預期的搜尋空間：

| 感應器      | 搜尋空間半徑（大約是） | 詳細資料 |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | 取決於其他因素的 GPS 不確定性。 報告的數位是以-GPS （也就是7度）為行動電話的中間 GPS 準確度估計。 |
| WiFi        | 50 m-100 m | 取決於無線存取點的範圍。 取決於頻率、發送器強度、實體障礙物、干擾等等。 |
| BLE 信標 |  70 m | 取決於指標的範圍。 取決於頻率、傳輸強度、實體障礙物、干擾等等。 |

## <a name="per-platform-support"></a>每個平臺支援

下表摘要說明每個支援的平臺上所收集的感應器資料，以及任何平臺特定的注意事項：


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/A | 透過[LocationManager][3] api 支援（GPS 和網路） | 透過[CLLocationManager][4] api 支援 |
| WiFi        | 以每3秒大約一次掃描的速率提供支援 | 支援。 從 API 層級28開始，每隔2分鐘就會將 WiFi 掃描節流至4個呼叫。 在 Android 10 中，您可以從 [開發人員設定] 功能表停用節流。 如需詳細資訊，請參閱[Android 檔][5]。 | N/A-沒有公用 API |
| BLE 信標 | 僅限於[Eddystone][1]和[iBeacon][2] | 僅限於[Eddystone][1]和[iBeacon][2] | 僅限於[Eddystone][1]和[iBeacon][2] |

## <a name="next-steps"></a>後續步驟

在應用程式中使用粗略的 relocalization。

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
