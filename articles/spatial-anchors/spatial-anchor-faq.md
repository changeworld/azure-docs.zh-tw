---
title: 常見問題集
description: Azure Spatial Anchors 服務的相關常見問題。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 05/18/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: eb02de25cfcb0dde01699c1169d03f0d822c4ae2
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097400"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Azure Spatial Anchors 的相關常見問題集

Azure Spatial Anchors 是一個受控雲端服務和開發人員平台，可在 HoloLens、iOS 和 Android 裝置之間支援多使用者、空間感知的混合實境體驗。

如需詳細資訊，請參閱 [Azure Spatial Anchors 概觀](overview.md)。

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors 產品的常見問題集

**問：Azure Spatial Anchors 支援哪些裝置？**

**答：** Azure Spatial Anchors 可讓開發人員在 HoloLens 上、在支援 ARKit 的 iOS 裝置上，以及在支援 ARCore 的 Android 裝置上建置應用程式；就 iOS 和 Android 而言，這包括手機和平板電腦。

**問：必須連線至雲端才能使用 Azure Spatial Anchors 嗎？**

**答：** Azure Spatial Anchors 目前需要網際網路的網路連線。 歡迎您到我們的[意見反應網站](https://feedback.azure.com/forums/919252-azure-spatial-anchors)提供意見。

**問：Azure Spatial Anchors 有哪些連線需求？**

**答：** Azure Spatial Anchors 適用 Wi-Fi 與行動寬頻連線。

**問：Azure Spatial Anchors 定位錨點的精準度為何？**

**答：** 許多因素都可能影響定位錨點的精確度，包括光照條件、環境中的物體，甚至錨點放置所在的介面。 若要判斷精確度是否符合您的需求，請在您預計要使用這些錨點的代表性環境中嘗試使用錨點。 如果您發現某些環境中的精確度不符合您的需求，請參閱 [Azure Spatial Anchors 中的記錄和診斷](./concepts/logging-diagnostics.md)。

**問：建立和尋找錨點需要多久的時間？**

**答：** 建立和尋找錨點的所需時間取決於許多因素，包括網路連線、裝置的處理和負載情形，以及特定環境。 我們建置應用程式的客戶來自於許多產業，包括製造、零售和遊戲等產業，這表示此服務在其案例中可帶來絕佳的使用者體驗。

## <a name="privacy-faq"></a>隱私權常見問題集

**問：我的應用程式在某處放置了空間錨點後，是否所有應用程式都可加以存取？**

**答：** 錨點會依 Azure 帳戶隔離。 應用程式必須已獲得帳戶的存取權，才能存取該帳戶中的錨點。

**問：Azure Spatial Anchors 如何儲存資料？**

**答：** 所有資料都會以 Microsoft 受控資料加密金鑰進行加密，並針對每個資源儲存區域內的所有資料。

**問：使用 Azure Spatial Anchors 時，會在服務上傳送和儲存哪些環境相關資訊？是否會傳送及儲存環境的圖片？**

**答**：在建立或尋找錨點時，環境的圖片會在裝置上處理成衍生的格式。 這個衍生的格式傳送至服務並儲存於其中。

下圖提供環境和衍生的疏鬆點雲供您了解。 此點雲顯示在裝置上傳送和儲存的環境所呈現的幾何分布。 對於疏鬆點雲中的每個點，我們都會傳送並儲存該點視覺特性的雜湊。 雜湊衍生自 (但不包含) 任何像素資料。

Azure Spatial Anchors 會遵循 [Azure 服務合約條款](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)和 [Microsoft 隱私權聲明](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)。

![環境及其衍生的疏鬆點雲](./media/sparse-point-cloud.png)
*圖 1：環境及其衍生的疏鬆點雲*

**問：是否有方法可將診斷資訊傳送給 Microsoft？**

**答**：是。 Azure Spatial Anchors 有診斷模式，可供開發人員透過 Azure Spatial Anchors API 選擇加入。 舉例來說，如果您遇到無法以可預測的方式建立和尋找錨點的環境，此模式就可發揮效用。 我們可能會要求您提交診斷報告，並附上可協助我們進行偵錯的資訊。 如需詳細資訊，請參閱 [Azure Spatial Anchors 中的記錄和診斷](./concepts/logging-diagnostics.md)。

## <a name="availability-and-pricing-faqs"></a>可用性和定價的常見問題集

**問：您是否提供 SLA？**

**答：** 比照 Azure 服務的標準，我們預計提供超過 99.9% 的可用性。 

**問：我是否可將使用 Azure Spatial Anchors 的應用程式發佈至應用程式市集？是否可將 Azure Spatial Anchors 用於任務關鍵性生產案例？**

**答：** 是，Azure Spatial Anchors 已正式推出，並具有標準的 Azure 服務 SLA。 邀請您開發適用您生產環境部署的應用程式，並與我們[分享您對產品的相關意見反應](https://feedback.azure.com/forums/919252-azure-spatial-anchors)。

**問：您是否已設置任何節流限制？**

**答**：是，我們設有節流限制。  您的一般應用程式開發和測試應該不會達到這些限制。 針對生產環境部署，我們已有能力支援客戶的高度延展需求。 請[與我們連絡](mailto:azuremrs@microsoft.com)以了解詳情。 

**問：Azure Spatial Anchors 的適用區域為何？**

**答：** Azure Spatial Anchors 目前適用於美國西部 2、美國東部、美國東部 2、美國中南部、西歐、北歐、英國南部和澳大利亞東部。 未來將在其他區域提供。

這表示支援這項服務所需的計算和儲存能力位於這些區域。 不過，用戶端的所在位置並沒有限制。 

**問：使用 Azure Spatial Anchors 需要付費嗎？**

**答：** 您可以在我們的[定價頁面](https://azure.microsoft.com/pricing/details/spatial-anchors/)找到關於定價的詳細資訊。

## <a name="technical-faqs"></a>技術的常見問題集

**問：Azure Spatial Anchors 如何運作？**

**答：** Azure Spatial Anchors 須依賴混合實境/擴增實境追蹤器。 這些追蹤器可透過相機察覺環境，並以 6 個自由度 (6DoF) 追蹤在空間中移動的裝置。

藉由以 6DoF 追蹤器作為建置組塊，Azure Spatial Anchors 讓您能夠將真實環境中的特定景點指定為「錨點」。 舉例來說，您可以使用錨點呈現現實世界中特定位置的內容。

當您建立錨點時，用戶端 SDK 會擷取該點周圍的環境資訊，並將其傳送至服務。 如果有另一個裝置尋找該空間中的錨點，則類似的資料將會傳送至服務。 該資料會與先前儲存的環境資料進行比對。 接著會傳回錨點與裝置的相對位置，供應用程式使用。

**問：如何在 iOS 和 Android 上整合 Azure Spatial Anchors 與 ARKit 和 ARCore？**

**答：** Azure Spatial Anchors 會使用 ARKit 和 ARCore 的原生追蹤功能。 此外，我們適用於 iOS 和 Android 的 SDK 可提供相關功能，例如，允許應用程式開發人員在受控雲端服務中保存錨點，以及讓您的應用程式只要連線至服務即可再次尋找這些錨點。

**問：Azure Spatial Anchors 如何與 HoloLens 整合？**

**答：** Azure Spatial Anchors 會使用 HoloLens 的原生追蹤功能。 我們提供可在 HoloLens 上建置應用程式的 Azure Spatial Anchors SDK。 此 SDK 可與原生 HoloLens 功能整合，並提供其他功能。 這些功能包括允許應用程式開發人員在受控雲端服務中保存錨點，以及讓您的應用程式藉由連線至服務再次尋找這些錨點。

**問：Azure Spatial Anchors 支援哪些平台和語言？**

**答：** 開發人員可以使用他們熟悉的工具和裝置架構，透過 Azure Spatial Anchors 建置應用程式：

- 適用於 HoloLens、iOS 和 Android 的 Unity
- iOS 和 Android 上的 Xamarin
- iOS 上的 Swift 或 Objective-C
- Android 上的 Java 或 Android NDK
- HoloLens 上的 C++/WinRT

[在這裡開始進行開發](index.yml)。

**問：它是否適用於 Unreal？**

**答：** 未來會考慮加入 Unreal 的支援。

**問：Azure Spatial Anchors 使用哪些連接埠和通訊協定？**

**答：** Azure Spatial Anchors 使用加密通訊協定，透過 TCP 連接埠 443 進行通訊。 針對驗證，它使用 [Azure Active Directory](../active-directory/index.yml)，使用 HTTPS 透過連接埠 443 進行通訊。