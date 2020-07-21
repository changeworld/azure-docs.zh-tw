---
title: 有關 Azure 中 FHIR 服務的常見問題-Azure API for FHIR
description: 取得 Azure API for FHIR 常見問題的解答，例如 FHIR Api 背後資料的儲存位置，以及版本支援。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536720"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>關於 Azure API for FHIR 的常見問題

## <a name="azure-api-for-fhir"></a>適用於 FHIR 的 Azure API

### <a name="what-is-fhir"></a>什麼是 FHIR？
快速健康照護互通資源（FHIR）是一項互通性標準，目的是要在不同的健全狀況系統之間交換醫療保健資料。 此標準是由 HL7 組織所開發，並由世界各地的醫療保健組織採用。 可用的最新 FHIR 版本為 R4 （第4版）。 Azure API for FHIR 支援 R4，而且也支援先前的版本 STU3 （試用版的標準3）。 如需 FHIR 的詳細資訊，請造訪[HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 背後的資料會儲存在 Azure 中嗎？

是，資料會儲存在 Azure 中的受控資料庫。 Azure API for FHIR 不會提供基礎資料存放區的直接存取權。

### <a name="what-identity-provider-do-you-support"></a>您支援哪些身分識別提供者？

我們目前支援 Microsoft Azure Active Directory 做為身分識別提供者。

### <a name="what-fhir-version-do-you-support"></a>您支援哪些 FHIR 版本？

在適用于 Azure 的 Azure API for FHIR （PaaS）和 FHIR 伺服器（開放原始碼）上，我們支援版本4.0.0 和3.0.1。

如需詳細資訊，請參閱[支援的功能](fhir-features-supported.md)。 閱讀[HL7 FHIR 版本歷程記錄](https://hl7.org/fhir/R4/history.html)版本之間變更的內容。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>適用于 Azure 的開放原始碼 Microsoft FHIR 伺服器與 Azure API for FHIR 之間有何差異？

Azure API for FHIR 是 Azure 開放原始碼 Microsoft FHIR Server 的託管和受控版本。 在受控服務中，Microsoft 提供所有維護和更新。 

當您執行適用于 Azure 的 FHIR 伺服器時，您可以直接存取基礎服務。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的相容性工作。

從開發的觀點來看，每項功能會先部署到適用于 Azure 的開放原始碼 Microsoft FHIR Server。 在開放原始碼中驗證後，它就會發行到 PaaS Azure API for FHIR 解決方案。 以開放原始碼和 PaaS 發行的時間，取決於功能和其他藍圖優先順序的複雜性。 

### <a name="what-is-smart-on-fhir"></a>什麼是 FHIR 的智慧型功能？

FHIR 上的智慧型（可替換醫學應用程式和可重複使用的技術）是一組開放的規格，可將合作夥伴應用程式與 FHIR 伺服器和其他健全狀況 IT 系統（例如電子健康記錄和健康狀態資訊交換）整合。 藉由建立智慧型 on FHIR 應用程式，您可以確保您的應用程式可由不同系統的眾多存取和利用。
驗證和 Azure API for FHIR。 若要深入瞭解智慧型，請造訪[智慧型健全狀況](https://smarthealthit.org/)。


## <a name="iot-connector-preview"></a>IoT 連接器（預覽）

### <a name="what-is-iomt"></a>什麼是 IoMT？
IoMT 代表醫療物聯網，它是一種 IoT 裝置類別，可透過網路與其他醫療保健 IT 系統一起捕捉及交換健康情況和福利資料。 IoMT 裝置的一些範例包括健身和臨床穿戴式裝置、監控感應器、活動追蹤器、護理 kiosk，甚至是智慧型膠囊按鈕。

### <a name="how-many-iot-connectors-do-i-need"></a>我需要多少個 IoT 連接器？
您可以使用單一 IoT 連接器，從大量不同類型的裝置內嵌資料。 基於下列原因，您仍可能決定使用不同的連接器：
- **規模**：對於公開預覽，IoT 連接器資源容量是固定的，而且預期會提供大約每秒200個訊息的輸送量。 如果需要更高的輸送量，您可以新增更多 IoT 連接器。
- **裝置類型**：您可以針對裝置管理的各種類型的 IoMT 裝置，設定個別的 IoT 連接器。

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>公開預覽期間，IoT 連接器的數目是否有限制？
是，當此功能處於公開預覽狀態時，您只能為每個訂用帳戶建立兩個 IoT 連接器。 這項限制的存在是為了避免非預期的費用，因為此功能在預覽期間免費提供。 在要求上，此限制可能會產生最多五個 IoT 連接器。

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>公開預覽期間有哪些 Azure 區域 IoT 連接器功能可供使用？
IoT 連接器適用于可使用 Azure API for FHIR 的所有 Azure 區域。

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>我可以設定 IoT 連接器的調整容量嗎？
由於 IoT 連接器在公開預覽期間是免費的，因此其調整容量已固定且有限。 公開預覽中提供的 IoT 連接器設定，應提供每秒大約200個訊息的輸送量。 某些形式的資源容量設定將在公開上市（GA）中提供。

### <a name="what-fhir-version-does-iot-connector-support"></a>IoT 連接器支援哪些 FHIR 版本？
IoT 連接器目前僅支援 FHIR 版本 R4。 因此，這項功能只會在 Azure API for FHIR 的 R4 實例上顯示，而 Microsoft 目前不打算支援版本 STU3。

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>為什麼在 Azure API for FHIR 上啟用私人連結時，我無法安裝 IoT 連接器？
IoT 連接器目前不支援私用連結功能。 因此，如果您在 Azure API for FHIR 上啟用私人連結，就無法安裝 IoT 連接器，反之亦然。 當 IoT 連接器適用于正式運作（GA）時，這項限制預期會消失。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Azure API for FHIR 服務的開放原始碼 IoMT FHIR Connector for Azure 和 IoT Connector 功能有何不同？
IoT 連接器是適用于 Azure 的開放原始碼 IoMT FHIR 連接器託管和受控版本。 在受控服務中，Microsoft 提供所有維護和更新。

當您執行適用于 Azure 的 IoMT FHIR Connector 時，您可以直接存取基礎資源。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的相容性工作。

從開發的觀點來看，每項功能會先部署到適用于 Azure 的開放原始碼 IoMT FHIR 連接器。 在開放原始碼中驗證後，它就會發行到 Azure API for FHIR 服務的 PaaS IoT 連接器功能。 以開放原始碼和 PaaS 發行的時間，取決於功能的複雜性和其他的道路對應優先順序。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀一些關於 Azure API for FHIR 的常見問題。 閱讀適用于 Azure 的 FHIR 伺服器中支援的功能：
 
>[!div class="nextstepaction"]
>[支援的 FHIR 功能](fhir-features-supported.md)