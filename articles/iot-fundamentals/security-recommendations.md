---
title: Azure IoT 的安全建議 |微軟文檔
description: 本文總結了確保 Azure IoT 中心解決方案中安全性的其他步驟。
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048468"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物聯網 （IoT） 部署的安全建議

本文包含 IoT 的安全建議。 實施這些建議將説明您履行我們共同責任模型中所述的安全義務。 有關 Microsoft 如何履行服務提供者職責的詳細資訊，請閱讀[雲計算的共用責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的某些建議可以由 Azure 安全中心自動監視。 Azure 安全中心是保護 Azure 中資源的第一道防線。 它定期分析 Azure 資源的安全狀態，以識別潛在的安全性漏洞。 然後，它為您提供關於如何解決它們的建議。

- 有關 Azure 安全中心建議的詳細資訊，請參閱[Azure 安全中心中的安全建議](../security-center/security-center-recommendations.md)。
- 有關 Azure 安全中心的資訊，請參閱[什麼是 Azure 安全中心？](../security-center/security-center-intro.md)

## <a name="general"></a>一般

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 隨時掌握最新資訊 | 使用受支援的平臺、程式設計語言、協定和框架的最新版本。 | - |
| 保護身份驗證金鑰的安全 | 部署後，請保持設備 I 及其身份驗證金鑰的物理安全。 這將避免偽裝成註冊設備的惡意設備。 | - |
| 盡可能使用設備 SDK | 設備 SDK 實現各種安全功能，如加密、身份驗證等，以説明您開發強大而安全的設備應用程式。 有關詳細資訊[，請參閱瞭解和使用 Azure IoT 中心 SDK。](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理 

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 定義中心的存取控制 | 根據功能瞭解和定義每個元件在 IoT 中心解決方案中將[具有的訪問類型](iot-security-deployment.md#securing-the-cloud)。 允許的許可權是*註冊表讀取*、*註冊表讀取、**服務連接*和*設備連接*。 [IoT 中心中的預設共用訪問策略](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions)還有助於根據每個元件的角色定義許可權。 | - |
| 定義後端服務的存取控制 | IoT 中心解決方案引入的資料可由其他 Azure 服務（如[Cosmos DB、](https://docs.microsoft.com/azure/cosmos-db/)[流分析](https://docs.microsoft.com/azure/stream-analytics/)、[應用服務](https://docs.microsoft.com/azure/app-service/)、[邏輯應用](https://docs.microsoft.com/azure/logic-apps/)和 Blob[存儲](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)）使用。 確保瞭解和允許這些服務記錄的適當存取權限。 | - |

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 安全設備身份驗證 | 通過使用[唯一的身份金鑰或安全權杖](iot-security-deployment.md#iot-hub-security-tokens)，或每個[設備的設備上 X.509 憑證](iot-security-deployment.md#x509-certificate-based-device-authentication)，確保設備和 IoT 中心之間的安全通信。 使用適當的方法[根據所選協定（MQTT、AMQP 或 HTTPS）使用安全權杖](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。 | - |
| 安全設備通信 | IoT 集線器使用傳輸層安全 （TLS） 標準（支援版本 1.2 和 1.0）確保與設備的連接。 使用[TLS 1.2](https://tools.ietf.org/html/rfc5246)確保最大的安全性。 | - |
| 保護服務通訊 | IoT 中心提供終結點以僅使用 TLS 協定連接到後端服務（如[Azure 存儲](/azure/storage/)或[事件中心](/azure/event-hubs)），並且未加密通道上不會公開終結點。 一旦此資料到達這些後端服務進行存儲或分析，請確保為該服務使用適當的安全和加密方法，並保護後端的敏感資訊。 | - |

## <a name="networking"></a>網路

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 保護對設備的訪問 | 將設備中的硬體埠保持在最低限度，以避免不必要的訪問。 此外，建立機制以防止或檢測設備的物理篡改。 有關詳細資訊，請閱讀[IoT 安全最佳實踐](iot-security-best-practices.md)。 | - |
| 構建安全硬體 | 集成加密存儲或可信平臺模組 （TPM） 等安全功能，確保設備和基礎架構更加安全。 保持設備作業系統和驅動程式升級到最新版本，如果空間允許，安裝防病毒和反惡意軟體功能。 閱讀[IoT 安全體系結構](iot-security-architecture.md)，瞭解這如何説明緩解多種安全威脅。 | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 由 ASC 支援 |
|-|----|--|
| 監控對設備的未授權訪問 |  使用設備作業系統的日誌記錄功能監視設備或其埠的任何安全性漏洞或物理篡改。 | - |
| 從雲監控 IoT 解決方案 | 使用[Azure 監視器 中的指標](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)監視 IoT 中心解決方案的總體運行狀況。 | - |
| 設置診斷 | 通過在解決方案中記錄事件，然後將診斷日誌發送到 Azure 監視器，以深入瞭解性能，密切監視操作。 有關詳細資訊，請閱讀[IoT 中心中的監視器並診斷問題](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health)。 | - |

## <a name="next-steps"></a>後續步驟

對於涉及 Azure IoT 的高級方案，您可能需要考慮其他安全要求。 有關詳細資訊[，請參閱 IoT 安全體系結構](iot-security-architecture.md)。

