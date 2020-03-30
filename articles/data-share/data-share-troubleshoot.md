---
title: 針對 Azure Data Share 進行疑難排解
description: 瞭解如何在使用 Azure 資料共用創建或接收資料共用時解決邀請和錯誤的問題。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964221"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>解決 Azure 資料共用中的常見問題 

本文演示如何解決 Azure 資料共用的常見問題。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀請 

在某些情況下，當新的使用者從已傳送的電子郵件邀請中按一下 [接受邀請]**** 時，他們可能會看到空白的邀請清單。 

![沒有邀請](media/no-invites.png)

這可能是由於以下原因：

* **Azure 資料共用服務未註冊為 Azure 租戶中的任何 Azure 訂閱的資來源提供者。** 如果 Azure 租戶中沒有資料共用資源，則會遇到此問題。 創建 Azure 資料共用資源時，它會自動在 Azure 訂閱中註冊資來源提供者。 您還可以按照以下步驟手動註冊資料共用服務。 您需要具有 Azure 參與者角色才能完成這些步驟。

    1. 在 Azure 入口網站中，瀏覽到 [訂用帳戶]****。
    1. 選擇要用於創建 Azure 資料共用資源的訂閱
    1. 按一下 [資源提供者]****
    1. 搜索**微軟.資料共用**
    1. 按一下 **"註冊"** 

    您必須擁有 [Azure 參與者 RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)才能完成這些步驟。 

* **邀請將發送到您的電子郵件別名，而不是 Azure 登錄電子郵件。** 如果您已註冊 Azure 資料共用服務或在 Azure 租戶中創建了資料共用資源，但仍看不到邀請，則可能是因為提供程式已輸入您的電子郵件別名作為收件者，而不是您的 Azure 登錄電子郵件地址。 請與資料供應商聯繫，確保他們已將邀請發送到 Azure 登錄電子郵件地址，而不是您的電子郵件別名。

* **邀請已被接受。** 電子郵件中的連結將帶您到 Azure 門戶中的"資料共用邀請"頁，該頁面僅列出掛起的邀請。 如果您已經接受了邀請，該邀請將不再顯示在"資料共用邀請"頁中。 轉到資料共用資源，用於接受邀請以查看已接收的共用並配置目標 Azure 資料資源管理器群集設置。

## <a name="error-when-creating-or-receiving-a-new-share"></a>創建或接收新共用時出錯

"無法添加資料集"

"無法映射資料集"

"無法授予資料共用資源 x 對 y 的訪問"

"您沒有適當的 x 許可權"

"無法將 Azure 資料共用帳戶的寫入權限添加到一個或多個所選資源中"

如果在創建新共用或映射資料集時收到上述任何錯誤，則可能是由於對 Azure 資料存儲的許可權不足。 有關所需許可權[，請參閱角色和要求](concepts-roles-permissions.md)。 

您需要編寫許可權才能共用或接收來自 Azure 資料存儲的資料，而 Azure 資料存儲通常存在於"參與者"角色中。 

如果這是您第一次從 Azure 資料存儲共用或接收資料，則還需要*Microsoft.授權/角色指派/寫入*許可權，這通常存在於擁有者角色中。 即使您創建了 Azure 資料存儲資源，也不會自動使您成為資源的擁有者。 獲得適當的許可權後，Azure 資料共用服務會自動授予資料共用資源對資料存儲的託管標識存取權限。 此過程可能需要幾分鐘才能生效。 如果由於此延遲而出現故障，請在幾分鐘內重試。

基於 SQL 的共用需要其他許可權。 有關詳細資訊，請參閱故障排除基於 SQL 的共用。

## <a name="troubleshooting-sql-based-sharing"></a>針對以 SQL 為基礎的共用進行疑難排解

SQL 資料庫中不存在使用者 x

如果從基於 SQL 的源添加資料集時收到此錯誤，可能是因為您沒有在 SQL 伺服器上為 Azure 資料共用託管標識創建使用者。  要解決此問題，運行以下腳本：

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
如果在將資料集映射到基於 SQL 的目標時收到此錯誤，可能是因為您沒有在 SQL 伺服器上為 Azure 資料共用託管標識創建使用者。  要解決此問題，運行以下腳本：

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
請注意，*<share_acc_name>* 是您 Data Share 資源的名稱。      

確保您已遵循["共用資料](share-your-data.md)"中列出的所有先決條件，並接受[和接收資料](subscribe-to-data-share.md)教程。

## <a name="snapshot-failed"></a>快照失敗
由於各種原因，快照可能會失敗。 通過按一下快照的開始時間以及每個資料集的狀態，可以找到詳細的錯誤訊息。 

如果錯誤訊息與許可權相關，請驗證資料共用服務具有所需的許可權。 有關詳細資訊，請參閱[角色和要求](concepts-roles-permissions.md)。 如果這是第一次拍攝快照，則資料共用資源可能需要幾分鐘才能被授予對 Azure 資料存儲的存取權限。 請等待幾分鐘，然後重試。

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。 

要瞭解如何接收資料，請繼續[接受和接收資料](subscribe-to-data-share.md)教程。

