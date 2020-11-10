---
title: Azure 中 FHIR 服務的相關常見問題-Azure API for FHIR
description: 取得 Azure API for FHIR 常見問題的解答，例如 FHIR Api 後方資料的儲存位置，以及版本支援。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 8f4d16931f09f94af81dd4e0f178ce6e0f990881
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426199"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Azure API for FHIR 的常見問題

## <a name="azure-api-for-fhir-the-basics"></a>Azure API for FHIR：基本概念

### <a name="what-is-fhir"></a>什麼是 FHIR？
快速健康照護互通資源 (FHIR 發音「火災」 ) 是一種互通性標準，目的是要在不同的健康情況系統之間交換醫療保健資料。 這項標準是由 HL7 組織所開發，並且是由世界各地的醫療保健組織所採用。 可用的最新 FHIR 版本為 R4 (第4版) 。 Azure API for FHIR 支援 R4，也支援舊版 STU3 (Standard for 試用版使用 3) 。 如需 FHIR 的詳細資訊，請造訪 [HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 背後的資料會儲存在 Azure 中嗎？

是，資料會儲存在 Azure 中的受控資料庫。 Azure API for FHIR 不會提供基礎資料存放區的直接存取權。

### <a name="what-identity-provider-do-you-support"></a>您支援哪些身分識別提供者？

我們目前支援 Microsoft Azure Active Directory 作為身分識別提供者。

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Azure API for FHIR 的 RPO)  (RPO 的復原點目標為何？
Azure API for FHIR 是由 Cosmos DB 的持續性提供者所支援。 因此，服務的 RPO 等於 [Cosmos DB (單一區域) ](https://docs.microsoft.com/azure/cosmos-db/consistency-levels) 且 < 240 分鐘。

### <a name="what-fhir-version-do-you-support"></a>您支援的 FHIR 版本為何？

我們在 Azure API for FHIR (PaaS) 和適用于 Azure 的 FHIR 伺服器上支援版本4.0.0 和 3.0.1 (開放原始碼) 。

如需詳細資訊，請參閱 [支援的功能](fhir-features-supported.md)。 閱讀 FHIR 版本之間變更的內容 (例如 STU3 至 R4) [（在 HL7 FHIR 的版本歷程記錄](https://hl7.org/fhir/R4/history.html)中）。

適用于 FHIR (preview) 的 Azure IoT Connector 目前僅支援 FHIR 版本 R4，而且只會在 Azure API for FHIR R4 實例上顯示。

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>「Microsoft FHIR Server for Azure」與「Azure API for FHIR」之間有何差異？

Azure API for FHIR 是適用于 Azure 的開放原始碼 Microsoft FHIR Server 託管和受控版本。 在受控服務中，Microsoft 會提供所有維護和更新。 

當您執行適用于 Azure 的 FHIR 伺服器時，您可以直接存取基礎服務，但如果您要儲存 PHI 資料，則負責維護和補救伺服器以及所有必要的合規性工作。

針對開發的觀點，每項未套用至受管理服務的功能都會先部署到開放原始碼的 Microsoft FHIR Server for Azure。 在開放原始碼中驗證之後，它就會發行至 PaaS Azure API for FHIR 解決方案。 開放原始碼和 PaaS 的發行之間的時間，取決於功能的複雜度和其他藍圖優先權。 這是所有服務的相同程式，例如適用于 FHIR (preview) 的 Azure IoT Connector。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>我可以在哪裡看到 Azure API for FHIR 的發行內容？

若要查看 Azure API for FHIR 中發行的部分內容，請參閱開放原始碼 FHIR 伺服器的 [版本](https://github.com/microsoft/fhir-server/releases) 。 自2020年11月起，如果開放原始碼專案將發行至受管理的服務，我們已使用 Azure FHIR 標記專案。 在開放原始碼的發行頁面上，這些功能通常會在兩周內提供。 https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md)如果您想要在自己的環境中進行測試，我們也包含如何測試組建 [這裡] (的指示。 我們正在評估如何以最佳方式共用額外的受控服務更新。

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>哪些區域 Azure API for FHIR 可用？

目前，在 [多個地理區域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)中，公用和政府都已正式推出。 如需 Microsoft 政府雲端服務的相關資訊，請參閱 [FedRAMP 的 Azure 服務](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)。

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>我可以在哪裡看到 Azure API for FHIR 的發行內容？

若要查看 Azure API for FHIR 中發行的部分內容，請參閱開放原始碼 FHIR 伺服器的 [版本](https://github.com/microsoft/fhir-server/releases) 。 我們已將專案標記為 FHIR，如果它們將會發行至受控服務，且通常會在兩周內于開放原始碼的發行頁面上推出。 如果您想要在自己的環境中進行測試，我們也包含如何在 [此](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 測試組建的指示。 我們正在評估如何以最佳方式共用額外的受控服務更新。

### <a name="what-is-smart-on-fhir"></a>FHIR 有什麼智慧？

智慧型 (可將醫療應用程式和可重複使用的技術) 在 FHIR 上，是一組開放的規格，可將夥伴應用程式與 FHIR 伺服器和其他健康情況的 IT 系統整合，例如電子健康記錄和健康情況資訊交換。 藉由建立智慧型 on FHIR 應用程式，您可以確定您的應用程式可以透過眾多的不同系統來存取及利用。
驗證與 Azure API for FHIR。 若要深入瞭解 SMART，請造訪 [Smart Health](https://smarthealthit.org/)。

## <a name="fhir-implementations-and-specifications"></a>FHIR 的實施和規格

### <a name="can-i-create-a-custom-fhir-resource"></a>我可以建立自訂的 FHIR 資源嗎？

我們不允許自訂 FHIR 資源。 如果您需要自訂 FHIR 資源，您可以在具有擴充功能的 [基本資源](http://www.hl7.org/fhir/basic.html) 之上建立自訂資源。 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Azure API for FHIR 支援 [延伸](https://www.hl7.org/fhir/extensibility.html) 模組嗎？

我們可讓您將任何有效的 FHIR JSON 資料載入伺服器。 如果您想要儲存定義延伸模組的結構定義，可以將其儲存為結構定義資源。 您目前無法搜尋延伸模組。

### <a name="what-is-the-limit-on-_count"></a>_Count 的限制為何？

_Count 目前的限制為100。 如果您將 _count 設定為100以上，您將會在組合中收到警告，表示只會顯示100筆記錄。

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>群組匯出功能是否有任何限制？

針對群組匯出，我們只會匯出群組中包含的參考，而不是 [群組資源](https://www.hl7.org/fhir/group.html)的所有特性。

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>我可以將配套發佈到 Azure API for FHIR 嗎？

我們目前支援張貼 [批次](https://www.hl7.org/fhir/valueset-bundle-type.html) 套件組合，但不支援在 Azure API for FHIR 中張貼交易配套。 您可以使用 SQL 支援的開放原始碼 FHIR 伺服器來張貼交易配套。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>如何在 Azure API for FHIR 中取得單一患者的所有資源？

我們支援 Azure API for FHIR 中的區間 [搜尋](https://www.hl7.org/fhir/compartmentdefinition.html) 。 這可讓您取得與特定患者相關的所有資源。 請注意，現在的區間會包含與患者相關的所有資源，而不是患者本身，因此，如果您的結果中需要患者資源，您也必須搜尋以取得患者。

以下是一些範例：

* 取得患者/<id>/*
* 取得患者/ <id> /Observation
* 取得患者/ <id> /Observation？ code = 8302-2

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>搜尋 Azure API for FHIR 中的資源時，預設排序為何？

我們支援依上次更新日期排序： _sort = _lastUpdated。 如需其他支援之搜尋參數的詳細資訊，請參閱 [支援的功能頁面](./fhir-features-supported.md#search)。

### <a name="how-does-export-work"></a>$Export 如何運作？

$export 是 FHIR 規格的一部分： https://hl7.org/fhir/uv/bulkdata/export/index.html 。 如果已使用受控識別和儲存體帳戶設定 FHIR 服務，而且受控識別可以存取該儲存體帳戶，您只需要在 FHIR API 上呼叫 $export，所有 FHIR 資源都會匯出至儲存體帳戶。 如需詳細資訊，請參閱 [$export 的文章](./export-data.md)。

## <a name="using-azure-api-for-fhir"></a>使用 Azure API for FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>如何? 啟用 Azure API for FHIR 的 log analytics？

我們會啟用診斷記錄，並允許查看這些記錄的範例查詢。 如需啟用審核記錄和查詢範例的詳細資訊，請參閱 [這一節](./enable-diagnostic-logging.md)。 如果您想要在記錄中包含其他資訊，請參閱 [使用自訂 HTTP 標頭](./use-custom-headers.md)。

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>哪裡可以查看在工作流程中使用 Azure API for FHIR 的一些範例？

我們在 [健康情況架構 GitHub 頁面](https://github.com/microsoft/health-architectures)上有一組可用的參考架構。

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>我可以在哪裡看到將 web 應用程式連接到 Azure API for FHIR 的範例？

我們有一個包含範例應用程式和案例的 [健康情況架構 GitHub 頁面](https://github.com/microsoft/health-architectures) 。 它說明如何將 web 應用程式連線至 Azure API for FHIR。  

## <a name="azure-api-for-fhir-features-and-services"></a>Azure API for FHIR 的功能與服務 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>是否有方法可以使用我的個人金鑰來加密我的資料，而不是預設的金鑰？

是的，Azure API for FHIR 可讓您設定客戶管理的金鑰，並利用 Cosmos DB 的支援。 如需使用個人金鑰加密您的資料的詳細資訊，請參閱 [這一節](./customer-managed-key.md)。

## <a name="azure-api-for-fhir-preview-features"></a>Azure API for FHIR：預覽功能

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>是否可以為 Azure IoT Connector 設定 Azure (preview) 的調整容量？

因為 Azure IoT Connector for FHIR 在公開預覽期間免費，所以其調整容量是固定的，而且有限。 適用于 FHIR 的 Azure IoT Connector 設定可在公開預覽中取得，且每秒應提供大約200個訊息的輸送量。 某些形式的資源容量設定將提供正式運作 (GA) 。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>為什麼在 Azure API for FHIR 上啟用 Private Link 時，無法安裝 Azure IoT Connector for FHIR (preview) ？

Azure IoT Connector for FHIR 目前不支援 Private Link 功能。 因此，如果您已在 Azure API for FHIR 上啟用 Private Link，則無法安裝適用于 FHIR 的 Azure IoT Connector，反之亦然。 當適用于 FHIR 的 Azure IoT Connector 正式運作 (GA) 時，預期會有這項限制。