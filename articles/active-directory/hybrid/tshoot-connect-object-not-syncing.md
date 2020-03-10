---
title: 針對未與 Azure Active Directory 同步的物件進行疑難排解 | Microsoft Docs
description: 針對未與 Azure Active Directory 同步的物件進行疑難排解。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376189"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>針對未與 Azure Active Directory 同步的物件進行疑難排解

如果物件未如預期般與 Microsoft Azure Active Directory (Azure AD) 同步，則可能由數個原因所造成。 如果您從 Azure AD 收到錯誤電子郵件，或是在 Azure AD Connect Health 中看到錯誤，請改為閱讀[針對同步期間的錯誤進行疑難排解](tshoot-connect-sync-errors.md)。 但如果您要針對物件不在 Azure AD 中的問題進行疑難排解，則本文正適合您。 它說明如何尋找內部部署元件 Azure AD Connect 同步中的錯誤。

>[!IMPORTANT]
>對於 1.1.749.0 版或更新版本的 Azure AD Connect 部署，請在精靈中使用[疑難排解工作](tshoot-connect-objectsync.md)，針對物件同步處理問題進行疑難排解。 

## <a name="synchronization-process"></a>同步處理程序

調查同步處理問題之前，讓我們先了解 Azure AD Connect 同步處理程序：

  ![Azure AD Connect 同步處理程序的圖表](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**詞彙**

* **CS：** 連接器空間，資料庫中的資料表
* **MV：** 中繼資料，資料庫中的資料表

### <a name="synchronization-steps"></a>**同步處理步驟**
同步處理程序包含下列步驟：

1. **從 AD 匯入：** Active Directory 物件會帶入 Active Directory CS。

2. **從 Azure AD 匯入：** Azure AD 物件會帶入 Azure AD CS。

3. **同步處理：** 輸入同步處理規則和輸出同步處理規則會依優先順序編號的順序（從低到高）執行。 若要檢視同步處理規則，請從桌面應用程式移至 [同步處理規則編輯器]。 輸入同步處理規則會將資料從 CS 帶入 MV。 輸出同步處理規則會將資料從 MV 帶入 CS。

4. **匯出至 AD：** 同步處理之後，物件會從 Active Directory CS 匯出到 Active Directory。

5. **匯出至 Azure AD：** 同步處理之後，物件會從 Azure AD CS 匯出到 Azure AD。

## <a name="troubleshooting"></a>疑難排解

若要找出錯誤，請依下列順序查看幾個不同的地方：

1. [作業記錄](#operations)，尋找同步引擎在匯入和同步處理期間所識別的錯誤。
2. [連接器空間](#connector-space-object-properties)，尋找遺失的物件和同步處理錯誤。
3. [Metaverse](#metaverse-object-properties)，尋找資料相關問題。

請先啟動 [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md)，再開始進行這些步驟。

## <a name="operations"></a>作業
Synchronization Service Manager 中的 [作業] 索引標籤是您應該開始進行疑難排解的地方。 此索引標籤顯示最新作業的結果。 

![Synchronization Service Manager 的螢幕擷取畫面，其中顯示選取的 [作業] 索引標籤](./media/tshoot-connect-object-not-syncing/operations.png)  

[作業] 索引標籤的上半部會依時間順序顯示所有執行。 根據預設，作業記錄會保留最後 7 天的相關資訊，但是您可以利用 [排程器](how-to-connect-sync-feature-scheduler.md)來變更此設定。 尋找任何未顯示**成功**狀態的執行。 您可以按一下標頭來變更排序。

[狀態] 資料行包含最重要的資訊，並顯示最嚴重的執行問題。 以下快速摘要依照調查優先順序排列的最常見狀態 (其中 * 表示數個可能的錯誤字串)。

| 狀態 | 註解 |
| --- | --- |
| stopped-* |執行無法完成。 這可能會發生，例如，如果遠端系統已關閉且無法聯繫。 |
| stopped-error-limit |有 5,000 個以上的錯誤。 執行因錯誤數量過多而自動停止。 |
| completed-\*-errors |執行完成，但發生應調查的錯誤 (數量少於 5,000 個)。 |
| completed-\*-warnings |執行完成，但某些資料並未處於預期的狀態。 如果您遇到錯誤，此訊息通常只是一個徵狀。 在解決錯誤之前，請勿調查警告。 |
| 成功 |沒有問題。 |

當您選取資料列時，[作業] 索引標籤的底部會更新以顯示該執行的詳細資料。 在此區域的最左邊端，您可能有標題為 [步驟 #] 的清單。 只有當您的樹系中有多個網域，而每個網域都是以一個步驟表示，才會顯示此清單。 您可以在 [分割區]標題下方找到網域名稱。 在 [同步處理統計資料] 標題下方，您可以找到關於已處理的變更次數詳細資訊。 選取連結，以取得變更的物件清單。 如果您有物件發生錯誤，這些錯誤會顯示於 [同步處理錯誤] 標題下方。

### <a name="errors-on-the-operations-tab"></a>[作業] 索引標籤上的錯誤
當您遇到錯誤時，Synchronization Service Manager 會顯示發生錯誤的物件，而錯誤本身會顯示為可提供更多資訊的連結。

![Synchronization Service Manager 錯誤的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/errorsync.png)  
首先選取錯誤字串。 （在上圖中，錯誤字串為 [**同步-規則-錯誤**-函式-已觸發]）。您會先看到物件的總覽。 若要查看實際錯誤，請選取 [堆疊追蹤]。 此追蹤會提供錯誤的偵錯層級資訊。

在 [呼叫堆疊資訊] 方塊上按一下滑鼠右鍵、按一下 [全選]，然後選取 [複製]。 接著複製堆疊，並在您喜好的編輯器 (例如 [記事本]) 中查看此錯誤。

如果錯誤來自 **SyncRulesEngine**，則呼叫堆疊資訊會先列出物件上的所有屬性。 向下捲動，直到您看到 **InnerException =>** 標題為止。  

  ![Synchronization Service Manager 的螢幕擷取畫面，其中顯示 InnerException = > 標題之下的錯誤資訊](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
標題之後的行會顯示錯誤。 在上圖中，錯誤是來自 Fabrikam 所建立的自訂同步處理規則。

如果錯誤未提供足夠的資訊，則應該查看資料本身。 選取帶有物件識別碼的連結，然後繼續針對[連接器空間匯入的物件](#cs-import)進行疑難排解。

## <a name="connector-space-object-properties"></a>連接器空間物件屬性
如果 [ **[作業]** ](#operations) 索引標籤未顯示任何錯誤，請依循連接器空間物件從 Active Directory 到 Metaverse，再到 Azure AD。 在此路徑中，您應該會找到問題的所在。

### <a name="searching-for-an-object-in-the-cs"></a>在 CS 中搜尋物件

在 Synchronization Service Manager 中，選取 [連接器]、選取 [Active Directory 連接器]，然後選取 [搜尋連接器空間]。

在 [範圍] 方塊中，選取 [RDN] (當您想要依據 CN 屬性進行搜尋時)，或選取 [DN 或錨點] (當您想要依據 **distinguishedName** 屬性進行搜尋時)。 輸入一個值並選取 [搜尋]。 
 
![連接器空間搜尋的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/cssearch.png)  

如果您找不到所要尋找的物件，它可能已被[網域型篩選](how-to-connect-sync-configure-filtering.md#domain-based-filtering)或 [OU 型篩選](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)所篩選掉。 若要確認篩選已如預期般設定，請閱讀[Azure AD Connect 同步：設定篩選](how-to-connect-sync-configure-filtering.md)。

您可選取 Azure AD 連接器以執行另一個實用的搜尋。 在 [範圍] 方塊中，選取 [擱置匯入]，然後選取 [新增] 核取方塊。 此搜尋會提供您 Azure AD 中無法與內部部署物件建立關聯的所有已同步處理物件。  

![連接器空間搜尋中孤立物件的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
這些物件是由另一個同步引擎所建立，或是由具有不同篩選組態的同步引擎所建立。 這些孤立物件不再受管理。 檢閱此清單，然後考慮使用 [Azure AD PowerShell](https://aka.ms/aadposh) Cmdlet 來移除這些物件。

### <a name="cs-import"></a>CS 匯入
當您開啟 CS 物件時，頂端會出現數個索引標籤。 [匯入] 索引標籤會顯示匯入後暫存的資料。  

![[連接器空間物件屬性] 視窗的螢幕擷取畫面，其中已選取 [匯入] 索引標籤](./media/tshoot-connect-object-not-syncing/csobject.png)    

[舊值] 資料行顯示目前儲存在 Connect 中的值，而 [新值] 資料行則顯示從來源系統收到但尚未套用的值。 如果物件上發生錯誤，系統便不會處理變更。

只有當物件發生問題時，[連接器空間物件屬性] 視窗中才會顯示 [同步處理錯誤] 索引標籤。 如需詳細資訊，請檢閱如何[在 [作業] 索引標籤上針對同步處理錯誤進行疑難排解](#errors-on-the-operations-tab)。

![[連接器空間物件屬性] 視窗中 [同步處理錯誤] 索引標籤的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS 歷程
[連接器空間物件屬性] 視窗中的 [歷程] 索引標籤會顯示連接器空間物件與 Metaverse 物件的關聯方式。 您可以看到連接器上次從連接的系統匯入變更的時間，以及套用哪些規則以便在 metaverse 中填入資料。  

![在 [連接器空間物件屬性] 視窗中顯示 [歷程] 索引標籤的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/cslineage.png)  

在上圖中，[動作] 資料行會顯示 [佈建] 動作的輸入同步處理規則。 這表示，只要此連接器空間物件存在，就會保留 metaverse 物件。 如果同步處理規則清單顯示具有 [佈建] 動作的輸出同步處理規則，這表示刪除 Metaverse 物件時會刪除此物件。  

![[連接器空間物件屬性] 視窗中 [歷程] 索引標籤上歷程視窗的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

在上圖中，您也可以在 [PasswordSync] 資料行中發現輸入連接器空間可進行密碼變更，因為有一個同步處理規則的值為 **True**。 此密碼會透過輸出規則傳送至 Azure AD。

從 [歷程] 索引標籤，您可以選取 [**Metaverse 物件屬性**](#mv-attributes)以移至 Metaverse。

### <a name="preview"></a>預覽
[連接器空間物件屬性] 視窗的左下角有 [預覽] 按鈕。 選取此按鈕可開啟 [預覽] 頁面，您可以在其中同步單一物件。 如果您正在針對某些自訂的同步處理規則進行疑難排解，而想要查看變更對單一物件的影響，此頁面很有用。 您可以選取 [**完整同步**處理] 或 [**差異同步**處理]。您也可以選取 [**產生預覽**]，這只會在記憶體中保留變更。 或選取 [認可預覽]，這會更新 Mataverse 並暫存對目標連接器空間所做的一切變更。  

![[預覽] 頁面的螢幕擷取畫面，其中已選取 [開始預覽]](./media/tshoot-connect-object-not-syncing/preview.png)  

在預覽中，您可以檢查物件，以及查看哪個規則適用於特定的屬性流程。  

![[預覽] 頁面的螢幕擷取畫面，其中顯示匯入屬性流程](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
在 [預覽] 按鈕旁邊，選取 [記錄] 按鈕來開啟 [記錄] 頁面。 您可以在此查看密碼同步狀態和歷程記錄。 如需詳細資訊，請參閱[使用 Azure AD Connect 同步針對密碼雜湊同步處理進行疑難排解](tshoot-connect-password-hash-synchronization.md)。

## <a name="metaverse-object-properties"></a>Metaverse 物件屬性
通常最好是從來源 Active Directory 連接器空間開始搜尋。 但是您也可以從 Metaverse 開始搜尋。

### <a name="searching-for-an-object-in-the-mv"></a>在 MV 中搜尋物件
在 Synchronization Service Manager 中，選取 [Metaverse 搜尋]，如下圖所示。 建立一個您知道可找到使用者的查詢。 搜尋通用屬性，例如 **accountName** (**sAMAccountName**) 和 **userPrincipalName**。 如需詳細資訊，請參閱 [Sync Service Manager Metaverse 搜尋](how-to-connect-sync-service-manager-ui-mvsearch.md)。

![Synchronization Service Manager 的螢幕擷取畫面，其中已選取 [Metaverse 搜尋] 索引標籤](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

在 [搜尋結果] 視窗中，按一下該物件。

如果您沒有找到該物件，表示它尚未抵達 Metaverse。 請繼續在 Active Directory [連接器空間](#connector-space-object-properties)中搜尋該物件。 如果您在 Active Directory 連接器空間中發現該物件，則可能有阻止物件到達 Metaverse 的同步處理錯誤，或是可能套用了某個同步處理規則範圍篩選器。

### <a name="object-not-found-in-the-mv"></a>MV 中找不到物件
如果物件位於 Active Directory CS 中，但是不存在於 MV 中，則會套用範圍篩選器。 若要查看範圍篩選器，請移至桌面應用程式的功能表，並選取 [同步處理規則編輯器]。 藉由調整以下篩選器，來篩選物件適用的規則。

  ![[同步處理規則編輯器] 的螢幕擷取畫面，其中顯示輸入同步處理規則搜尋](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

檢視上述清單中的每個規則，並檢查**範圍篩選器**。 在下列範圍篩選器中，如果 **isCriticalSystemObject** 值為 Null、FALSE 或空白，則其在範圍內。

  ![輸入同步處理規則搜尋中範圍篩選器的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

移至 [CS 匯入](#cs-import)屬性清單，並檢查是哪個篩選器阻止物件移到 MV。 [連接器空間] 屬性清單只會顯示非 Null 或非空白的屬性。 例如，如果 **isCriticalSystemObject** 未在清單中顯示出來，則此屬性的值為 Null 或空白。

### <a name="object-not-found-in-the-azure-ad-cs"></a>Azure AD CS 中找不到物件
如果物件不存在於 Azure AD 的連接器空間中，卻存在於 MV 中，請查看對應連接器空間的輸出規則範圍篩選器，並找出該物件是否因為 [MV屬性](#mv-attributes)不符合準則而被篩選掉。

若要查看輸出範圍篩選器，請藉由調整以下篩選器來選取該物件適用的規則。 檢視每個規則，並查看對應的 [MV 屬性](#mv-attributes)值。

  ![同步處理規則編輯器中輸出同步處理規則搜尋的螢幕擷取畫面](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV 屬性
在 [屬性] 索引標籤上，您可以看到一些值以及這些值是由哪些連接器所提供。  

![[Metaverse 物件屬性] 視窗的螢幕擷取畫面，其中已選取 [屬性] 索引標籤](./media/tshoot-connect-object-not-syncing/mvobject.png)  

如果物件並未進行同步處理，請在 Metaverse 中提供下列關於屬性狀態的問題：
- **cloudFiltered** 屬性是否存在且設定為 **True**？ 如果是，則表示已根據[屬性型篩選](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)中的步驟將它篩除。
- **sourceAnchor** 屬性是否存在？ 如果不存在，您是否擁有帳戶資源樹系拓撲？ 如果物件被識別為已連結的信箱 (**msExchRecipientTypeDetails** 屬性的值為 **2**)，則會由具有已啟用 Active Directory 帳戶的樹系提供 **sourceAnchor**。 請確定已正確匯入並同步處理主要帳戶。 主要帳戶必須列在物件的[連接器](#mv-connectors)當中。

### <a name="mv-connectors"></a>MV 連接器
[連接器] 索引標籤會顯示所有具有物件表示法的連接器空間。 
 
![[Metaverse 物件屬性] 視窗的螢幕擷取畫面，其中已選取 [連接器] 索引標籤](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

您應該要有連至下列各項的連接器：

- 每個呈現出使用者的 Active Directory 樹系。 這項呈現可以包括 **foreignSecurityPrincipals** 和 **Contact** 物件。
- Azure AD 中的連接器。

如果您沒有連至 Azure AD 的連接器，請檢閱 [MV 屬性](#mv-attributes)一節，以確認佈建到 Azure AD 所需符合的準則。

從 [連接器] 索引標籤，您也可以移至[連接器空間物件](#connector-space-object-properties)。 請選取一個資料列，然後按一下 [屬性]。

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- 深入了解[混合式身分識別](whatis-hybrid-identity.md)。
