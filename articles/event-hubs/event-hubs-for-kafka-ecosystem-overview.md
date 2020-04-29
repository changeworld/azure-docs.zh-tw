---
title: 從 Apache Kafka 應用程式使用事件中樞 - Azure 事件中樞 | Microsoft Docs
description: 本文提供與 Azure 事件中樞所支援的 Apache Kafka 有關的資訊。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 91e2d70bab8c1be4b3e5b400ce21122eccb1e9eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811371"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>從 Apache Kafka 應用程式使用 Azure 事件中樞
事件中樞提供的 Kafka 端點可讓您現有的 Kafka 型應用程式使用，作為執行您本身的 Kafka 叢集以外的另一項選擇。 事件中樞支援 [Apache Kafka 通訊協定 1.0 和更新版本](https://kafka.apache.org/documentation/)，並且可與您現有的 Kafka 應用程式搭配使用，包括 MirrorMaker。  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>適用於 Kafka 的事件中樞提供哪些功能？

適用於 Kafka 的事件中樞功能在 Azure 事件中樞之上提供了通訊協定標頭，與 Kafka 1.0 版和更新版本之間具有讀取和寫入 Kafka 主題的二進位相容性。 您可以從應用程式開始使用 Kafka 端點，不必變更程式碼，但需要稍微變更設定。 您可以更新設定中的連接字串，以指向事件中樞所公開的 Kafka 端點，而非指向 Kafka 叢集。 然後，您可以開始將事件從使用 Kafka 通訊協定的應用程式串流到事件中樞。 此整合也支援 [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) \(英文\) (目前處於預覽狀態) 之類的架構。 

概念上的 Kafka 和事件中樞幾乎完全相同：兩者都是針對串流資料所建立的分割記錄。 下表提供 Kafka 與事件中樞之間的概念對應。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中樞的概念對應

| Kafka 概念 | 事件中樞概念|
| --- | --- |
| 叢集 | 命名空間 |
| 主題 | 事件中樞 |
| 資料分割 | 資料分割|
| 取用者群組 | 取用者群組 |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka 與事件中樞的主要差異

[Apache Kafka](https://kafka.apache.org/) 是一項軟體，可供您隨處執行，而事件中樞則是類似於 Azure Blob 儲存體的雲端服務。 並沒有任何伺服器或網路需要管理，也沒有需要設定的代理程式。 您會建立命名空間，作為您的主題存留所在的 FQDN，然後在該命名空間中建立事件中樞或主題。 如需關於事件中樞和命名空間的詳細資訊，請參閱[事件中樞功能](event-hubs-features.md#namespace)。 作為雲端服務，事件中樞使用單一的穩定虛擬 IP 位址作為端點，因此用戶端不需要知道叢集中的代理程式或機器。 

事件中樞內的調整由您所購買的輸送量單位數所控制，每個輸送量單位可讓您獲得每秒輸送 1 MB 或每秒輸入 1000 個事件的配額。 根據預設，事件中樞會在您達到限制時使用[自動擴充](event-hubs-auto-inflate.md)功能相應增加輸送量單位；此功能也可供適用於 Kafka 的事件中樞功能使用。 

### <a name="security-and-authentication"></a>安全性和驗證
每當您從事件中樞發佈或取用來自 Kafka 的事件時，您的用戶端就會嘗試存取事件中樞資源。 您想要確保使用已授權的實體來存取資源。 搭配使用 Apache Kafka 通訊協定與您的用戶端時，您可以使用 SASL 機制來設定驗證和加密的設定。 使用事件中樞進行 Kafka 時，需要 TLS 加密（因為傳輸中的所有資料都有事件中樞的 TLS 已加密）。 您可以在設定檔中指定 SASL_SSL 選項來完成此作業。 

Azure 事件中樞提供多個選項來授權存取您的安全資源。 

- OAuth
- 共用存取簽章 (SAS)

#### <a name="oauth"></a>OAuth
事件中樞與 Azure Active Directory （Azure AD）整合，它會提供與**OAuth** 2.0 相容的集中式授權伺服器。 使用 Azure AD，您可以使用角色型存取控制（RBAC），將更細緻的許可權授與您的用戶端身分識別。 您可以藉由指定通訊協定的**SASL_SSL**和機制的**OAUTHBEARER** ，將此功能與 Kafka 用戶端搭配使用。 如需有關 RBAC 角色的詳細資訊以及範圍存取的層級，請參閱[使用 Azure AD 授權存取](authorize-access-azure-active-directory.md)。

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>共用存取簽章 (SAS)
事件中樞也會提供**共用存取簽章（SAS）** ，以供委派存取 Kafka 資源的事件中樞。 使用 OAuth 2.0 權杖型機制來授權存取，可提供優於 SAS 的高安全性和易用性。 內建角色也可以消除 ACL 型授權的需求，這必須由使用者維護及管理。 您可以將此功能與 Kafka 用戶端搭配使用，方法是指定通訊協定的**SASL_SSL**和**純**文本機制。 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>範例 
如需**教學**課程和使用 SAS 或 OAuth 來建立事件中樞並加以存取的逐步指示，請參閱[快速入門：使用 Kafka 通訊協定事件中樞串流處理](event-hubs-quickstart-kafka-enabled-event-hubs.md)。

如需示範如何搭配 Kafka 使用 OAuth 與事件中樞的更多**範例**，請參閱[GitHub 上的範例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。

## <a name="other-event-hubs-features-available-for-kafka"></a>適用於 Kafka 的其他事件中樞功能

適用於 Kafka 的事件中樞功能可讓您以不同的通訊協定執行寫入和讀取，讓您目前的 Kafka 產生者可以繼續透過 Kafka 發行，並讓您能透過事件中樞新增讀取器，例如 Azure 串流分析或 Azure Functions。 此外，[擷取](event-hubs-capture-overview.md)和[異地災害復原](event-hubs-geo-dr.md)等事件中樞功能也可供適用於 Kafka 的事件中樞功能使用。

## <a name="features-that-are-not-yet-supported"></a>尚不支援的功能 

以下是尚不支援的 Kafka 功能清單：

*   等冪產生者
*   交易
*   壓縮
*   以大小為基礎的保留期
*   記錄壓縮
*   將分割區新增至現有主題
*   HTTP Kafka API 支援
*   Kafka Streams

## <a name="next-steps"></a>後續步驟
本文概略介紹了適用於 Kafka 的事件中樞。 若要深入了解，請參閱[適用於 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)。


