---
title: 針對 Azure Data Share 進行疑難排解
description: 瞭解如何在使用 Azure 資料共用建立或接收資料共用時，針對邀請和錯誤的問題進行疑難排解。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/30/2020
ms.openlocfilehash: 0fb2f9dd156d18705308b41ef8d6b015b3b6d71b
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534442"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>針對 Azure 資料共用中的常見問題進行疑難排解 

本文說明如何針對 Azure 資料共用的常見問題進行疑難排解。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀請 

在某些情況下，當新的使用者從已傳送的電子郵件邀請中按一下 [接受邀請]**** 時，他們可能會看到空白的邀請清單。 

![沒有邀請](media/no-invites.png)

這可能是因為下列原因：

* **Azure 資料共用服務並未註冊為 Azure 租使用者中任何 Azure 訂用帳戶的資源提供者。** 如果您的 Azure 租使用者中沒有資料共用資源，您將會遇到此問題。 當您建立 Azure 資料共用資源時，它會自動在您的 Azure 訂用帳戶中註冊資源提供者。 您也可以依照下列步驟，手動註冊資料共用服務。 您必須擁有 Azure 參與者角色，才能完成這些步驟。

    1. 在 Azure 入口網站中，瀏覽到 [訂用帳戶]****。
    1. 選取您想要用來建立 Azure 資料共用資源的訂用帳戶
    1. 按一下 [資源提供者]****
    1. 搜尋**DataShare**
    1. 按一下 [註冊] 

    您必須具有[參與者 Azure 角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)，才能完成這些步驟。 

* **邀請會傳送至您的電子郵件別名，而不是您的 Azure 登入電子郵件。** 如果您已註冊 Azure 資料共用服務，或已在 Azure 租使用者中建立資料共用資源，但仍無法看到邀請，可能是因為提供者已將您的電子郵件別名輸入為收件者，而不是您的 Azure 登入電子郵件地址。 請洽詢您的資料提供者，並確定他們已將邀請傳送至您的 Azure 登入電子郵件地址，而不是您的電子郵件別名。

* **已接受邀請。** 電子郵件中的連結會帶您前往 Azure 入口網站中的資料共用邀請頁面，其中只會列出暫止的邀請。 如果您已經接受邀請，它就不會再顯示在 [資料共用邀請] 頁面中。 繼續前往您用來接受邀請的資料共用資源，以查看已接收的共用，並設定您的目標 Azure 資料總管叢集設定。

## <a name="error-when-creating-or-receiving-a-new-share"></a>建立或接收新的共用時發生錯誤

「無法加入資料集」

「無法對應資料集」

「無法將資料共用資源 x 存取權授與 y」

「您沒有 x 的適當許可權」

「我們無法將 Azure 資料共用帳戶的寫入權限新增到一或多個選取的資源」

如果您在建立新的共用或對應資料集時收到上述任何錯誤，可能是因為 Azure 資料存放區的許可權不足。 如需必要許可權，請參閱[角色和需求](concepts-roles-permissions.md)。 

您需要有寫入權限，才能共用或接收 Azure 資料存放區中的資料，這通常存在於參與者角色中。 

如果這是您第一次從 Azure 資料存放區共用或接收資料，您也需要有*Microsoft 授權/角色指派/寫入*許可權，這通常存在於擁有者角色中。 即使您已建立 Azure 資料存放區資源，也不會自動將您設為資源的擁有者。 有了適當的許可權，Azure 資料共用服務就會自動授與資料共用資源的受控識別對資料存放區的存取權。 此程式可能需要幾分鐘的時間才會生效。 如果您因為這項延遲而遇到失敗，請在幾分鐘後再試一次。

以 SQL 為基礎的共用需要額外的許可權。 如需詳細資訊，請參閱疑難排解以 SQL 為基礎的共用。

## <a name="troubleshooting-sql-based-sharing"></a>針對以 SQL 為基礎的共用進行疑難排解

「使用者 x 不存在於 SQL Database 中」

如果您從以 SQL 為基礎的來源新增資料集時收到此錯誤，可能是因為您未在 SQL Database 中建立 Azure 資料共用受控識別的使用者。  若要解決此問題，請執行下列腳本：

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
如果您在將資料集對應至以 SQL 為基礎的目標時收到此錯誤，可能是因為您未在 SQL Server 上建立 Azure 資料共用受控識別的使用者。  若要解決此問題，請執行下列腳本：

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
請注意，*<share_acc_name>* 是您 Data Share 資源的名稱。      

請確定您已遵循[共用資料](share-your-data.md)及[接受和接收資料](subscribe-to-data-share.md)教學課程中所列的所有必要條件。

## <a name="snapshot-failed"></a>快照集失敗
快照集可能會因為各種原因而失敗。 若要尋找詳細的錯誤訊息，請按一下快照的開始時間，然後按每個資料集的狀態。 

如果錯誤訊息與許可權相關，請確認資料共用服務具有必要的許可權。 如需詳細資訊，請參閱[角色和需求](concepts-roles-permissions.md)。 如果這是您第一次建立快照集，可能需要幾分鐘的時間，才會將 Azure 資料存放區的存取權授與資料共用資源。 請稍候幾分鐘，然後再試一次。

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。 

若要瞭解如何接收資料，請繼續進行[接受和接收資料](subscribe-to-data-share.md)教學課程。

