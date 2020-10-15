---
title: 在 C++/WinRT 中建立及尋找錨點
description: 深入說明如何在 C++/WinRT 中使用 Azure Spatial Anchors 建立和尋找錨點。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 084058edca59eda776c47a3e20bb49178de78681
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74790076"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-cwinrt"></a>如何在 C++/WinRT 中使用 Azure Spatial Anchors 建立和尋找錨點

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors 可讓您在世界各地的不同裝置間共用錨點。 其支援數種不同的開發環境。 在本文中，我們將深入探討如何在 C++/WinRT 中使用 Azure Spatial Anchors SDK，以：

- 正確設定和管理 Azure Spatial Anchors 工作階段。
- 在本機錨點建立及設定屬性。
- 將屬性上傳至雲端。
- 找出並刪除雲端空間錨點。

## <a name="prerequisites"></a>Prerequisites

若要完成本指南，請確定您具有下列項目︰

- 已完整閱讀 [Azure Spatial Anchors 概觀](../overview.md)。
- 已完成其中一個 [5 分鐘快速入門](../index.yml)。
- 已基本了解 C++ 和 <a href="https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows 執行階段 API</a>。

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

深入了解 [CloudSpatialAnchorSession](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) 類別。

```cpp
    CloudSpatialAnchorSession m_cloudSession{ nullptr };

    m_cloudSession = CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

深入了解 [SessionConfiguration](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionconfiguration) 類別。

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccountKey(LR"(MyAccountKey)");
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccessToken(LR"(MyAccessToken)");
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

深入了解 [TokenRequiredDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/tokenrequireddelegate) 委派。

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AccessToken(LR"(MyAccessToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AccessToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AuthenticationToken(LR"(MyAuthenticationToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AuthenticationToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

深入了解 [Start](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#start) 方法。

```cpp
    m_cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

深入了解 [ProcessFrame](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession) 方法。

```cpp
    m_cloudSession->ProcessFrame(ar_frame_);
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

深入了解 [SessionUpdatedDelegate](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/sessionupdateddelegate) 委派。

```cpp
    m_sessionUpdatedToken = m_cloudSession.SessionUpdated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        auto status = args.Status();
        if (status.UserFeedback() == SessionUserFeedback::None) return;
        m_feedback = LR"(Feedback: )" + FeedbackToString(status.UserFeedback()) + LR"( -)" +
            LR"( Recommend Create=)" + FormatPercent(status.RecommendedForCreateProgress() * 100.f);
    });
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

深入了解 [CloudSpatialAnchor](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor) 類別。

```cpp
    // Initialization
    SpatialStationaryFrameOfReference m_stationaryReferenceFrame = nullptr;
    HolographicDisplay defaultHolographicDisplay = HolographicDisplay::GetDefault();
    SpatialLocator spatialLocator = defaultHolographicDisplay.SpatialLocator();
    m_stationaryReferenceFrame = spatialLocator.CreateStationaryFrameOfReferenceAtCurrentLocation();

    // Create a local anchor, perhaps by positioning a SpatialAnchor a few meters in front of the user
    SpatialAnchor localAnchor{ nullptr };
    PerceptionTimestamp timestamp = PerceptionTimestampHelper::FromHistoricalTargetTime(DateTime::clock::now());
    SpatialCoordinateSystem currentCoordinateSystem = m_attachedReferenceFrame.GetStationaryCoordinateSystemAtTimestamp(timestamp);
    SpatialPointerPose pose = SpatialPointerPose::TryGetAtTimestamp(currentCoordinateSystem, timestamp);

    // Get the gaze direction relative to the given coordinate system.
    const float3 headPosition = pose.Head().Position();
    const float3 headDirection = pose.Head().ForwardDirection();

    // The anchor is positioned two meter(s) along the user's gaze direction.
    constexpr float distanceFromUser = 2.0f; // meters
    const float3 gazeAtTwoMeters = headPosition + (distanceFromUser * headDirection);

    localAnchor = SpatialAnchor::TryCreateRelativeTo(currentCoordinateSystem, gazeAtTwoMeters);

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
    m_feedback = LR"(Created a cloud anchor with ID=)" + cloudAnchor.Identifier();
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

深入了解 [GetSessionStatusAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getsessionstatusasync) 方法。

```cpp
    SessionStatus status = co_await m_cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress() < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

深入了解 [AppProperties](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#appproperties) 方法。

```cpp
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    auto properties = m_cloudAnchor.AppProperties();
    properties.Insert(LR"(model-type)", LR"(frame)");
    properties.Insert(LR"(label)", LR"(my latest picture)");
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

深入了解 [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#updateanchorpropertiesasync) 方法。

```cpp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
    co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

深入了解 [GetAnchorPropertiesAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getanchorpropertiesasync) 方法。

```cpp
    CloudSpatialAnchor anchor = co_await m_cloudSession.GetAnchorPropertiesAsync(LR"(anchorId)");
    if (anchor != nullptr)
    {
        anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
        co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

深入了解 [Expiration](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchor#expiration) 方法。

```cpp
    const int64_t oneWeekFromNowInHours = 7 * 24;
    const DateTime oneWeekFromNow = DateTime::clock::now() + std::chrono::hours(oneWeekFromNowInHours);
    cloudAnchor.Expiration(oneWeekFromNow);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

深入了解 [CreateWatcher](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#createwatcher) 方法。

```cpp
    AnchorLocateCriteria criteria = AnchorLocateCriteria();
    criteria.Identifiers({ LR"(id1)", LR"(id2)", LR"(id3)" });
    auto cloudSpatialAnchorWatcher = m_cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

深入了解 [AnchorLocated](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/anchorlocateddelegate) 委派。

```cpp
    m_anchorLocatedToken = m_cloudSession.AnchorLocated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        switch (args.Status())
        {
            case LocateAnchorStatus::Located:
            {
                CloudSpatialAnchor foundAnchor = args.Anchor();
            }
                break;
            case LocateAnchorStatus::AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus::NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus::NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    });
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

深入了解 [DeleteAnchorAsync](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#deleteanchorasync) 方法。

```cpp
    co_await m_cloudSession.DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

深入了解 [Stop](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#stop) 方法。

```cpp
    m_cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

深入了解 [Reset](https://docs.microsoft.com/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#reset) 方法。

```cpp
    m_cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```cpp
    m_cloudSession = nullptr;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
