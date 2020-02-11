---
title: Azure IoT 中樞裝置佈建服務概觀 | Microsoft Docs
description: 描述在 Azure 中使用裝置佈建服務 (DPS) 與 IoT 中樞佈建的裝置
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d097894c841d91d344b5958e7f5e1c10249f8b6e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904883"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中樞裝置佈建服務來佈建裝置
Microsoft Azure 提供一組豐富的整合式公用雲端服務，可滿足所有的 IoT 方案需求。 IoT 中樞裝置佈建服務 (DPS) 是 IoT 中樞的一項協助程式服務，可對正確的 IoT 中樞進行自動 Just-in-Time 佈建，完全無須人為介入。 DPS 可讓您以安全且可擴充的方式佈建數百萬個裝置。

## <a name="when-to-use-device-provisioning-service"></a>何時要使用裝置佈建服務
在許多佈建案例中，DPS 都是用來將裝置設定並連線至 IoT 中樞的絕佳選擇，例如：

* 無須人為介入佈建到單一 IoT 解決方案，無須在出廠時硬式編碼 IoT 中樞連線資訊 (初始安裝)
* 跨多個中樞的負載平衡裝置
* 以銷售交易資料作為基礎，將裝置連線到其擁有者的 IoT 解決方案 (多組織用戶管理)
* 根據使用案例，將裝置連線到特定的 IoT 解決方案 (解決方案隔離)
* 以最低的延遲將裝置連線到 IoT 中樞 (地區分區化)
* 以裝置中的變更作為基礎重新佈建
* 移動裝置所用的金鑰，以連線到 IoT 中樞 (不使用 X.509 憑證連線時)

## <a name="behind-the-scenes"></a>在幕後
上一節中所列的所有案例，都可以相同的流程使用自動佈建的 DPS 來完成。 傳統上包含於佈建的許多手動步驟，都會使用 DPS 加以自動化，從而減少部署 IoT 裝置的時間，並降低手動錯誤的風險。 下一節說明佈建裝置背後的運作原理。 第一個步驟為手動，所有的後續步驟則為自動的。

![基本佈建流程](./media/about-iot-dps/dps-provisioning-flow.png)

1. 裝置製造商會將裝置註冊資訊新增至 Azure 入口網站中的註冊清單。
2. 裝置會聯繫出廠時設定的 DPS 端點。 裝置會將識別資訊傳遞給 DPS 來證明其身分識別。
3. DPS 會驗證裝置的身分識別，方法是使用 nonce 挑戰 ([信賴平台模組](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) 或標準 X.509 驗證 (X.509)，來驗證註冊清單項目的註冊識別碼和金鑰。
4. DPS 會使用 IoT 中樞來註冊裝置，並填入裝置的[必要對應項狀態](../iot-hub/iot-hub-devguide-device-twins.md)。
5. IoT 中樞會將裝置識別碼資訊傳回給 DPS。
6. DPS 會將 IoT 中樞連線資訊傳回給裝置。 裝置現在可以開始直接將資料傳送到 IoT 中樞。
7. 裝置會連線到 IoT 中樞。
8. 裝置會在 IoT 中樞從其裝置對應項取得所需的狀態。

## <a name="provisioning-process"></a>佈建程序
裝置的部署程序中有兩個不同的步驟，DPS 會參與其中，並能夠獨立完成：

* 建立裝置及出廠時準備的**製造步驟**，和
* **雲端安裝步驟**，可在其中設定裝置佈建服務以進行自動佈建。

這兩個步驟能順暢地符合現有的製造和部署程序。 DPS 甚至可簡化某些部署程序，這些程序需要手動工作才能取得裝置的連線資訊。

### <a name="manufacturing-step"></a>製造步驟
這個步驟是關於製造線的事項。 此步驟中所涉及的角色包括矽設計工具、矽製造商、整合者及/或裝置的製造商。 這個步驟是關於建立本身的硬體。

DPS 不會在製造過程中導入新的步驟；相反地，它所繫結的現有步驟會在裝置上安裝初始軟體和 (在理想情況下) HSM。 裝置會使用佈建服務資訊來進行程式設計，而不是在此步驟中建立裝置識別碼；因此裝置會呼叫佈建服務，在它開啟時取得其連線資訊/IoT 解決方案指派。

在這個步驟中，製造商也會提供裝置部署器/運算子，當中包含識別金鑰資訊。 提供該資訊可能很簡單，就像確認所有裝置都具有由裝置部署者/操作員提供之簽署憑證所產生的 X.509 憑證；也可能很複雜，就像從每個 TPM 裝置擷取 TPM 簽署金鑰的公開部分。 這些服務目前由許多矽製造商所提供。

### <a name="cloud-setup-step"></a>雲端安裝步驟
這個步驟與設定適用於自動佈建的雲端相關。 通常雲端安裝步驟中會包含兩種類型的使用者：知道裝置必須初始設定方式的使用者 (裝置運算子)，以及其他知道裝置如何在 IoT 中樞 (方案運算子) 之間分割的使用者。

佈建必須進行一次性的初始安裝，這通常是由解決方案運算子處理。 一旦佈建服務設定之後，就不需要修改，除非使用案例有所變更。

服務設定為自動佈建之後，必須準備進行註冊裝置。 這個步驟是由裝置運算子完成，該裝置運算子知道裝置的必要設定，並在尋找其 IoT 中樞時，負責確定佈建服務可以適當證明裝置的身分識別。 裝置運算子會向製造商取得識別金鑰資訊，並將它新增至註冊清單。 隨著使用裝置的最新相關資訊來新增新的項目或更新現有的項目時，會對註冊清單進行後續更新。

## <a name="registration-and-provisioning"></a>註冊和佈建
佈建  表示根據在當中使用字詞的產業之各種項目。 在將 IoT 裝置佈建到其雲端解決方案的內容中，佈建是一個包含兩個部分的程序：

1. 第一個部分是在裝置與 IoT 解決方案之間註冊裝置來建立初始連線。
2. 第二個部分是以其註冊之解決方案的特定需求作為基礎套用正確的組態。

這兩個步驟都完成之後，該裝置才算佈建完成。 有些雲端服務只提供佈建程序的第一個步驟，也就是將裝置註冊至 IoT 解決方案端點，但並未提供初始組態。 DPS 會自動進行這兩個步驟，以提供順暢的裝置佈建體驗。

## <a name="features-of-the-device-provisioning-service"></a>裝置佈建服務的功能
DPS 有許多功能，因此適合用來佈建裝置。

* **安全證明**支援 X.509 與 TPM 型身分識別。
* **註冊清單**包含可能在某處註冊的完整裝置記錄/裝置群組。 一旦註冊後，註冊清單就會包含裝置所需設定的相關資訊，並可以在任何時間進行更新。
* **多個配置原則**可控制 DPS 將裝置指派給 IoT 中樞的方式，從而支援您的案例：透過註冊清單控制最低延遲、權重相等的分佈 (預設值) 和靜態設定。 延遲會使用與[流量管理員](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance)相同的方法來決定。
* **監視和診斷記錄**並確定一切運作正常。
* **多重中樞支援**可讓 DPS 在執行階段將裝置延後繫結至 IoT 中樞。 DPS 可跨多個 Azure 訂用帳戶與中樞通訊。
* **跨區域支援**可讓 DPS 將裝置指派給其他區域的 IoT 中樞。

您可以在[裝置概念](concepts-device.md)、[服務概念](concepts-service.md)和[安全性概念](concepts-security.md)中，深入了解裝置佈建中包含的概念和功能。

## <a name="cross-platform-support"></a>跨平台支援
DPS 和所有 Azure IoT 服務一樣，都能跨平台用於各種作業系統。 Azure 提供多種[程式設計語言](https://github.com/Azure/azure-iot-sdks)的開放原始碼 SDK，以協助建立裝置的連線及管理服務。 DPS 支援使用下列通訊協定建立裝置的連線：

* HTTPS
* AMQP
* 透過 Web 通訊端的 AMQP
* MQTT
* 透過 Web 通訊端的 MQTT

DPS 僅支援服務作業的 HTTPS 連線。

## <a name="regions"></a>區域
DPS 可在許多區域中使用。 所有服務的現有和最新宣佈區域的更新清單位於 [Azure 區域](https://azure.microsoft.com/regions/)。 您可以在 [Azure 狀態](https://azure.microsoft.com/status/)頁面上檢查裝置佈建服務的可用性。

> [!NOTE]
> DPS 為全域服務，並非僅限於某個位置。 不過，您必須指定一個區域，讓與您的 DPS 設定檔相關聯的中繼資料位在該區域。

## <a name="availability"></a>可用性
DPS 有 99.9% 的服務等級協定，且您可以[閱讀 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/) 說明保證的 Azure 整體可用性。

## <a name="quotas"></a>配額
每個 Azure 訂用帳戶都有預設配額限制，而此限制會對 IoT 解決方案的範圍造成影響。 目前每個訂用帳戶的限制為每個訂用帳戶 10 個裝置佈建服務。

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

如需有關配額限制的詳細資料：
* [Azure 訂用帳戶服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>相關的 Azure 元件
DPS 可使用 Azure IoT 中樞將裝置佈建自動化。 深入了解 [IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)。

## <a name="next-steps"></a>後續步驟
現在您已了解 Azure 中 IoT 裝置的概觀。 下一個步驟是嘗試端對端 IoT 情節。
> [!div class="nextstepaction"]
> [使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](quick-setup-auto-provision.md)
> [建立及佈建模擬裝置](quick-create-simulated-device.md)
> [設定佈建的裝置](tutorial-set-up-device.md)
