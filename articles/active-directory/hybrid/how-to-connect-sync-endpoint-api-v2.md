---
title: Azure AD Connect 同步 V2 端點公開預覽 | Microsoft Docs
description: 本文件涵蓋 Azure AD Connect 同步 V2 端點 API 的更新。
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4eba1b48b651c8efe9e9d737e226727cb244fb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662478"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect sync V2 端點 API (公開預覽) 
Microsoft 已為 Azure AD Connect 部署了新的端點 (API)，以提升 Azure Active Directory 同步處理服務作業的效能。 藉由使用新的 V2 端點，您將會在匯出和匯入至 Azure AD 時，遇到顯著的效能提升。 這個新的端點支援下列項目：
    
 -  同步處理最多 250k 個成員的群組
 - 匯出和匯入至 Azure AD 的效能提升
 
> [!NOTE]
> 目前，新的端點對於回寫的 Microsoft 365 群組沒有設定的群組大小限制。 這可能會對您的 Active Directory 和同步處理週期延遲有所影響。 建議您以累加方式增加群組大小。  


## <a name="pre-requisites"></a>必要條件  
若要使用新的 V2 端點，您必須使用 [Azure AD Connect 1.5.30.0 版](https://www.microsoft.com/download/details.aspx?id=47594)或更新版本，並遵循下面提供的部署步驟，為您的 Azure AD Connect 伺服器啟用 V2 端點。   

>[!NOTE]
>此公開預覽目前僅適用於 Azure 全球雲端，不適用於[國家雲端](../develop/authentication-national-cloud.md)。

### <a name="public-preview-limitations"></a>公開預覽限制  
雖然此版本經過大量測試，但您可能仍會遇到問題。 此公開預覽版本的其中一個目標，是尋找並修正任何這類的問題。  

>[!IMPORTANT]
> 雖然提供此公開預覽版本的支援，但 Microsoft 不一定能夠立即修正您可能遇到的所有問題。 基於這個理由，建議您在實際執行環境中部署此版本之前，先運用您的最佳判斷。 

## <a name="deployment-guidance"></a>部署指導 
您將需要部署 [Azure AD Connect 1.5.30.0 版](https://www.microsoft.com/download/details.aspx?id=47594)或更新版本，才能使用 V2 端點。 使用所提供的連結進行下載。 

建議您遵循[變換移轉](./how-to-upgrade-previous-version.md#swing-migration)方法，在您的環境中推出新的端點。 這會在事件中提供清楚的應變計劃，而這是必要的復原。 下列範例說明如何在此情節中使用變換移轉。 如需變換移轉部署方法的詳細資訊，請參閱提供的連結。 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>部署 V2 端點的變換移轉
下列步驟將引導您使用變換方法來部署 V2 端點。

1. 在目前的暫存伺服器上部署 V2 端點。 在下列步驟中，此伺服器將會稱為 **V2 伺服器**。 目前作用中的伺服器將會繼續使用 V1 端點來處理生產工作負載，其將會稱為以下的 **V1 伺服器**。
1. 驗證 **V2 伺服器**仍如預期般處理匯入。 在這個階段中，大型群組將不會佈建到 Azure AD 或內部部署 AD，但是您可以確認升級未導致對現有同步處理程序造成任何其他非預期的影響。 
2. 驗證完成後，請將 **V2 伺服器**切換為作用中伺服器，而 **V1 伺服器**為暫存伺服器。 此時，要同步處理範圍內的大型群組將會布建到 Azure AD，而且如果啟用群組回寫，則會將大型 Microsoft 365 整合群組布建到 AD。
3. 驗證 **V2 伺服器**是否成功執行及處理大型群組。 您可以選擇繼續進行此步驟，並監視同步處理程序一段期間。
  >[!NOTE]
  > 如果您需要轉換回先前的設定，可以從 **V2 伺服器**執行變換移轉回到 **V1 伺服器**。 由於 V1 端點不支援具有超過 50k 個成員的群組，因此任何由 Azure AD Connect 所佈建的大型群組 (在 Azure AD 或內部部署 AD 中) 隨後都會予以刪除。 
4. 一旦您確信使用 V2 端點後，請升級 **V1 伺服器**以開始使用 V2 端點。 
 

## <a name="expectations-of-performance-impact"></a>對效能影響的期望  
使用 V2 端點時，效能提升是同步群組數目的功能、這些群組的大小，以及其群組變換 (以群組成員的身分產生新增及移除使用者的活動)。 使用新的端點而不增加同步群組的數目、大小或變換量，應該會導致匯出和匯入 Azure AD 的時間較短。 
 
不過，在同步處理大型群組時所需的其他處理，可能會否定效能提升。 最後，您可以藉由將太多大型群組新增至同步處理程序來增加整體同步時間。  

若要進一步了解新增群組會如何影響您的同步處理效能，建議您一開始只同步幾個低於 10 萬個成員的大型群組。 接著，您可以透過 OU、屬性或最大群組大小篩選，在範圍中帶入更多群組來增加群組的數目和大小。 在 Azure AD 連接器 (而非內部部署 AD 連接器) 的匯出和匯入工作上，會實現效能改進。 

## <a name="deployment-step-by-step"></a>逐步部署 
下列三個階段是部署新 V2 端點的深入範例。  使用階段做為部署的指導方針。

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>第 1 階段 – 安裝及驗證 Azure AD Connect 
建議您先執行步驟來安裝或升級至 [Azure AD Connect 1.5.30.0 版](https://www.microsoft.com/download/details.aspx?id=47594)或更新版本，然後在移至您將啟用 V2 端點的第二個階段之前，先驗證同步處理程序。 在 Azure AD Connect 伺服器上： 


1. [選擇性] 進行資料庫備份 
2. 安裝或升級至 [Azure AD Connect 1.5.30.0 版](https://www.microsoft.com/download/details.aspx?id=47594)或更新版本。
3. 驗證安裝 

### <a name="phase-2--enable-the-v2-endpoint"></a>第 2 階段 – 啟用 V2 端點 
下一個步驟是啟用 V2 端點。 

> [!NOTE]
> 啟用伺服器的 V2 端點之後，您將能夠看到現有工作負載的一些效能改進。 不過，您還無法同步大於 50K 個成員的群組。 

若要切換至 V2 端點，請使用下列步驟： 

1. 以系統管理員身分開啟 PowerShell 提示字元。 
2. 確認沒有任何同步處理作業正在執行之後，請停用同步排程器： 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. 匯入新的模組： 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  切換至 v2 端點：

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
現在您已啟用伺服器的 V2 端點。 在您移至下一個階段 (將增加群組大小限制) 之前，請花一點時間確認在啟用 V2 端點之後，沒有任何非預期的結果。 
>[!NOTE]
>檔案/模組路徑可能會使用不同的磁碟機代號，視安裝 Azure AD Connect 時所提供的安裝路徑而定。 


### <a name="phase-3--increase-the-group-membership-limit"></a>第 3 階段 – 增加群組成員資格限制 
在您確認服務執行時沒有任何非預期的結果之後，就可以繼續提高群組成員資格的限制。 建議先將成員資格限制提高到稍微高一點的值，例如 75K 個成員，以查看同步至 Azure AD 的群組較大。 在您對結果感到滿意之後，可進一步提高成員限制。  

最大限制是每個群組 250K 個成員。 

您可以使用下列步驟來增加成員資格限制：  

1. 開啟 Azure AD 同步處理規則編輯器 
2. 在編輯器中，針對 [方向] 選擇 [輸出] 
3. 按一下 [Out to AAD – Group Join] 同步處理規則 
4. 按一下 [編輯] 按鈕 ![編輯同步處理規則](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. 按一下 [是] 按鈕來停用預設規則，並建立可編輯的複本。
 ![編輯同步處理規則](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. 在 [描述] 頁面的快顯視窗中，將優先順序設定為介於 1 到 99 之間的可用值![編輯同步處理規則](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. 在 [轉換] 頁面上，更新 [成員] 轉換的 [來源] 值，並以 50001 與 250000 之間的值取代 ‘50000’。 這項取代作業會增加將同步至 Azure AD 之群組的成員資格大小上限。 我們建議從 100k 的數字開始，以了解同步處理大型群組對您同步效能所造成的影響。 
 
 **範例** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![編輯同步處理規則](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. 按一下 [Save] \(儲存)。 
10. 開啟系統管理 PowerShell 提示字元 
11. 重新啟用同步排程器 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> 如果未啟用 Azure AD Connect Health，請將 Windows 應用程式事件記錄檔設定變更為封存記錄，而非加以覆寫。 記錄可用來協助未來的疑難排解投入量。 

>[!NOTE]
> 啟用新端點之後，您可能會在 AAD 連接器上看到名為「dn-attributes-failur」的其他匯出錯誤。 針對識別碼為 6949 的每個錯誤，將會有對應的事件記錄項目。 錯誤僅供參考且不會指出您安裝上的問題，而是同步處理程序無法將特定成員新增至 Azure AD 中的群組，因為成員物件本身並未同步到 Azure AD。 

新的 V2 端點程式碼處理某些類型匯出錯誤的方式，會與 V1 程式碼的執行方式稍有不同。  當您使用 V2 端點時，可能會看到更多的參考用錯誤訊息。 

>[!NOTE]
> 升級 Azure AD Connect 時，請確定第 2 階段中的步驟重新執行，因為變更不會透過升級程序予以保留。 

在後續增加 [Out to AAD – Group Join] 同步處理規則中的群組成員限制期間，不需要完整同步處理，因此您可以在 PowerShell 中執行下列命令，藉此選擇隱藏完整同步處理。 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> 如果您 Microsoft 365 的統一群組擁有超過50k 的成員，則會將群組讀入 Azure AD Connect，而且如果啟用群組回寫，則會將它們寫入您的內部部署 AD。 

## <a name="rollback"></a>復原 
如果您已啟用 v2 端點且需要復原，請遵循下列步驟： 

1. 在 Azure AD Connect 伺服器上：a. [選擇性] 進行資料庫備份 
2. 開啟系統管理 PowerShell 提示字元：
3. 確認沒有任何同步處理作業正在執行之後，請停用同步排程器
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. 切換至 V1 端點 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. 開啟 Azure AD 同步處理規則編輯器 
6. 刪除 [Out to AAD – Group Join] 同步處理規則的可編輯複本 
7. 啟用 [Out to AAD – Group Join] 同步處理規則的預設複本 
8. 開啟系統管理 PowerShell 提示字元 
9. 重新啟用同步排程器 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> 從 V2 切換回 V1 端點時，在執行完整同步處理之後，將會刪除以超過50k 的成員進行同步處理的群組，並針對布建至 Azure AD 的 AD 群組，以及布建至 AD 的 Microsoft 365 統一群組。 

## <a name="frequently-asked-questions"></a>常見問題集  
**問：客戶可以在生產環境中使用這項功能嗎？**   
</br>可以，這可以在實際執行環境中使用，需注意先前所述的警告。
 
**問：當發生問題時，客戶可以聯絡誰？**   
</br>如果您在使用這項功能時需要支援，應該開啟支援案例。 
 
**問：我是否可預期會經常更新公開預覽？**   
</br>在公開預覽期間進行中的變更有一定限度。 在生產環境中部署公開預覽功能時，您應該評估這項風險。  
 
**問：到下一個里程碑需要多少時間？**   
</br>公開預覽功能可能會撤銷，且可能會在到達進一步里程碑之前進行重新設計。  
 
## <a name="next-steps"></a>後續步驟

* [Azure AD Connect 同步：了解並自訂同步處理](how-to-connect-sync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)