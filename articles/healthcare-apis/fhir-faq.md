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
ms.openlocfilehash: 9c32ebef16750954f3df1a1d1b379bf42853f2b3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056850"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Azure API for FHIR 的常見問題

## <a name="azure-api-for-fhir"></a>適用於 FHIR 的 Azure API

### <a name="what-is-fhir"></a>什麼是 FHIR？
快速健康照護互通資源 (FHIR 發音「火災」 ) 是一種互通性標準，目的是要在不同的健康情況系統之間交換醫療保健資料。 這項標準是由 HL7 組織所開發，並且是由世界各地的醫療保健組織所採用。 可用的最新 FHIR 版本為 R4 (第4版) 。 Azure API for FHIR 支援 R4，也支援舊版 STU3 (Standard for 試用版使用 3) 。 如需 FHIR 的詳細資訊，請造訪 [HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 背後的資料會儲存在 Azure 中嗎？

是，資料會儲存在 Azure 中的受控資料庫。 Azure API for FHIR 不會提供基礎資料存放區的直接存取權。

### <a name="what-identity-provider-do-you-support"></a>您支援哪些身分識別提供者？

我們目前支援 Microsoft Azure Active Directory 作為身分識別提供者。

### <a name="what-fhir-version-do-you-support"></a>您支援的 FHIR 版本為何？

我們在 Azure API for FHIR (PaaS) 和適用于 Azure 的 FHIR 伺服器上支援版本4.0.0 和 3.0.1 (開放原始碼) 。

如需詳細資訊，請參閱 [支援的功能](fhir-features-supported.md)。 閱讀 [HL7 FHIR 版本歷程記錄](https://hl7.org/fhir/R4/history.html)中版本之間的變更內容。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>開放原始碼的 Microsoft FHIR Server for Azure 和 Azure API for FHIR 有何不同？

Azure API for FHIR 是適用于 Azure 的開放原始碼 Microsoft FHIR Server 託管和受控版本。 在受控服務中，Microsoft 會提供所有維護和更新。 

當您執行適用于 Azure 的 FHIR Server 時，您可以直接存取基礎服務。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的合規性工作。

從開發的觀點來看，每項功能都是先部署到開放原始碼的 Microsoft FHIR Server for Azure。 在開放原始碼中驗證之後，它就會發行至 PaaS Azure API for FHIR 解決方案。 開放原始碼和 PaaS 的發行之間的時間，取決於功能的複雜度和其他藍圖優先權。 

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>我可以在哪裡看到 Azure API for FHIR 的發行內容？

若要查看 Azure API for FHIR 中發行的部分內容，請參閱開放原始碼 FHIR 伺服器的 [版本](https://github.com/microsoft/fhir-server/releases) 。 我們已將專案標記為 FHIR，如果它們將會發行至受控服務，且通常會在兩周內于開放原始碼的發行頁面上推出。 如果您想要在自己的環境中進行測試，我們也包含如何在 [此](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) 測試組建的指示。 我們正在評估如何以最佳方式共用額外的受控服務更新。

### <a name="what-is-smart-on-fhir"></a>FHIR 有什麼智慧？

智慧型 (可將醫療應用程式和可重複使用的技術) 在 FHIR 上，是一組開放的規格，可將夥伴應用程式與 FHIR 伺服器和其他健康情況的 IT 系統整合，例如電子健康記錄和健康情況資訊交換。 藉由建立智慧型 on FHIR 應用程式，您可以確定您的應用程式可以透過眾多的不同系統來存取及利用。
驗證與 Azure API for FHIR。 若要深入瞭解 SMART，請造訪 [Smart Health](https://smarthealthit.org/)。

### <a name="can-i-create-a-custom-fhir-resource"></a>我可以建立自訂的 FHIR 資源嗎？

我們不允許自訂 FHIR 資源。 如果您需要自訂 FHIR 資源，您可以在具有擴充功能的 [基本資源](http://www.hl7.org/fhir/basic.html) 之上建立自訂資源。 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Azure API for FHIR 支援 [延伸](https://www.hl7.org/fhir/extensibility.html) 模組嗎？

我們可讓您將任何有效的 FHIR JSON 資料載入伺服器。 如果您想要儲存定義延伸模組的結構定義，可以將其儲存為結構定義資源。 您目前無法搜尋延伸模組。

### <a name="what-is-the-limit-on-_count"></a>_Count 的限制為何？

目前的計數限制為100。

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

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>哪裡可以查看在工作流程中使用 Azure API for FHIR 的一些範例？

我們在 [健康情況架構 GitHub 頁面](https://github.com/microsoft/health-architectures)上有一組可用的參考架構。

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (預覽)

### <a name="what-is-iomt"></a>什麼是 IoMT？
IoMT 代表的是醫療物聯網，它是一種 IoT 裝置類別，可透過網路與其他醫療保健 IT 系統一起抓住和交換健康情況和福利資料。 IoMT 裝置的一些範例包括健身與臨床穿戴式裝置、監視感應器、活動追蹤器、護理資訊亭，甚至是智慧藥丸。

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>我需要多少個適用于 FHIR (preview) 的 Azure IoT Connector？
適用于 FHIR 的單一 Azure IoT Connector * 可以用來從大量不同類型的裝置內嵌資料。 基於下列原因，您可能仍決定使用不同的連接器：
- **調整**：針對公開預覽版，FHIR 資源容量的 Azure IoT Connector 是固定的，而且預期會提供大約每秒200個訊息的輸送量。 如果需要較高的輸送量，您可以新增更多 Azure IoT Connector 的 FHIR。
- **裝置類型**：您可以針對您基於裝置管理考慮的每一種 IoMT 裝置，設定個別的 Azure IoT Connector 以進行 FHIR。

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>在公開預覽期間，FHIR (preview) 的 Azure IoT Connector 數目是否有限制？
是的，您可以在此功能處於公開預覽狀態時，每個訂用帳戶只能建立兩個 Azure IoT Connector for FHIR。 這項限制是為了避免非預期的費用，因為功能在預覽期間免費提供。 在要求中，此限制最多可能會產生五個 FHIR 的 Azure IoT Connector。

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>在公開預覽期間，有哪些 Azure 區域適用于 FHIR 的 azure IoT Connector (preview) 功能？
適用于 FHIR 的 azure IoT Connector 適用于 Azure API for FHIR 可供使用的所有 Azure 區域。

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>是否可以為 Azure IoT Connector 設定 Azure (preview) 的調整容量？
因為 Azure IoT Connector for FHIR 在公開預覽期間免費，所以其調整容量是固定的，而且有限。 適用于 FHIR 的 Azure IoT Connector 設定可在公開預覽中取得，且每秒應提供大約200個訊息的輸送量。 某些形式的資源容量設定將提供正式運作 (GA) 。

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Azure IoT Connector FHIR (preview) 支援的 FHIR 版本為何？
適用于 FHIR 的 Azure IoT Connector 目前僅支援 FHIR 版本 R4。 因此，只有 Azure API for FHIR 的 R4 實例上才會顯示這項功能，而且 Microsoft 不打算支援 STU3 版本。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>為什麼在 Azure API for FHIR 上啟用 Private Link 時，無法安裝 Azure IoT Connector for FHIR (preview) ？
Azure IoT Connector for FHIR 目前不支援 Private Link 功能。 因此，如果您已在 Azure API for FHIR 上啟用 Private Link，則無法安裝適用于 FHIR 的 Azure IoT Connector，反之亦然。 當適用于 FHIR 的 Azure IoT Connector 正式運作 (GA) 時，預期會有這項限制。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>適用于 Azure 的開放原始碼 IoMT FHIR Connector 與 Azure IoT Connector Azure API for FHIR 服務的 FHIR (preview) 功能有何差異？
適用于 FHIR 的 azure IoT Connector 是適用于 Azure 的開放原始碼 IoMT FHIR Connector 託管和受控版本。 在受控服務中，Microsoft 會提供所有維護和更新。

當您執行適用于 Azure 的 IoMT FHIR Connector 時，您可以直接存取基礎資源。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的合規性工作。

從開發的觀點來看，每項功能都是先部署到開放原始碼 IoMT FHIR Connector for Azure。 在開放原始碼中驗證之後，它就會發行至 Azure API for FHIR 服務的 PaaS Azure IoT Connector for FHIR 功能。 開放原始碼和 PaaS 中的發行之間的時間，取決於功能的複雜度和其他的藍圖優先順序。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀 Azure API for FHIR 的一些常見問題。 閱讀 FHIR Server for Azure 中支援的功能：
 
>[!div class="nextstepaction"]
>[支援的 FHIR 功能](fhir-features-supported.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。