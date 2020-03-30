---
title: 在 Azure IoT 集中應用程式中管理帳單並從免費定價計畫進行轉換 |微軟文檔
description: 作為管理員，瞭解如何管理帳單，並在 Azure IoT 中央應用程式中從免費定價計畫遷移到標準定價計畫
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157487"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT 中央應用程式中管理帳單

本文介紹作為管理員，如何在管理部分的 Azure IoT 集中應用程式中管理帳單。 您將瞭解如何將應用程式從免費定價計畫移動到標準定價計畫，以及如何升級或降級定價計畫。

要訪問和使用**管理**部分，您必須處於*管理員*角色中，或者具有允許您查看 Azure IoT 中央應用程式的計費的*自訂使用者角色*。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>從免費定價計畫轉向標準定價計畫

- 使用免費定價計畫的應用程式在過期前七天免費。 為了避免遺失資料，您可以在資料過期前隨時將其移動到標準定價計畫。
- 使用標準定價計畫的應用程式按設備收費，每個應用程式前兩台設備免費。

在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。

在定價部分中，您可以將應用程式從免費版移動到標準定價計畫。

若要完成此自助程序，請遵循這些步驟：

1. 轉到 **"管理**"部分中的 **"定價**"頁面。

    ![試用狀態](media/howto-view-bill/freetrialbilling.png)

1. 選擇 **"轉換為付費計畫**"。

    ![轉換試用](media/howto-view-bill/convert.png)

1. 選擇相應的 Azure 活動目錄，然後選擇 Azure 訂閱，用於使用付費計畫的應用程式。

1. 選擇 **"轉換"** 後，應用程式現在使用付費計畫，然後開始計費。

> [!Note]
> 預設情況下，您將轉換為*標準 2*定價計畫。

## <a name="how-to-change-your-application-pricing-plan"></a>如何更改應用程式定價計畫

使用標準定價計畫的應用程式按設備收費，每個應用程式前兩台設備免費。

在定價部分中，您可以隨時升級或降級 Azure IoT 定價計畫。

1. 轉到 **"管理**"部分中的 **"定價**"頁面。

    ![試用狀態](media/howto-view-bill/pricing.png)

1. 選擇 **"計畫****"，然後按一下"保存"** 以升級或降級。

## <a name="view-your-bill"></a>檢視您的帳單

1. 選擇相應的 Azure 活動目錄，然後選擇 Azure 訂閱，用於使用付費計畫的應用程式。

1. 選擇 **"轉換"** 後，應用程式現在使用付費計畫，然後開始計費。

> [!Note]
> 預設情況下，您將轉換為*標準 2*定價計畫。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了如何在 Azure IoT 中央應用程式中管理帳單，建議的下一步是瞭解 Azure IoT 中央的[自訂應用程式 UI。](howto-customize-ui.md)