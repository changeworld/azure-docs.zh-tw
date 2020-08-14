---
title: 裝置安全性的憑證 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 會使用憑證來驗證裝置、模組和分葉裝置，並建立它們之間的安全連線。
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 9d7caf332239d364b5bc47b5d58a808ead70395d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210588"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>瞭解 Azure IoT Edge 如何使用憑證

模組和下游 IoT 裝置會使用 IoT Edge 憑證來驗證 [IoT Edge 中樞](iot-edge-runtime.md#iot-edge-hub) 執行時間模組的身分識別和合法性。 這類驗證作業可在執行階段、模組和 IoT 裝置之間建立 TLS (傳輸層安全性) 安全連線。 和 IoT 中樞本身一樣，IoT Edge 也需要 IoT 下游 (或分葉) 裝置和 IoT Edge 模組的安全加密連線。 為了建立安全的 TLS 連線，IoT Edge 中樞模組會提供連線用戶端的伺服器信任鏈結，以便用戶端驗證其身分識別。

>[!NOTE]
>本文討論用來保護 IoT Edge 裝置上不同元件之間或 IoT Edge 裝置與任何分葉裝置之間連線的憑證。 您也可以使用憑證來驗證您的 IoT Edge 裝置，以 IoT 中樞。 這些驗證憑證不同，而且不會在本文中討論。 如需使用憑證驗證裝置的詳細資訊，請參閱 [使用 x.509 憑證建立和布建 IoT Edge 裝置](how-to-auto-provision-x509-certs.md)。

這篇文章說明 IoT Edge 憑證如何在生產、開發和測試環境中運作。 雖然指令碼不同 (Powershell 和 bash)，但在 Linux 和 Windows 上的概念皆相同。

## <a name="iot-edge-certificates"></a>IoT Edge 憑證

在 IoT Edge 裝置上設定憑證有兩個常見的案例。 有時候，裝置的終端使用者或操作員會購買由製造商製造的一般裝置，然後管理憑證本身。 有時候，製造商會在合約下運作，以建立操作員的自訂裝置，並在處理裝置之前執行一些初始憑證簽署。 IoT Edge 憑證在設計上嘗試將這兩種案例均納入考量。

下圖說明了 IoT Edge 使用憑證的情況。 端視涉及的實體數而定，根 CA 憑證與裝置 CA 憑證之間可能會有一個以上的中繼簽署憑證，也可能完全沒有。 這裡我們示範一種情況。

![一般憑證關聯性圖表](./media/iot-edge-certs/edgeCerts-general.png)

> [!NOTE]
> 目前，libiothsm 的限制可防止使用2050年1月1日之後到期的憑證。 這項限制適用于裝置 CA 憑證、信任配套中的任何憑證，以及用於 x.509 布建方法的裝置識別碼憑證。

### <a name="certificate-authority"></a>憑證授權單位

憑證授權單位，簡稱 CA，是核發數位憑證的實體。 憑證授權單位單位可作為憑證擁有者與接收者之間受信任的協力廠商。 數位憑證會確認憑證接收者對公開金鑰的所有權。 憑證信任鏈結一開始會先核發根憑證，這是憑證授權單位核發的所有憑證獲得信任的基礎。 此後，擁有者即可使用根憑證核發其他中繼憑證 (「分葉」憑證)。

### <a name="root-ca-certificate"></a>根 CA 憑證

根 CA 憑證是整個程序的信任根憑證。 在生產環境中，通常是向信任的商業憑證授權單位 (例如 Baltimore、Verisign、DigiCert) 購買這個 CA 憑證。 至於連線到您 IoT Edge 裝置的裝置，如果您有完整控制權，就可以使用公司層級的憑證授權單位。 在任一事件中，來自 IoT Edge 中樞的整個憑證鏈都會擲回它，因此分葉 IoT 裝置必須信任根憑證。 您可以將根 CA 憑證儲存在信任的根憑證授權單位存放區中，也可以在您的程式碼中提供憑證詳細資料。

### <a name="intermediate-certificates"></a>中繼憑證

在建立安全裝置的一般製造程序中，很少直接使用根 CA 憑證，主要是因為有外洩或暴露的風險。 根 CA 憑證會建立一個或多個中繼 CA 憑證，並以數位方式簽署該憑證。 可能只有一個，或可能有這些中繼憑證的連鎖。 需要中繼憑證鏈結的案例包括：

* 製造商的部門階層。

* 參與同款裝置生產流程的多家公司。

* 購買根 CA 並以此獲得簽署憑證的客戶，以提供給製造商簽署代客戶製造的裝置。

在任何情況下，製造商都會使用這個鏈結尾端的中繼 CA 憑證簽署終端裝置上的裝置 CA 憑證。 一般而言，這些中繼憑證會受到製造工廠嚴密保護。 這些憑證在實體和電子的使用上均會經過嚴格的流程。

### <a name="device-ca-certificate"></a>裝置 CA 憑證

裝置 CA 憑證是流程內最終的中繼 CA 憑證所產生和簽署的憑證。 此憑證會安裝在 IoT Edge 裝置上，最好是放置在安全儲存體中，例如硬體安全性模組 (HSM)。 此外，裝置 CA 憑證可唯一識別出 IoT Edge 裝置。 裝置 CA 憑證可以簽署其他憑證。

### <a name="iot-edge-workload-ca"></a>IoT Edge 工作負載 CA

[IoT Edge 安全性管理員](iot-edge-security-manager.md) 會產生工作負載 CA 憑證，這是 IoT Edge 第一次啟動時在流程的「操作員」端出現的第一個憑證。 此憑證是由裝置 CA 憑證所產生和簽署。 此憑證又是另一個中繼簽署憑證，用來產生和簽署 IoT Edge 執行階段所使用的其他所有憑證。 目前這主要是會在下節討論到的 IoT Edge 中樞伺服器憑證，但未來可能會加入用來驗證 IoT Edge 元件的其他憑證。

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge 中樞伺服器憑證

IoT Edge 中樞伺服器憑證是提供給分葉裝置和模組的實際憑證，用於在建立 IoT Edge 所需的 TLS 連線期間進行身分識別驗證。 此憑證會提供簽署憑證的完整鏈結，用來向分葉 IoT 裝置必須信任的根 CA 憑證產生簽署憑證。 由 IoT Edge 安全性管理員產生此 IoT Edge 中樞憑證時，憑證的一般名稱 (CN) 會設定為 config.yaml 檔案中的「hostname」屬性，並轉換成小寫。 此設定是與 IoT Edge 混淆的常見來源。

## <a name="production-implications"></a>生產環境影響

有個問題可能很有道理：「IoT Edge 為何需要『工作負載 CA』額外憑證？ 無法使用裝置 CA 憑證來直接產生 IoT Edge 中樞伺服器憑證嗎？」 這在技術上可行。 不過，此「工作負載」的中繼憑證是用來區隔裝置製造商和裝置營運商彼此的考量。 假設一位客戶將 IoT Edge 裝置售予或轉讓予另一位客戶。 就可能會希望製造商提供的裝置 CA 憑證維持不變。 不過，應該抹除裝置作業特定的「工作負載」憑證，並針對新的部署重新建立該憑證。

因為製造和作業程序分開，因此準備生產裝置時請考慮下列含意：

* 若有任何以憑證為基礎的流程，在推出 IoT Edge 裝置的整個流程中，根 CA 憑證和所有的中繼 CA 憑證均應受到保護和監控。 IoT Edge 裝置製造商應要備有穩健的流程，以妥善儲存和使用中繼憑證。 此外，裝置 CA 憑證應盡可能留存在裝置的安全儲存體中，最好是硬體安全性模組。

* IoT Edge 中樞伺服器憑證會由 IoT Edge 中樞向連線的用戶端裝置和模組顯示。 裝置 CA 憑證的一般名稱 (CN) **不得**與 IoT Edge 裝置上的 config.yaml 中將使用的「hostname」相同。 用戶端用來連線到 IoT Edge 的名稱 (例如，透過連接字串的 GatewayHostName 參數或 MQTT 中的 CONNECT 命令) **不能** 與裝置 CA 憑證中使用的一般名稱相同。 這項限制是因為 IoT Edge 中樞會提供整個信任鏈結由用戶端驗證。 如果 IoT Edge 中樞伺服器憑證和裝置 CA 憑證兩者都有相同的 CN 憑證，則會進入驗證迴圈中，而導致憑證無效。

* 因為 IoT Edge 安全性精靈會使用裝置 CA 憑證來產生最終 IoT Edge 憑證，因此裝置 CA 憑證必須是簽署憑證，也就是本身具有憑證簽署功能。 將「V3 Basic constraints CA:True」套用於裝置 CA 憑證後，基本上會自動設定必要的金鑰使用屬性。

>[!Tip]
> 如果您已經在開發/測試情況中使用我們的「便利性指令碼」 (請參閱下一節) 安裝 IoT Edge 做為透明的閘道，而且您在建立主機 CA 憑證時使用的主機名稱與 config.yaml 中的主機名稱相同，您可能會很好奇為何可正常運作。 為了簡化開發人員作業的過程，便利性指令碼會在您傳遞至指令碼的名稱後面附加「.ca」。 因此，比方說您指令碼中的裝置名稱和 config.yaml 中主機名稱都是「mygateway」，前者會轉換為 mygateway.ca，然後才會做為裝置 CA 憑證的 CN 使用。

## <a name="devtest-implications"></a>開發/測試影響

為了簡化開發和測試過程，Microsoft 提供了一組[便利性指令碼](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)，用來產生透明閘道案例中試用 IoT Edge 的非生產憑證。 如需腳本如何工作的範例，請參閱 [建立示範憑證來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。

>[!Tip]
> 若要將裝置的 IoT「分葉」裝置連線到透過 IoT Edge 使用我們的 IoT 裝置 SDK 的應用程式，必須將選擇性的「GatewayHostName」參數家到裝置連接字串的尾端。 產生 Edge 中樞伺服器憑證時，會以小寫的 config.yaml 主機名稱為基礎，因此，若要名稱相符並且成功通過 TLS 憑證驗證，請輸入小寫的 GatewayHostName 參數。

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IoT Edge 憑證階層的範例

為了說明這個憑證路徑的範例，下列螢幕擷取畫面取自於設為透明閘道的運作中 IoT Edge 裝置。 OpenSSL 用來連線到 IoT Edge 中樞、驗證和傾印出憑證。

![憑證階層各層級的螢幕擷取畫面](./media/iot-edge-certs/iotedge-cert-chain.png)

可以在螢幕擷取畫面看到憑證深度階層：

| 根 CA 憑證         | 僅限 Azure IoT 中樞 CA 憑證測試                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| 中繼 CA 憑證 | 僅限 Azure IoT 中樞中繼憑證測試                                                                 |
| 裝置 CA 憑證       | iotgateway.ca (「iotgateway」作為 <閘道器主機名稱> 傳入便利性指令碼)   |
| 工作負載 CA 憑證     | IoT Edge 工作負載 CA                                                                                       |
| IoT Edge 中樞伺服器憑證 | iotedgegw.local (與 config.yaml 的「hostname」相符)                                            |

## <a name="next-steps"></a>後續步驟

[了解 Azure IoT Edge 模組](iot-edge-modules.md)

[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
