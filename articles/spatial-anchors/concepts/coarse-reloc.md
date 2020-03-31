---
title: 粗重的重新當地語系化
description: 瞭解如何使用粗定位重新當地語系化來查找您附近的錨點。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844389"
---
# <a name="coarse-relocalization"></a>粗略地重新當地語系化

粗略的重新當地語系化是一項功能，它為問題提供了初步答案：*我的裝置現在在哪裡/我應該觀察哪些內容？* 回應並不精確，而是以形式出現：*你靠近這些錨點;嘗試定位其中一個*錨點。

粗糙的重新當地語系化的工作原理是，將各種設備上的感應器讀數與錨點的創建和查詢相關聯。 對於室外場景，感應器資料通常是設備的 GPS（全球定位系統）位置。 當 GPS 不可用或不可靠（如室內）時，感應器資料由 WiFi 存取點和藍牙信標組成。 所有收集的感應器資料都有助於維護 Azure 空間錨點用於快速確定設備大約 100 米範圍內的錨點的空間索引。

通過粗略的重新當地語系化啟用的錨點快速查找簡化了由世界範圍的錨點集合（例如數百萬個地理分佈）錨點支援的應用程式的開發。 錨點管理的複雜性被隱藏起來，使您能夠將更多精力放在令人敬畏的應用程式邏輯上。 所有錨點重裝都由 Azure 空間錨點在後臺完成。

## <a name="collected-sensor-data"></a>收集的感應器資料

可以發送到錨點服務的感應器資料是以下資料之一：

* GPS 位置：緯度、經度、高度。
* 範圍內 WiFi 存取點的信號強度。
* 藍牙信標的信號強度範圍。

通常，您的應用程式需要獲取特定于設備的許可權才能訪問 GPS、WiFi 或 BLE 資料。 此外，上述某些感應器資料在某些平臺上的設計不可用。 考慮到這些情況，感應器資料的收集是可選的，預設情況下處于關閉狀態。

## <a name="set-up-the-sensor-data-collection"></a>設置感應器資料收集

讓我們從創建感應器指紋提供程式開始，讓會話瞭解它：

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

# <a name="java"></a>[JAVA](#tab/java)

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

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

接下來，您需要決定要使用哪些感應器進行粗略的重新當地語系化。 此決策特定于您正在開發的應用程式，但下表中的建議應為您提供一個良好的起點：


|             | 室內 | 戶外 |
|-------------|---------|----------|
| GPS         | 關閉 | 另一 |
| WiFi        | 另一 | 打開（可選） |
| BLE 信標 | 打開（可選，有注意事項，見下文） | 關閉 |


### <a name="enabling-gps"></a>啟用 GPS

假設應用程式已具有訪問設備的 GPS 位置的許可權，則可以配置 Azure 空間錨點以使用它：

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

# <a name="java"></a>[JAVA](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

在應用程式中使用 GPS 時，請記住硬體提供的讀數通常是：

* 非同步和低頻（小於 1 Hz）。
* 不可靠/噪音（平均7米標準差）。

通常，設備作業系統和 Azure 空間錨點都會對原始 GPS 信號執行一些篩選和外推，以試圖緩解這些問題。 此額外處理需要額外的收斂時間，因此，為了獲得最佳效果，您應該嘗試：

* 儘早在應用中創建一個感應器指紋提供程式
* 在多個會話之間保持感應器指紋提供程式處於活動狀態
* 在多個會話之間共用感應器指紋提供程式

如果計畫在錨點會話之外使用感應器指紋提供程式，請確保在請求感應器估計之前啟動它。 例如，以下代碼將負責即時更新設備在地圖上的位置：

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

# <a name="java"></a>[JAVA](#tab/java)

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

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

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

假設應用程式已具有訪問設備的 WiFi 狀態的許可權，則可以配置 Azure 空間錨點以使用它：

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

# <a name="java"></a>[JAVA](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

在應用程式中使用 WiFi 時，請記住硬體提供的讀數通常是：

* 非同步和低頻（小於 0.1 Hz）。
* 在作業系統級別可能會受到限制。
* 不可靠/噪音（平均3 dBm標準差）。

Azure 空間錨點將嘗試在會話期間構建經過篩選的 WiFi 信號強度映射，以嘗試緩解這些問題。 為獲得最佳效果，您應該嘗試：

* 在放置第一個錨點之前創建會話。
* 盡可能長時間地保持會話處於活動狀態（也就是說，在一個會話中創建所有錨點和查詢）。

### <a name="enabling-bluetooth-beacons"></a>啟用藍牙信標

假設應用程式已具有訪問設備的藍牙狀態的許可權，則可以配置 Azure 空間錨點以使用它：

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

# <a name="java"></a>[JAVA](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

信標通常是多功能裝置，可以在這裡配置所有設備，包括 UUD 和 MAC 位址。 這種靈活性對於 Azure 空間錨點來說可能存在問題，因為它認為信標由其 UUD 唯一標識。 未能確保這種唯一性很可能會導致空間蟲洞。 為獲得最佳效果，您應該：

* 為信標分配唯一的 UUD。
* 部署它們 - 通常採用常規模式，如網格。
* 將唯一信標 UUUD 的清單傳遞給感應器指紋供應商：

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

# <a name="java"></a>[JAVA](#tab/java)

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

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 空間錨點將僅跟蹤已知信標接近 UUD 清單中的藍牙信標。 但是，被程式設計為允許上市的 UUID 的惡意信標仍可能對服務品質產生負面影響。 因此，您應該僅在可以控制其部署的已定空間中使用信標。

## <a name="querying-with-sensor-data"></a>使用感應器資料查詢

使用關聯的感應器資料創建錨點後，可以使用設備報告的感應器讀數開始檢索錨點。 您不再需要向服務提供您期望找到的已知錨點清單， 而是讓服務知道設備的位置，如其板載感應器所報告的那樣。 然後，Azure 空間錨點將找出靠近設備的錨點集，並嘗試直觀地匹配它們。

要讓查詢使用感應器資料，首先創建"近設備"條件：

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

# <a name="java"></a>[JAVA](#tab/java)

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

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

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

參數`DistanceInMeters`控制我們將探索錨點圖以檢索內容的遠。 例如，假設您已填充了一些空間，錨點密度為每米 2。 此外，設備上的攝像機正在觀察單個錨點，並且服務已成功定位它。 您最有可能對檢索您在附近放置的所有錨點感興趣，而不是您當前正在觀察的單個錨點。 假設您放置的錨點在圖形中連接，則服務可以通過跟蹤圖形中的邊緣來檢索您附近的所有錨點。 完成的圖形遍歷量由 控制`DistanceInMeters`。您將獲得連接到您所在的錨點的所有錨點，這些錨點比`DistanceInMeters`更近。

請記住，大`MaxResultCount`值可能會對性能產生負面影響。 將其設置為應用程式的合理值。

最後，您需要告訴會話使用基於感應器的查找：

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

# <a name="java"></a>[JAVA](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++溫RT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>預期的結果

消費級 GPS 設備通常相當不精確。 [Zandenbergen 和 Barbeau （2011）][6]的一項研究報告，使用輔助 GPS （A-GPS） 的手機的中位精度約為 7 米 - 相當大的價值不容忽視！ 為了考慮這些測量誤差，服務將錨點視為 GPS 空間中的概率分佈。 因此，錨現在最有可能（即置信度超過95%）包含其真實、未知的GPS位置的空間區域。

使用 GPS 查詢時，也應用了相同的推理。 該設備被表示為圍繞其真實、未知的 GPS 位置的另一個空間置信區域。 發現附近的錨點意味著只需找到具有*足夠接近*設備置信區域的置信區域的錨點，如下圖所示：

![使用 GPS 選擇錨點候選](media/coarse-reloc-gps-separation-distance.png)

GPS 信號的精度，無論是在錨點創建還是查詢期間，都對返回的錨點集有很大的影響。 相反，基於 WiFi/信標的查詢將考慮與查詢至少具有一個存取點/信標的所有錨點。 從這個意義上說，基於 WiFi/信標的查詢結果主要取決於存取點/信標的物理範圍和環境障礙物。

下表估計了每種感應器類型的預期搜索空間：

| 感應器      | 搜索空間半徑（約） | 詳細資料 |
|-------------|:-------:|---------|
| GPS         | 20 米 - 30 米 | 由其他因素之間的 GPS 不確定性決定。 報告的數位估計，手機與A-GPS的GPS精度中位數，即7米。 |
| WiFi        | 50 米 - 100 米 | 由無線存取點的範圍決定。 取決於頻率、發射機強度、物理障礙物、干擾等。 |
| BLE 信標 |  70 米 | 由信標的範圍決定。 取決於頻率、傳輸強度、物理障礙物、干擾等。 |

## <a name="per-platform-support"></a>每個平臺支援

下表匯總了在每個受支援平臺上收集的感應器資料，以及任何特定于平臺的警告：


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/A | 通過[位置管理器][3]API（GPS 和網路）支援 | 通過[CLLocationManager][4] API 支援 |
| WiFi        | 支援的速度約為每 3 秒掃描一次 | 支援。 從 API 級別 28 開始，WiFi 掃描每 2 分鐘限制 4 次呼叫。 從 Android 10 中，可以從"開發人員設置"功能表中禁用限制。 有關詳細資訊，請參閱 Android[文檔][5]。 | 不適用 - 無公共 API |
| BLE 信標 | 僅限於[艾迪斯通][1]和[iBeacon][2] | 僅限於[艾迪斯通][1]和[iBeacon][2] | 僅限於[艾迪斯通][1]和[iBeacon][2] |

## <a name="next-steps"></a>後續步驟

在應用中使用粗重的重新當地語系化。

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [目標C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [JAVA](../how-tos/set-up-coarse-reloc-java.md)

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
