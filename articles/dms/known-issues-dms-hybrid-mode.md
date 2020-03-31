---
title: 使用混合模式的已知問題/遷移限制
description: 在混合模式下使用 Azure 資料庫移轉服務，瞭解已知問題/遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: aedc7ea3d778d52f6f348837430987568af188ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649597"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式的已知問題/遷移限制

以下各節介紹了在混合模式下使用 Azure 資料庫移轉服務相關的已知問題和限制。

## <a name="installer-fails-to-authenticate"></a>安裝程式無法進行身份驗證

將證書上載到 AdApp 後，最多延遲幾分鐘才能使用 Azure 進行身份驗證。 安裝程式將嘗試重試一些延遲，但傳播延遲可能比重試長，您將看到**一條"失敗到GetAccessTokenException"** 消息。 如果證書已上載到正確的 AdApp，並且 dmsSettings.json 中提供了正確的 AppId，請嘗試再次運行安裝命令。

## <a name="service-offline-after-successful-installation"></a>成功安裝後"離線"服務

如果服務在安裝過程成功完成後顯示為離線，請嘗試使用以下步驟。

1. 在 Azure 門戶中，在 Azure 資料庫移轉服務實例中，導航到**混合**設置選項卡，然後通過檢查已註冊工作人員的網格來驗證工作人員是否註冊。

    此工作人員的狀態應**為連線**，但如果出現問題，可能會顯示為**離線**。

2. 在輔助電腦上，通過運行以下 PowerShell 命令來檢查服務的狀態：

    ```
    Get-Service Scenario*
    ```

    此命令提供運行輔助角色的 Windows 服務的狀態。 應該只有一個結果。 如果工作人員已停止，則可以使用以下 PowerShell 命令嘗試重新開機它：

    ```
    Start-Service Scenario*
    ```

    您還可以在 Windows 服務 UI 中檢查該服務。

3. 如果 Windows 服務在"正在運行"和"已停止"之間迴圈，則工作人員在啟動時遇到問題。 檢查 Azure 資料庫移轉服務混合工作日志以確定問題。

    - 安裝過程日誌存儲在運行安裝程式可執行檔的資料夾中的"日誌"資料夾中。
    - Azure 資料庫移轉服務混合輔助角色日誌存儲在**輔助日誌**資料夾中，該資料夾中安裝輔助角色。 混合輔助角色日誌檔的預設位置是**C：\程式檔\資料庫移轉服務混合_WorkerLogs**。

## <a name="using-your-own-signed-certificate"></a>使用您自己的簽章憑證

操作 GenerateCert 生成的證書是自簽章憑證，根據內部安全性原則可能無法接受。 您可以提供自己的證書並在 dmsSettings.json 中提供指紋，而不是使用此證書。 此證書需要上載到 AdApp 並安裝在要安裝 Azure 資料庫移轉服務混合工作執行緒的電腦上。 然後，使用私密金鑰將此證書安裝到本地電腦憑證存放區中。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>將輔助服務作為低特權帳戶運行

預設情況下，Azure 資料庫移轉服務混合輔助工作執行緒服務作為本機系統帳戶運行。 只要您使用的帳戶具有網路許可權，就可以更改用於此服務的帳戶。 要更改服務"以"身份運行帳戶，請使用以下過程。

1. 通過 Windows 服務或使用 PowerShell 中的"停止服務"命令停止服務。

2. 更新服務以使用不同的登錄帳戶。

3. 在本地電腦證書證書中，為**DMS 混合應用金鑰**和**DMS 方案引擎金鑰組**證書的新帳戶授予私密金鑰許可權。

    a. 打開證書，查看以下鍵：

    - DMS 混合應用金鑰
    - DMS 混合輔助角色設置金鑰
    - DMS 方案引擎金鑰組

    b. 按右鍵**DMS 混合應用金鑰**條目，指向**所有任務**，然後選擇"**管理私密金鑰**"。

    c. 在 **"安全"** 選項卡上，選擇 **"添加**"，然後輸入帳戶的名稱。

    d. 使用相同的步驟將新帳戶的私密金鑰許可權授予**DMS 方案引擎金鑰組**證書。

## <a name="unregistering-the-worker-manually"></a>手動取消註冊工作人員

如果不再有權訪問輔助電腦，則可以通過執行以下步驟取消註冊輔助角色並重用 Azure 資料庫移轉服務實例：

1. 在 Azure 門戶中，到達 Azure 資料庫移轉服務實例，然後導航到**混合**設置頁。

   您的工作人員條目將顯示在清單中，狀態顯示為 **"離線**"。

2. 在輔助角色條目清單的最右側，選擇橢圓，然後選擇 **"取消註冊**"。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解決特定遷移方案的問題

以下各節介紹與使用 Azure 資料庫移轉服務混合模式執行連線遷移相關的特定于方案的問題。

### <a name="online-migrations-to-azure-sql-database-managed-instance"></a>連線遷移到 Azure SQL 資料庫託管實例

**高 CPU 用量**

**問題**：對於連線遷移到 SQL 資料庫託管實例，如果備份太多或備份太大，運行混合工作臺的電腦將遇到高 CPU 使用率。

**緩解**：為了緩解此問題，請使用壓縮備份、拆分遷移以便使用多個共用，或向上擴展運行混合輔助角色的電腦。
