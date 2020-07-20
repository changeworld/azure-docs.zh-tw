---
title: 在 Azure IoT Central 應用程式中管理您的帳單並從免費定價方案轉換 |Microsoft Docs
description: 身為系統管理員，瞭解如何管理您的帳單，並從免費定價方案移至 Azure IoT Central 應用程式中的標準定價方案
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80157487"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 應用程式中管理您的帳單

本文說明如何以系統管理員身分，在 [管理] 區段中管理 Azure IoT Central 應用程式中的帳單。 您將瞭解如何將您的應用程式從免費定價方案移至標準定價方案，以及如何升級或降級您的定價方案。

若要存取並使用 [**管理**] 區段，您必須是 [*系統管理員*] 角色，或具有可讓您查看 Azure IoT Central 應用程式計費的*自訂使用者角色*。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>從免費移至標準定價方案

- 使用免費定價方案的應用程式在到期前七天免費。 為了避免資料遺失，您可以在到期前隨時將它們移至標準定價方案。
- 使用標準定價方案的應用程式會依每個裝置計費，而每個應用程式的前兩個裝置免費。

在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。

在 [定價] 區段中，您可以將應用程式從免費移至標準定價方案。

若要完成此自助程序，請遵循這些步驟：

1. 移至 [**管理**] 區段中的 [**定價**] 頁面。

    ![試用狀態](media/howto-view-bill/freetrialbilling.png)

1. 選取 [**轉換為付費方案**]。

    ![轉換試用](media/howto-view-bill/convert.png)

1. 選取適當的 Azure Active Directory，然後選取要用於使用付費方案之應用程式的 Azure 訂用帳戶。

1. 在您選取 [**轉換**] 之後，您的應用程式現在會使用付費方案，並開始計費。

> [!Note]
> 根據預設，您會轉換成*標準 2*定價方案。

## <a name="how-to-change-your-application-pricing-plan"></a>如何變更您的應用程式定價方案

使用標準定價方案的應用程式會依每個裝置計費，而每個應用程式的前兩個裝置免費。

在 [定價] 區段中，您可以隨時升級或降級 Azure IoT 定價方案。

1. 移至 [**管理**] 區段中的 [**定價**] 頁面。

    ![試用狀態](media/howto-view-bill/pricing.png)

1. 選取**方案**，然後按一下 [**儲存**] 以升級或降級。

## <a name="view-your-bill"></a>檢視您的帳單

1. 選取適當的 Azure Active Directory，然後選取要用於使用付費方案之應用程式的 Azure 訂用帳戶。

1. 在您選取 [**轉換**] 之後，您的應用程式現在會使用付費方案，並開始計費。

> [!Note]
> 根據預設，您會轉換成*標準 2*定價方案。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中管理您的帳單，建議的下一個步驟是瞭解如何在 Azure IoT Central 中[自訂應用程式 UI](howto-customize-ui.md) 。