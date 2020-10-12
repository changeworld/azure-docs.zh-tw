---
title: 使用混合模式的已知問題/遷移限制
description: 瞭解在混合模式中使用 Azure 資料庫移轉服務的已知問題/遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291838"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式的已知問題/遷移限制

下列各節將說明在混合模式中使用 Azure 資料庫移轉服務的已知問題和限制。

## <a name="installer-fails-to-authenticate"></a>安裝程式無法驗證

將憑證上傳至 AdApp 之後，最多會有幾分鐘的時間，才能向 Azure 進行驗證。 安裝程式會嘗試以一些延遲來重試，但傳播延遲可能會比重試更長，且您會看到 **FailedToGetAccessTokenException** 訊息。 如果憑證已上傳至正確的 AdApp，且 dmsSettings.js中提供正確的 AppId，請嘗試再次執行安裝命令。

## <a name="service-offline-after-successful-installation"></a>成功安裝後的服務「離線」

如果在安裝程式順利完成之後，服務顯示為離線，請嘗試使用下列步驟。

1. 在 Azure 入口網站的 Azure 資料庫移轉服務實例中，流覽至 [ **混合** 式設定] 索引標籤，然後藉由檢查已註冊之背景工作的方格來確認已註冊背景工作角色。

    此背景工作角色的狀態應該是 **線上**的，但如果發生問題，它可能會顯示為 **離線** 。

2. 在背景工作電腦上，執行下列 PowerShell 命令來檢查服務的狀態：

    ```
    Get-Service Scenario*
    ```

    此命令會提供執行背景工作角色之 Windows 服務的狀態。 應該只會有單一結果。 如果背景工作已停止，您可以嘗試使用下列 PowerShell 命令來重新開機它：

    ```
    Start-Service Scenario*
    ```

    您也可以在 Windows 服務 UI 中檢查服務。

3. 如果 Windows 服務在執行和停止之間迴圈，則工作者會在啟動時發生問題。 檢查 Azure 資料庫移轉服務的混合式背景工作角色記錄，以判斷問題。

    - 安裝程式記錄會儲存在執行安裝程式可執行檔之資料夾內的 "logs" 資料夾中。
    - Azure 資料庫移轉服務混合式背景工作記錄會儲存在安裝了背景工作的資料夾中的 **WorkerLogs** 資料夾中。 混合式背景工作角色記錄檔的預設位置為 **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**。

## <a name="using-your-own-signed-certificate"></a>使用您自己簽署的憑證

動作 GenerateCert 所產生的憑證是自我簽署的憑證，可能無法根據您的內部安全性原則接受。 您可以提供自己的憑證，並在 dmsSettings.js中提供憑證指紋，而不是使用此憑證。 必須將此憑證上傳到您的 AdApp，並安裝在您要安裝 Azure 資料庫移轉服務混合式背景工作角色的電腦上。 然後，在本機電腦憑證存放區中安裝具有私密金鑰的此憑證。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>以低許可權帳戶執行背景工作角色服務

根據預設，Azure 資料庫移轉服務混合式背景工作角色服務會以本機系統帳戶的形式執行。 只要您使用的帳戶具有網路許可權，您就可以變更此服務所使用的帳戶。 若要變更服務「執行身份」帳戶，請使用下列進程。

1. 停止服務，不論是透過 Windows 服務，或是在 PowerShell 中使用 Stop-Service 命令。

2. 將服務更新為使用不同的登入帳戶。

3. 在 [本機電腦憑證的 certmgr.msc] 中，將 [ **Dms 混合式應用程式金鑰** ] 和 [ **Dms 案例引擎金鑰** 組] 憑證的私密金鑰許可權授與新帳戶。

    a. 開啟 certmgr.msc 以查看下列索引鍵：

    - DMS 混合式應用程式金鑰
    - DMS 混合式背景工作角色安裝程式金鑰
    - DMS 案例引擎金鑰組

    b. 以滑鼠右鍵按一下 **DMS 混合式應用程式金鑰** 專案，指向 [ **所有**工作]，然後選取 [ **管理私密金鑰**]。

    c. 在 [ **安全性** ] 索引標籤上，選取 [ **新增**]，然後輸入帳戶的名稱。

    d. 使用相同的步驟，將新帳戶的私密金鑰許可權授與 **DMS 案例引擎金鑰** 組憑證。

## <a name="unregistering-the-worker-manually"></a>手動取消註冊背景工作

如果您無法再存取背景工作電腦，您可以執行下列步驟來取消註冊背景工作，並重複使用您的 Azure 資料庫移轉服務實例：

1. 在 Azure 入口網站中，前往您的 Azure 資料庫移轉服務實例，然後流覽至 [ **混合** 式設定] 頁面。

   您的背景工作專案會出現在清單中，且狀態會顯示為 [ **離線**]。

2. 在背景工作專案清單的最右邊，選取省略號，然後選取 [ **取消註冊**]。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解決特定遷移案例的問題

下列各節說明與使用 Azure 資料庫移轉服務混合模式來執行線上遷移相關的案例特定問題。

### <a name="online-migrations-to-azure-sql-managed-instance"></a>線上遷移至 Azure SQL 受控執行個體

**高 CPU 使用量**

**問題**：若要線上遷移至 SQL 受控執行個體，執行混合式背景工作角色的電腦如果有太多備份或備份太大，就會發生高 CPU 使用率。

**風險**降低：若要解決此問題，請使用壓縮的備份、分割遷移，使其使用多個共用，或擴大執行混合式背景工作角色的電腦。
