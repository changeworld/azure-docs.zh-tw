---
title: 關於 Cisco ISE pxGrid 整合
titleSuffix: Azure Defender for IoT
description: 透過 Cisco ISE pxGrid 橋接 Defender for IoT 的功能，可為安全性團隊提供前所未有的企業生態系統可見度。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/28/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 3f1fb099aa18ebec5a7e2063740556cf806302e7
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557637"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>關於 Cisco ISE pxGrid 整合

適用于 IoT 的 Defender 提供的 ICS 和 IoT 網路安全性平臺，都是由 blue 團隊專家打造的追蹤記錄，以保護重要的國家基礎結構，以及具備可獲得專利之 ICS 感知威脅分析和機器學習的唯一平臺。 適用于 IoT 的 Defender 提供：

- 有關 ICS 裝置、弱點以及已知和零時差威脅的立即見解。

- 以 ICS 感知的深度內嵌知識，以及關於通訊協定、裝置、應用程式及其行為的知識。

- 自動化 ICS 威脅模型化技術，可透過專屬分析來預測目標 ICS 攻擊最可能的路徑。

## <a name="powerful-device-visibility-and-control"></a>功能強大的裝置可見度和控制

適用于 IoT 的 Defender 與 Cisco ISE pxGrid 整合提供了一種新層級的集中式可見度、監視及控制。

這些橋接的平臺可讓您對先前無法存取的 ICS 和 IIoT 裝置進行自動裝置可見度和保護。

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>ICS 和 IIoT 裝置可見度：全方位和深度

適用于 IoT 技術的專利 Defender 可確保企業資料的全方位和深度 ICS 和 IIoT 裝置探索和清查管理。

裝置類型、製造商、開放埠、序號、固件修訂、IP 位址和 MAC 位址資料等會即時更新。 適用于 IoT 的 Defender 可以整合重要的企業資料來源，進一步增強此基準的可見度、探索和分析。 例如，Cmdb、DNS、防火牆和 Web Api。

此外，適用于 IoT 的 Defender 平臺會結合被動監視和選擇性的選擇性探查技術，以提供產業和重要基礎結構組織中最精確且更詳細的裝置清查。

### <a name="bridged-capabilities"></a>橋接功能

將這些功能與 Cisco ISE pxGrid 橋接，提供安全性團隊前所未有的企業生態系統可見度。

與 Cisco ISE pxGrid 完美且穩固的整合，可確保不會探索任何裝置，也不會錯過任何裝置資訊。

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="端點分類 OUI 的範例。":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="系統中的範例端點":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="位於系統中之屬性的螢幕擷取畫面。":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>使用案例涵蓋範圍：以 Defender for IoT 屬性為基礎的 ISE 原則

使用以 Defender for IoT 深度學習為基礎的強大 ISE 原則，來處理 ICS 和 IoT 使用案例需求。

使用原則可讓您關閉安全性週期，並讓您的網路即時符合規範。

例如，客戶可以使用 Defender 作為 IoT ICS 和 IoT 屬性來建立原則，以處理下列使用案例，例如：

- 根據允許的屬性建立授權原則，以允許已知和已授權的裝置進入敏感性區域，例如，適用于宋體的特定固件版本 Plc。

- 當在非 OT 區域中偵測到 ICS 裝置時，通知安全性小組。

- 修復具有過期或不相容廠商的電腦。

- 視需要隔離和封鎖裝置。

- 使用 (Cve) 的已知弱點，在 Plc 或 RTUs 上產生報告。

### <a name="about-this-article"></a>本文內容

本文說明如何設定 pxGrid 和適用于 IoT 的 Defender 平臺，以確保適用于 IoT 的 Defender 會將屬性插入 Cisco ISE。

### <a name="getting-more-information"></a>取得詳細資訊

如需 Cisco ISE pxGrid 整合需求的詳細資訊，請參閱 <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>整合系統需求

本文說明整合系統需求：

適用于 IoT 的 Defender 需求

- 適用于 IoT 的 Defender 版本2。5

Cisco 需求

- pxGrid 版本2。0

- Cisco ISE 2.4 版

網路需求

- 確認 Defender for IoT 設備可存取 Cisco ISE 管理介面。

- 確認您有 CLI 可存取您企業中的所有 IoT 裝置 Defender。

> [!NOTE]
> 只有具有 MAC 位址的裝置會與 Cisco ISE pxGrid 同步。

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ISE pxGrid 安裝程式

這篇文章說明如何：

- 設定與 pxGrid 的通訊

- 訂閱端點裝置主題

- 產生憑證

- 定義 pxGrid 設定

## <a name="set-up-communication-with-pxgrid"></a>設定與 pxGrid 的通訊

本文說明如何設定與 pxGrid 的通訊。

若要設定通訊：

1. 登入 Cisco ISE。

1. 選取 [系統 **管理** > **系統** 和 **部署**]。

1. 選取所需的節點。 在 [一般設定] 索引標籤中，選取 [ **pxGrid] 核取方塊**。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="確定已選取 [pxgrid] 核取方塊。":::

1. 選取 [ **分析** 設定] 索引標籤。

1. 選取 [ **pxGrid] 核取方塊**。 新增描述。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="[新增描述] 的螢幕擷取畫面":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>訂閱端點裝置主題

確認 ISE pxGrid 節點已訂閱端點裝置主題。 流覽至 **Administration** > **pxGrid Services** > **Web 用戶端**。 您可以在該處確認 ISE 已訂閱端點裝置主題。

## <a name="generate-certificates"></a>產生憑證

本文說明如何產生憑證。

若要產生：

1. 選取 [ **Administration**  >  **pxGrid Services**]，然後選取 [**憑證**]。

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="選取 [憑證] 索引標籤，以產生憑證。":::

1. 在 [ **我想要** ] 欄位中，選取 [ **產生單一憑證 (不使用憑證簽署要求)**。

1. 填寫其餘欄位，然後選取 [ **建立**]。

1. 建立用戶端憑證金鑰和伺服器憑證，然後將它們轉換成 java 金鑰儲存區格式。 您必須將這些備份複製到網路中的每個適用于 IoT 的 Defender 感應器。

## <a name="define-pxgrid-settings"></a>定義 pxGrid 設定

若要定義設定：

1. 選取 [**管理**  >  **pxGrid 服務**]，然後選取 [**設定**]。

1. 啟用 **自動核准以憑證為基礎的新帳戶** ，並 **允許建立以密碼為基礎的帳戶。**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="確定已選取兩個核取方塊。":::

## <a name="set-up-the-defender-for-iot-appliances"></a>設定適用于 IoT 的 Defender 裝置

本文說明如何設定適用于 IoT 設備的 Defender 來與 pxGrid 通訊。 您應在將資料插入 Cisco ISE 的所有 IoT 裝置 Defender 上執行設定。

設定設備：

1. 登入感應器 CLI。

1. 複製 `trust.jks` ，以及先前在感應器上建立的。 將它們複製到： `/var/cyberx/properties/` 。

1. 編輯 `/var/cyberx/properties/pxgrid.properties`：

    1. 新增金鑰和信任，然後儲存檔案名和密碼。

    2. 新增 pxGrid 實例的主機名稱。

    3. `Enabled=true`

1. 重新開機設備。

1. 針對您企業中將插入資料的每個設備，重複執行這些步驟。

## <a name="view-and-manage-detections-in-cisco-ise"></a>查看和管理 Cisco ISE 中的偵測

1. 端點偵測會顯示在 ISE 內容 **可見度** 的 [  >  **端點**] 索引標籤中。

1. 選取 **原則**  >  **分析**  >  **新增**  >  **規則**  >  **+ 條件**  >  **建立新條件**。

1. 選取 [ **屬性** ]，並使用 IOT 裝置字典，根據插入的屬性來建立分析規則。 下列屬性會插入。

    - 裝置類型

    - 硬體修訂

    - IP 位址

    - MAC 位址

    - 名稱

    - Product ID

    - 通訊協定

    - 序號

    - 軟體修訂

    - 廠商

只有具有 MAC 位址的裝置會進行同步處理。

## <a name="troubleshooting-and-logs"></a>疑難排解和記錄

您可以在下列內容中找到記錄：

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
