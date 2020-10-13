---
title: 相機
description: 描述攝影機設定和使用案例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: 5ad39ad9f5e585029cff5d573a026702f259607e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564811"
---
# <a name="camera"></a>相機

為了確保本機和遠端轉譯的內容可以順暢地合成，在伺服器與用戶端之間必須保持同步各種不同的相機屬性。 最值得注意的是相機的轉換和投射。 例如，在本機轉譯的內容必須使用相同的相機轉換和投影，來呈現最新的遠端框架。

![本機和遠端攝影機](./media/camera.png)

在上圖中，相較于伺服器傳送的遠端框架，本機相機已移動。 如此一來，就必須從與遠端框架相同的觀點來轉譯本機內容，最後產生的影像會 reprojected 到本機相機空間中，這在 [晚期階段 reprojection](late-stage-reprojection.md)中有詳細的說明。

遠端和本機轉譯之間的延遲，表示這些設定的任何變更都會套用延遲。 因此，任何新的值都不能立即用在相同的框架中。

您可以在相機設定上設定近和遠的平面距離。 在 HoloLens 2 上，其餘的轉換和投影資料是由硬體所定義。 使用 [桌面模擬](../../concepts/graphics-bindings.md)時，會透過其功能的輸入來設定 `Update` 。

變更的值可以在本機使用變更後的第一個遠端框架抵達時使用。 在 HoloLens 2 上，要用於轉譯的資料是由 *HolographicFrame* 所提供，就像在任何其他的全像任何應用程式一樣。 在 [桌面模擬](../../concepts/graphics-bindings.md)中，它們是透過函式的輸出來抓取 `Update` 。

## <a name="camera-settings"></a>攝影機設定

下列屬性可以在相機設定上變更：

**近和遠的平面：**

為確保無法設定不正確範圍， **NearPlane** 和 **FarPlane** 屬性是唯讀的，而且有個別的函式 **SetNearAndFarPlane** 可變更範圍。 此資料將會傳送至框架結尾的伺服器。

> [!IMPORTANT]
> 在 Unity 中，這會在變更主要相機接近或遠的平面時自動處理。

**EnableDepth**：

有時，停用遠端影像的深度緩衝區寫入以進行偵測是很有説明的。 停用深度也會阻止伺服器傳送深度資料，進而減少頻寬。

> [!TIP]
> 在 Unity 中，提供稱為 **EnableDepthComponent** 的 debug 元件，可用來在編輯器 UI 中切換這項功能。

您可以依照下列方式來變更相機設定：

```cs
void ChangeCameraSetting(AzureSession session)
{
    CameraSettings settings = session.Actions.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<CameraSettings> settings = session->Actions()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
}
```

## <a name="api-documentation"></a>API 文件

* [C # CameraSettings](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C + + CameraSettings](https://docs.microsoft.com/cpp/api/remote-rendering/camerasettings)
* [C # GraphicsBindingSimD3d11. Update 函數](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C + + GraphicsBindingSimD3d11：： Update 函數](https://docs.microsoft.com/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>後續步驟

* [圖形繫結](../../concepts/graphics-bindings.md)
* [延遲階段重新投影](late-stage-reprojection.md)
