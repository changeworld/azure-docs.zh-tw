---
title: 針對 Azure Data Share 進行疑難排解
description: 瞭解如何在使用 Azure Data Share 建立或接收資料共用時，對邀請和錯誤的問題進行疑難排解。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: c93ce9c81ada3c30128846b43041603e132abd88
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617233"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>針對 Azure Data Share 的常見問題進行疑難排解 

本文說明如何針對 Azure Data Share 的常見問題進行疑難排解。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀請 

在某些情況下，當新使用者按一下已傳送電子郵件邀請中的 [ **接受邀請** ] 時，可能會顯示空白的邀請清單。 

![沒有邀請](media/no-invites.png)

這可能是下列原因所造成的：

* **Azure Data Share 服務未註冊為 Azure 租用戶中任何 Azure 訂用帳戶的資源提供者。** 如果您的 Azure 租用戶中沒有 Data Share 資源，您將會遇到此問題。 Azure Data Share 資源會在建立時，自動於您的 Azure 訂用帳戶中註冊資源提供者。 您也可以依照下列步驟，手動註冊 Data Share 服務。 您必須擁有 Azure 參與者角色才能完成這些步驟。

    1. 在 Azure 入口網站中，瀏覽到 [訂用帳戶]。
    1. 選取您想要用來建立 Azure Data Share 資源的訂用帳戶
    1. 按一下 [資源提供者]
    1. 搜尋 **Microsoft.DataShare**
    1. 按一下 [註冊] 

    您必須擁有 azure 訂用帳戶的 [Azure 參與者角色](../role-based-access-control/built-in-roles.md#contributor) ，才能完成這些步驟。 

* **邀請會傳送至您的電子郵件別名，而不是您的 Azure 登入電子郵件。** 如果您已註冊 Azure Data Share 服務，或已在 Azure 租用戶中建立 Data Share 資源，但仍無法看到邀請，可能是因為提供者將您的電子郵件別名輸入為收件者，而不是輸入您的 Azure 登入電子郵件地址。 請連絡您的資料提供者，並確保他們是將邀請傳送到您的 Azure 登入電子郵件地址，而不是您的電子郵件別名。

* **已接受邀請。** 電子郵件中的連結會帶您前往 Azure 入口網站中的 Data Share 邀請頁面，其中只會列出擱置中的邀請。 如果您已經接受邀請，該邀請就不會再顯示在 [Data Share 邀請] 頁面中。 請繼續前往您用來接受邀請的 Data Share 資源，以檢視已接收的共用，並設定您的目標 Azure 資料總管叢集設定。

## <a name="error-when-creating-or-receiving-a-new-share"></a>建立或接收新的共用時發生錯誤

「無法新增資料集」

「無法對應資料集」

「無法將 Data Share 資源 x 存取權授與 y」

「您沒有 x 的適當許可權」

「我們無法將 Azure Data Share 帳戶的寫入權限新增至您選取的一或多個資源」

如果您在建立新的共用、新增資料集或對應資料集時收到上述任何錯誤，可能是因為許可權不足而無法使用 Azure 資料存放區。 請參閱 [角色和](concepts-roles-permissions.md) 必要許可權的需求。 

您需要有寫入權限，才能共用或接收來自 Azure 資料存放區的資料，這通常存在於 **參與者** 角色中。 

如果這是您第一次從 Azure 資料存放區共用或接收資料，您也需要 *Microsoft. 授權/角色指派/寫入* 許可權，這通常存在於 **擁有** 者角色中。 即使您已建立 Azure 資料存放區資源，也不會自動成為資源的擁有者。 使用適當的許可權時，Azure Data Share 服務會自動將資料存放區的受控識別存取權授與 data Share 資源。 此程式可能需要幾分鐘的時間才會生效。 如果您因為此延遲而遇到失敗，請在幾分鐘後再試一次。

以 SQL 為基礎的共用需要額外的許可權。 如需必要條件的詳細清單，請參閱 [SQL 來源的共用](how-to-share-from-sql.md) 。

## <a name="snapshot-failed"></a>快照集失敗
快照集可能會因為各種原因而失敗。 您可以按一下快照集的開始時間，然後按一下每個資料集的狀態，以找到詳細的錯誤訊息。 以下是快照失敗的常見原因：

* Data Share 沒有從來源資料存放區讀取或寫入目標資料存放區的許可權。 如需詳細許可權需求，請參閱 [角色和需求](concepts-roles-permissions.md) 。 如果這是您第一次拍攝快照集，則可能需要幾分鐘的時間，才會將 Azure 資料存放區的存取權授與 Data Share 資源。 請稍候幾分鐘，然後再試一次。
* 防火牆會封鎖來源或目標資料存放區的 Data Share 連接。
* 共用資料集或來源或目標資料存放區會被刪除。

針對儲存體帳戶，以下是快照集失敗的額外原因。

* 正在進行快照集時，正在更新來源的檔案。 這可能會導致目標有0位元組的檔案。 在來源上完成更新之後的後續快照應該會成功。

針對 SQL 來源，以下是快照集失敗的額外原因。 

* 無法執行授與 Data Share 許可權的來源或目標 SQL 腳本。 或者，對於先前的 Azure SQL DW) Azure SQL Database 或 Azure Synapse Analytics (，它會使用 SQL 驗證來執行，而不是 Azure Active Directory 驗證。  
* 來源或目標 SQL 資料存放區已暫停。
* 快照集進程或目標資料存放區不支援 SQL 資料類型。 請參閱 [SQL 來源的共用](how-to-share-from-sql.md#supported-data-types) 以取得詳細資料。
* 來源或目標 SQL 資料存放區已被其他進程鎖定。 Azure Data Share 不會將鎖定套用到來源和目標 SQL 資料存放區。 不過，來源和目標 SQL 資料存放區上的現有鎖定會導致快照集失敗。
* 目標 SQL 資料表是由 foreign key 條件約束所參考。 在快照集期間，如果存在具有相同名稱的目標資料表，Azure Data Share 會卸載資料表，並建立新的資料表。 如果 foreign key 條件約束參考目標 SQL 資料表，則無法卸載資料表。
* 會產生目標 CSV 檔案，但無法在 Excel 中讀取資料。 當來源 SQL 資料表包含非英文字元的資料時，就可能發生這種情況。 在 Excel 中，選取 [取得資料] 索引標籤並選擇 CSV 檔案，然後選取 [檔案來源] 為65001： Unicode (UTF-8) 並載入資料。

## <a name="snapshot-issue-after-updating-snapshot-schedule"></a>更新快照集排程之後的快照集問題
資料提供者更新已傳送共用的快照集排程之後，資料取用者必須停用先前的快照集排程，並重新啟用所接收共用的已更新快照集排程。 

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。 

若要瞭解如何接收資料，請繼續進行「 [接受和接收資料](subscribe-to-data-share.md) 」教學課程。