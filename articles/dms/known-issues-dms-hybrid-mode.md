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
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 5347cda14773583bcfe92a702e59d4967ce2ea09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196269"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式的已知問題/遷移限制

在混合模式中使用 Azure 資料庫移轉服務相關的已知問題和限制，將在下列各節中說明。

## <a name="installer-fails-to-authenticate"></a>安裝程式無法驗證

將憑證上傳至您的 Adapp icons 之後，最多需要幾分鐘的時間，才能向 Azure 進行驗證。 安裝程式會嘗試以一些延遲重試，但傳播延遲可能會比重試更長，而您會看到**FailedToGetAccessTokenException**訊息。 如果已將憑證上傳至正確的 Adapp icons，並在 dmsSettings.js中提供正確的 AppId，請嘗試再次執行 [安裝] 命令。

## <a name="service-offline-after-successful-installation"></a>成功安裝後的服務「離線」

如果在安裝程式順利完成後，服務顯示為離線，請嘗試使用下列步驟。

1. 在 Azure 入口網站的 Azure 資料庫移轉服務實例中，流覽至 [**混合**式設定] 索引標籤，然後檢查已註冊背景工作角色的方格，確認是否已註冊背景工作角色。

    此背景工作角色的狀態應該是 [**線上**]，但如果發生問題，它可能會顯示為**離線**。

2. 在背景工作電腦上，執行下列 PowerShell 命令來檢查服務的狀態：

    ```
    Get-Service Scenario*
    ```

    此命令可提供執行背景工作的 Windows 服務狀態。 應該只有單一結果。 如果背景工作已停止，您可以使用下列 PowerShell 命令嘗試重新開機：

    ```
    Start-Service Scenario*
    ```

    您也可以在 Windows 服務 UI 中檢查服務。

3. 如果 Windows 服務在 [執行中] 和 [已停止] 之間迴圈，則工作者在啟動時遇到問題。 檢查 Azure 資料庫移轉服務混合式背景工作記錄以判斷問題。

    - 安裝程式記錄檔會儲存在執行安裝程式可執行檔所在的資料夾中的 "logs" 資料夾內。
    - Azure 資料庫移轉服務混合式背景工作角色記錄會儲存在**WorkerLogs**資料夾中，其位於已安裝 worker 的資料夾中。 混合式背景工作角色記錄檔的預設位置是**C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**。

## <a name="using-your-own-signed-certificate"></a>使用您自己的簽署憑證

動作 GenerateCert 所產生的憑證是自我簽署的憑證，根據您的內部安全性原則可能無法接受。 您可以提供自己的憑證，並在 dmsSettings.js中提供指紋，而不是使用此憑證。 必須將此憑證上傳至您的 Adapp icons，並安裝在您要安裝 Azure 資料庫移轉服務混合式背景工作角色的電腦上。 然後，使用私密金鑰將此憑證安裝到本機電腦憑證存放區。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>以低許可權帳戶執行背景工作角色服務

根據預設，Azure 資料庫移轉服務的混合式背景工作角色服務會以本機系統帳戶的身分執行。 只要您使用的帳戶具有網路許可權，您就可以變更此服務所使用的帳戶。 若要變更服務的「執行身分」帳戶，請使用下列進程。

1. 停止服務，不論是透過 Windows 服務，或是在 PowerShell 中使用停止服務命令。

2. 將服務更新為使用不同的登入帳戶。

3. 在 [本機電腦憑證的 certmgr.msc] 中，為**Dms 混合式應用程式金鑰**和**Dms 案例引擎金鑰**組憑證提供新帳戶的私密金鑰許可權。

    a. 開啟 certmgr.msc 以查看下列機碼：

    - DMS 混合式應用程式金鑰
    - DMS 混合式背景工作角色設定金鑰
    - DMS 案例引擎金鑰組

    b. 以滑鼠右鍵按一下 [ **DMS 混合式應用程式金鑰**] 專案，指向 [**所有**工作]，然後選取 [**管理私密金鑰**]。

    c. 在 [**安全性**] 索引標籤上，選取 [**新增**]，然後輸入帳戶的名稱。

    d. 使用相同的步驟，將新帳戶的私密金鑰許可權授與**DMS 案例引擎金鑰**組憑證。

## <a name="unregistering-the-worker-manually"></a>手動取消註冊背景工作

如果您不再擁有背景工作電腦的存取權，您可以執行下列步驟來取消註冊背景工作，並重複使用您的 Azure 資料庫移轉服務實例：

1. 在 Azure 入口網站中，前往您的 Azure 資料庫移轉服務實例，然後流覽至 [**混合**式設定] 頁面。

   您的背景工作專案會出現在清單中，其狀態會顯示為 [**離線**]。

2. 在背景工作專案清單的最右側，選取省略號，然後選取 [**取消註冊**]。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解決特定遷移案例的問題

下列各節說明與使用 Azure 資料庫移轉服務混合模式來執行線上遷移相關的特定案例問題。

### <a name="online-migrations-to-azure-sql-managed-instance"></a>線上遷移至 Azure SQL 受控執行個體

**高 CPU 使用率**

**問題**：若要在線上遷移至 SQL 受控執行個體，執行混合式背景工作角色的電腦如果有太多備份或備份太大，則會遇到高 CPU 使用量。

**緩和**：若要減輕此問題，請使用壓縮的備份、分割遷移，使其使用多個共用，或相應增加執行混合式背景工作角色的電腦。
