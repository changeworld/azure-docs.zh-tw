---
title: IoT 中樞裝置布建服務-角色和作業
description: 本文提供使用 IoT 裝置布建服務 (DPS) 開發和 IoT 解決方案時所涉及的角色和作業概念。
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 52c0dcf4bd9f7b2ca8928eab5272a6500cbb379d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533049"
---
# <a name="roles-and-operations"></a>角色和作業

開發 IoT 解決方案的階段可能會跨越數周或數個月，這是因為生產環境的實際情況，例如製造時間、運送、海關處理等等。此外，他們可以跨多個角色跨越相關的各項實體。 本主題深入探討每個階段相關的各種角色和作業，然後在順序圖表中說明流程。 

布建也會在裝置製造商上放置需求，特別是啟用 [證明機制](concepts-service.md#attestation-mechanism)。 製造作業的進行也可能不受自動佈建階段的時機影響，特別是在已經建立自動佈建之後才購得新裝置的情況下。

左邊目錄中提供了一系列快速入門，可透過實際操作體驗來協助說明自動佈建。 為了協助/簡化學習程序，會使用軟體來模擬實體裝置以進行註冊和登錄。 由於快速入門的模擬本質緣故，有些快速入門會要求您執行多個角色的作業，包括不存在之角色的作業。

| 角色 | 作業 | 描述 |
|------| --------- | ------------|
| 製造商 | 將身分識別和登錄 URL 編碼 | 根據所使用的證明機制，製造商需負責將裝置身分識別資訊及「裝置佈建服務」登錄 URL 編碼。<br><br>**快速入門**：由於裝置是模擬裝置，因此沒有「製造商」角色。 如需有關如何取得此資訊的詳細資料，請參閱「開發人員」角色，撰寫範例登錄應用程式的程式碼時會使用此角色。 |
| | 提供裝置身分識別 | 作為裝置身分識別資訊的來源，製造商需負責向操作員 (或指定的代理人) 傳達此資訊，或透過 API 將直接向「裝置佈建服務」註冊此資訊。<br><br>**快速入門**：由於裝置是模擬裝置，因此沒有「製造商」角色。 如需有關如何取得裝置身分識別的詳細資料，請參閱「操作員」角色，在於「裝置佈建服務」執行個體中註冊模擬裝置時會使用此角色。 |
| 運算子 | 設定自動佈建 | 此作業與自動佈建的第一個階段對應。<br><br>**快速入門**：您需執行「操作員」角色，在您的 Azure 訂用帳戶中設定「裝置佈建服務」和「IoT 中樞」執行個體。 |
|  | 註冊裝置身分識別 | 此作業與自動佈建的第二個階段對應。<br><br>**快速入門**：您需執行「操作員」角色，在您的「裝置佈建服務」執行個體中註冊模擬裝置。 裝置身分識別取決於快速入門中所模擬的證明方法 (TPM 或 X.509)。 如需了解證明詳細資料，請參閱「開發人員」角色。 |
| 裝置佈建服務、<br>IoT 中樞 | \<all operations\> | 不論是使用實體裝置進行的生產環境實作，還是使用模擬裝置進行的快速入門，都是透過您在 Azure 訂用帳戶中所設定的 IoT 服務來執行這些角色。 這些角色/作業完全相同，因為 IoT 服務並不在乎佈建的是實體裝置還是模擬裝置。 |
| 開發人員 | 建置/部署登錄軟體 | 此作業與自動佈建的第三個階段對應。 「開發人員」需負責使用適當的 SDK 來建置登錄軟體並部署至裝置。<br><br>**快速入門**：您所建置的範例登錄應用程式會針對您選擇的平台/語言來模擬真實裝置，此應用程式會在您的工作站上執行 (而不會部署至實體裝置)。 此登錄應用程式所執行的作業，會與部署至實體裝置之應用程式所執行的作業相同。 您需指定證明方法 (TPM 或 X.509 憑證)，再加上「裝置佈建服務」執行個體的登錄 URL 及「識別碼範圍」。 裝置身分識別取決於執行階段的 SDK 證明邏輯，根據的是您所指定的方法： <ul><li>**TPM 證明** - 您的開發工作站會執行 [TPM 模擬器應用程式](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator)。 一旦執行，就會使用個別的應用程式來擷取 TPM 的「簽署金鑰」和「註冊識別碼」，以用於註冊和裝置識別碼。 SDK 證明邏輯也會在登錄期間使用模擬器，以針對驗證和註冊驗證出示已簽署的 SAS 權杖。</li><li>**X509 證明** - 您需使用工具來[產生憑證](how-to-use-sdk-tools.md#x509-certificate-generator)。 產生憑證之後，您需建立要在註冊作業中使用的必要憑證檔。 SDK 證明邏輯也會在登錄期間使用憑證，以針對驗證和註冊驗證出示憑證。</li></ul> |
| 裝置 | 開機和註冊 | 此作業與自動佈建的第三個階段對應，會由「開發人員」所建置的裝置登錄軟體來執行。 如需詳細資料，請參閱「開發人員」角色。 第一次開機時： <ol><li>應用程式會依據在開發期間所指定的全域 URL 和服務「識別碼範圍」，與「裝置佈建服務」執行個體連線。</li><li>連線之後，會依據在註冊期間所指定的證明方法和身分識別來驗證裝置。</li><li>驗證之後，就會向佈建服務執行個體所指定的「IoT 中樞」執行個體註冊裝置。</li><li>登錄成功時，會將唯一裝置識別碼和「IoT 中樞」端點傳回給登錄應用程式，以與「IoT 中樞」進行通訊。</li><li> 裝置可以從該處下拉其初始[裝置對應項](~/articles/iot-hub/iot-hub-devguide-device-twins.md)狀態來進行設定，並開始回報遙測資料的程序。</li></ol>**快速入門**：由於裝置是模擬裝置，因此登錄軟體會在您的開發工作站上執行。|

下圖摘要說明裝置自動佈建期間的角色和作業順序：
<br><br>
[![裝置的自動布建順序](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> 製造商也可以視需要使用「裝置佈建服務 API」(而不透過「操作員」) 來執行「註冊裝置身分識別」作業。 如需此排序及更多的詳細討論，請觀看[向 Azure IoT 進行全自動裝置登錄](https://youtu.be/cSbDRNg72cU?t=2460)影片 (從 41:00 標記開始)

## <a name="roles-and-azure-accounts"></a>角色和 Azure 帳戶

每個角色對應至 Azure 帳戶的方式與案例相關，而且可能涉及相當多案例。 以下常見模式應有助於大致了解角色通常如何對應到 Azure 帳戶。

#### <a name="chip-manufacturer-provides-security-services"></a>晶片製造商提供安全性服務

在此案例中，製造商會管理第一級客戶的安全性。 這些第一級客戶可能偏好此個案例，因為他們不必管理詳細的安全性。 

製造商在硬體安全性模組 (HSM) 中引入安全性。 此安全性可包括製造商從已設定 DPS 執行個體和註冊群組的潛在客戶取得的金鑰、憑證等。 製造商也可為其客戶產生安全性資訊。

在此案例中，可能有涉及兩個 Azure 帳戶：

- **帳戶 #1**：可能會在不同的操作員和開發人員角色間共用。 此合作對象可以向製造商購買 HSM 晶片。 這些晶片會指向與帳戶 #1 相關聯的 DPS 執行個體。 DPS 註冊，此合作對象可藉由在 DPS 中重新設定裝置註冊設定，將裝置租給第 2 級客戶。 此合作物件也可能已為終端使用者後端系統組態 IoT 中樞，以便與互動以存取裝置遙測等等。在後者的情況下，可能不需要第二個帳戶。

- **帳戶 #2**：終端使用者，層級2客戶可能會有自己的 IoT 中樞。 與帳戶 #1 相關聯的合作對象只是將租用裝置指向此帳戶中的正確中樞。 此組態需要跨 Azure 帳戶連結 DPS 和 IoT 中樞，這可透過 Azure Resource Manager 範本完成。

#### <a name="all-in-one-oem"></a>全方位 OEM

製造商可以是「全方位 OEM」，其只需要單一製造商帳戶。 製造商會處理端對端安全性和佈建。

製造商可以將雲端式應用程式提供給購買裝置的客戶。 此應用程式會與製造商所配置的 IoT 中樞聯繫。

販賣機或全自動咖啡機代表此案例的範例。




## <a name="next-steps"></a>後續步驟

隨著您逐步進行對應的自動佈建快速入門，您可能會發現將本文加入書籤以作為參考點相當有幫助。 

請從最適合您管理工具喜好設定的「設定自動佈建」快速入門開始著手，這會引導您完成「服務設定」階段：

- [使用 Azure CLI 來設定自動佈建](quick-setup-auto-provision-cli.md)
- [使用 Azure 入口網站設定自動布建](quick-setup-auto-provision.md)
- [使用 Resource Manager 範本來設定自動佈建](quick-setup-auto-provision-rm.md)

然後繼續進行「布建裝置」快速入門，以符合您的裝置證明機制和裝置布建服務 SDK/語言喜好設定。 在此快速入門中，您將逐步完成「裝置註冊」和「裝置登錄與設定」階段： 

| 裝置證明機制 | 快速入門 SDK/語言 | 
| ------------------------------- | -------------------- |
| 對稱金鑰 | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| X.509 憑證 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| 模擬的信賴平臺模組 (TPM)  | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




