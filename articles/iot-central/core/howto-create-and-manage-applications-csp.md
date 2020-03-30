---
title: 從 CSP 門戶創建和管理 Azure IoT 中心應用程式 |微軟文檔
description: 身為 CSP，如何代表您的客戶建立 Azure IoT Central 應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 5d876d7db13ce73603f212ec11544cb2f680bf46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158175"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>從 CSP 門戶創建和管理 Azure IoT 中央應用程式

雲端解決方案提供者 (CSP) 方案是 Microsoft 轉銷商方案。 其用意是要為我們的通路合作夥伴提供轉銷所有 Microsoft 商務線上服務的單站式方案。 深入了解[雲端解決方案提供者方案](https://partner.microsoft.com/cloud-solution-provider)。

身為 CSP，您可以透過 [Microsoft 合作夥伴中心](https://partnercenter.microsoft.com/partner/home)代表您的客戶建立和管理 Microsoft Azure IoT Central 應用程式。 當 CSP 代表客戶建立 Azure IoT Central 應用程式時，就像其他 CSP 管理的 Azure 服務一樣，CSP 會管理客戶的帳單。 Azure IoT Central 的費用將會出現在 Microsoft 合作夥伴中心的總計費中。

若要開始使用，請在 Microsoft 合作夥伴入口網站上登入您的帳戶，並選取您要為其建立 Azure IoT Central 應用程式的客戶。 從左側導航到客戶的服務管理。

![Microsoft 夥伴中心客戶、客戶檢視](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central 會列為可供系統管理員使用的服務。 選擇頁面上的 Azure IoT 中心連結以為此客戶創建新應用程式或管理現有應用程式。

![可用來管理的 Azure IoT Central](media/howto-create-and-manage-applications-csp/image2.png)

您會進入 Azure IoT Central 的 [應用程式管理員] 頁面。 Azure IoT Central 會保留您來自 Microsoft 合作夥伴中心和您前往管理該客戶的內容。 您會在 [應用程式管理員] 頁面的標頭中看到對此內容的確認。 從這裡，您可以瀏覽至您先前為此客戶建立的現有應用程式以便為客戶管理或建立新的應用程式。

![建立 CSP 的管理員](media/howto-create-and-manage-applications-csp/image3.png)

要創建 Azure IoT 中心應用程式，請在左側功能表中選擇 **"生成**"。 選擇其中一個行業範本，或選擇 **"繼承應用程式**"從頭開始創建應用程式。 這會載入 [應用程式建立] 頁面。 您必須完成此頁面上的所有欄位，然後選擇 [建立]****。 您可以在下方找到更多關於每個欄位的詳細資訊。

![建立 CSP 的應用程式頁面](media/howto-create-and-manage-applications-csp/image4.png)

![建立 CSP 的應用程式頁面](media/howto-create-and-manage-applications-csp/image4-1.png)

![為 CSP 計費資訊創建應用程式頁面](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>定價方案

您只能創建使用標準定價計畫作為 CSP 的應用程式。 要向客戶展示 Azure IoT 中心，可以創建單獨使用免費定價計畫的應用程式。 若要深入了解免費和標準定價方案，請參閱 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)。

您只能創建使用標準定價計畫作為 CSP 的應用程式。 要向客戶展示 Azure IoT 中心，可以創建單獨使用免費定價計畫的應用程式。 若要深入了解免費和標準定價方案，請參閱 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="application-name"></a>應用程式名稱

您的應用程式名稱會顯示在 [應用程式管理員]**** 頁面上以及每個 Azure IoT 中心應用程式內。 您可以為 Azure IoT 中心應用程式選擇任何名稱。 選擇對您和組織中其他人有意義的名稱。

## <a name="application-url"></a>應用程式 URL

應用程式的 URL 是您應用程式的 URL。 您可以將其書籤儲存在您的瀏覽器中，或與他人共用 URL。

當您輸入您的應用程式名稱時，就會自動產生您的應用程式 URL。 如果您想要，可以為您的應用程式選擇不同的 URL。 在 Azure IoT Central 中，每個 Azure IoT Central URL 都必須是唯一的。 如果您選擇的 URL 已被採用，則會收到錯誤訊息。

## <a name="directory"></a>目錄

由於 Azure IoT Central 保有您前往管理客戶 (您在 Microsoft 合作夥伴入口網站中所選取) 的內容，因此您在 [目錄] 欄位中只會看到該客戶的 Azure Active Directory 租用戶。 

Azure Active Directory 租用戶包含使用者身分識別、認證和其他組織資訊。 多個 Azure 訂用帳戶可與單一 Azure Active Directory 租用戶產生關聯。

若要深入了解，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)。

## <a name="azure-subscription"></a>Azure 訂用帳戶

Azure 訂用帳戶可讓您建立 Azure 服務執行個體。 Azure IoT Central 會自動尋找您可以存取的客戶所擁有的所有 Azure 訂用帳戶，並顯示於 [建立應用程式]**** 頁面的下拉式清單中。 選擇 Azure 訂用帳戶，以建立新的 Azure IoT 中心應用程式。

如果沒有 Azure 訂閱，則可以在 Microsoft 合作夥伴中心創建訂閱。 建立 Azure 訂用帳戶之後，請瀏覽回到 [建立應用程式]**** 頁面。 新的訂用帳戶會出現在 [Azure 訂用帳戶]**** 下拉式清單中。

若要深入了解，請參閱 [Azure 訂用帳戶](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)。

## <a name="location"></a>Location

**位置**是要創建應用程式的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 一般而言，您應選擇實際上與您的裝置最接近的位置，以取得最佳效能。 目前，您可以在**澳大利亞**、**亞太地區**、**歐洲**、**美國**、**英國****和日本**地區創建 IoT 中心應用程式。 選擇位置後，就無法再將應用程式移到不同的位置。

## <a name="application-template"></a>應用程式範本

選擇要用於應用程式的應用程式範本。

## <a name="next-steps"></a>後續步驟

您現在已了解如何以 CSP 的身分建立 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)
