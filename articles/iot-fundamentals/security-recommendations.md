---
title: Azure IoT 的安全性建議 |Microsoft Docs
description: 本文將摘要說明可確保 Azure IoT 中樞解決方案中安全性的其他步驟。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5a7fc2a5a5a6be7695fa816f28e3cdf61c5839f2
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208258"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>適用于 Azure 物聯網 (IoT) 部署的安全性建議

本文包含適用于 IoT 的安全性建議。 依照我們的共同責任模型所述，執行這些建議有助於您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 這會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 接著，這會為您提供如何解決這些問題的建議。

- 如需 Azure 資訊安全中心建議的詳細資訊，請參閱 [Azure 資訊安全中心的安全性建議](../security-center/security-center-recommendations.md)。
- 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../security-center/security-center-introduction.md)

## <a name="general"></a>一般

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 隨時掌握最新資訊 | 使用最新版本的支援平臺、程式設計語言、通訊協定和架構。 | - |
| 保持驗證金鑰安全 | 在部署之後，讓裝置識別碼和其驗證金鑰在實際安全的地方保持安全。 這可避免惡意裝置偽裝為已註冊的裝置。 | - |
| 盡可能使用裝置 Sdk | 裝置 Sdk 會執行各種安全性功能，例如加密、驗證等，以協助您開發健全且安全的裝置應用程式。 如需詳細資訊，請參閱 [瞭解及使用 Azure IoT 中樞 sdk](../iot-hub/iot-hub-devguide-sdks.md) 。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理 

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 定義中樞的存取控制 | 根據功能，[瞭解並定義](iot-security-deployment.md#securing-the-cloud)每個元件在 IoT 中樞解決方案中所擁有的存取類型。 允許的許可權為 *Registry Read*、 *RegistryReadWrite*、 *ServiceConnect*和 *>deviceconnect*。 [IoT 中樞內的預設共用存取原則](../iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)也可以根據其角色來協助定義每個元件的許可權。 | - |
| 定義後端服務的存取控制 | IoT 中樞解決方案所內嵌的資料可供其他 Azure 服務使用，例如 [Cosmos DB](../cosmos-db/index.yml)、 [串流分析](../stream-analytics/index.yml)、 [App Service](../app-service/index.yml)、 [Logic Apps](../logic-apps/index.yml)和 [Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 請務必瞭解並允許適當的存取權限，如這些服務所述。 | - |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 安全裝置驗證 | 您可以使用唯一的身分 [識別金鑰或安全性權杖](iot-security-deployment.md#iot-hub-security-tokens)，或每個裝置的 [裝置 x.509 憑證](iot-security-deployment.md#x509-certificate-based-device-authentication) ，以確保您的裝置與 IoT 中樞之間的通訊安全。 使用適當的方法， [根據選擇的通訊協定（ (MQTT、AMQP 或 HTTPS) ）使用安全性權杖 ](../iot-hub/iot-hub-devguide-security.md)。 | - |
| 安全裝置通訊 | IoT 中樞會使用傳輸層安全性 (TLS) 標準（支援1.2 和1.0 版）來保護裝置的連線。 使用 [TLS 1.2](https://tools.ietf.org/html/rfc5246) 來確保最高的安全性。 | - |
| 保護服務通訊 | IoT 中樞提供端點來連線至後端服務（例如 [Azure 儲存體](../storage/index.yml) 或僅使用 TLS 通訊協定的 [事件中樞](../event-hubs/index.yml) ），而且不會在未加密的通道上公開任何端點。 一旦此資料到達這些後端服務以進行儲存或分析，請務必為該服務採用適當的安全性和加密方法，並保護後端的機密資訊。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 保護裝置的存取權 | 將裝置中的硬體埠維持在最低限度，以避免不必要的存取。 此外，也會建立防止或偵測裝置實體篡改的機制。 如需詳細資訊，請參閱 [IoT 安全性最佳作法](iot-security-best-practices.md) 。 | - |
| 建立安全的硬體 | 將加密儲存體或信賴平臺模組 (TPM) 等安全性功能納入保護，讓裝置和基礎結構更安全。 將裝置作業系統和驅動程式保持升級到最新版本，如果空間允許，請安裝防毒軟體和反惡意程式碼功能。 閱讀 [IoT 安全性架構](iot-security-architecture.md) ，以瞭解其如何有助於減輕數個安全性威脅。 | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 監視對您裝置的未經授權存取 |  使用裝置作業系統的記錄功能來監視裝置或其埠的任何安全性缺口或實體篡改。 | - |
| 從雲端監視您的 IoT 解決方案 | 使用 [Azure 監視器中的計量](../iot-hub/iot-hub-metrics.md)，監視 IoT 中樞解決方案的整體健康情況。 | - |
| 設定診斷 | 藉由記錄您解決方案中的事件，然後將診斷記錄傳送至 Azure 監視器，以瞭解效能，以密切觀賞您的作業。 如需詳細資訊，請參閱 [監視及診斷 IoT 中樞的問題](../iot-hub/iot-hub-monitor-resource-health.md) 。 | - |

## <a name="next-steps"></a>後續步驟

針對牽涉到 Azure IoT 的 advanced 案例，您可能需要考慮其他安全性需求。 如需詳細指引，請參閱 [IoT 安全性架構](iot-security-architecture.md) 。