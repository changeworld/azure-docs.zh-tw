---
title: Azure Spatial Anchors 概觀
description: 了解 Azure Spatial Anchors 如何協助您開發跨平台的混合實境體驗。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657302"
---
# <a name="azure-spatial-anchors-overview"></a>Azure Spatial Anchors 概觀

歡迎使用 Azure Spatial Anchors。 Azure Spatial Anchors 可為開發人員提供建置空間感知混合實境應用程式所需的基本功能。 這些應用程式可支援 Microsoft HoloLens、支援 ARKit 的 iOS 裝置，和支援 ARCore 的 Android 裝置。 Azure Spatial Anchors 可讓開發人員使用混合實境平台來察覺空間、指定精確的景點，以及從支援的裝置重新叫用這些景點。
這些精確的景點稱為空間錨點。

![跨平台](./media/cross-platform.png)

## <a name="examples"></a>範例

Spatial Anchors 可支援的範例使用案例包括：

- [多使用者體驗](tutorials/tutorial-share-anchors-across-devices.md)。 Spatial Anchors 可讓處於相同位置的人員輕鬆參與多使用者的混合實境應用程式。 例如，兩個人可以在桌上放置虛擬棋盤，展開混合實境的西洋棋遊戲。 然後，他們只要將裝置指向桌面，就可以一起檢視虛擬棋盤並與其互動。

- [指向](concepts/anchor-relationships-way-finding.md)。 開發人員也可以將 Spatial Anchors 連接在一起，以建立其間的關聯性。 例如，應用程式可能有某項體驗包含兩個或更多須由使用者互動來完成工作的景點。 這些景點可透過連線的方式建立。 之後，當使用者完成多步驟工作時，應用程式可要求現有錨點附近的錨點，以指示使用者前往工作中的下一個步驟。

- [真實世界中保存虛擬內容](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor)。 應用程式可讓使用者在會議室的牆上掛置虛擬日曆，讓其他人可使用手機應用程式或 HoloLens 裝置查看日曆。 在產業設定中，使用者可藉由將支援的裝置相機指向某機器，以接收其相關內容資訊。

Azure Spatial Anchors 由支援的裝置平台適用的受控服務和用戶端 SDK 所組成。 以下各節將提供開始使用 Azure Spatial Anchors 建置應用程式的相關資訊。

## <a name="next-steps"></a>後續步驟

使用 Spatial Anchors 建立您的第一個應用程式。

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
