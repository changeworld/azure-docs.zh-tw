---
title: 如何匯入和匯出 Azure AD Connect 的設定
description: 本文件說明雲端佈建的常見問題。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94d356cb3c0345f575b4b5a44aa7f228535e66d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019874"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>匯入和匯出 Azure AD Connect 設定（公開預覽） 

Azure AD Connect 部署會因單一樹系快速模式安裝而異，到使用自訂同步處理規則在多個樹系之間同步的複雜部署。 由於有大量的設定選項和機制，因此請務必瞭解有哪些設定生效，並且能夠快速部署具有相同設定的伺服器。 這項功能引進了將指定之同步處理伺服器的設定進行編目的功能，並將設定匯入新的部署中。 您可以比較不同的同步處理設定快照，輕鬆地將兩個伺服器之間的差異或一段時間內的相同伺服器視覺化。 

每次從 Azure AD Connect wizard 變更設定時，會將新時間戳記的 JSON 設定檔案自動匯出至 **%ProgramData%\AADConnect**。 設定檔案名的格式為**SynchronizationPolicy-*。JSON** ，其中檔案名的最後部分是時間戳記。 

> [!IMPORTANT]
> 只有 Azure AD Connect 所做的變更會自動匯出。 使用 PowerShell、Synchronization Service Manager 或同步處理規則編輯器所做的任何變更，都必須視需要匯出，以維持最新的複本。 [隨選匯出] 也可用來將設定複本放在安全的位置，以供嚴重損壞修復之用。 

## <a name="exporting-azure-ad-connect-settings"></a>匯出 Azure AD Connect 設定 

若要查看您的設定摘要，請開啟 [Azure AD Connect] 工具，然後選擇名為： View 或 Export Current Configuration 的其他工作。 系統會顯示您設定的快速摘要，以及匯出伺服器完整設定的功能。 

根據預設，設定會匯出至 **%ProgramData%\AADConnect**，不過您可以選擇將設定儲存到受保護的位置，以確保在發生嚴重損壞時的可用性。 設定是使用 JSON 檔案格式匯出，不應手動建立或編輯以確保邏輯一致性。 不支援匯入手動建立或編輯的檔案，而且可能會導致非預期的結果。 

## <a name="importing-azure-ad-connect-settings"></a>匯入 Azure AD Connect 設定 

若要匯入先前匯出的設定，請執行下列動作：
 
1. 在新的伺服器上安裝**Azure AD Connect** 。 
2. 選取 [**歡迎使用**] 頁面之後的 [**自訂**] 選項。 
3. 按一下 **[匯入同步處理設定**]。 流覽先前匯出的 json 設定檔案。  
4. 按一下 [安裝] 。

![安裝元件](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 請覆寫此頁面上的設定，例如使用 SQL Server 而不是 LocalDB，或使用現有的服務帳戶，而非預設的 VSA 等。這些設定不會從設定檔案匯入，而是基於資訊和比較目的。

### <a name="import-installation-experience"></a>匯入安裝體驗 

匯入安裝體驗會刻意保持簡單，並以最少的使用者輸入，輕鬆提供現有伺服器的重現性。  

以下是在安裝體驗期間唯一可以進行的變更。 所有其他變更都可以在從 Azure AD Connect wizard 安裝後進行。： 
- **Azure Active Directory 認證**  -預設會建議用來設定源伺服器之 Azure 全域管理員的帳戶名稱， ** **   如果您想要將資訊同步處理至新的目錄，則必須加以變更。
- **使用者登入**  –預設會選取針對您的源伺服器所設定的登入選項，並會在設定期間自動提示認證或其他所需的資訊。 在罕見的情況下，可能需要使用不同的選項來設定伺服器，以避免變更作用中伺服器的行為。 否則，只要按 [下一步]，即可使用相同的設定。 
- **內部部署目錄認證**  –針對您的同步處理設定中包含的每個內部部署目錄，您必須提供認證來建立同步處理帳戶，或提供預先建立的自訂同步處理帳戶。 此程式與全新安裝體驗相同，因為例外狀況您無法新增或移除目錄。 
- 設定**選項**  –如同全新安裝，您可以選擇設定是否要啟動自動同步處理或啟用預備模式的初始設定。 主要差異在於預備模式預設為刻意啟用，以便在將結果主動匯出至 Azure 之前，先允許比較設定和同步處理結果。 

![連線目錄](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 主要角色中只能有一部同步處理伺服器，並會主動將設定變更匯出至 Azure。 所有其他伺服器都必須置於預備模式。 

## <a name="migrating-settings-from-an-existing-server"></a>從現有的伺服器遷移設定 

如果現有的伺服器不支援設定管理，您可以選擇就地升級伺服器，或遷移設定以用於新的預備伺服器。  

遷移需要執行 PowerShell 腳本來解壓縮現有的設定，以便在新的安裝中使用。建議使用此方法，將現有伺服器的設定編目，然後將它們套用至新安裝的預備伺服器。將源伺服器的設定與新建立的伺服器進行比較，可以快速視覺化伺服器之間的變更。一如往常，請遵循您組織的認證程式，以確保不需要進行其他設定。  

### <a name="migration-process"></a>遷移程式 
若要遷移設定，請執行下列動作：

1. 在新的預備伺服器上啟動**AzureADConnect.msi** ，並在 Azure AD Connect 的歡迎頁面停止。

2. 將**MigrateSettings.ps1**從 Microsoft Azure AD Connect\Tools 目錄複寫到現有伺服器上的位置。  例如，C:\setup。  其中，安裝程式是在現有伺服器上建立的目錄。

   ![連線目錄](media/how-to-connect-import-export-config/migrate1.png)

3. 如下所示執行腳本，並儲存整個下層伺服器設定目錄。 將此目錄複寫到新的預備伺服器。 請注意，您必須將整個**匯出的-ServerConfiguration-*** 資料夾複製到新的伺服器。

   ![連接目錄 ](media/how-to-connect-import-export-config/migrate2.png)
    ![ 連接目錄](media/how-to-connect-import-export-config/migrate3.png)

5. 按兩下桌面上的圖示來啟動**Azure AD Connect** 。 接受 EULA，在下一個頁面上按一下 [**自訂**] 按鈕。 
6. 選取 [匯**入同步處理設定**] 核取方塊，然後按一下 [**流覽]** 按鈕以流覽已匯出的 ServerConfiguration-* 資料夾，然後選取 MigratedPolicy.js開啟以匯入遷移的設定。

   ![連線目錄](media/how-to-connect-import-export-config/migrate4.png)

7. 若要比較已遷移的設定與套用的設定，請尋找最新的已**遷移-SynchronizationPolicy-*。JSON**並套用 **-SynchronizationPolicy-*。** **%ProgramData%\AADConnect**下的 JSON （* 是時間戳記）。 使用您最愛的檔案比較工具來比較同位檢查。 

## <a name="post-installation-verification"></a>安裝後驗證 

比較原先匯入的設定檔案（連同匯出的設定檔案）新部署的伺服器，是瞭解所需的任何差異與產生的部署之間的重要步驟。 使用您最愛的並存文字比較應用程式會產生立即的視覺效果，以快速強調任何所需或意外的變更。 雖然目前已排除許多先前手動設定步驟，但您仍應遵循組織的認證程式，以確保不需要進行其他設定。 如果您使用的是 [設定管理] 公開預覽版本中目前未捕捉到的 [高級設定]，則可能會發生此設定。 

已知的限制包括下列各項： 
- **同步處理規則**  –自訂規則的優先順序必須在0-99 的保留範圍內，以避免與 Microsoft 的標準規則發生衝突。 將自訂規則放在保留範圍外，可能會導致您的自訂規則隨著標準規則新增至設定而移動。 如果您的設定包含修改過的標準規則，就會發生類似的問題。 強烈建議您不要修改標準規則，而規則放置可能會不正確。 
- **裝置回寫**  –這些設定是已編目的，但在設定期間，它們目前不適用。 如果您的源伺服器已啟用裝置回寫，您必須在新部署的伺服器上手動設定此功能。 
- **同步處理的物件類型**  –雖然可以使用 Synchronization Service Manager 來限制已同步處理的物件類型（使用者、連絡人、群組等）的清單，但這項功能目前不受同步處理設定支援。 完成安裝之後，您必須手動重新套用 advanced 設定。 
- **自訂執行設定檔**  -雖然可以使用 Synchronization Service Manager 修改一組預設的執行設定檔，但這項功能目前不受同步處理設定支援。 完成安裝之後，您必須手動重新套用 advanced 設定。 
- 設定布建階層**Configuring the Provisioning Hierarchy**  –不支援透過同步處理設定來執行這項 Synchronization Service Manager 的 advanced 功能，而且必須在完成初始部署之後手動重新設定。 
- **AD FS 和 PingFederate Authentication**  –與這些驗證功能相關聯的登入方法將會自動預先選取，不過您必須以互動方式提供所有其他必要的設定參數。 
- **已停用的自訂同步處理規則將匯入為已啟用**已停用的自訂同步處理規則將匯入為已啟用。 也請務必在新伺服器上停用它。

 ## <a name="next-steps"></a>後續步驟

- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)
- [安裝 Azure AD Connect Health 代理程式](how-to-connect-health-agent-install.md) 
