---
title: 粗略粗略重新置放
description: 瞭解如何使用粗略粗略重新置放來尋找您附近的錨點。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a65b2ca4ba9f1912adeaf60df123bcd3c8833bd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496897"
---
# <a name="coarse-relocalization"></a>粗略地重新當地語系化

粗略粗略重新置放是一項功能，可提供問題的初始解答： [ *我的裝置現在]/[我的裝置] 現在/應該觀察哪些內容？* 回應不是精確的，而是格式如下： *您接近這些錨點，請試著找出其中一個*。

粗略粗略重新置放的運作方式是將各種裝置感應器讀數與建立和查詢錨點產生關聯。 針對戶外案例，感應器資料通常是裝置的 GPS (全球定位系統) 位置。 當 GPS 無法使用或不可靠 (例如室內) 時，感應器資料會包含 WiFi 存取點和範圍中的藍牙指標。 所有收集的感應器資料都有助於維護空間索引，Azure 空間錨點會使用這項功能來快速判斷您裝置大約100計量內的錨點。

粗略粗略重新置放所啟用錨點的快速查閱可簡化應用程式的開發，而這些應用程式是由世界規模的 (例如，數百萬個地理位置分散) 錨點所支援的。 錨定管理的複雜性全部隱藏起來，讓您更專注于絕佳的應用程式邏輯。 Azure 空間錨點會在幕後為您完成所有錨點繁重工作。

## <a name="collected-sensor-data"></a>收集的感應器資料

您可以傳送給錨點服務的感應器資料是下列其中一項：

* GPS 位置：緯度、經度、海拔高度。
* 範圍中 WiFi 存取點的信號強度。
* 範圍中藍牙指標的信號強度。

一般而言，您的應用程式需要取得裝置特定的許可權，才能存取 GPS、WiFi 或 BLE 資料。 此外，某些平臺上的設計不提供上述的某些感應器資料。 為了考慮這些情況，感應器資料的收集是選擇性的，預設會關閉。

## <a name="set-up-the-sensor-data-collection"></a>設定感應器資料收集

讓我們從建立感應器指紋提供者開始，讓會話知道它：

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

接下來，您必須決定要用於粗略粗略重新置放的感應器。 這是您所開發的應用程式專用的決策，但下表中的建議應提供您一個絕佳的起點：

|                 | 室內 | 戶外 |
|-----------------|---------|----------|
| **GPS**         | 關閉 | 另一 |
| **無線**        | 開啟 | On (選擇性)  |
| **BLE 指標** | 在 (選擇性的警告，請參閱下面)  | 關閉 |

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

在您的應用程式中使用 GPS 時，請記住，硬體所提供的讀數通常是：

* 非同步和低頻率 (小於 1 Hz) 。
* 不可靠/雜訊 (平均) 7-m 標準差。

一般而言，裝置作業系統和 Azure 空間錨點會在未經處理的 GPS 信號上進行某些篩選和外推，以嘗試減輕這些問題。 此額外處理需要額外的時間來進行聚合，因此為了獲得最佳結果，您應該嘗試：

* 儘早在您的應用程式中建立一個感應器指紋提供者
* 讓感應器指紋提供者在多個會話之間保持運作
* 在多個會話之間共用感應器指紋提供者

如果您打算在錨點會話之外使用感應器指紋提供者，請務必在要求感應器估價之前先將它啟動。 比方說，下列程式碼將負責即時更新您裝置在地圖上的位置：

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

在您的應用程式中使用 WiFi 時，請記住，硬體所提供的讀數通常是：

* 非同步和低頻率 (小於 0.1 Hz) 。
* 可能在作業系統層級進行節流。
* 不可靠/雜訊 (平均) 3-dBm 標準差。

Azure 空間錨點會在會話期間嘗試建立已篩選的 WiFi 信號強度地圖，以嘗試減輕這些問題。 為了獲得最佳結果，您應該嘗試：

* 先建立會話，再放置第一個錨點。
* 盡可能讓會話保持運作狀態， (也就是在一個會話中建立所有錨點和查詢) 。

### <a name="enabling-bluetooth-beacons"></a>啟用藍牙指標

假設您的應用程式已經有存取裝置藍牙狀態的許可權，您可以設定 Azure 空間錨點來使用它：

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

指標通常是多功能的裝置，其中所有專案（包括 Uuid 和 MAC 位址）都可以設定。 這種彈性可能會對 Azure 空間錨點造成問題，因為它會將指標視為其 Uuid 唯一識別。 無法確保這種唯一性，很可能會造成空間 wormholes。 為了獲得最佳結果，您應該：

* 將唯一的 Uuid 指派給您的信標。
* 部署它們-通常是在一般模式下，例如格線。
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

Azure 空間錨點只會追蹤已知的「標記相近」 Uuid 清單中的藍牙信號。 程式設計成具有允許清單的 Uuid 的惡意指標，仍可能對服務的品質造成負面影響。 基於這個理由，您應該只在可控制其部署的策劃空間中使用信號。

## <a name="querying-with-sensor-data"></a>使用感應器資料進行查詢

一旦您建立了具有相關聯感應器資料的錨點，就可以開始使用裝置所報告的感應器讀數來加以取出。 您不再需要為服務提供您預期要尋找的已知錨點清單，而只是讓服務知道裝置上架的感應器所報告的裝置位置。 然後，Azure 空間錨點會找出靠近您裝置的錨點集合，並嘗試以視覺化方式進行比對。

若要讓查詢使用感應器資料，請從建立「近端裝置」準則開始：

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

此 `DistanceInMeters` 參數可控制我們要探索錨點圖形以抓取內容的距離。 假設您已填入一些空間，而錨點在每個計量的固定密度上。 此外，您裝置上的相機會觀察到單一錨點，且服務已成功找出它。 您很可能會想要抓取您最近放置的所有錨點，而不是您目前觀察到的單一錨點。 假設您所放置的錨點是在圖形中連接，則服務可以依照圖形中的邊緣，為您抓取所有鄰近錨點。 完成的圖形控制量是由控制 `DistanceInMeters` ，您可以將所有的錨點都連接到您找到的所有錨點，而這會比更接近 `DistanceInMeters` 。

請記住，的大型值 `MaxResultCount` 可能會對效能造成負面影響。 將它設定為您應用程式的合理值。

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

消費者級 GPS 裝置通常很不精確。 [Zandenbergen 和 Barbeau () 2011][6]的研究會使用輔助 gps 來報告行動電話的中位數精確度 (gps) 約為7個度量-相當大的值會被忽略！ 為了考慮這些測量錯誤，服務會將錨點視為 GPS 空間中的機率分佈。 因此，錨點現在是最可能 (的空間區域，具有超過95% 的信賴度) 包含其真正未知的 GPS 位置。

使用 GPS 進行查詢時，會套用相同的推理。 裝置會以另一個空間信賴區域表示，其為其真正未知的 GPS 位置。 探索近接錨點會轉譯成單純地找出具有信賴區域的錨點， *足以接近* 裝置的信賴區域，如下圖所示：

![選取具有 GPS 的錨點候選項目](media/coarse-reloc-gps-separation-distance.png)

GPS 信號的精確度（在錨點建立和查詢期間）對於傳回的錨點集合會有很大的影響。 相反地，以 WiFi/指標為基礎的查詢會將所有具有至少一個存取點/指標的錨點，視為與查詢共通的所有錨點。 如此一來，以 WiFi/指標為基礎的查詢結果大多是由存取點/指標的實體範圍和環境障礙物所決定。

下表估計每個感應器類型的預期搜尋空間：

| Sensor      | 搜尋空間 radius (大約 )  | 詳細資料 |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | 由其他因素之間的 GPS 不確定性所決定。 系統會為具有 GPS 的行動電話的中間 GPS 精確度預估回報的數位，也就是7個計量。 |
| WiFi        | 50 m-100 m | 取決於無線存取點的範圍。 取決於頻率、發送器強度、實體障礙物、干擾等等。 |
| BLE 指標 |  70 m | 取決於指標的範圍。 取決於頻率、傳輸強度、實體障礙物、干擾等等。 |

## <a name="per-platform-support"></a>每個平臺支援

下表摘要說明每個支援的平臺上所收集的感應器資料，以及任何平臺特定的注意事項：

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N/A | 透過 [LocationManager][3] API 支援 GPS 和網路)  ( | 透過 [CLLocationManager][4] api 支援 |
| **無線**        | 每3秒大約一次掃描的支援速率 | 支援。 從 API 層級28開始，WiFi 掃描會每2分鐘節流為4次呼叫。 從 Android 10 中，您可以從 [開發人員設定] 功能表停用節流。 如需詳細資訊，請參閱 [Android 檔][5]。 | N/A-沒有公用 API |
| **BLE 指標** | 僅限 [Eddystone][1] 和 [iBeacon][2] | 僅限 [Eddystone][1] 和 [iBeacon][2] | 僅限 [Eddystone][1] 和 [iBeacon][2] |

## <a name="next-steps"></a>後續步驟

在應用程式中使用粗略粗略重新置放。

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
