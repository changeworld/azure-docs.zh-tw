---
title: C++/WinRT 中的粗略重新定位
description: 深入說明如何在 C++/WinRT 中使用粗略重新定位建立和尋找錨點。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6d2bb4c563bd7bae186ce9832ff0472f17a8afd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548283"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-cwinrt"></a>如何在 C++/WinRT 中使用粗略重新定位建立和尋找錨點

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure 空間錨點可以將裝置上、定位感應器資料與您建立的錨點建立關聯。 此資料也可以用來快速判斷您的裝置附近是否有任何錨點。 如需詳細資訊，請參閱[粗略重新定位](../concepts/coarse-reloc.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本指南，請確定您具有下列項目︰

- 已基本了解 C++ 和 <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows 執行階段 API</a>。
- 已完整閱讀 [Azure Spatial Anchors 概觀](../overview.md)。
- 已完成其中一個 [5 分鐘快速入門](../index.yml)。
- 請參閱[建立並尋找錨點操作說明](../create-locate-anchors-overview.md)。

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);

// Allow WiFi scanning
sensors.WifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

// Allow the set of known BLE beacons
sensors.BluetoothEnabled(true);
sensors.KnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```cpp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters(5.0f);
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]