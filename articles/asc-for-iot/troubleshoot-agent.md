---
title: 針對安全性代理程式啟動進行疑難排解（Linux）
description: 針對適用于 Linux 的 IoT 安全性代理程式使用 Azure 資訊安全中心進行疑難排解。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81310556"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全性代理程式疑難排解指南 (Linux)

本文說明如何解決安全性代理程式啟動程式中的潛在問題。

IoT 代理程式的 Azure 資訊安全中心會在安裝後立即啟動。 代理程式啟動進程包括讀取本機設定、連接到 Azure IoT 中樞，以及抓取遠端對應項設定。 任何一個步驟中的失敗都可能會導致安全性代理程式失敗。

在此疑難排解指南中，您將瞭解如何：

> [!div class="checklist"]
> * 驗證安全性代理程式是否正在執行
> * 取得安全性代理程式錯誤
> * 瞭解及補救安全性代理程式錯誤

## <a name="validate-if-the-security-agent-is-running"></a>驗證安全性代理程式是否正在執行

1. 若要驗證安全性代理程式是否正在執行，請在安裝代理程式之後等候幾分鐘，然後執行下列命令。
     <br>

    **C 代理程式**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C # 代理程式**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. 如果命令傳回空白行，安全性代理程式就無法順利啟動。

## <a name="force-stop-the-security-agent"></a>強制停止安全性代理程式

在安全性代理程式無法啟動的情況下，請使用下列命令停止代理程式，然後繼續進行下列錯誤表格：

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>取得安全性代理程式錯誤

1. 執行下列命令來取出安全性代理程式錯誤：

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. [取得安全性代理程式錯誤] 命令會抓取 IoT 代理程式的 Azure 資訊安全中心所建立的所有記錄。 請使用下表來瞭解錯誤，並採取正確的補救步驟。

> [!Note]
> 錯誤記錄會以時間順序顯示。 請務必記下每個錯誤的時間戳記，以協助您進行補救。

## <a name="restart-the-agent"></a>重新啟動代理程式

1. 找出並修正安全性代理程式錯誤之後，請執行下列命令來嘗試重新開機代理程式。

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 重複先前的進程來抓取 stop，如果代理程式繼續使啟動進程失敗，則抓取錯誤。

## <a name="understand-security-agent-errors"></a>瞭解安全性代理程式錯誤

大部分的安全性代理程式錯誤會以下列格式顯示：

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 錯誤碼 | 錯誤副程式代碼 | 錯誤詳細資料 | 補救 C | 補救 C# |
|:-----------|:---------------|:--------|:------------|:------------|
| 本機設定 | 遺失設定 | 本機設定檔中遺漏了設定。 錯誤訊息應會陳述遺失的索引鍵。 | 將遺漏的金鑰新增至/var/LocalConfiguration.json 檔案，如需詳細資訊，請參閱[cs-localconfig-參考](azure-iot-security-local-configuration-c.md)。| 將遺漏的索引鍵新增至 General.config 檔案中，如需詳細資訊，請參閱[c # localconfig-參考](azure-iot-security-local-configuration-csharp.md)。 |
| 本機設定 | 無法剖析設定 | 無法剖析設定值。 錯誤訊息應會陳述無法剖析的金鑰。 因為值不是預期的類型，或值超出範圍，所以無法剖析設定值。 | 修正/var/LocalConfiguration.json 檔案中的金鑰值，使其符合 LocalConfiguration 架構，如需詳細資訊，請參閱[c #-localconfig-參考](azure-iot-security-local-configuration-csharp.md)。 |  修正 General.config 檔案中的金鑰值，使其符合架構，如需詳細資訊，請參閱[cs-localconfig-參考](azure-iot-security-local-configuration-c.md)。|
| 本機設定 | 檔案格式 | 無法剖析設定檔案。 | 設定檔案已損毀，請下載代理程式並重新安裝。 | |
| 遠端設定 | 逾時 | 代理程式無法在超時時間內提取 azureiotsecurity 模組對應項。 | 請確定驗證設定正確，然後再試一次。 | 代理程式無法在超時時間內提取 azureiotsecurity 模組對應項。 | 請確定驗證設定正確，然後再試一次。 |
| 驗證 | 檔案不存在 | 指定路徑中的檔案不存在。 | 請確定檔案存在於指定的路徑中，或移至檔案**上的LocalConfiguration.js** ，並變更**FilePath**設定。 | 請確定檔案存在於指定的路徑中，或移至**Authentication.config**檔案，然後變更**filePath**設定。|
| 驗證 | File 許可權 | 代理程式沒有足夠的許可權來開啟檔案。 | 授與**asciotagent**使用者指定路徑中檔案的讀取權限。 | 請確定該檔案可供存取。 |
| 驗證 | 檔案格式 | 指定的檔案格式不正確。 | 請確定檔案的格式正確。 支援的檔案類型為 .pfx 和 pem。 | 請確定檔案是有效的憑證檔案。 |
| 驗證 | 未經授權 | 代理程式無法使用指定的認證對 IoT 中樞進行驗證。 | 驗證 LocalConfiguration 檔案中的驗證設定，進行驗證設定，並確定所有詳細資料都正確無誤，並驗證檔案中的密碼是否符合驗證的身分識別。 | 驗證 Authentication.config 中的驗證設定、流覽驗證設定，並確定所有詳細資料都正確無誤，然後驗證檔案中的密碼是否符合驗證的身分識別。
| 驗證 | 找不到 | 找到裝置/模組。 | 驗證驗證設定-請確定主機名稱正確，裝置存在於 IoT 中樞中，而且具有 azureiotsecurity 對應項模組。 |  驗證驗證設定-請確定主機名稱正確，裝置存在於 IoT 中樞中，而且具有 azureiotsecurity 對應項模組。 |
| 驗證 | 遺失設定 | *Authentication.config*檔案中遺漏設定。 錯誤訊息應會陳述遺失的索引鍵。 | 將遺漏的金鑰新增至檔案中的*LocalConfiguration.js* 。| 將遺漏的索引鍵新增至*Authentication.config*檔案中，如需詳細資訊，請參閱[c # localconfig-參考](azure-iot-security-local-configuration-csharp.md)。 |
| 驗證 | 無法剖析設定 | 無法剖析設定值。 錯誤訊息應會陳述無法剖析的金鑰。 無法剖析設定值，因為值不是預期的類型，或值超出範圍。 |修正檔案中**LocalConfiguration.js**的機碼值。 |修正**Authentication.config**檔案中的機碼值以符合架構，如需詳細資訊，請參閱[cs-localconfig-參考](azure-iot-security-local-configuration-c.md)。|
|

## <a name="restart-the-agent"></a>重新啟動代理程式

1. 找出並修正安全性代理程式錯誤之後，請執行下列命令來重新開機代理程式：

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 如有需要，請重複先前的進程，以強制停止代理程式，並在代理程式繼續使啟動進程失敗時，抓取錯誤。

## <a name="next-steps"></a>後續步驟

- 閱讀 IoT 服務的 Azure 資訊安全中心[總覽](overview.md)
- 深入瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 啟用 IoT[服務](quickstart-onboard-iot-hub.md)的 Azure 資訊安全中心
- 閱讀 IoT 服務的 Azure 資訊安全中心[常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性[警示](concept-security-alerts.md)
