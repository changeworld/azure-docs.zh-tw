---
title: 關於 ServiceNow 整合
titleSuffix: Azure Defender for IoT
description: 適用于 ServiceNow 的 Defender for IoT ICS 管理應用程式會提供 SOC 分析師，以多維度可見度部署在產業環境中的特殊面向通訊協定和 IoT 裝置，以及具備 ICS 感知的行為分析，以快速偵測可疑或異常行為。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557649"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>適用于 ServiceNow 的 Defender for IoT ICS 管理應用程式

適用于 ServiceNow 的 Defender for IoT ICS 管理應用程式會提供 SOC 分析師，以多維度可見度部署在產業環境中的特殊面向通訊協定和 IoT 裝置，以及具備 ICS 感知的行為分析，以快速偵測可疑或異常行為。 這是為了支援新的 IoT 計畫（例如智慧型電腦和即時智慧）而提供的重要演進。

應用程式也會從一個公司 SOC 內部啟用它和事件回應。

## <a name="about-defender-for-iot"></a>關於適用于 IoT 的 Defender

適用于 IoT 的 Defender 提供的 ICS 和 IoT 網路安全性平臺，都是由 blue 團隊專家打造的追蹤記錄，以保護重要的國家基礎結構，以及具備可獲得專利之 ICS 感知威脅分析和機器學習的唯一平臺。 適用于 IoT 的 Defender 提供：

- 有關 ICS 裝置環境的即時深入解析，以及有關屬性的廣泛詳細資料。

- 以 ICS 感知的深度內嵌知識，以及關於通訊協定、裝置、應用程式及其行為的知識。

- 立即深入瞭解弱點，以及已知的零時差威脅。

- 自動化 ICS 威脅模型化技術，可透過專屬分析來預測目標 ICS 攻擊最可能的路徑。

> [!Note]
> CyberX 的參考會參考適用于 IoT 的 Azure Defender。

## <a name="about-the-integration"></a>關於整合

適用于 IoT 的 Defender 與 ServiceNow 的整合提供了新層級的集中式可見度、監視，以及對 IoT 和 OT 的控制。 這些橋接平臺可讓先前無法連線到 ICS & IoT 裝置的裝置可見度和威脅管理自動化。

### <a name="threat-management"></a>威脅管理

Defender for IoT ICS 管理應用程式有助於：

- 減少產業和重要基礎結構組織偵測、調查及因應網路威脅所需的時間。

- 取得有關承擔風險的即時情報。

- 將適用于 IoT 警示的 Defender 與 ServiceNow 威脅監視和事件管理工作流程相互關聯。

- 使用 ServiceNow 平臺上的其他服務和應用程式來觸發 ServiceNow 票證和工作流程。

ICS 和 SCADA 安全性威脅是由 Defender 針對 IoT 安全性引擎所識別，可針對惡意程式碼攻擊、網路和安全性原則偏差提供立即的警示回應，以及操作和通訊協定異常。 如需傳送至 ServiceNow 之警示詳細資料的詳細資訊，請參閱 [警示報告](#alert-reporting)。

### <a name="device-visibility-and-management"></a>裝置可見度與管理

ServiceNow 設定管理資料庫 (CMDB) 經過擴充，並以 Defender for IoT 平臺所推送的一組豐富裝置屬性來補充。 這可確保對裝置環境進行完整且持續的可見度，並可讓您從單一窗格進行監視和回應。 如需將裝置屬性傳送至 ServiceNowSee 的詳細資訊，請參閱 [ServiceNow 中的 View Defender For IoT](#view-defender-for-iot-detections-in-servicenow)偵測。

## <a name="system-requirements-and-architecture"></a>系統需求和架構

本文章說明：

- **軟體需求**  
- **架構**

## <a name="software-requirements"></a>軟體需求

- ServiceNow 服務管理版本3.0。2

- Defender for IoT patch 2.8.11.1 或更高版本

> [!Note]
> 如果您已經使用 Defender 進行 IoT 和 ServiceNow 整合，並使用內部部署管理主控台進行升級，則應該從 ServiceNow 清除從 Defender 針對 IoT 感應器所接收的舊資料。

## <a name="architecture"></a>架構

### <a name="on-premises-management-console-architecture"></a>內部部署管理主控台架構

內部部署管理主控台提供所有裝置的整合來源，以及傳送至 ServiceNow 的警示資訊。

您可以設定內部部署管理主控台，以與一個 ServiceNow 實例進行通訊。 內部部署管理主控台會使用 REST API，將感應器資料推送至適用于 IoT 的 Defender 應用程式。

如果您要將系統設定為使用內部部署管理主控台，請在感應器中停用 ServiceNow 同步、轉送規則和 proxy 設定（如果已設定）。

您應該為內部部署管理主控台設定這些設定。 本文將說明設定指示。

### <a name="sensor-architecture"></a>感應器架構

如果您想要設定您的環境以包含感應器與 ServiceNow 之間的直接通訊，則針對每個感應器定義 ServiceNow 同步、轉送規則和 proxy 設定 (如果需要 proxy) 。

建議您使用內部部署管理主控台來設定整合，以與 ServiceNow 進行通訊。

## <a name="create-access-tokens-in-servicenow"></a>在 ServiceNow 中建立存取權杖

本文說明如何在 ServiceNow 中建立存取權杖。 需要權杖才能與適用于 IoT 的 Defender 進行通訊。

您將需要 **用戶端識別碼** 和 **用戶端密碼** ，才能建立適用于 Iot 轉送規則的 defender、將警示資訊轉送至 Servicenow，以及將 defender 設定為 iot 以將裝置屬性推送至 servicenow 資料表。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>設定適用于 IoT 的 Defender 以與 ServiceNow 通訊

本文說明如何設定適用于 IoT 的 Defender 以與 ServiceNow 進行通訊。

### <a name="send-defender-for-iot-alert-information"></a>傳送 IoT 的 Defender 警示資訊

本文說明如何設定 Defender for IoT 以將警示資訊推送至 ServiceNow 資料表。 如需有關傳送警示資料的詳細資訊，請參閱 [警示報告](#alert-reporting)。

防禦者 for IoT 警示會以安全性事件的形式出現在 ServiceNow 中。

定義適用于 IoT *轉送* 的 Defender 規則，以傳送警示資訊給 ServiceNow。

若要定義規則：

1. 在 [Defender for IoT] 的左窗格中，選取 [ **轉送**]。  

1. 選取 :::image type="content" source="media/integration-servicenow/plus.png" alt-text="加號圖示按鈕。"::: 圖示。 [建立轉送規則] 對話方塊隨即開啟。  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="建立轉送規則":::

1. 新增規則名稱。

1. 定義適用于 IoT 的 Defender 將會觸發轉送規則的準則。 使用轉送規則準則有助於找出並管理從 Defender for IoT 傳送到 ServiceNow 的資訊量。 可用選項如下：

    - **嚴重性層級：** 這是要轉寄的最低安全性層級事件。 例如，如果選取 [ **次要** ]，[次要警示] 和高於此嚴重性層級的任何警示都會轉寄。 適用于 IoT 的 Defender 已預先定義層級。

    - **通訊協定：** 只有在偵測到的流量是在特定的通訊協定上執行時，才會觸發轉送規則。 從下拉式清單中選取所需的通訊協定，或選擇 [全部]。

    - **引擎：** 選取所需的引擎或選擇 [全部]。 將會傳送所選引擎的警示。

1. 確認已選取 [ **報告警示通知** ]。

1. 在 [動作] 區段中，選取 [ **新增** ]，然後選取 [ **ServiceNow**]。

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="從下拉式清單選項中選取 ServiceNow。":::

1. 輸入 ServiceNow 動作參數：

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="填寫 ServiceNow 動作參數":::

1. 在 [ **動作** ] 窗格中，設定下列參數：

  | 參數 | 描述 |
  |--|--|
  | Domain | 輸入 ServiceNow 伺服器 IP 位址。 |
  | 使用者名稱 | 輸入 ServiceNow 伺服器使用者名稱。 |
  | 密碼 | 輸入 ServiceNow 伺服器密碼。 |
  | 用戶端識別碼 | 在 ServiceNow 的 [ **應用程式** 登錄] 頁面中，輸入您為 Defender for IoT 所收到的用戶端識別碼。 |
  | 用戶端密碼 | 在 ServiceNow 的 [ **應用程式** 登錄] 頁面中，輸入您為 Defender for IoT 建立的用戶端秘密字串。 |
  | 報表類型 | **事件**：轉寄 ServiceNow 中顯示的警示清單，並提供每個警示的事件識別碼和簡短描述。<br /><br />**適用于 IoT 的 Defender 應用程式**：轉寄完整的警示資訊，包括感應器詳細資料、引擎、來源和目的地位址。 在 ServiceNow 應用程式上，會將資訊轉送至適用于 IoT 的 Defender。 |

1. 選取 [儲存]  。 IoT 警示的防禦者會顯示為 ServiceNow 中的事件。

### <a name="send-defender-for-iot-device-attributes"></a>傳送適用于 IoT 的 Defender 裝置屬性

本文說明如何設定 Defender for IoT，以將範圍廣泛的裝置屬性推送至 ServiceNow 資料表。 如需推送至 ServiceNow 之資訊類型的詳細資訊，請參閱 **_清查資訊_* _。

若要將屬性傳送至 ServiceNow，您必須將內部部署管理主控台對應至 ServiceNow 實例。 這可確保適用于 IoT 平臺的 Defender 可以與實例通訊並進行驗證。

若要新增 ServiceNow 實例：

1. 登入您的 Defender for IoT 內部部署管理主控台。

1. 從內部部署管理主控台的 [整合] 區段中，選取 [*系統設定*]，然後選取 [ **ServiceNow** ]。

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="選取 [ServiceNow] 按鈕。":::

1. 在 [ServiceNow 同步處理] 對話方塊中，輸入下列同步參數。

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="[ServiceNow 同步處理] 對話方塊。":::

     參數 | 描述 |
    |--|--|
    | 啟用同步處理 | 在定義參數之後啟用和停用同步處理。 |
    | 同步頻率 (分鐘)  | 依預設，資訊會每隔60分鐘推送至 ServiceNow。 最小值為5分鐘。 |
    | ServiceNow 執行個體 | 輸入 ServiceNow 實例 URL。 |
    | 用戶端識別碼 | 在 ServiceNow 的 [ **應用程式** 登錄] 頁面中，輸入您為 Defender for IoT 所收到的用戶端識別碼。 |
    | 用戶端密碼 | 在 ServiceNow 的 [ **應用程式** 登錄] 頁面中，輸入您為 Defender for IoT 建立的用戶端秘密字串。 |
    | 使用者名稱 | 輸入此實例的使用者名稱。 |
    | 密碼 | 輸入此實例的密碼。 |

1. 選取 [儲存]  。

## <a name="verify-communication"></a>驗證通訊

藉由檢查 *上次同步* 處理日期，確認內部部署管理主控台已連線到 ServiceNow 實例。

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="查看最後一次同步處理來確認通訊發生。":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>設定使用 HTTPS proxy 的整合

設定 Defender 進行 IoT 和 ServiceNow 整合時，內部部署管理主控台和 ServiceNow 伺服器會使用通訊埠443進行通訊。 如果 ServiceNow 伺服器位於 proxy 後方，則無法使用預設通訊埠。

適用于 IoT 的 Defender 藉由啟用用於整合的預設埠變更，在 ServiceNow 整合中支援 HTTPS proxy。

若要設定 proxy：

1. 在內部部署管理主控台中編輯全域屬性：  
    `sudo vim /var/cyberx/properties/global.properties`

2. 新增下列參數：

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 儲存並結束。

4. 執行下列命令：`sudo monit restart all`

設定之後，會使用設定的 proxy 轉送所有 ServiceNow 資料。

## <a name="download-the-defender-for-iot-application"></a>下載適用于 IoT 的 Defender 應用程式

本文說明如何下載應用程式。

若要存取適用于 IoT 的 Defender 應用程式：

1. 巡覽到 <https://store.servicenow.com/>

2. 搜尋 `Defender for IoT` 或 `CyberX IoT/ICS Management` 。

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="在搜尋列中搜尋 CyberX。":::

3. 選取應用程式。

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="從清單中選取應用程式。":::

4. 選取 [ **要求應用程式]。**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="使用您的認證登入應用程式。":::

5. 登入並下載應用程式。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>在 ServiceNow 中查看適用于 IoT 的 Defender 偵測

本文說明 ServiceNow 中顯示的裝置屬性和警示資訊。

若要查看裝置屬性：

1. 登入 ServiceNow。

2. 流覽至 **CyberX Platform**。

3. 流覽至 **清查** 或 **警示**。

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="清查或警示":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>清查資訊

 (CMDB) 的設定管理資料庫會經過擴充，並由 Defender for IoT 傳送給 ServiceNow 的資料進行補充。 藉由在 ServiceNow 的 CMDB 設定專案資料表上新增或更新裝置屬性，適用于 IoT 的 Defender 可以觸發 ServiceNow 工作流程和商務規則。

可用資訊如下：

- 裝置屬性，例如偵測到的裝置 MAC、OS、廠商或通訊協定。

- 固件資訊，例如，固件版本和序號。

- 連接的裝置資訊，例如來源和目的地之間的流量方向。

### <a name="devices-attributes"></a>裝置屬性

本文說明推送至 ServiceNow 的裝置屬性。

| 項目 | 描述 |
|--|--|
| 設備 | 偵測到流量的感應器名稱。 |
| ID | Defender 為 IoT 指派的裝置識別碼。 |
| 名稱 | 裝置名稱。 |
| IP 位址 | 裝置 IP 位址或位址。 |
| 類型 | 裝置類型，例如交換器、PLC、historian 或網域控制站。 |
| MAC 位址 | 裝置 MAC 位址或位址。 |
| 作業系統 | 裝置作業系統。 |
| 廠商 | 裝置廠商。 |
| 通訊協定 | 在裝置產生的流量中偵測到的通訊協定。 |
| 擁有者 | 輸入裝置擁有者的名稱。 |
| 位置 | 輸入裝置的實體位置。 |

在此視圖中，查看連線到裝置的裝置。

若要查看連線的裝置：

1. 選取裝置，然後選取 [針對該 **裝置** 列出的設備]。

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="從清單中選取所需的設備。":::

1. 在 [ **裝置詳細資料** ] 對話方塊中，選取 [ **已連線的裝置**]。

### <a name="firmware-details"></a>固件詳細資料

本文說明推送至 ServiceNow 的裝置固件資訊。

| 項目 | 描述 |
|--|--|
| 設備 | 偵測到流量的感應器名稱。 |
| 裝置 | 裝置名稱。 |
| 位址 | 裝置 IP 位址。 |
| 模組位址 | 裝置型號和插槽號碼或識別碼。 |
| 序列 | 裝置序號。 |
| 型號 | 裝置型號。 |
| 版本 | 固件版本號碼。 |
| 其他資料 | 如廠商所定義的更多有關固件的資料，例如裝置類型。 |

### <a name="connection-details"></a>連線詳細資料

本文說明推送至 ServiceNow 的裝置連線資訊。

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="裝置的連接資訊":::

| 項目 | 描述 |
|--|--|
| 設備 | 偵測到流量的感應器名稱。 |
| 方向 | 流量的方向。 <br /> <br /> - **其中一個方法** 表示目的地為伺服器，而來源是用戶端。 <br /> <br /> - **有兩種方式** 表示來源和目的地都是伺服器，或用戶端未知。 |
| 來源裝置識別碼 | 與連線裝置通訊的裝置 IP 位址。 |
| 來源裝置名稱 | 與連線裝置通訊之裝置的名稱。 |
| 目的地裝置識別碼 | 連線裝置的 IP 位址。 |
| 目的地裝置名稱 | 連線裝置的名稱。 |

## <a name="alert-reporting"></a>警示報告

當 IoT 引擎的防禦者偵測到需要您注意的網路流量和行為變更時，就會觸發警示。 如需每個引擎產生之警示類型的詳細資訊，請參閱 [關於警示引擎](#about-alert-engines)。

本文說明推送至 ServiceNow 的裝置警示資訊。

| 項目 | 描述 |
|--|--|
| 建立時間 | 產生警示的時間和日期。 |
| 引擎 | 偵測到事件的引擎。 |
| 標題 | 警示標題。 |
| 描述 | 警示描述。 |
| 通訊協定 | 在流量中偵測到的通訊協定。 |
| 嚴重性 | Defender 針對 IoT 定義的警示嚴重性。 |
| 設備 | 偵測到流量的感應器名稱。 |
| 來源名稱 | 來源名稱。 |
| 來源 IP 位址| 來源 IP 位址。 |
| 目的地名稱 | 目的地名稱。 |
| 目的地 IP 位址 | 目的地 IP 位址。 |
| 工作 | 輸入指派給票證的個人名稱。 |

### <a name="updating-alert-information"></a>正在更新警示資訊

選取 [已建立] 欄中的專案，以在表單中查看警示資訊。 您可以更新警示詳細資料，並將警示指派給個別的評論和處理。

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="查看警示的資訊。":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>關於警示引擎

本文描述每個引擎觸發的警示類型。

| 警示類型 | 描述 |
|--|--|
| 原則違規警示 | 當原則違規引擎偵測到來自先前所學流量的偏差時，就會觸發。 例如： <br /><br />-偵測到新的裝置。 <br /><br />-在裝置上偵測到新的設定。 <br /><br />-未定義為程式設計裝置的裝置會執行程式設計變更。 <br /><br />-已變更的固件版本。 |
| 通訊協定違規警示 | 當通訊協定違規引擎偵測到不符合通訊協定規格的封包結構或域值時，就會觸發。 |
| 操作警示 | 當作業引擎偵測到網路操作事件或裝置故障時觸發。 例如，使用「停止 PLC」命令或感應器上的介面停止監視流量時，會停止網路裝置。 |
| 惡意程式碼警示 | 當惡意程式碼引擎偵測到惡意網路活動（例如，Conficker 之類的已知攻擊）時觸發。 |
| 異常警示 | 當異常引擎偵測到偏差時，就會觸發。 例如，裝置正在執行網路掃描，但未定義為掃描裝置。 |

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
