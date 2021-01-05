---
title: 加速警示工作流程
description: 改進警示和事件工作流程。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838643"
---
# <a name="accelerate-alert-workflows"></a>加速警示工作流程

本文說明如何在適用于 IoT 的 Azure Defender 中使用警示批註、警示群組和自訂警示規則，以加速警示工作流程。  這些工具可協助您：

- 分析和管理在網路中偵測到的大量警示事件。

- 找出並處理特定的網路活動。

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>使用警示批註來加速事件工作流程

使用警示批註，以改善調查警示事件期間個人和小組之間的通訊。

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="顯示惡意活動的螢幕擷取畫面。":::

使用警示批註來改善：

- **工作流程步驟**：提供警示風險降低步驟。

- **工作流程後續** 操作：通知已採取的步驟。

- **工作流程指引**：提供有關事件的建議、見解或警告。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="顯示警示批註的螢幕擷取畫面。":::

可用選項的清單會出現在每個警示中。 使用者可以選取一或多個訊息。

若要加入警示批註：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **系統設定** ] 視窗中，選取 [ **警示批註**]。

3. 在 [ **新增批註** ] 方塊中，輸入註解文字。 最多可使用50個字元。 不允許逗點。

4. 選取 [新增]。

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>使用警示群組加速事件工作流程

警示群組可讓 SOC 小組查看和篩選其 SIEM 解決方案中的警示，然後根據企業安全性原則和業務優先順序來管理這些警示。 例如，關於新偵測的警示會組織在探索群組中。 此群組包含的警示會處理新裝置的偵測、新的 Vlan、新的使用者帳戶、新的 MAC 位址等等。

當您為下列合作夥伴解決方案建立轉送規則時，會套用警示群組：

  - Syslog 伺服器

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="建立轉送規則的螢幕擷取畫面。":::

相關的警示群組會顯示在 [夥伴輸出] 解決方案中。 

### <a name="requirements"></a>需求

警示群組將會出現在支援的合作夥伴解決方案中，並具有下列首碼：

  - QRadar、ArcSight、Syslog CEF、Syslog LEEF 的 **貓**

  - Syslog 文字訊息的 **警示群組**

  - Syslog 物件的 **alert_group**

您應在合作夥伴解決方案中設定這些欄位，以顯示警示組名。 如果警示群組沒有相關聯的警示，夥伴解決方案中的欄位將會顯示 **NA**。

### <a name="default-alert-groups"></a>預設警示群組

系統會自動定義下列警示群組：
|  |  |  |
|--|--|--|
| 異常通訊行為 | 自訂警示 | 遠端存取 |
| 異常 HTTP 通訊行為 | 探索 | 重新開機和停止命令 |
| 驗證 | 固件變更 | 掃描 |
| 未經授權的通訊行為 | 不合法的命令 | 感應器流量 |
| 頻寬異常 | 網際網路存取 | 可疑的惡意程式碼 |
| 緩衝區溢位 | 作業失敗 | 可疑的惡意活動 |
| 命令失敗 | 作業問題 |  |
| 組態變更 | 程式設計 |  |

警示群組是預先定義的。 如需與警示群組相關聯之警示的詳細資訊，以及有關建立自訂警示群組的詳細資訊，請聯絡 [Microsoft 支援服務](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="customize-alert-rules"></a>自訂警示規則

您可以根據個別感應器偵測到的資訊來新增自訂警示規則。 例如，定義一個規則，指示感應器根據通訊協定) 內的來源 IP、目的地 IP 或命令 (觸發警示。 當感應器偵測到規則中所定義的流量時，就會產生警示或事件。

警示訊息表示使用者定義的規則引發了警示。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="顯示自訂警示的螢幕擷取畫面。":::

若要建立自訂警示規則：

1. 從感應器的側邊功能表選取 [ **自訂警示** ]。
1. 選取加號 (**+**) 以建立規則。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="顯示使用者定義規則的螢幕擷取畫面。":::

1. 定義規則名稱。
1. 從 [ **類別** ] 窗格中選取類別或通訊協定。
1. 定義特定的來源和目的地 IP 或 MAC 位址，或選擇任何位址。
1. 新增條件。 每個類別的條件清單和其屬性是唯一的。 您可以針對每個警示選取一個以上的條件。
1. 指出 **規則是否觸發警示或****事件**。
1. 指派嚴重性層級給警示。
1. 指出警示是否會包含 PCAP 檔。
1. 選取 [儲存]。

規則會新增至 [ **自訂警示規則** ] 清單中，您可以在其中查看基本規則參數、上次觸發規則的時間等等。 您也可以從清單中啟用和停用規則。

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="使用者新增自訂規則的螢幕擷取畫面。":::

### <a name="see-also"></a>請參閱

[查看警示中提供的資訊](how-to-view-information-provided-in-alerts.md)

[管理警示事件](how-to-manage-the-alert-event.md)
