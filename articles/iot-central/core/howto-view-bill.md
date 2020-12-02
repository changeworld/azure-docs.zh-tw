---
title: 在 Azure IoT Central 應用程式中管理您的帳單，並從免費定價方案進行轉換 |Microsoft Docs
description: 以系統管理員身分，瞭解如何管理您的帳單，並從免費定價方案移至 Azure IoT Central 應用程式中的標準定價方案
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8f46c402c8c25a144cd4c12137aeabfdfa31e096
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463160"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 應用程式中管理您的帳單

本文說明如何以系統管理員身分管理您的 Azure IoT Central 帳單。 您可以將應用程式從免費定價方案移至標準定價方案，也可以升級或降級您的定價方案。

若要存取 **管理** 區段，您必須是 *系統管理員* 角色，或具有可讓您查看帳單的 *自訂使用者角色* 。 如果您建立 Azure IoT Central 應用程式，系統就會自動將您指派給 **系統管理員** 角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>從免費移至標準定價方案

- 使用免費定價方案的應用程式在到期前七天免費。 若要避免資料遺失，您可以在到期前隨時將它們移至標準定價方案。
- 使用標準定價方案的應用程式會依據裝置收費，每個應用程式的前兩個裝置免費。

在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。

在 [定價] 區段中，您可以將應用程式從免費移至標準定價方案。

若要完成此自助程序，請遵循這些步驟：

1. 移至 [系統 **管理**] 區段中的 [**定價**] 頁面。

    ![試用狀態](media/howto-view-bill/freetrialbilling.png)

1. 選取 [ **轉換成付費方案**]。

    ![轉換試用](media/howto-view-bill/convert.png)

1. 選取適當的 Azure Active Directory，然後選取要用於使用付費方案的應用程式的 Azure 訂用帳戶。

1. 選取 [ **轉換**] 之後，您的應用程式現在會使用付費方案，並開始計費。

> [!Note]
> 根據預設，您會轉換成 *標準的 2* 定價方案。

## <a name="how-to-change-your-application-pricing-plan"></a>如何變更您的應用程式定價方案

使用標準定價方案的應用程式會依據裝置收費，每個應用程式的前兩個裝置免費。

在 [定價] 區段中，您可以隨時升級或降級您的 Azure IoT 定價方案。

1. 移至 [系統 **管理**] 區段中的 [**定價**] 頁面。

    ![升級 prcing 方案](media/howto-view-bill/pricing.png)

1. 選取 **方案** ，然後選取 [ **儲存** ] 以升級或降級。

## <a name="view-your-bill"></a>檢視您的帳單

1. 選取適當的 Azure Active Directory，然後選取要用於使用付費方案的應用程式的 Azure 訂用帳戶。

1. 選取 [ **轉換**] 之後，您的應用程式現在會使用付費方案，並開始計費。

> [!Note]
> 根據預設，您會轉換成 *標準的 2* 定價方案。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中管理您的帳單，建議的下一個步驟是瞭解如何在 Azure IoT Central 中 [自訂應用程式 UI](howto-customize-ui.md) 。