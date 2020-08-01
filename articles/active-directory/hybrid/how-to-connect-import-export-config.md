---
title: 如何匯入和匯出 Azure AD Connect 的設定
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
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447046"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>匯入和匯出 Azure AD Connect 設定（公開預覽）

Azure Active Directory （Azure AD）連線部署會因單一樹系快速模式安裝而異，且會使用自訂同步處理規則，在多個樹系之間同步處理複雜的部署。 因為有大量的設定選項和機制，請務必瞭解哪些設定有效，並且能夠快速部署具有相同設定的伺服器。 這項功能引進了將指定之同步處理伺服器的設定進行編目的功能，並將設定匯入新的部署中。 您可以比較不同的同步處理設定快照，輕鬆地將兩個伺服器之間的差異或一段時間內的相同伺服器視覺化。

每次從 Azure AD Connect wizard 變更設定時，會自動將新時間戳記的 JSON 設定檔匯出至 **%ProgramData%\AADConnect**。 設定檔案名稱的格式為**SynchronizationPolicy-*。JSON**，其中檔案名的最後部分是時間戳記。

> [!IMPORTANT]
> 只有 Azure AD Connect 所做的變更會自動匯出。 使用 PowerShell、Synchronization Service Manager 或同步處理規則編輯器所做的任何變更，都必須視需要匯出，以維持最新的複本。 [隨選匯出] 也可用來將設定複本放在安全的位置，以供嚴重損壞修復之用。

## <a name="export-azure-ad-connect-settings"></a>匯出 Azure AD Connect 設定 

若要查看您的設定摘要，請開啟 [Azure AD Connect] 工具，然後選取名為 [ **view] 或 [Export Current configuration**] 的其他工作。 系統會顯示您設定的快速摘要，以及匯出伺服器完整設定的功能。

根據預設，設定會匯出至 **%ProgramData%\AADConnect**。 您也可以選擇將設定儲存到受保護的位置，以確保發生災難時的可用性。 設定是使用 JSON 檔案格式匯出，不應以手動方式建立或編輯，以確保邏輯一致性。 不支援匯入手動建立或編輯的檔案，而且可能會導致非預期的結果。

## <a name="import-azure-ad-connect-settings"></a>匯入 Azure AD Connect 設定

若要匯入先前匯出的設定：
 
1. 在新的伺服器上安裝**Azure AD Connect** 。
1. 選取 [**歡迎使用**] 頁面之後的 [**自訂**] 選項。
1. 選取 **[匯入同步處理設定**]。 流覽先前匯出的 JSON 設定檔案。
1. 選取 [安裝]。

   ![顯示 [安裝必要元件] 畫面的螢幕擷取畫面](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 覆寫此頁面上的設定，例如使用 SQL Server 而非 LocalDB，或使用現有的服務帳戶，而不是預設的 VSA。 這些設定不會從設定檔案匯入。 這是為了進行資訊和比較。

### <a name="import-installation-experience"></a>匯入安裝體驗 

匯入安裝體驗會刻意保持簡單，並以最少的使用者輸入，輕鬆提供現有伺服器的重現性。

以下是在安裝體驗期間唯一可以進行的變更。 從 Azure AD Connect wizard 安裝之後，可以進行所有其他變更：
- **Azure Active Directory 認證**：預設會建議用來設定源伺服器之 Azure 全域管理員的帳戶名稱。 *must*   如果您想要將資訊同步處理至新的目錄，則必須加以變更。
- **使用者登入**：預設會選取針對您的源伺服器所設定的登入選項，並會自動提示您輸入認證或設定期間所需的其他資訊。 在罕見的情況下，可能需要使用不同的選項來設定伺服器，以避免變更作用中伺服器的行為。 否則，請選取 **[下一步]** 以使用相同的設定。
- 內部**部署目錄認證**：針對您的同步處理設定中包含的每個內部部署目錄，您必須提供認證來建立同步處理帳戶，或提供預先建立的自訂同步處理帳戶。 此程式與全新安裝體驗相同，但例外狀況是您無法新增或移除目錄。
- 設定**選項**：如同全新安裝，您可以選擇進行初始設定，以瞭解要啟動自動同步處理還是啟用預備模式。 主要差異在於預備模式預設為刻意啟用，以便在將結果主動匯出至 Azure 之前，先允許比較設定和同步處理結果。

![顯示 [連接您的目錄] 畫面的螢幕擷取畫面](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 主要角色中只能有一部同步處理伺服器，並會主動將設定變更匯出至 Azure。 所有其他伺服器都必須置於預備模式。

## <a name="migrate-settings-from-an-existing-server"></a>從現有的伺服器遷移設定 

如果現有的伺服器不支援設定管理，您可以選擇就地升級伺服器，或遷移設定以用於新的預備伺服器。

遷移需要執行 PowerShell 腳本來解壓縮現有的設定，以便在新的安裝中使用。使用此方法來分類現有伺服器的設定，然後將其套用至新安裝的預備伺服器。將源伺服器的設定與新建立的伺服器進行比較，可以快速視覺化伺服器之間的變更。一如往常，請遵循您組織的認證程式，以確保不需要進行其他設定。

### <a name="migration-process"></a>移轉程序 
若要遷移設定：

1. 在新的預備伺服器上啟動**AzureADConnect.msi** ，並在 Azure AD Connect 的**歡迎**頁面停止。

1. 將**MigrateSettings.ps1**從 Microsoft Azure AD Connect\Tools 目錄複寫到現有伺服器上的位置。 其中一個範例是 C:\setup，其中安裝程式是在現有伺服器上建立的目錄。

   ![顯示 Azure AD Connect 目錄的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate1.png)

1. 執行如這裡所示的腳本，然後儲存整個下層伺服器設定目錄。 將此目錄複寫到新的預備伺服器。 您必須將整個**匯出的-ServerConfiguration-*** 資料夾複製到新的伺服器。

   ![在 Windows PowerShell 中顯示腳本的螢幕擷取畫面。 ](media/how-to-connect-import-export-config/migrate2.png)
    ![顯示覆制導出-ServerConfiguration-* 資料夾的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate3.png)

1. 按兩下桌面上的圖示來啟動**Azure AD Connect** 。 接受 Microsoft 軟體授權條款，然後在下一個頁面上選取 [**自訂**]。
1. 選取 [匯**入同步處理設定**] 核取方塊。 選取 **[流覽**] 以流覽已複製的匯出-ServerConfiguration-* 資料夾。 選取 [MigratedPolicy.js開啟] 以匯入遷移的設定。

   ![顯示 [匯入同步處理設定] 選項的螢幕擷取畫面。](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>安裝後驗證 

比較原先匯入的設定檔案與新部署之伺服器的匯出設定檔案，是瞭解預期與所產生部署之間任何差異的重要步驟。 使用您最愛的並存文字比較應用程式會產生立即的視覺效果，以快速強調任何所需或意外的變更。

雖然目前已排除許多先前手動設定步驟，但您仍應遵循組織的認證程式，以確保不需要進行其他設定。 如果您使用的是 [設定管理] 公開預覽版本中目前未捕捉到的 [advanced settings]，則可能會發生此設定。

以下是已知的限制：
- **同步處理規則**：自訂規則的優先順序必須在0到99的保留範圍內，以避免與 Microsoft 的標準規則發生衝突。 將自訂規則放在保留範圍外，可能會導致您的自訂規則隨著標準規則新增至設定而移動。 如果您的設定包含修改過的標準規則，就會發生類似的問題。 不建議修改標準規則，而規則放置可能會不正確。
- **裝置回寫**：這些設定會進行編目。 它們目前不會在設定期間套用。 如果您的源伺服器已啟用裝置回寫，您必須在新部署的伺服器上手動設定此功能。
- 已同步處理的**物件類型**：雖然可以使用 Synchronization Service Manager 來限制已同步處理的物件類型（例如使用者、連絡人和群組）的清單，但這項功能目前不支援透過同步處理設定。 完成安裝之後，您必須手動重新套用 advanced 設定。
- **自訂執行設定檔**：雖然可以使用 Synchronization Service Manager 來修改一組預設的執行設定檔，但這項功能目前不支援透過同步處理設定。 完成安裝之後，您必須手動重新套用 advanced 設定。
- 設定布建**階層：不**支援透過同步處理設定來執行這項 Synchronization Service Manager 的先進功能。 完成初始部署之後，必須手動重新設定它。
- **Active Directory 同盟服務（AD FS）和 PingFederate authentication**：與這些驗證功能相關聯的登入方法會自動預先選取。 您必須以互動方式提供所有其他必要的設定參數。
- **已停用的自訂同步處理規則將匯入為啟用**：已停用的自訂同步處理規則已匯入為已啟用。 請務必在新伺服器上停用它。

 ## <a name="next-steps"></a>後續步驟

- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)
- [安裝 Azure AD Connect Health 代理程式](how-to-connect-health-agent-install.md) 
