---
title: Azure 物聯網 (IoT) 解決方案選項
description: 在平台服務或受控應用程式平台方法之間做選擇以建置 IoT 解決方案的指導方針。 此平台服務方法會使用 IoT 中樞和 Digital Twins 之類的服務作為建構元素。 受控應用程式平台方法會使用 IoT Central 快速開始。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 046116addf643d6c0eb0faf2f821b6ef78d440c6
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050625"
---
# <a name="choose-the-right-iot-solution"></a>選擇合適的 IoT 解決方案

若要為您的企業建置 IoT 解決方案，您通常會選擇使用「平台服務」  或「受控應用程式平台」  方法。

平台服務可為自訂和有彈性的 IoT 應用程式提供建構元素。 當您連接裝置，以及擷取、儲存和分析資料時，您有更多選項可供選擇和撰寫程式碼。 Azure IoT 平台服務包含 Azure IoT 中樞和 Azure Digital Twins 等產品。

相較於平台服務，受控應用程式平台可讓您藉由減少達成結果所需的決策數，更快速地開始建置應用程式。 受控應用程式平台會處理您解決方案的大部分元素，因此您可專注於新增產業知識，以及調整和連接裝置。 Azure IoT Central 是受控應用程式平台。

若要在這兩種方法之間做選擇，您應該考慮：

- 您要如何管理解決方案。
- 您的解決方案所需的自訂和控制層級。
- 您想要的定價結構。

## <a name="management"></a>管理性

您想要將系統管理時間和資源用於何處？ 

- 選擇平台服務方法，以充分掌控您解決方案中的基礎服務。 例如，您想要：

    - 管理調整和保護服務，以符合您的需求。
    - 利用內部或合作夥伴的專業知識，讓裝置上線並佈建服務。

- 選擇受控應用程式平台方法，利用平台來處理 IoT 應用程式和裝置的調整、安全性和管理。

## <a name="control"></a>控制

您想要自訂解決方案的哪些元素？

- 選擇平台服務方法，以便整體自訂和控制解決方案架構。

- 選擇受控應用程式平台方法，以自訂商標、儀表板、使用者角色、裝置和遙測。 不過，您不想要處理基礎 IoT 系統管理負荷。

## <a name="pricing"></a>定價

哪個定價結構最符合您的需求？

- 選擇平台服務方法，以微調服務和控制我的整體成本。

- 選擇受控應用程式平台方法，以取得簡單且可預測的定價結構。

## <a name="summary"></a>摘要

平台服務方法適合於具有雲端解決方案和裝置專業知識的企業，而且其想要：

- 微調解決方案中的服務。
- 對解決方案中的服務擁有高度的控制權。
- 完全自訂解決方案。

受控應用程式平台方法適合於下列企業：

- 不想要將大量資源專用於系統設計、開發和管理。
- 是否想要可預測的定價結構。
- 是否想要一些自訂功能。

## <a name="next-steps"></a>後續步驟

如需不同服務和平台以及其使用方式的更完整說明，請參閱 [Azure IoT 服務與技術](iot-services-and-technologies.md)。

若要深入了解成功 IoT 解決方案的重要屬性，請參閱[成功 IoT 解決方案的 8 個屬性](https://aka.ms/8attributes)白皮書。

如需 IoT 架構的深入討論，請參閱 [Microsoft Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)。
