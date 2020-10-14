---
title: 事件中樞中的 Azure 結構描述登錄 (預覽)
description: 本文提供 Azure 事件中樞所支援結構描述登錄的概觀 (預覽)。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 30ef2f102a4b8d9f9908ba915f179889710bafd0
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938792"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>事件中樞中的 Azure 結構描述登錄 (預覽)
在許多事件串流處理和訊息傳遞案例中，事件或訊息承載都包含使用結構描述驅動格式 (例如 Apache Avro) 進行序列化或還原序列化的結構化資料。 傳送者和接收者都可能需要使用與 JSON 結構描述相同的結構描述文件來驗證資料的完整性。 針對結構描述驅動格式，讓訊息取用者可以使用的結構描述是取用者能夠將資料還原序列化的必要條件。 

**Azure 結構描述登錄**是事件中樞的一項功能，可為事件驅動和以訊息為中心的應用程式，提供結構描述文件的中央存放庫。 其可讓您的生產者和取用者應用程式彈性地交換資料，而不需要管理及共用它們之間的結構描述，也能以不同的費率發展。 結構描述登錄也為可重複使用的結構描述提供簡單的治理架構，並透過群組結構 (結構描述群組) 定義結構描述之間的關聯性。

> [!NOTE]
> - **結構描述登錄**功能目前處於**預覽**狀態，不建議用於生產工作負載。
> - 此功能僅適用於**標準**和**專用**階層，而不適用於**基本**階層。

透過 Apache Avro 這類結構描述驅動的序列化架構，將序列化中繼資料外部化至共用結構描述也有助於大幅降低每個資料集所包含類型資訊和欄位名稱的每一訊息額外負荷，如同 JSON 之類的標記格式。 將結構描述連同事件一起儲存在事件基礎結構內，可確保序列化/還原序列化所需的中繼資料一律會觸達，且結構描述不能錯置。 

## <a name="event-hubs-namespace"></a>事件中樞命名空間
事件中樞命名空間現在可與事件中樞 (或 Kafka 主題) 一起裝載結構描述群組。 其會裝載結構描述登錄，且可以有多個結構描述群組。 儘管裝載在 Azure 事件中樞中，結構描述登錄也可以與所有 Azure 訊息服務和任何其他訊息或事件訊息代理程式通用使用。 這些結構描述群組中的每一個都是一組結構描述的個別安全存放庫。 群組可以與特定的應用程式或組織單位對齊。 

## <a name="schema-groups"></a>結構描述群組
結構描述群組是以您商務準則為基礎的類似結構描述邏輯群組。 結構描述群組可以保存多個版本的結構描述。 結構描述群組上的相容性強制設定有助於確保較新的結構描述版本回溯相容。

群組機制所施加的安全性界限有助於確保在多個合作夥伴之間共用命名空間的情況下，不會不慎透過中繼資料洩漏營業秘密。 其也可讓應用程式擁有者管理與其他共用相同命名空間的應用程式無關的結構描述。


## <a name="schemas"></a>結構描述
結構描述會定義生產者和取用者之間的合約。 在事件中樞結構描述登錄中定義的結構描述可協助管理事件資料以外的合約，因而移除承載額外負荷。 結構描述具有名稱、類型 (例如：記錄、陣列等等)、相容性模式 (無、轉接、回溯、完整) 和序列化類型 (目前只有 Avro)。 您可以建立多個版本的結構描述，並擷取及使用特定版本的結構描述。 

## <a name="client-sdks"></a>用戶端 SDK
您可以使用下列其中一個包含 Avro 序列化程式的程式庫，並用其序列化及還原序列化包含結構描述登錄結構描述識別碼和 Avro 編碼資料的承載。

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - 執行 Kafka 整合式 Apache Avro 序列化程式，以及 Azure 結構描述登錄所支援的還原序列化程式。 JAVA 用戶端的 Apache Kafka 用戶端序列化程式適用於 Azure 結構描述登錄，可用於任何 Apache Kafka 案例，以及搭配任何以 Apache Kafka® 為基礎的部署或雲端服務。 

下圖顯示結構描述登錄與事件中樞的資訊流程： 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="流程圖":::

## <a name="standard-vs-dedicated-limits"></a>標準與專用限制
如需標準和專用層級事件中樞的相同和不同限制 (例如：命名空間中的結構描述群組數目)，請參閱[結構描述登錄限制](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="role-based-access-control"></a>角色型存取控制
以程式設計方式存取結構描述登錄時，您需要在 Azure Active Directory (Azure AD) 中註冊應用程式，並將應用程式的安全性主體新增至其中一個角色型存取控制 (RBAC) 角色：

| 角色 | 描述 | 
| ---- | ----------- | 
| 擁有者 | 讀取、寫入及刪除結構描述登錄群組和結構描述。 |
| 參與者 | 讀取、寫入及刪除結構描述登錄群組和結構描述。 |
| [結構描述登錄讀取器 (預覽)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 讀取並列出結構描述登錄群組和結構描述。 |
| [結構描述登錄參與者 (預覽)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | 讀取、寫入及刪除結構描述登錄群組和結構描述。 |

如需使用 Azure 入口網站來建立及註冊應用程式的指示，請參閱[使用 Azure AD 註冊應用程式](../active-directory/develop/quickstart-register-app.md)。 記下用戶端識別碼 (應用程式識別碼)、租用戶識別碼，以及要在程式碼中使用的祕密。 

## <a name="next-steps"></a>後續步驟

- 若要了解如何使用 Azure 入口網站建立結構描述登錄，請參閱[使用 Azure 入口網站建立事件中樞結構描述登錄](create-schema-registry.md)。
- 請參閱下列**結構描述登錄 Avro 用戶端程式庫**範例。
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure 結構描述登錄的 Kafka Avro 整合](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
