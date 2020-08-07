---
title: 有關 Azure 中 FHIR 服務的常見問題-Azure API for FHIR
description: 取得 Azure API for FHIR 常見問題的解答，例如 FHIR Api 背後資料的儲存位置，以及版本支援。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 252abcac6e9e39930593c1b110bf6d55ffdfc33f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843516"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>關於 Azure API for FHIR 的常見問題

## <a name="azure-api-for-fhir"></a>適用於 FHIR 的 Azure API

### <a name="what-is-fhir"></a>什麼是 FHIR？
快速健康照護互通資源 (FHIR 的「火災」 ) 是一個互通性標準，目的是要在不同的健全狀況系統之間交換醫療保健資料。 此標準是由 HL7 組織所開發，並由世界各地的醫療保健組織採用。 目前可用的 FHIR 版本為 R4 (第4版) 。 Azure API for FHIR 支援 R4，而且也支援 (Standard for 試用版的舊版 STU3，請使用 3) 。 如需 FHIR 的詳細資訊，請造訪[HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 背後的資料會儲存在 Azure 中嗎？

是，資料會儲存在 Azure 中的受控資料庫。 Azure API for FHIR 不會提供基礎資料存放區的直接存取權。

### <a name="what-identity-provider-do-you-support"></a>您支援哪些身分識別提供者？

我們目前支援 Microsoft Azure Active Directory 做為身分識別提供者。

### <a name="what-fhir-version-do-you-support"></a>您支援哪些 FHIR 版本？

我們支援 Azure API for FHIR (PaaS) 和適用于 Azure (開放原始碼) 的 FHIR 伺服器上的版本4.0.0 和3.0.1。

如需詳細資訊，請參閱[支援的功能](fhir-features-supported.md)。 閱讀[HL7 FHIR 版本歷程記錄](https://hl7.org/fhir/R4/history.html)版本之間變更的內容。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>適用于 Azure 的開放原始碼 Microsoft FHIR 伺服器與 Azure API for FHIR 之間有何差異？

Azure API for FHIR 是 Azure 開放原始碼 Microsoft FHIR Server 的託管和受控版本。 在受控服務中，Microsoft 提供所有維護和更新。 

當您執行適用于 Azure 的 FHIR 伺服器時，您可以直接存取基礎服務。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的相容性工作。

從開發的觀點來看，每項功能會先部署到適用于 Azure 的開放原始碼 Microsoft FHIR Server。 在開放原始碼中驗證後，它就會發行到 PaaS Azure API for FHIR 解決方案。 以開放原始碼和 PaaS 發行的時間，取決於功能和其他藍圖優先順序的複雜性。 

### <a name="what-is-smart-on-fhir"></a>什麼是 FHIR 的智慧型功能？

智慧型 (可將醫療應用程式和可重複使用的技術) FHIR 是一組開放的規格，將合作夥伴應用程式與 FHIR 伺服器和其他健全狀況 IT 系統（例如電子健康記錄和健康狀態資訊交換）整合在一起。 藉由建立智慧型 on FHIR 應用程式，您可以確保您的應用程式可由不同系統的眾多存取和利用。
驗證和 Azure API for FHIR。 若要深入瞭解智慧型，請造訪[智慧型健全狀況](https://smarthealthit.org/)。

## <a name="azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector

### <a name="what-is-iomt"></a>什麼是 IoMT？
IoMT 代表醫療物聯網，它是一種 IoT 裝置類別，可透過網路與其他醫療保健 IT 系統一起捕捉及交換健康情況和福利資料。 IoMT 裝置的一些範例包括健身與臨床穿戴式裝置、監視感應器、活動追蹤器、護理資訊亭，甚至是智慧藥丸。

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>我需要多少個適用于 FHIR (preview) 的 Azure IoT 連接器？
適用于 FHIR 的單一 Azure IoT 連接器 * 可以用來從大量不同類型的裝置內嵌資料。 基於下列原因，您仍可能決定使用不同的連接器：
- **規模**：對於公開預覽，適用于 FHIR 資源容量的 Azure IoT Connector 已固定，而且預期每秒可提供大約200個訊息的輸送量。 如果需要更高的輸送量，您可以新增更多適用于 FHIR 的 Azure IoT 連接器。
- **裝置類型**：您可以針對裝置管理的每一種類型的 IoMT 裝置，設定個別的 Azure IoT 連接器以進行 FHIR。

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>在公開預覽期間，適用于 FHIR (preview) 的 Azure IoT 連接器數目是否有限制？
是，當此功能處於公開預覽狀態時，您只能為每個訂用帳戶建立兩個 Azure IoT 連接器以進行 FHIR。 這項限制的存在是為了避免非預期的費用，因為此功能在預覽期間免費提供。 在要求上，此限制最多可能會產生5個適用于 FHIR 的 Azure IoT 連接器。

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>公開預覽期間有哪些 Azure 區域的 Azure IoT Connector for FHIR (預覽) 功能可供使用？
適用于 FHIR 的 azure IoT Connector 可在有 Azure API for FHIR 的所有 Azure 區域中使用。

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>我可以設定 Azure IoT Connector for FHIR (preview) 的調整容量嗎？
由於適用于 FHIR 的 Azure IoT Connector 在公開預覽期間是免費的，因此其調整容量是固定且有限的。 公開預覽中提供的 Azure IoT Connector for FHIR 設定，應提供大約每秒200個訊息的輸送量。 某些形式的資源容量設定將會在正式推出 (GA) 中提供。

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>適用于 FHIR 的 Azure IoT 連接器 (preview) 支援有哪些 FHIR 版本？
適用于 FHIR 的 Azure IoT 連接器目前僅支援 FHIR 版本 R4。 因此，這項功能只會在 Azure API for FHIR 的 R4 實例上顯示，而 Microsoft 目前不打算支援版本 STU3。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>為什麼在 Azure API for FHIR 上啟用私人連結時，我無法安裝適用于 FHIR (preview) 的 Azure IoT Connector？
適用于 FHIR 的 Azure IoT 連接器目前不支援私用連結功能。 因此，如果您在 Azure API for FHIR 上啟用私人連結，就無法安裝適用于 FHIR 的 Azure IoT 連接器，反之亦然。 當適用于 FHIR 的 Azure IoT 連接器可供正式運作 (GA) 時，這項限制應該會消失。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>適用于 Azure 的開放原始碼 IoMT FHIR 連接器與適用于 FHIR 的 Azure IoT Connector (預覽) Azure API for FHIR 功能之間有何差異？
適用于 FHIR 的 azure IoT 連接器是 Azure 開放原始碼 IoMT FHIR 連接器的託管和受控版本。 在受控服務中，Microsoft 提供所有維護和更新。

當您執行適用于 Azure 的 IoMT FHIR Connector 時，您可以直接存取基礎資源。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的相容性工作。

從開發的觀點來看，每項功能會先部署到適用于 Azure 的開放原始碼 IoMT FHIR 連接器。 在開放原始碼中驗證之後，它就會發行到 Azure API for FHIR 服務的 PaaS Azure IoT Connector for FHIR 功能。 以開放原始碼和 PaaS 發行的時間，取決於功能的複雜性和其他的道路對應優先順序。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀一些關於 Azure API for FHIR 的常見問題。 閱讀適用于 Azure 的 FHIR 伺服器中支援的功能：
 
>[!div class="nextstepaction"]
>[支援的 FHIR 功能](fhir-features-supported.md)

* 在 Azure 入口網站中，適用于 FHIR 的 Azure IoT 連接器稱為 IoT 連接器 (預覽) 。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。