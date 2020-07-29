---
title: Azure IoT 的安全性建議 |Microsoft Docs
description: 本文摘要說明在您的 Azure IoT 中樞解決方案中確保安全性的其他步驟。
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
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81728988"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物聯網（IoT）部署的安全性建議

本文包含 IoT 的安全性建議。 依照我們的共同責任模型所述，執行這些建議有助於您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 這會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 接著，這會為您提供如何解決這些問題的建議。

- 如需 Azure 資訊安全中心建議的詳細資訊，請參閱 [Azure 資訊安全中心的安全性建議](../security-center/security-center-recommendations.md)。
- 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../security-center/security-center-intro.md)

## <a name="general"></a>一般

| 建議 | 註解 | 支援 ASC |
|-|----|--|
| 隨時掌握最新資訊 | 使用支援的平臺、程式設計語言、通訊協定和架構的最新版本。 | - |
| 保持驗證金鑰安全 | 部署之後，請讓裝置識別碼和其驗證金鑰實際安全。 這可避免惡意裝置偽裝成已註冊的裝置。 | - |
| 盡可能使用裝置 Sdk | 裝置 Sdk 會執行各種不同的安全性功能，例如加密、驗證等等，以協助您開發健全且安全的裝置應用程式。 如需詳細資訊，請參閱[瞭解及使用 Azure IoT 中樞 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理 

| 建議 | 註解 | 支援 ASC |
|-|----|--|
| 定義中樞的存取控制 | 根據功能，瞭解並定義每個元件在您的 IoT 中樞解決方案中將擁有[的存取類型](iot-security-deployment.md#securing-the-cloud)。 允許的許可權包括*Registry Read*、 *RegistryReadWrite*、 *ServiceConnect*和*DeviceConnect*。 [IoT 中樞內的預設共用存取原則](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)也可以協助根據每個元件的角色定義其許可權。 | - |
| 定義後端服務的存取控制 | IoT 中樞解決方案所內嵌的資料可供其他 Azure 服務使用，例如[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)、[串流分析](https://docs.microsoft.com/azure/stream-analytics/)、 [App Service](https://docs.microsoft.com/azure/app-service/)、 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)和[Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)。 請務必瞭解並允許這些服務所記載的適當存取權限。 | - |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 支援 ASC |
|-|----|--|
| 安全裝置驗證 | 使用唯一的身分[識別金鑰或安全性權杖](iot-security-deployment.md#iot-hub-security-tokens)，或每個裝置的[裝置 x.509 憑證](iot-security-deployment.md#x509-certificate-based-device-authentication)，確保您的裝置與 IoT 中樞之間的通訊安全。 使用適當的方法，[根據所選的通訊協定（MQTT、AMQP 或 HTTPS）來使用安全性權杖](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 | - |
| 保護裝置通訊 | IoT 中樞使用傳輸層安全性（TLS）標準保護裝置的連線，支援1.2 和1.0 版。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)以確保最高的安全性。 | - |
| 保護服務通訊 | IoT 中樞提供端點來連接到後端服務（例如[Azure 儲存體](/azure/storage/)或[事件中樞](/azure/event-hubs)僅使用 TLS 通訊協定），而且未在未加密的通道上公開任何端點。 一旦此資料抵達這些後端服務進行儲存或分析，請務必為該服務採用適當的安全性和加密方法，並在後端保護機密資訊。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 支援 ASC |
|-|----|--|
| 保護對您裝置的存取 | 將裝置中的硬體埠保持在最低限度，以避免不必要的存取。 此外，建立機制來防止或偵測裝置的實體篡改。 如需詳細資訊，請參閱[IoT 安全性最佳做法](iot-security-best-practices.md)。 | - |
| 建立安全硬體 | 結合加密儲存體或信賴平臺模組（TPM）之類的安全性功能，讓裝置和基礎結構更安全。 將裝置作業系統和驅動程式保持升級至最新版本，如果空間允許，請安裝防毒軟體和反惡意程式碼功能。 閱讀[IoT 安全性架構](iot-security-architecture.md)，以瞭解如何協助減輕數個安全性威脅。 | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 支援 ASC |
|-|----|--|
| 監視裝置的未經授權存取 |  使用您裝置的作業系統記錄功能來監視裝置或其埠的任何安全性缺口或實體篡改。 | - |
| 從雲端監視您的 IoT 解決方案 | 使用[Azure 監視器中的計量](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)，監視 IoT 中樞解決方案的整體健全狀況。 | - |
| 設定診斷 | 藉由在您的解決方案中記錄事件，然後將診斷記錄傳送至 Azure 監視器，以取得效能的可見度，藉此密切監看您的作業。 如需詳細資訊，請參閱[監視和診斷 IoT 中樞的問題](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)。 | - |

## <a name="next-steps"></a>後續步驟

針對牽涉到 Azure IoT 的先進案例，您可能需要考慮其他安全性需求。 如需詳細指引，請參閱[IoT 安全性架構](iot-security-architecture.md)。

