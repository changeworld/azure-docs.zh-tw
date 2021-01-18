---
title: 關於 Splunk 整合
titleSuffix: Azure Defender for IoT
description: 為了解決缺乏對 OT 網路的安全性和復原能力，Defender for IoT 開發了適用于 Splunk 的 defender for IoT、IIoT 和 ICS 威脅監視應用程式，這是適用于 IoT 和 Splunk 的 Defender 之間的原生整合，可讓您整合方法和安全性。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557585"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>適用于 Splunk 的 Defender for IoT 和 ICS 威脅監視應用程式

適用于 IoT 的 Defender 可透過已獲專利、可感知 ICS 的自我學習引擎來減輕 IIoT、ICS 和 SCADA 的風險，在不到一小時的時間內提供 ICS 裝置、弱點和威脅的立即深入解析，而不需要依賴代理程式、規則或簽章、特製化技能，或事先瞭解環境。

為了解決缺乏對 OT 網路的安全性和復原能力，Defender for IoT 開發了適用于 Splunk 的 defender for IoT、IIoT 和 ICS 威脅監視應用程式，這是適用于 IoT 和 Splunk 的 Defender 之間的原生整合，可讓您整合方法和安全性。

> [!Note]
> CyberX 的參考會參考適用于 IoT 的 Azure Defender。

## <a name="about-the-splunk-application"></a>關於 Splunk 應用程式

此應用程式會提供 SOC 分析師，以多維度可見度來查看在產業環境中部署的特殊面向通訊協定和 IIoT 裝置，以及具備 ICS 感知的行為分析，以快速偵測可疑或異常行為。 應用程式也會從一個公司 SOC 內部啟用它和事件回應。 這是為了支援新的 IIoT 計畫（例如智慧型電腦和即時智慧）而提供的重要演進。

Splunk 應用程式可以安裝在本機或在雲端上執行。 與 Defender for IoT 的整合支援這兩種部署。

## <a name="about-the-integration"></a>關於整合

透過原生應用程式整合 Defender for IoT 與 Splunk 可讓使用者：

- 減少產業和重要基礎結構組織偵測、調查及因應網路威脅所需的時間。

- 取得有關承擔風險的即時情報。

- 讓適用于 IoT 的 Defender 與 Splunk 企業安全性威脅情報存放庫產生相互關聯。

- 從單一窗格進行監視和回應。

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk 工具的主頁面。":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk 中的 [警示] 頁面。":::

應用程式可讓 Splunk 系統管理員分析 Defender for IoT 傳送的警示，並監視整個安全性部署，包括如下的詳細資料：

- 其中五個分析引擎偵測到警示。

- 哪個通訊協定產生警示。

- 哪些 Defender for IoT 感應器產生警示。

- 警示的嚴重性層級。

- 通訊的來源和目的地。

## <a name="requirements"></a>需求

### <a name="version-requirements"></a>版本需求

以下是需求的版本。

- 適用于 IoT 2.4 版和更新版本的 Defender。

- 從 splunkbase 取得第11版或更新版本。

- Splunk Enterprise 7.2 版或更新版本。
  
## <a name="download-the-application"></a>下載應用程式

從 [從 splunkbase 取得](https://splunkbase.splunk.com/app/4313/)下載 *適用于 Splunk 應用程式的 CyberX ICS 威脅監控*。

## <a name="splunk-permission-requirements"></a>Splunk 許可權需求

需要下列 Splunk 許可權：

- 具有系統 *管理員* 使用者角色許可權的任何使用者。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>將 Defender for IoT 警示傳送至 Splunk

Defender for IoT 警示提供廣泛安全性事件的相關資訊，包括：

- 與已學習的基準網路活動偏差。

- 惡意程式碼偵測。

- 根據可疑操作變更的偵測。

- 網路異常。

- 通訊協定與通訊協定規格之間的差異。

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="偵測畫面。":::

您可以設定 Defender for IoT 將警示傳送至 Splunk 伺服器，警示資訊會顯示在 Splunk 企業儀表板中。

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="查看所有警示及其詳細資料。":::

下列警示資訊會傳送至 Splunk 伺服器。

- 警示的日期和時間。

- 偵測到事件的 Defender for IoT 引擎：通訊協定違規、原則違規、惡意程式碼、異常或操作引擎。

- 警示標題。

- 警示訊息。

- 警示的嚴重性：「警告」、「次要」、「主要」或「重大」。

- 來源裝置名稱。

- 來源裝置 IP 位址。

- 目的地裝置名稱。

- 目的地裝置 IP 位址。

- Defender for IoT platform IP 位址 (主機) 。

- IoT 平臺設備的 Defender 名稱 (來源類型) 。

範例輸出如下所示：

| Time | 事件 |
|--|--|
| 7/23/15<br />9：28： 31.000 PM | **Defender For IoT 平臺警示**：已由 PLC 命令停止裝置<br /><br />**類型**：操作違規 <br /><br />**嚴重性**：主要 <br /><br />**來源名稱**： my_device1 <br /><br />**來源 IP**：192.168.110.131 <br /><br />**目的地名稱**： my_device2<br /><br /> **目的地 IP**：10.140.33.238 <br /><br />**訊息**：網路裝置已使用 Stop PLC 命令停止。 在傳送啟動命令之前，此裝置將無法運作。 使用 PLC Stop 命令，10.140.33.238 (Siemens S7 裝置) 來停止192.168.110.131。<br /><br />**主機**：192.168.90.43 <br /><br />**Sourcetype**： Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>定義警示轉送規則

使用 Defender 進行 IoT *轉送規則* ，將警示資訊傳送至 Splunk 伺服器。

您可以根據下列選項自訂警示規則：

- 偵測到特定的通訊協定。

- 事件的嚴重性。

- 偵測事件的 Defender for IoT 引擎。

若要建立轉送規則：

1. 從 [感應器] 或 [內部部署管理主控台] 的左窗格中，選取 [ **轉送]。**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="選取藍色按鈕的 [建立轉送警示]。":::

1. 選取 [ **建立轉送規則**]。 在 [ **建立轉送規則** ] 視窗中，定義規則參數。

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="建立轉送規則的規則。":::

    | 參數 | 描述 |
    |--|--|
    | **名稱** | 轉送規則名稱。 |
    | **選取嚴重性** | 要轉寄的最基本安全性層級事件。 例如，如果選取 [次要]，將會轉送 [次要警示] 和高於此嚴重性層級的任何警示。 |
    | **通訊協定** | 預設會選取所有的通訊協定。 若要選取特定的通訊協定，請選取 [ **特定** ]，然後選取要套用此規則的通訊協定。 |
    | **引擎** | 依預設，會牽涉到所有的安全性引擎。 若要選取套用此規則的特定安全性引擎，請選取 [ **特定** ]，然後選取引擎。 |
    | **系統通知** | 轉寄感應器上線/離線狀態。 只有當您已登入中央管理員時，才能使用此選項。 |                                            |

1. 若要指示 Defender 讓 IoT 將資產資訊傳送至 Splunk，請選取 [ **動作**]，然後選取 [ **傳送至 Splunk 伺服器**]。

1. 輸入下列 Splunk 參數。

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="您應該在此畫面上輸入的 Splunk 參數。":::

    | 參數 | 描述 |
    |--|--|
    | **主控件** | Splunk 伺服器位址 |
    | **通訊埠** | 8089 |
    | **使用者名稱** | Splunk 伺服器使用者名稱 |
    | **密碼** | Splunk 伺服器密碼 |

1. 選取 [**提交**]

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
