---
title: 針對 Azure Data Share 進行疑難排解
description: 瞭解如何在 Azure Data Share 中建立或接收資料共用時，對邀請和錯誤的問題進行疑難排解。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964502"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>針對 Azure Data Share 中的常見問題進行疑難排解 

本文說明如何針對 Azure Data Share 中的常見問題進行疑難排解。 

## <a name="azure-data-share-invitations"></a>Azure Data Share 邀請 

在某些情況下，當新使用者選取 [接受電子郵件邀請中的 **邀請** ] 時，他們可能會看到空白的邀請清單。 

:::image type="content" source="media/no-invites.png" alt-text="顯示邀請清單空白的螢幕擷取畫面。":::

這個問題可能是下列其中一個原因所造成：

* **Azure Data Share 服務未註冊為 Azure 租使用者中任何 Azure 訂用帳戶的資源提供者。** 當您的 Azure 租使用者沒有 Data Share 資源時，就會發生此問題。 

    Azure Data Share 資源會在建立時，自動於您的 Azure 訂用帳戶中註冊資源提供者。 您可以使用下列步驟，手動註冊 Data Share 服務。 若要完成這些步驟，您需要 Azure 訂用帳戶的「 [參與者」角色](../role-based-access-control/built-in-roles.md#contributor) 。 

    1. 在 Azure 入口網站中，前往 **訂用帳戶**。
    1. 選取您要用來建立 Azure Data Share 資源的訂用帳戶。
    1. 選取 **資源提供者**。
    1. 搜尋 **microsoft.datashare**。
    1. 選取 [註冊]。

* **邀請會傳送至您的電子郵件別名，而不是您的 Azure 登入電子郵件地址。** 如果您已註冊 Azure Data Share 服務或在 Azure 租使用者中建立 Data Share 資源，但仍看不到該邀請，則您的電子郵件別名可能會列為收件者。 請洽詢您的資料提供者，並確定邀請會傳送至您的 Azure 登入電子郵件地址，而不是您的電子郵件別名。

* **邀請已被接受。** 電子郵件中的連結會帶您前往 Azure 入口網站中的 **Data Share 邀請** 頁面。 此頁面只會列出擱置中的邀請。 頁面上沒有顯示接受的邀請。 若要查看已接收的共用並設定您的目標 Azure 資料總管叢集設定，請移至您用來接受邀請的 Data Share 資源。

## <a name="creating-and-receiving-shares"></a>建立和接收共用

當您建立新的共用、新增資料集或對應資料集時，可能會出現下列錯誤：

* 無法新增資料集。
* 無法對應資料集。
* 無法授與 Data Share 資源 x 對 y 的存取權。
* 您沒有 x 的適當許可權。
* 我們無法將 Azure Data Share 帳戶的寫入權限新增至您選取的一或多個資源。

如果您沒有 Azure 資料存放區的足夠許可權，您可能會看到其中一個錯誤。 如需詳細資訊，請參閱 [角色和需求](concepts-roles-permissions.md)。 

您需要有寫入權限，才能共用或接收來自 Azure 資料存放區的資料。 此許可權通常是參與者角色的一部分。 

如果您是第一次從 Azure 資料存放區共用資料或接收資料，您也需要 *Microsoft 授權/角色指派/寫入* 許可權。 此許可權通常是擁有者角色的一部分。 即使您已建立 Azure 資料存放區資源，您也不一定是資源的擁有者。 

當您擁有適當的許可權時，Azure Data Share 服務會自動允許資料共用資源的受控識別存取資料存放區。 此程序可能需要幾分鐘的時間。 如果您因為此延遲而遇到失敗，請在幾分鐘後再試一次。

以 SQL 為基礎的共用需要額外的許可權。 如需必要條件的詳細資訊，請參閱 [從 SQL 來源共用](how-to-share-from-sql.md)。

## <a name="snapshots"></a>快照集
快照集可能會因各種原因而失敗。 選取快照集的開始時間，然後選取每個資料集的狀態，以開啟詳細的錯誤訊息。 

快照集通常會因為下列原因而失敗：

* Data Share 缺少讀取來源資料存放區或寫入目標資料存放區的許可權。 如需詳細資訊，請參閱 [角色和需求](concepts-roles-permissions.md)。 如果您是第一次拍攝快照集，Data Share 資源可能需要幾分鐘的時間才能存取 Azure 資料存放區。 幾分鐘後再試一次。
* 與來源資料存放區或目標資料存放區的 Data Share 連接已被防火牆封鎖。
* 已刪除共用資料集、來源資料存放區或目標資料存放區。

若為儲存體帳戶，快照集可能會失敗，因為在發生快照集時，檔案會在來源上更新。 因此，目標上可能會出現0位元組的檔案。 在來源更新之後，快照集應該會成功。

針對 SQL 來源，快照集可能會因下列其他原因而失敗：

* 授與 Data Share 許可權的來源 SQL 腳本或目標 SQL 腳本尚未執行。 或者，對於先前的 Azure SQL 資料倉儲) Azure SQL Database 或 Azure Synapse Analytics (，腳本會使用 SQL 驗證來執行，而不是 Azure Active Directory 驗證。  
* 來源資料存放區或目標 SQL 資料存放區已暫停。
* 快照集進程或目標資料存放區不支援 SQL 資料類型。 如需詳細資訊，請參閱 [從 SQL 來源共用](how-to-share-from-sql.md#supported-data-types)。
* 來源資料存放區或目標 SQL 資料存放區已被其他進程鎖定。 Azure Data Share 不會鎖定這些資料存放區。 但是這些資料存放區上的現有鎖定可能會導致快照集失敗。
* 目標 SQL 資料表是由 foreign key 條件約束所參考。 在快照集期間，如果目標資料表的名稱與來源資料中的資料表相同，Azure Data Share 會卸載資料表，並建立新的資料表。 如果 foreign key 條件約束參考目標 SQL 資料表，則無法卸載資料表。
* 會產生目標 CSV 檔案，但無法在 Excel 中讀取資料。 當來源 SQL 資料表包含包含非英文字元的資料時，您可能會看到此問題。 在 Excel 中，選取 [ **取得資料** ] 索引標籤，然後選擇 CSV 檔案。 選取檔案來源 **65001： Unicode (utf-8)**，然後載入資料。

## <a name="updated-snapshot-schedules"></a>已更新快照集排程
在資料提供者更新已傳送共用的快照集排程之後，資料取用者必須停用先前的快照集排程。 然後為已接收的共用啟用已更新的快照集排程。 

## <a name="next-steps"></a>後續步驟

若要瞭解如何開始共用資料，請繼續進行 [共用資料](share-your-data.md) 教學課程。 

若要瞭解如何接收資料，請繼續進行「 [接受和接收資料](subscribe-to-data-share.md) 」教學課程。