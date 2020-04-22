---
title: Azure IoT 中樞開發人員指南 | Microsoft Docs
description: Azure IoT 中樞開發人員指南中討論到端點、安全性、身分識別登錄、裝置管理、直接方法、裝置對應項、檔案上傳、作業、IoT 中心查詢語言及傳訊。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 4e380fdcd72ab21f00272536b9f08145dcb09bc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728806"
---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT 中樞開發人員指南

Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT 中樞可提供您︰

* 使用每一裝置的安全性認證和存取控制來保護通訊的安全。

* 多個裝置到雲端和雲端到裝置的超大規模通訊選項。

* 每一裝置狀態資訊和中繼資料的可查詢儲存體。

* 透過最受歡迎的語言和平台的裝置程式庫，進行簡單的裝置連線。

此 IoT 中樞開發人員指南包含下列文章︰

* [裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)可協助您在裝置對雲端訊息、裝置對應項報告屬性和檔案上傳之間做出選擇。

* [雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)可協助您在直接方法、裝置對應項所需屬性和雲端對裝置訊息之間做出選擇。

* [IoT 中樞的裝置到雲端及雲端到裝置傳訊](iot-hub-devguide-messaging.md)說明 IoT 中樞所公開的傳訊功能 (裝置到雲端和雲端到裝置)。

  * [將裝置到雲端訊息傳送到 IoT 中心](iot-hub-devguide-messages-d2c.md)。

  * [從內建終結點讀取裝置到雲的消息](iot-hub-devguide-messages-read-builtin.md)。

  * [對裝置到雲端訊息使用自訂終結點與路由規則](iot-hub-devguide-messages-read-custom.md)。

  * [從 IoT 中心傳送到裝置的訊息](iot-hub-devguide-messages-c2d.md)。

  * [建立與讀取 IoT 中心訊息](iot-hub-devguide-messages-construct.md)。

* [從裝置上傳檔案](iot-hub-devguide-file-upload.md)說明如何從裝置上傳檔案。 本文也包含上傳程序可傳送之通知等主題的相關資訊。

* [在 IoT 中樞管理裝置身分識別](iot-hub-devguide-identity-registry.md)說明每個 IoT 中樞身分識別登錄所儲存的資訊。 本文也說明如何存取和修改該資訊。

* [控制 IoT 中樞的存取權](iot-hub-devguide-security.md)說明用來將存取權授與適用於裝置和雲端元件之 IoT 中樞功能的安全性模型。 本文包含使用權杖和 X.509 憑證的相關資訊，以及您可授與之權限的詳細資料。

* [使用裝置對應項來同步狀態和設定](iot-hub-devguide-device-twins.md)說明「裝置對應項」** 概念。 本文還介紹了孿生公開的功能設備,例如將設備與其設備孿生同步。 本文包含裝置對應項中儲存之資料的相關資訊。

* [在裝置上叫用直接方法](iot-hub-devguide-direct-methods.md)說明直接方法的生命週期。 本文說明如何從後端應用程式在裝置上叫用方法，以及在裝置上處理直接方法。

* [排程多個裝置上的作業](iot-hub-devguide-jobs.md)說明如何排程多個裝置上的作業。 本文說明如何將執行工作的作業提交為執行直接方法，使用裝置對應項更新裝置。 它也說明如何查詢作業的狀態。

* [參考 - 選擇通訊協定](iot-hub-devguide-protocols.md)說明 IoT 中樞進行裝置通訊所支援的通訊協定，並列出應開啟的連接埠。

* [參考 - IoT 中樞端點](iot-hub-devguide-endpoints.md)說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。 本文也說明如何在 IoT 中樞中建立額外端點，以及如何使用現場閘道器啟用非標準案例中對 IoT 中樞端點的連線能力。

* [參考 - 裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)說明的 IoT 中樞查詢語言可讓您從中樞擷取有關裝置對應項和作業的資訊。

* [參考 - 配額和節流](iot-hub-devguide-quotas-throttling.md)摘要說明 IoT 中樞服務中設定的配額，以及超過配額時會發生的節流。

* [參考 - 價格](iot-hub-devguide-pricing.md)提供關於 IoT 中樞之不同 SKU 和價格的一般資訊，以及 IoT 中樞如何以訊息的形式針對各種 IoT 中樞功能進行計量的詳細資料。

* [參考 - 裝置和服務 SDK](iot-hub-devguide-sdks.md) 列出的 Azure IoT SDK 可用來開發與 IoT 中樞互動的裝置和服務應用程式。 本文包含線上 API 文件的連結。

* [參考 - IoT 中樞 MQTT 支援](iot-hub-mqtt-support.md)提供 IoT 中樞如何支援 MQTT 通訊協定的詳細資訊。 本文說明 Azure IoT SDK 內建之 MQTT 通訊協定的支援，並提供直接使用 MQTT 通訊協定的相關資訊。

* [詞彙](iot-hub-devguide-glossary.md)是常見 IoT 中樞相關術語的清單。