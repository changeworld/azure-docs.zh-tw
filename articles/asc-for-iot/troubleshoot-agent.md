---
title: 容錯排除安全代理啟動 (Linux)
description: 使用 Azure 安全中心為 Linux 的 IoT 安全代理進行故障排除。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310556"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>安全性代理程式疑難排解指南 (Linux)

本文介紹了如何解決安全代理啟動過程中的潛在問題。

安裝後立即啟動 IoT 代理的 Azure 安全中心。 代理啟動過程包括讀取本地配置、連接到 Azure IoT 中心以及檢索遠端孿生配置。 這些步驟中的任何一個失敗都可能導致安全代理失敗。

在此故障排除指南中,您將瞭解如何:

> [!div class="checklist"]
> * 驗證安全代理是否正在執行
> * 取得安全代理程式錯誤
> * 瞭解並修復安全代理程式錯誤

## <a name="validate-if-the-security-agent-is-running"></a>驗證安全代理是否正在執行

1. 要驗證安全代理是否正在運行,請在安裝代理后等待幾分鐘並運行以下命令。
     <br>

    **C 代理**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C# 代理**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. 如果命令返回空行,則安全代理無法成功啟動。

## <a name="force-stop-the-security-agent"></a>強制停止安全代理

如果安全代理無法啟動,請使用以下指令停止代理,然後繼續到下面的錯誤表:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>取得安全代理程式錯誤

1. 通過執行以下指令檢索安全代理錯誤:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. 獲取安全代理錯誤命令檢索由 IoT 代理的 Azure 安全中心創建的所有日誌。 使用下表瞭解錯誤並採取正確的方法進行補救。

> [!Note]
> 錯誤日誌按時間順序顯示。 請務必記下每個錯誤的時間戳,以幫助進行補救。

## <a name="restart-the-agent"></a>重新啟動代理程式

1. 尋找和修復安全代理錯誤後,請嘗試通過運行以下命令重新啟動代理。

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 如果代理繼續失敗啟動過程,請重複上一個進程以檢索停止並檢索錯誤。

## <a name="understand-security-agent-errors"></a>瞭解安全性代理程式錯誤

大多數安全代理錯誤以以下格式顯示:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 錯誤碼 | 錯誤子代碼 | 錯誤詳細資料 | 修復 C | 修復 C# |
|:-----------|:---------------|:--------|:------------|:------------|
| 本地設定 | 遺失設定 | 本地設定檔中缺少配置。 錯誤消息應說明缺少哪個密鑰。 | 將缺少的金鑰加入 /var/Local 設定.json 檔,有關詳細資訊,請參考[cs 本地設定參考](azure-iot-security-local-configuration-c.md)。| 將缺少的鍵加入 General.config 檔案,有關詳細資訊,請參考[c#本地設定參考](azure-iot-security-local-configuration-csharp.md)。 |
| 本地設定 | 無法剖析設定 | 無法分析配置值。 錯誤消息應說明無法解析的鍵。 無法分析配置值,因為該值不在預期類型中,或者該值不在範圍範圍內。 | 修復了 /var/Local 設定.json 檔中密鑰的值,以便它與本地設定架構匹配,有關詳細資訊,請參閱[c#本地設定參考](azure-iot-security-local-configuration-csharp.md)。 |  修復了 General.config 檔中的鍵的值,以便它與架構符合,有關詳細資訊,請參閱[cs-localconfig 引用](azure-iot-security-local-configuration-c.md)。|
| 本地設定 | 檔案格式 | 無法分析配置檔。 | 設定檔已損壞,下載代理並重新安裝。 | |
| 遠端設定 | 逾時 | 代理無法在超時期間獲取 Azureiotasecurity 模組孿生。 | 請確保身份驗證配置正確,然後重試。 | 代理無法在超時期間獲取 Azureiotasecurity 模組孿生。 | 請確保身份驗證配置正確,然後重試。 |
| 驗證 | 檔案不存在 | 給定路徑中的檔不存在。 | 請確保該檔存在於給定的路徑中,或轉到**Local 配置.json**檔並更改**FilePath**配置。 | 請確保該檔存在於給定的路徑中,或轉到**身份驗證.config**檔並更改**檔案Path**配置。|
| 驗證 | 檔案權限 | 代理沒有足夠的許可權來打開該檔。 | 為**asciotagent**使用者授予給定路徑中檔的讀取許可權。 | 確保文件可訪問。 |
| 驗證 | 檔案格式 | 給定的檔案格式不正確。 | 確保檔格式正確。 支援的檔案類型是 .pfx 和 .pem。 | 確保該檔是有效的證書檔。 |
| 驗證 | 未經授權 | 代理無法使用給定的認證對IoT中心進行身分驗證。 | 驗證本地設定檔中的身份驗證配置,通過身份驗證配置並確保所有詳細資訊都正確,驗證檔中的機密是否與經過身份驗證的標識匹配。 | 驗證身份驗證配置在身份驗證.config 中,通過身份驗證配置並確保所有詳細資訊都正確,然後驗證檔中的機密是否與經過身份驗證的標識匹配。
| 驗證 | 找不到 | 找到設備/模組。 | 驗證身份驗證配置 - 確保主機名正確,設備存在於 IoT 中心中,並且具有 Azureiot 安全孿模組。 |  驗證身份驗證配置 - 確保主機名正確,設備存在於 IoT 中心中,並且具有 Azureiot 安全孿模組。 |
| 驗證 | 遺失設定 | *身份驗證.config 檔中*缺少配置。 錯誤消息應說明缺少哪個密鑰。 | 將丟失的金鑰添加到*本機設定.json*檔。| 將遺失的金鑰加入*認證.config*檔案,有關詳細資訊,請參考[c#本地設定參考](azure-iot-security-local-configuration-csharp.md)。 |
| 驗證 | 無法剖析設定 | 無法分析配置值。 錯誤消息應說明無法解析的鍵。 無法分析配置值,因為該值不是預期類型,或者該值不在範圍範圍內。 |修復**本地設定.json**檔中金鑰的值。 |修復了**認證.config**檔中的金鑰的值以符合架構,有關詳細資訊,請參考[cs-localconfig 。](azure-iot-security-local-configuration-c.md)|
|

## <a name="restart-the-agent"></a>重新啟動代理程式

1. 尋找及修復安全代理錯誤後,透過執行以下指令重新啟動代理:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 如果需要,請重複前面的進程以強制停止代理,並在代理繼續失敗的啟動過程時檢索錯誤。

## <a name="next-steps"></a>後續步驟

- 閱讀 Azure 安全中心,瞭解 IoT 服務[概述](overview.md)
- 瞭解有關 IoT[架構結構](architecture.md)的 Azure 安全中心
- 啟用適用於 IoT[服務的](quickstart-onboard-iot-hub.md)Azure 安全中心
- 閱讀 Azure 安全中心,瞭解 IoT 服務[常見問題解答](resources-frequently-asked-questions.md)
- 了解如何存取[未經處理的安全性資料](how-to-security-data-access.md)
- 了解[建議](concept-recommendations.md)
- 瞭解安全[警報](concept-security-alerts.md)
