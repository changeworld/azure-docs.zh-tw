---
title: 在 Unity 中建立及尋找錨點
description: 深入說明如何在 Unity 中使用 Azure Spatial Anchors 建立和尋找錨點。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 08/10/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: caa343b0715e3e4b33c45fcc9ba6441533c3d056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858501"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-unity"></a>如何在 Unity 中使用 Azure Spatial Anchors 建立和尋找錨點

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors 可讓您在世界各地的不同裝置間共用錨點。 其支援數種不同的開發環境。 在本文中，我們將深入探討如何在 Unity 中使用 Azure Spatial Anchors SDK，以：

- 正確設定和管理 Azure Spatial Anchors 工作階段。
- 在本機錨點建立及設定屬性。
- 將屬性上傳至雲端。
- 找出並刪除雲端空間錨點。

## <a name="prerequisites"></a>Prerequisites

若要完成本指南，請確定您具有下列項目︰

- 已完整閱讀 [Azure Spatial Anchors 概觀](../overview.md)。
- 已完成其中一個 [5 分鐘快速入門](../index.yml)。
- 已基本了解 C# 和 Unity。
- 已基本了解 <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a> (如果您想要使用 Android) 或 <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a> (如果您想要使用 iOS)。

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

深入了解 [CloudSpatialAnchorSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession) 類別。

```csharp
    CloudSpatialAnchorSession cloudSession;
    // In your view handler
    this.cloudSession = new CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

深入了解 [SessionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionconfiguration) 類別。

```csharp
    this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```csharp
    this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

深入了解 [TokenRequiredDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.tokenrequireddelegate) 委派。

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AccessToken = @"MyAccessToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AccessToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```csharp
    this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```csharp
    this.cloudSession.TokenRequired += (object sender, TokenRequiredEventArgs args) =>
    {
        args.AuthenticationToken = @"MyAuthenticationToken";
    };
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```csharp
    this.cloudSession.TokenRequired += async (object sender, TokenRequiredEventArgs args) =>
    {
        var deferral = args.GetDeferral();
        string myToken = await MyGetTokenAsync();
        if (myToken != null) args.AuthenticationToken = myToken;
        deferral.Complete();
    };
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

深入了解 [Start](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.start) 方法。

```csharp
#if UNITY_ANDROID || UNITY_IOS
    this.cloudSession.Session = aRSession.subsystem.nativePtr.GetPlatformPointer();
#elif UNITY_WSA || WINDOWS_UWP
    // No need to set a native session pointer for HoloLens.
#else
    throw new NotSupportedException("The platform is not supported.");
#endif

    this.cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

深入了解 [ProcessFrame](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.processframe) 方法。

```csharp
#if UNITY_ANDROID || UNITY_IOS
    XRCameraFrame xRCameraFrame;
    if (aRCameraManager.subsystem.TryGetLatestFrame(cameraParams, out xRCameraFrame))
    {
        long latestFrameTimeStamp = xRCameraFrame.timestampNs;

        bool newFrameToProcess = latestFrameTimeStamp > lastFrameProcessedTimeStamp;

        if (newFrameToProcess)
        {
            session.ProcessFrame(xRCameraFrame.nativePtr.GetPlatformPointer());
            lastFrameProcessedTimeStamp = latestFrameTimeStamp;
        }
    }
#endif
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

深入了解 [SessionUpdatedDelegate](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.sessionupdateddelegate) 委派。

```csharp
    this.cloudSession.SessionUpdated += (object sender, SessionUpdatedEventArgs args) =>
    {
        var status = args.Status;
        if (status.UserFeedback == SessionUserFeedback.None) return;
        this.feedback = $"Feedback: {Enum.GetName(typeof(SessionUserFeedback), status.UserFeedback)} -" +
            $" Recommend Create={status.RecommendedForCreateProgress: 0.#%}";
    };
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

深入了解 [CloudSpatialAnchor](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor) 類別。

```csharp
    // Create a local anchor, perhaps by hit-testing and spawning an object within the scene
    Vector3 hitPosition = new Vector3();
#if UNITY_ANDROID || UNITY_IOS
    Vector2 screenCenter = new Vector2(0.5f, 0.5f);
    List<ARRaycastHit> aRRaycastHits = new List<ARRaycastHit>();
    if(arRaycastManager.Raycast(screenCenter, aRRaycastHits) && aRRaycastHits.Count > 0)
    {
        ARRaycastHit hit = aRRaycastHits[0];
        hitPosition = hit.pose.position;
    }
#elif WINDOWS_UWP || UNITY_WSA
    RaycastHit hit;
    if (this.TryGazeHitTest(out hit))
    {
        hitPosition = hit.point;
    }
#endif

    Quaternion rotation = Quaternion.AngleAxis(0, Vector3.up);
    this.localAnchor = GameObject.Instantiate(/* some prefab */, hitPosition, rotation);
    this.localAnchor.CreateNativeAnchor();

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
    cloudAnchor.LocalAnchor = this.localAnchor.GetNativeSpatialAnchorPtr();
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
    this.feedback = $"Created a cloud anchor with ID={cloudAnchor.Identifier}");
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

深入了解 [GetSessionStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getsessionstatusasync) 方法。

```csharp
    SessionStatus value = await this.cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

深入了解 [AppProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.appproperties) 屬性。

```csharp
    CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor() { LocalAnchor = localAnchor };
    cloudAnchor.AppProperties[@"model-type"] = @"frame";
    cloudAnchor.AppProperties[@"label"] = @"my latest picture";
    await this.cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

深入了解 [UpdateAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.updateanchorpropertiesasync) 方法。

```csharp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties[@"last-user-access"] = @"just now";
    await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

深入了解 [GetAnchorPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.getanchorpropertiesasync) 方法。

```csharp
    var anchor = await cloudSession.GetAnchorPropertiesAsync(@"anchorId");
    if (anchor != null)
    {
        anchor.AppProperties[@"last-user-access"] = @"just now";
        await this.cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

深入了解 [Expiration](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchor.expiration) 屬性。

```csharp
    cloudAnchor.Expiration = DateTimeOffset.Now.AddDays(7);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

深入了解 [CreateWatcher](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.createwatcher) 方法。

```csharp
    AnchorLocateCriteria criteria = new AnchorLocateCriteria();
    criteria.Identifiers = new string[] { @"id1", @"id2", @"id3" };
    this.cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

深入了解 [AnchorLocated](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.anchorlocateddelegate) 委派。

```csharp
    this.cloudSession.AnchorLocated += (object sender, AnchorLocatedEventArgs args) =>
    {
        switch (args.Status)
        {
            case LocateAnchorStatus.Located:
                CloudSpatialAnchor foundAnchor = args.Anchor;
                // Go add your anchor to the scene...
                break;
            case LocateAnchorStatus.AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus.NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus.NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

深入了解 [DeleteAnchorAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.deleteanchorasync) 方法。

```csharp
    await this.cloudSession.DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

深入了解 [Stop](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.stop) 方法。

```csharp
    this.cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

深入了解 [Reset](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.reset) 方法。

```csharp
    this.cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-unity.md)]

深入了解 [Dispose](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.dispose) 方法。

```csharp
    this.cloudSession.Dispose();
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
