---
title: 如何匯入和匯出 Azure AD Connect 設定
description: 本文說明雲端布建的常見問題。
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
ms.openlocfilehash: da80af9fe598186fa25d59601c9fa4faccb4286a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87447046"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>匯入和匯出 Azure AD Connect 設定 (公開預覽) 

Azure Active Directory (Azure AD) 連接部署會因單一樹系快速模式安裝而異，因為會使用自訂同步處理規則，在多個樹系之間進行同步處理的複雜部署。 由於有大量的設定選項和機制，因此務必瞭解哪些設定有效，且能夠快速部署具有相同設定的伺服器。 這項功能引進了將指定同步處理伺服器的設定編目，以及將設定匯入新部署的功能。 您可以比較不同的同步處理設定快照集，輕鬆地將兩部伺服器之間的差異視覺化，或一段時間內的同一部伺服器。

每次從 Azure AD Connect wizard 變更設定時，會自動將新的時間戳記 JSON 設定檔案匯出到 **%ProgramData%\AADConnect**。 設定檔案名稱的格式為 **SynchronizationPolicy-*。JSON**，其中檔案名的最後部分是時間戳記。

> [!IMPORTANT]
> 只有 Azure AD Connect 所做的變更會自動匯出。 使用 PowerShell、Synchronization Service Manager 或同步處理規則編輯器所做的任何變更，都必須視需要匯出，以維護最新的複本。 您也可以使用「視需要匯出」將設定的複本放在安全的位置，以供災難修復之用。

## <a name="export-azure-ad-connect-settings"></a>匯出 Azure AD Connect 設定 

若要查看設定設定的摘要，請開啟 Azure AD Connect 工具，然後選取名為 **view 或 Export Current configuration**的其他工作。 系統會顯示您設定的快速摘要，以及匯出伺服器完整設定的功能。

預設會將設定匯出至 **%ProgramData%\AADConnect**。 您也可以選擇將設定儲存到受保護的位置，以確保發生嚴重損壞時的可用性。 設定是使用 JSON 檔案格式匯出，不應手動建立或編輯，以確保邏輯一致性。 不支援匯入手動建立或編輯過的檔案，而且可能會導致非預期的結果。

## <a name="import-azure-ad-connect-settings"></a>匯入 Azure AD Connect 設定

若要匯入先前匯出的設定：
 
1. 在新的伺服器上安裝 **Azure AD Connect** 。
1. 在 [**歡迎使用**] 頁面之後，選取 [**自訂**] 選項。
1. 選取 **[匯入同步處理設定**]。 流覽先前匯出的 JSON 設定檔案。
1. 選取 [安裝]。

   ![顯示 [安裝必要元件] 畫面的螢幕擷取畫面](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 覆寫此頁面上的設定，例如使用 SQL Server 而非 LocalDB 或使用現有的服務帳戶，而不是預設的 VSA。 這些設定不會從設定設定檔案匯入。 其中有資訊和比較用途。

### <a name="import-installation-experience"></a>匯入安裝體驗 

匯入安裝體驗是刻意保持簡單，只要使用者輸入最少量的資訊，就能輕鬆地提供現有伺服器的重現性。

以下是可在安裝體驗期間進行的唯一變更。 您可以從 Azure AD Connect wizard 進行安裝之後進行其他所有變更：
- **Azure Active Directory 認證**：預設建議用來設定源伺服器之 Azure 全域管理員的帳戶名稱。 *must*   如果您想要將資訊同步處理至新的目錄，則必須加以變更。
- **使用者登入**：預設會選取針對您的源伺服器所設定的登入選項，並且會自動提示您輸入在設定期間所需的認證或其他資訊。 在罕見的情況下，可能需要使用不同的選項來設定伺服器，以避免變更作用中伺服器的行為。 否則，請選取 **[下一步]** 以使用相同的設定。
- **內部部署目錄認證**：針對同步處理設定中包含的每個內部部署目錄，您必須提供認證以建立同步處理帳戶，或提供預先建立的自訂同步處理帳戶。 此程式與全新安裝體驗完全相同，但您無法新增或移除目錄。
- 設定**選項**：如同全新安裝，您可能會選擇設定是否要啟動自動同步處理或啟用預備模式的初始設定。 主要的差異在於，預設會刻意啟用預備模式，以便在將結果主動匯出至 Azure 之前，先比較設定和同步處理結果。

![顯示 [連線您的目錄] 畫面的螢幕擷取畫面](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 只有一部同步處理伺服器可以在主要角色中，並主動將設定變更匯出至 Azure。 所有其他伺服器都必須放在預備模式中。

## <a name="migrate-settings-from-an-existing-server"></a>從現有伺服器遷移設定 

如果現有的伺服器不支援設定管理，您可以選擇就地升級伺服器，或遷移設定以便在新的預備伺服器上使用。

遷移需要執行 PowerShell 腳本以解壓縮現有的設定，以便在新的安裝中使用。使用這個方法來編目現有伺服器的設定，然後將它們套用至新安裝的預備伺服器。將源伺服器的設定與新建立的伺服器進行比較，將可快速視覺化伺服器之間的變更。如往常一樣，請遵循組織的認證程式，以確保不需要進行其他設定。

### <a name="migration-process"></a>移轉程序 
若要遷移設定：

1. 在新的預備伺服器上啟動 **AzureADConnect.msi** ，並在 Azure AD Connect 的 **歡迎** 頁面上停止。

1. 將 **MigrateSettings.ps1** 從 Microsoft Azure AD Connect\Tools 目錄複寫到現有伺服器上的位置。 例如，C:\setup，其中的安裝程式是在現有伺服器上建立的目錄。

   ![顯示 Azure AD Connect 目錄的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate1.png)

1. 執行如下所示的腳本，並儲存整個下層伺服器設定目錄。 將此目錄複寫到新的預備伺服器。 您必須將整個 **匯出的-ServerConfiguration-*** 資料夾複製到新的伺服器。

   ![顯示 Windows PowerShell 中腳本的螢幕擷取畫面。 ](media/how-to-connect-import-export-config/migrate2.png)
    ![顯示覆制導出-ServerConfiguration-* 資料夾的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate3.png)

1. 按兩下桌面上的圖示開始 **Azure AD Connect** 。 接受 Microsoft 軟體授權條款，然後在下一個頁面上選取 [ **自訂**]。
1. 選取 [匯 **入同步處理設定** ] 核取方塊。 選取 [ **流覽** ] 以流覽複製的匯出-ServerConfiguration-* 資料夾。 選取 [MigratedPolicy.js] 以匯入已遷移的設定。

   ![顯示 [匯入同步處理設定] 選項的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>安裝後驗證 

比較原始匯入的設定檔案與新部署伺服器的匯出設定檔案，是瞭解預期與所產生部署之間任何差異的重要步驟。 使用您最愛的並列文字比較應用程式，可產生立即的視覺效果，以快速醒目顯示任何所需或意外的變更。

雖然現在已排除許多先前手動設定的步驟，但您仍應遵循組織的認證流程，以確保不需要進行其他設定。 如果您使用的是 [設定管理] 公開預覽版本中目前未捕獲的 [advanced settings]，就可能會發生這種設定。

以下是已知的限制：
- **同步處理規則**：自訂規則的優先順序必須在0到99的保留範圍內，以避免與 Microsoft 的標準規則發生衝突。 將自訂規則放在保留範圍之外，可能會導致您的自訂規則在設定中新增標準規則時四處移動。 如果您的設定包含修改過的標準規則，將會發生類似的問題。 不建議修改標準規則，而且可能會有不正確的規則放置。
- **裝置回寫**：這些設定已編目。 它們目前不會在設定期間套用。 如果您的源伺服器已啟用裝置回寫，您必須在新部署的伺服器上手動設定此功能。
- 已同步處理的**物件類型**：雖然可以使用 Synchronization Service Manager 來限制已同步處理的物件類型清單 (例如使用者、連絡人和群組) ，但目前並不支援透過同步處理設定來執行這項功能。 完成安裝之後，您必須手動重新套用 advanced configuration。
- **自訂執行設定檔**：雖然您可以使用 Synchronization Service Manager 來修改一組預設的回合設定檔，但目前並不支援透過同步處理設定來執行這項功能。 完成安裝之後，您必須手動重新套用 advanced configuration。
- 設定布建階層：不支援透過同步處理設定**來執行 Synchronization Service Manager**的這項 advanced 功能。 您必須在完成初始部署之後，以手動方式重新設定它。
- **Active Directory 同盟服務 (AD FS) 和 PingFederate authentication**：會自動預先選取與這些驗證功能相關聯的登入方法。 您必須以互動方式提供所有其他必要的設定參數。
- **停用的自訂同步處理規則將匯入為已啟用**：已停用的自訂同步處理規則已匯入為已啟用。 也請務必在新伺服器上停用它。

 ## <a name="next-steps"></a>後續步驟

- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)
- [安裝 Azure AD Connect Health 代理程式](how-to-connect-health-agent-install.md) 
