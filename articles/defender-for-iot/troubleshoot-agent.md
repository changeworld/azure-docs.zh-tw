---
title: 針對 (Linux) 的安全性代理程式啟動進行疑難排解
description: 針對適用于 Linux 的 IoT 安全性代理程式使用 Azure Defender 進行疑難排解。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e9904e9157a560e2a4853a1a9cd37977defe73ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933915"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全性代理程式疑難排解指南 (Linux)

本文說明如何解決安全性代理程式啟動流程中的潛在問題。

適用于 IoT 的 Azure Defender 代理程式在安裝之後立即啟動。 代理程式啟動套裝程式括讀取本機設定、連接到 Azure IoT 中樞，以及抓取遠端對應項設定。 這些步驟中的任何一個失敗都可能導致安全性代理程式失敗。

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

1. 如果命令傳回空白行，則安全性代理程式無法順利啟動。

## <a name="force-stop-the-security-agent"></a>強制停止安全性代理程式

如果安全性代理程式無法啟動，請使用下列命令來停止代理程式，然後繼續進行下列錯誤資料表：

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>取得安全性代理程式錯誤

1. 藉由執行下列命令，以取得安全性代理程式錯誤 (s) ：

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. 取得安全性代理程式錯誤命令會抓取 Defender for IoT 代理程式所建立的所有記錄。 使用下表來瞭解錯誤，並採取正確的修復步驟。

> [!Note]
> 錯誤記錄檔會依時間順序顯示。 請務必記下每個錯誤的時間戳記，以協助您進行補救。

## <a name="restart-the-agent"></a>重新啟動代理程式

1. 找出並修正安全性代理程式錯誤之後，請執行下列命令來重新開機代理程式。

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 重複先前的進程以抓取停止，並在代理程式繼續使啟動進程失敗時取出錯誤。

## <a name="understand-security-agent-errors"></a>瞭解安全性代理程式錯誤

大部分的安全性代理程式錯誤會以下列格式顯示：

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 錯誤碼 | 錯誤的副程式代碼 | 錯誤詳細資料 | 補救 C | 補救 C# |
|:-----------|:---------------|:--------|:------------|:------------|
| 本機設定 | 遺失設定 | 本機設定檔中遺漏了設定。 錯誤訊息應該會指出遺漏的金鑰。 | 將遺漏的金鑰新增至/var/LocalConfiguration.json 檔案，如需詳細資料，請參閱 [cs-localconfig 參考](azure-iot-security-local-configuration-c.md) 。| 將遺漏的金鑰新增至 General.config 檔案中，如需詳細資料，請參閱 [c # localconfig 參考](azure-iot-security-local-configuration-csharp.md) 。 |
| 本機設定 | 無法剖析設定 | 無法剖析設定值。 錯誤訊息應該指出無法剖析的金鑰。 無法剖析設定值，因為值不在預期的類型中，或值超出範圍。 | 修正 LocalConfiguration.js/var/中的索引鍵值，使其符合 LocalConfiguration 架構，如需詳細資料，請參閱 [c # localconfig 參考](azure-iot-security-local-configuration-csharp.md) 。 |  修正 General.config 檔案中的機碼值，使其符合架構。如需詳細資料，請參閱 [cs localconfig 參考](azure-iot-security-local-configuration-c.md) 。|
| 本機設定 | 檔案格式 | 無法剖析設定檔案。 | 設定檔已損毀，請下載代理程式並重新安裝。 | |
| 遠端設定 | 逾時 | 代理程式無法在超時期間內提取 azureiotsecurity 模組對應項。 | 請確認驗證設定正確，然後再試一次。 | 代理程式無法在超時期間內提取 azureiotsecurity 模組對應項。 | 請確認驗證設定正確，然後再試一次。 |
| 驗證 | 檔案不存在 | 指定路徑中的檔案不存在。 | 請確定檔案存在於指定的路徑中，或移至檔案 ** 上的LocalConfiguration.js** ，然後變更 **FilePath** 設定。 | 請確定檔案存在於指定的路徑中，或移至 **Authentication.config** 檔案並變更 **filePath** 設定。|
| 驗證 | 檔案許可權 | 代理程式沒有足夠的許可權可以開啟檔案。 | 授與 **asciotagent** 使用者指定路徑中檔案的讀取權限。 | 請確定該檔案可供存取。 |
| 驗證 | 檔案格式 | 指定的檔案格式不正確。 | 請確定檔案的格式正確。 支援的檔案類型為 .pfx 和 pem。 | 請確定檔案是有效的憑證檔案。 |
| 驗證 | 未經授權 | 代理程式無法使用指定的認證對 IoT 中樞進行驗證。 | 驗證 LocalConfiguration 檔中的驗證設定，並進行驗證設定，並確定所有詳細資料都正確無誤，然後驗證檔案中的密碼是否符合已驗證的身分識別。 | 驗證 Authentication.config 中的驗證設定，進行驗證設定，並確定所有詳細資料都正確無誤，然後驗證檔案中的密碼是否符合已驗證的身分識別。
| 驗證 | 找不到 | 找到裝置/模組。 | 驗證驗證設定-請確定主機名稱正確、裝置是否存在於 IoT 中樞，且具有 azureiotsecurity 對應項模組。 |  驗證驗證設定-請確定主機名稱正確、裝置是否存在於 IoT 中樞，且具有 azureiotsecurity 對應項模組。 |
| 驗證 | 遺失設定 | *Authentication.config*檔案中遺漏了設定。 錯誤訊息應該會指出遺漏的金鑰。 | 將遺漏的金鑰新增至檔案中的 *LocalConfiguration.js* 。| 將遺漏的金鑰新增至 *Authentication.config* 檔案中，如需詳細資料，請參閱 [c # localconfig 參考](azure-iot-security-local-configuration-csharp.md) 。 |
| 驗證 | 無法剖析設定 | 無法剖析設定值。 錯誤訊息應該指出無法剖析的金鑰。 無法剖析設定值，因為值不是預期的類型，或值超出範圍。 |修正檔案 **LocalConfiguration.js** 中的機碼值。 |修正 **Authentication.config** 檔案中的機碼值以符合架構，如需詳細資料，請參閱 [cs localconfig 參考](azure-iot-security-local-configuration-c.md) 。|
|

## <a name="next-steps"></a>後續步驟

- 閱讀適用于 IoT 的 Defender 服務 [總覽](overview.md)
- 深入瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 啟用適用于 IoT 的 Defender [服務](quickstart-onboard-iot-hub.md)
- 閱讀適用于 IoT 的 Defender 服務 [常見問題](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全性 [警示](concept-security-alerts.md)
