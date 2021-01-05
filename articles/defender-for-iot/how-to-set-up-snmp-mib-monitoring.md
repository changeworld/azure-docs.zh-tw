---
title: 設定 SNMP MIB 監視
description: 您可以使用 SNMP 來執行感應器健康情況監視。 感應器會回應從授權的監視伺服器傳送的 SNMP 查詢。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e0ae029323d5b64288c5e61ea28a494c1106a53f
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838766"
---
# <a name="set-up-snmp-mib-monitoring"></a>設定 SNMP MIB 監視

您可以使用簡單的網路管理通訊協定 (SNMP) 來執行感應器健康情況監視。 感應器會回應從授權的監視伺服器傳送的 SNMP 查詢。 SNMP 監視器會定期輪詢感應器 Oid (最多每秒50次) 。

SNMP 支援的版本為 SNMP v2 或 SNMP v3。 SNMP 使用 UDP 作為其傳輸通訊協定，並使用埠 161 (SNMP) 。

開始設定 SNMP 監視之前，您必須在防火牆中開啟埠 UDP 161。

## <a name="oids"></a>Oid

| 管理主控台和感應器 | OID | [格式] | 描述 |
|--|--|--|--|
| 設備名稱 | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | 內部部署管理主控台的設備名稱 |
| 廠商 | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft 支援服務 (support.microsoft.com)  |
| 平台 | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | 感應器或內部部署管理主控台 |
| 序號 | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | 授權使用的字串 |
| 軟體版本 | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Xsense 完整版的字串和管理的完整版本字串 |
| CPU 使用量 | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | 0到100的指示 |
| CPU 溫度 | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | 以 Linux 輸入為基礎的攝氏表示為0到100 |
| 記憶體使用量 | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | 0到100的指示 |
| 磁碟使用量 | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | 0到100的指示 |
| 服務狀態 | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | 當四個重要元件中的其中一個關閉時，線上或離線 |
| 頻寬 | 2.4 的範圍外 |  | 在 Xsense 的每個監視介面上收到的頻寬 |

   - 根據 [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)，非現有的索引鍵會以 Null （HTTP 200）回應。
    
   - 硬體相關的 Mib (CPU 使用率、CPU 溫度、記憶體使用量、磁片使用量) 應該在所有架構和實體感應器上進行測試。 虛擬機器上的 CPU 溫度應不適用。

您可以下載包含感應器所接收所有 SNMP 查詢的記錄檔，包括連接資料和封包中的原始資料。

若要定義 SNMP v2 健康情況監視：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **主動式探索** ] 窗格中，選取 [ **SNMP MIB 監視**] :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="編輯您的 SNMP 視窗。":::

3. 在 [ **允許的主機** ] 區段中，選取 [ **新增主機** ]，然後輸入執行系統健康情況監視之伺服器的 IP 位址。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="輸入允許的主機的 IP 位址。":::

4. 在 [ **驗證** ] 區段的 [ **SNMP v2 社區字串** ] 方塊中，輸入字串。 SNMP 社區字串最多可以包含32個字元，並包含任何英數位元 (大寫字母、小寫字母及數位) 的組合。 不允許空格。

5. 選取 [儲存]。

若要定義 SNMP v3 健康情況監視：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **主動式探索** ] 窗格中，選取 [ **SNMP MIB 監視**] :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: 。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="編輯您的 SNMP 視窗。":::

3. 在 [ **允許的主機** ] 區段中，選取 [ **新增主機** ]，然後輸入執行系統健康情況監視之伺服器的 IP 位址。

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="輸入允許的主機的 IP 位址。":::

4. 在 [ **驗證** ] 區段中，設定下列參數：

    | 參數 | 描述 |
    |--|--|
    | **使用者名稱** | SNMP 使用者名稱最多可以包含32個字元，並包含任何英數位元的組合 (大寫字母、小寫字母和數位) 。 不允許空格。 <br /> <br />SNMP v3 驗證的使用者名稱必須在系統和 SNMP 伺服器上進行設定。 |
    | **密碼** | 輸入區分大小寫的驗證密碼。 驗證密碼可包含8到12個字元，並包含任何英數位元的組合 (大寫字母、小寫字母和數位) 。 <br /> <br/>SNMP v3 驗證的使用者名稱必須在系統和 SNMP 伺服器上進行設定。 |
    | **驗證類型** | 選取 [MD5] 或 [SHA]。 |
    | **加密** | 選取 DES 或 AES。 |
    | **祕密金鑰** | 金鑰必須包含剛好8個字元，並包含任何英數位元的組合 (大寫字母、小寫字母和數位) 。 |

5. 選取 [儲存]。

## <a name="see-also"></a>請參閱

[匯出疑難排解記錄](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
