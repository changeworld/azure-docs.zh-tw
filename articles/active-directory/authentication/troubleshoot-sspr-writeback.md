---
title: 針對自助式密碼重設回寫進行疑難排解-Azure Active Directory
description: 瞭解如何針對 Azure Active Directory 中自助式密碼重設回寫的常見問題和解決步驟進行疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a3044127aacb5910a270d40d94d3255031a71a2
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741298"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>針對 Azure Active Directory 中的自助式密碼重設回寫進行疑難排解

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 可讓使用者重設其在雲端中的密碼。 密碼回寫是透過 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 所實現的功能，可將雲端中的密碼變更即時回寫至現有內部部署目錄。

如果您在 SSPR 回寫方面遇到問題，下列疑難排解步驟和常見錯誤可能會有所説明。 如果您找不到問題的答案， [我們的支援小組隨時都](#contact-microsoft-support) 能協助您。

## <a name="troubleshoot-connectivity"></a>針對連線問題進行疑難排解

如果您有 Azure AD Connect 的密碼回寫問題，請參閱下列可協助您解決問題的步驟。 若要復原您的服務，建議您依序執行下列步驟：

* [檢查網路連線](#confirm-network-connectivity)
* [重新啟動 Azure AD Connect 同步處理服務](#restart-the-azure-ad-connect-sync-service)
* [將密碼回寫功能先停用再重新啟用](#disable-and-re-enable-the-password-writeback-feature)
* [安裝最新版的 Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [疑難排解密碼回寫](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>檢查網路連線

最常見的失敗點是防火牆或 proxy 埠或閒置的超時設定不正確。

針對 Azure AD Connect *1.1.443.0* 版和更新版本，下列位址需要 *輸出 HTTPS* 存取：

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

如果您需要更多細微性，請參閱 [Microsoft Azure DATACENTER IP 範圍清單](https://www.microsoft.com/download/details.aspx?id=41653)。 這份清單會在每星期三更新，並在下一個星期一生效。

如需詳細資訊，請參閱 [Azure AD Connect 的連線性必要條件](../hybrid/how-to-connect-install-prerequisites.md)。

### <a name="restart-the-azure-ad-connect-sync-service"></a>重新啟動 Azure AD Connect 同步處理服務

若要解決服務的連線問題或其他暫時性問題，請完成下列步驟以重新開機 Azure AD Connect 同步處理服務：

1. 以執行 Azure AD Connect 之伺服器的系統管理員身分，選取 [ **啟動**]。
1. 在搜尋欄位中輸入 *services.msc* 並選取 [輸入]。
1. 找出 *Microsoft Azure AD Sync* 項目。
1. 以滑鼠右鍵按一下服務專案，選取 [ **重新開機**]，然後等候作業完成。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="使用 GUI 重新啟動 Azure AD 同步服務" border="false":::

這些步驟會重新建立您與 Azure AD 的連線，並應解決您的連線問題。

如果重新開機 Azure AD Connect 同步服務無法解決您的問題，請嘗試在下一節中停用再重新啟用密碼回寫功能。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>先將「密碼回寫」功能停用，然後再重新啟用

若要繼續對問題進行疑難排解，請完成下列步驟，以停用再重新啟用密碼回寫功能：

1. 以執行 Azure AD Connect 之伺服器的系統管理員身分，開啟 [ **Azure AD Connect 設定]**。
1. 在 [連線到 Azure AD] 中，輸入您的「Azure AD 全域系統管理員認證」。
1. 在 **[連線到 AD DS]** 中，輸入您的內部部署 Active Directory Domain Services 管理員認證。
1. 在 [專門識別您的使用者] 中，選取 [下一步] 按鈕。
1. 在 [選用功能] 中，清除 [密碼回寫] 核取方塊。
1. 在其餘的對話方塊頁面上逐一選取 [下一步] 而不變更任何項目，直到到達 [準備好設定] 頁面為止。
1. 檢查 [ **準備設定] 頁面** 是否會將 [ *密碼回寫* ] 選項顯示為 *停用*。 選取綠色的 [ **設定** ] 按鈕以認可您的變更。
1. 在 [已完成] 中，清除 [立即同步處理] 選項，然後選取 [完成] 來關閉精靈。
1. 重新開啟 **Azure AD Connect 設定]**。
1. 重複步驟2-8，這次在 [**選用功能**] 頁面上選取 [*密碼回寫*] 選項來重新啟用服務。

這些步驟會重新建立您與 Azure AD 的連線，並應解決您的連線問題。

如果停用再重新啟用密碼回寫功能無法解決您的問題，請重新安裝下一節中的 Azure AD Connect。

### <a name="install-the-latest-azure-ad-connect-release"></a>安裝最新版的 Azure AD Connect

重新安裝 Azure AD Connect 可以解決 Azure AD 和本機 Active Directory Domain Services 環境之間的設定和連線問題。 建議您在嘗試先前的步驟來驗證連線能力並進行疑難排解之後，才執行此步驟。

> [!WARNING]
> 如果您已自訂現成的同步處理規則，請在繼續進行 *升級之前先備份，然後在完成後以手動方式重新部署這些規則。*

1. 從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?LinkId=615771)下載最新版的 Azure AD Connect。
1. 由於您已安裝 Azure AD Connect，請執行就地升級以將您的 Azure AD Connect 安裝更新至最新版本。

    執行下載的封裝，並遵循螢幕上的指示來更新 Azure AD Connect。

這些步驟應重新建立與 Azure AD 的連線，並解決您的連線問題。

如果安裝最新版的 Azure AD Connect server 無法解決您的問題，請嘗試在安裝最新版本之後，先停用再重新啟用密碼回寫，以做為最後一個步驟。

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>確認 Azure AD Connect 具有必要的許可權

Azure AD Connect 需要 AD DS **重設密碼** ] 許可權才能執行密碼回寫。 若要檢查 Azure AD Connect 是否具有指定內部部署 AD DS 使用者帳戶的必要許可權，請使用 **Windows 有效許可權** 功能：

1. 登入 Azure AD Connect 伺服器，並啟動 **Synchronization Service Manager**，方法是選取 [開始] > [同步處理服務]。
1. 在 [連接器] 索引標籤下，選取內部部署 [Active Directory Domain Services] 連接器，然後選取 [屬性]。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="示範如何編輯屬性的 Synchronization Server Manager" border="false":::
  
1. 在快顯視窗中，選取 [連線到 Active Directory 樹系] 索引標籤，然後記下 [使用者名稱] 屬性。 這個屬性是 Azure AD Connect 用來執行目錄同步作業的 AD DS 帳戶。

    若要讓 Azure AD Connect 能夠執行密碼回寫，AD DS 帳戶必須有「重設密碼」權限。 您可以在下列步驟中檢查此使用者帳戶的許可權。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="尋找同步處理服務 Active Directory 使用者帳戶" border="false":::
  
1. 登入內部部署網域控制站，然後啟動 **Active Directory 使用者和電腦** 應用程式。
1. 選取 [檢視]，並確定 [進階功能] 選項已啟用。  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Active Directory 使用者和電腦會顯示進階功能" border="false":::
  
1. 尋找您要驗證的 AD DS 使用者帳戶。 以滑鼠右鍵按一下帳戶名稱，然後選取 [屬性]。  
1. 在快顯視窗中，移至 [安全性] 索引標籤，然後選取 [進階]。  
1. 在 [系統管理員的進階安全性設定] 快顯視窗中，移至 [有效存取權] 索引標籤。
1. 選擇 [ **選取使用者**]，選取 Azure AD Connect 使用的 AD DS 帳戶，然後選取 [ **View 有效率存取**]。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="顯示同步處理帳戶的 [有效存取權] 索引標籤" border="false":::
  
1. 向下捲動並尋找 [重設密碼]。 如果該項目有核取記號，AD DS 帳戶就有權限可重設選定 Active Directory 使用者帳戶的密碼。  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="驗證同步帳戶具備重設密碼權限" border="false":::

## <a name="common-password-writeback-errors"></a>一般密碼回寫錯誤

密碼回寫可能會發生下列更具體的問題。 如果您有上述其中一個錯誤，請查看建議的解決方案，並檢查密碼回寫是否正確運作。

| 錯誤 | 解決方法 |
| --- | --- |
| 密碼重設服務無法在內部部署環境中啟動。 Azure AD Connect 電腦的應用程式事件記錄檔中出現錯誤6800。 <br> <br> 上架之後，同盟、傳遞驗證或密碼雜湊同步使用者無法重設其密碼。 | 啟用「密碼回寫」時，同步處理引擎會透過與雲端上線服務通訊，呼叫回寫程式庫來執行設定 (上線)。 在上線期間或啟動 Windows Communication Foundation (WCF) 端點來進行「密碼回寫」時，如果發生任何錯誤，都會導致 Azure AD Connect 電腦的事件記錄中出現錯誤。 <br> <br> 在 Azure AD Sync (ADSync) 服務重新啟動期間，若已設定回寫，就會啟動 WCF 端點。 但是，如果端點啟動失敗，我們會記錄事件6800，並讓同步處理服務啟動。 此事件的存在表示密碼回寫端點未啟動。 此事件 (6800) 的事件記錄詳細資料，以及 PasswordResetService 元件所產生的事件記錄項目會指出為何無法啟動端點。 請檢查這些事件記錄檔錯誤，如果密碼回寫仍然無法運作，請嘗試重新開機 Azure AD Connect。 如果此問題持續發生，請嘗試先將「密碼回寫」停用再重新啟用。
| 當使用者嘗試在已啟用密碼回寫時重設密碼或解除鎖定帳戶，作業會失敗。 <br> <br> 此外，在執行解除鎖定作業之後，您會在 Azure AD Connect 事件記錄檔中看到一個事件，其中包含：「同步處理引擎傳回錯誤 hr = 800700CE，訊息 = 檔案名或副檔名太長」。 | 尋找 Azure AD Connect 的 Active Directory 帳戶，並將密碼重設，使其包含不超過 256 個字元。 接下來，從 [**開始**] 功能表開啟 **同步處理服務**。 瀏覽至 [連接器]，然後尋找 [Active Directory 連接器]。 選取它，然後選取 [屬性]。 瀏覽至 [認證] 頁面，然後輸入新密碼。 選取 [確定] 以關閉頁面。 |
| 在 Azure AD Connect 安裝程序的最後一個步驟，您會看到一個錯誤，指出無法設定「密碼回寫」。 <br> <br> Azure AD Connect 的應用程式事件記錄檔包含錯誤32009，其文字為「取得驗證權杖時發生錯誤」。 | 下列兩種情況會發生此錯誤： <br><ul><li>您為 Azure AD Connect 安裝程式開始時所提供的全域系統管理員帳戶指定了不正確的密碼。</li><li>您嘗試將同盟使用者用於在 Azure AD Connect 安裝程式開始時指定的全域系統管理員帳戶。</li></ul> 若要修正此問題，請確定您並未針對在安裝程式開始時指定的全域系統管理員使用同盟帳戶，而且指定的密碼正確無誤。 |
| Azure AD Connect 電腦的事件記錄包含透過執行 PasswordResetService 所擲回的錯誤 32002。 <br> <br> 錯誤會顯示：「連接到「匯流排」時發生錯誤。 權杖提供者無法提供安全性權杖。」 | 您的內部部署環境無法連線到雲端的 Azure 服務匯流排端點。 這個錯誤是因為防火牆規則封鎖連往特定連接埠或網址的輸出連線所導致。 如需詳細資訊，請參閱[連線必要條件](../hybrid/how-to-connect-install-prerequisites.md)。 在您更新這些規則之後，請重新開機 Azure AD Connect 伺服器，密碼回寫應該會再次開始運作。 |
| 運作一段時間之後，同盟、傳遞驗證或密碼雜湊同步使用者無法重設其密碼。 | 在某些罕見的情況下，Azure AD Connect 重新啟動後，「密碼回寫」服務可能仍無法重新啟動。 在這些情況下，請先檢查是否已在內部部署環境中啟用密碼回寫。 您可以使用 Azure AD Connect wizard 或 PowerShell 進行檢查。 如果此功能顯示為已啟用，請再次嘗試啟用或停用此功能。 如果此疑難排解步驟無法運作，請嘗試完整卸載，然後重新安裝 Azure AD Connect。 |
| 同盟、傳遞驗證或密碼雜湊同步使用者若嘗試重設其密碼，會在嘗試提交密碼後看到錯誤。 此錯誤表示發生服務問題。 <br ><br> 除了這個問題之外，在密碼重設作業期間，您可能會在內部部署的事件記錄中看到關於管理代理程式存取遭拒的錯誤。 | 如果您在事件記錄中看到這些錯誤，請確認在設定時於精靈中指定的 Active Directory 管理代理程式 (ADMA) 帳戶具有密碼回寫的必要權限。 <br> <br> 這項權限在授與後，最多需要一小時的時間，才會透過網域控制站 (DC) 上的 `sdprop` 背景工作往下傳遞。 <br> <br> 若要讓密碼重設正常運作，必須在要重設密碼的使用者物件安全性描述元上為權限加上戳記。 在使用者物件上出現此權限之前，密碼重設會繼續因存取遭拒訊息而失敗。 |
| 同盟、傳遞驗證或密碼雜湊同步使用者若嘗試重設其密碼，會在嘗試提交密碼後看到錯誤。 此錯誤表示發生服務問題。 <br> <br> 除了這個問題之外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄檔中看到錯誤，指出「找不到物件」錯誤。 | 這個錯誤通常表示同步處理引擎找不到 Azure AD 連接器空間中的使用者物件，或連結的 Metaverse (MV) 或 Azure AD 連接器空間物件。 <br> <br> 若要針對這個問題進行疑難排解，請確定使用者已確實透過 Azure AD Connect 的目前執行個體從內部部屬同步處理到 Azure AD，並檢查連接器空間和 MV 中物件的狀態。 確認 Active Directory 憑證服務 (AD CS) 物件會透過 "Microsoft.InfromADUserAccountEnabled.xxx" 規則連線到 MV 物件。|
| 同盟、傳遞驗證或密碼雜湊同步使用者若嘗試重設其密碼，會在嘗試提交密碼後看到錯誤。 此錯誤表示發生服務問題。 <br> <br> 除了這個問題之外，在密碼重設作業期間，您可能會在 Azure AD Connect 服務的事件記錄檔中看到錯誤，指出有「找到多個相符專案」錯誤。 | 這表示同步處理引擎偵測到 MV 物件透過 "Microsoft.InfromADUserAccountEnabled.xxx" 連接到一個以上的 AD CS 物件。 這表示使用者在多個樹系中啟用帳戶。 密碼回寫不支援此案例。 |
| 密碼作業因設定錯誤而失敗。 應用程式事件記錄中包含 Azure AD Connect 錯誤 6329 和文字：「0x8023061f (作業失敗，因為此管理代理程式上未啟用密碼同步處理)」。 | 如果在已經啟用密碼回寫功能之後，變更 Azure AD Connect 設定以新增 Active Directory 樹系 (或移除現有樹系再重新加入)，就會發生此錯誤。 位於在這些最近新增樹系中的使用者，其密碼作業會失敗。 若要修正此問題，請在完成樹系設定變更之後，將「密碼回寫」功能先停用，然後再重新啟用。 |

## <a name="password-writeback-event-log-error-codes"></a>密碼回寫事件記錄檔錯誤碼

針對密碼回寫問題進行疑難排解時，最佳做法是檢查 Azure AD Connect 電腦上的應用程式事件記錄檔。 此事件記錄檔包含來自兩個來源的密碼回寫事件。 *PasswordResetService* 來源會說明與密碼回寫操作相關的作業和問題。 *ADSync* 來源描述與在 Active Directory Domain Services 環境中設定密碼相關的作業和問題。

### <a name="if-the-source-of-the-event-is-adsync"></a>如果事件來源是 ADSync

| 程式碼 | 名稱或訊息 | 描述 |
| --- | --- | --- |
| 6329 | 0x80230619： MMS (4924) ：「限制可防止密碼變更為目前指定的密碼。」 | 當密碼回寫服務嘗試在本機目錄上設定不符合密碼存留期、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此事件。 <br> <br> 如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，就必須在到達網域中指定的使用期限後，才能再次變更密碼。 若要進行測試，最短使用期限應該設定為 0。 <br> <br> 如果您已啟用密碼歷程記錄需求，則必須選取過去 *N* 次未使用的密碼，其中 *n* 是密碼歷程記錄設定。 如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。 若要進行測試，密碼歷程記錄應該設定為 0。 <br> <br> 如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。 <br> <br> 如果您已啟用密碼篩選，而使用者選取的密碼不符合篩選準則，則重設或變更作業會失敗。 |
| 6329 | MMS (3040) ： admaexport.cpp .cpp (2837) ：伺服器不包含 LDAP 密碼原則控制項。 | 如果未在 DC 上啟用 LDAP_SERVER_POLICY_HINTS_OID 控制項 (1.2.840.113556.1.4.2066)，就會發生此問題。 若要使用密碼回寫功能，您必須啟用該控制項。 若要進行此操作，DC 必須是 Windows Server 2008R2 或更新版本。 |
| HR 8023042 | 同步處理引擎傳回的錯誤 hr= 80230402，訊息=嘗試取得物件失敗，因為存在具有相同錨點的重複項目。 | 在多個網域中啟用相同的使用者識別碼時，就會發生此錯誤。 例如，如果您在同步處理的帳戶和資源樹系中皆存在相同的使用者識別碼，且在每個樹系中皆為啟用時。 <br> <br> 如果您使用非唯一的錨點屬性 (例如別名或 UPN)，而且兩個使用者共用該相同的錨點屬性時，也會發生此錯誤。 <br> <br> 若要解決這個問題，請確定您的網域內沒有重複的使用者，且每個使用者皆使用唯一的錨點屬性。 |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>如果事件來源為 PasswordResetService

| 程式碼 | 名稱或訊息 | 描述 |
| --- | --- | --- |
| 31001 | PasswordResetStart | 這個事件表示內部部署服務偵測到源自雲端、針對同盟、傳遞驗證或密碼雜湊同步使用者所提出的密碼重設要求。 這個事件是每個密碼重設回寫作業的第一個事件。 |
| 31002 | PasswordResetSuccess | 這個事件表示使用者在密碼重設作業期間選取了新的密碼。 我們認為這個密碼符合公司的密碼需求。 密碼已成功地寫回至本機的 Active Directory 環境。 |
| 31003 | PasswordResetFail | 此事件表示使用者選取了密碼，且密碼已成功送達內部部署環境。 但是，當我們嘗試在本機 Active Directory 環境中設定密碼時，發生失敗。 此失敗有幾個可能原因： <br><ul><li>使用者的密碼不符合網域的年齡、歷程記錄、複雜性或篩選準則需求。 若要解決這個問題，請建立新的密碼。</li><li>ADMA 服務帳戶沒有適當的許可權，無法在有問題的使用者帳戶上設定新密碼。</li><li>使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員群組。</li></ul>|
| 31004 | OnboardingEventStart | 如果您啟用 Azure AD Connect 的密碼回寫，並已開始將您的組織上架至密碼回寫 web 服務，就會發生此事件。 |
| 31005 | OnboardingEventSuccess | 這個事件表示上線程序已順利完成，「密碼回寫」功能已可供使用。 |
| 31006 | ChangePasswordStart | 這個事件表示內部部署服務偵測到源自雲端、針對同盟、傳遞驗證或密碼雜湊同步使用者所提出的密碼變更要求。 這個事件是每個密碼變更回寫作業的第一個事件。 |
| 31007 | ChangePasswordSuccess | 這個事件表示使用者在密碼變更作業期間選取了新的密碼，我們判斷此密碼符合公司的密碼需求，因此該密碼已成功回寫至本機 Active Directory 環境。 |
| 31008 | ChangePasswordFail | 這個事件表示使用者選取了密碼，該密碼已成功抵達內部部署環境，但是當我們嘗試在本機 Active Directory 環境中設定密碼時，發生了失敗狀況。 此失敗有幾個可能原因： <br><ul><li>使用者的密碼不符合網域的年齡、歷程記錄、複雜性或篩選準則需求。 若要解決這個問題，請建立新的密碼。</li><li>ADMA 服務帳戶沒有適當的許可權，無法在有問題的使用者帳戶上設定新密碼。</li><li>使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | 內部部署服務偵測到源自系統管理員，代表使用者且針對同盟、傳遞驗證或密碼雜湊同步使用者所提出的密碼重設要求。 這個事件是每個由系統管理員所起始之密碼重設回寫作業的第一個事件。 |
| 31010 | ResetUserPasswordByAdminSuccess | 系統管理員在系統管理員起始密碼重設作業期間選取了新的密碼。 我們認為這個密碼符合公司的密碼需求。 密碼已成功地寫回至本機的 Active Directory 環境。 |
| 31011 | ResetUserPasswordByAdminFail | 系統管理員代表使用者選取了密碼。 密碼已成功送達內部部署環境。 但是，當我們嘗試在本機 Active Directory 環境中設定密碼時，發生失敗。 此失敗有幾個可能原因： <br><ul><li>使用者的密碼不符合網域的年齡、歷程記錄、複雜性或篩選準則需求。 若要解決這個問題，請嘗試新的密碼。</li><li>ADMA 服務帳戶沒有適當的許可權，無法在有問題的使用者帳戶上設定新密碼。</li><li>使用者的帳戶位於不允許密碼設定作業的受保護群組中，例如網域或企業系統管理員。</li></ul>  |
| 31012 | OffboardingEventStart | 如果您停用搭配 Azure AD Connect 運作的「密碼回寫」，就會發生此事件，此事件表示我們已開始將貴組織在「密碼回寫」Web 服務下架。 |
| 31013| OffboardingEventSuccess| 這個事件表示下架程序已順利完成，「密碼回寫」功能已順利停用。 |
| 31014| OffboardingEventFail| 此事件表示脫離程式未成功。 這可能是因為雲端上或是設定期間所指定之內部部署系統管理員帳戶的權限錯誤。 如果您在停用密碼回寫時，嘗試使用同盟的雲端全域管理員，也會發生錯誤。 若要修正此問題，請檢查您的系統管理權限，並確定您在設定「密碼回寫」功能時，未使用已同盟的帳戶。|
| 31015| WriteBackServiceStarted| 這個事件表示已成功啟動密碼回寫服務。 已準備好接受來自雲端的密碼管理要求。|
| 31016| WriteBackServiceStopped| 這個事件表示密碼回寫服務已停止。 來自雲端的任何密碼管理要求都不會成功。|
| 31017| AuthTokenSuccess| 這個事件表示我們已順利擷取 Azure AD Connect 設定期間所指定之全域系統管理員的授權權杖，以便開始下架或上架程序。|
| 31018| KeyPairCreationSuccess| 這個事件表示我們已成功建立密碼加密金鑰。 此金鑰是用來加密從雲端傳送至您內部部署環境的密碼。|
| 31034| ServiceBusListenerError| 此事件表示連接到您租使用者的服務匯流排接聽程式時發生錯誤。 如果錯誤訊息包含「遠端憑證無效」，請檢查以確定您的 Azure AD Connect 伺服器具有 [AZURE TLS 憑證變更](../../security/fundamentals/tls-certificate-changes.md)中所述的所有必要根 ca。 |
| 32000| UnknownError| 這個事件表示密碼管理作業期間發生未知的錯誤。 請查看事件中的例外狀況文字，以獲得詳細資訊。 如果您遇到問題，請嘗試先將「密碼回寫」停用，然後再重新啟用。 如果這沒有説明，請在您開啟支援要求時，包含事件記錄檔的複本以及所指定的追蹤識別碼。|
| 32001| ServiceError| 這個事件表示連線到雲端密碼重設服務時發生錯誤。 當內部部署服務無法連線到密碼重設 web 服務時，通常就會發生這個錯誤。|
| 32002| ServiceBusError| 此事件表示連接到您租使用者的服務匯流排實例時發生錯誤。 如果您在內部部署環境中封鎖了輸出連線，就會發生這個問題。 請檢查您的防火牆來確保您允許透過 TCP 443 及連到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的連線，然後再試一次。 如果仍遇到問題，請嘗試先將「密碼回寫」停用，然後再重新啟用。|
| 32003| InPutValidationError| 這個事件表示傳遞到我們的 Web 服務 API 的輸入無效。 請重試一次此作業。|
| 32004| DecryptionError| 這個事件表示在解密從雲端抵達的密碼時發生錯誤。 原因可能是雲端服務和內部部署環境的解密金鑰不符。 若要解決此問題，請先將您內部部署環境中的「密碼回寫」停用，然後再重新啟用。|
| 32005| ConfigurationError| 上架期間，我們將租用戶特定的資訊儲存在內部部署環境的設定檔中。 這個事件表示儲存此檔案時發生錯誤，或啟動服務時發生檔案讀取錯誤。 若要修正此問題，請嘗試先將「密碼回寫」停用，然後再重新啟用，以強制重寫此組態檔。|
| 32007| OnBoardingConfigUpdateError| 上架期間，我們會從雲端傳送資料到內部部署密碼重設服務。 接著，該資料會寫入記憶體內檔案中，然後再傳送至同步處理服務，安全地儲存在磁碟上。 此事件表示在記憶體中寫入或更新該資料時發生問題。 若要修正此問題，請嘗試先將「密碼回寫」停用，然後再重新啟用，以強制重寫此組態檔。|
| 32008| ValidationError| 這個事件表示我們從密碼重設 Web 服務收到的回應無效。 若要修正此問題，請嘗試先將「密碼回寫」停用，然後再重新啟用。|
| 32009| AuthTokenError| 這個事件表示我們無法取得 Azure AD Connect 設定期間所指定之全域系統管理員帳戶的授權權杖。 造成這個錯誤的原因可能是全域系統管理員帳戶所指定的使用者名稱或密碼錯誤。 如果所指定的全域管理員帳戶已同盟，也可能會發生這個錯誤。 若要修正此問題，請以正確的使用者名稱和密碼重新執行設定，並確保系統管理員是受控 (僅限雲端或已密碼同步處理的) 帳戶。|
| 32010| CryptoError| 這個事件表示在產生密碼加密金鑰或解密從雲端服務抵達的密碼時發生錯誤。 此錯誤可能表示您的環境有問題。 請查看事件記錄的詳細資料，深入了解此問題並加以解決。 您也可以嘗試先將「密碼回寫」服務停用，然後再重新啟用。|
| 32011| OnBoardingServiceError| 這個事件表示內部部署服務與密碼重設 Web 服務無法正確地通訊，來起始上架程序。 這可能是因為防火牆規則所造成，或是取得租使用者的驗證權杖時發生問題。 若要修正此問題，請確定您沒有封鎖透過 TCP 443 和 TCP 9350-9354 或連到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的輸出連線。 並請確認您用來上架的 Azure AD 系統管理員帳戶並未同盟。|
| 32013| OffBoardingError| 這個事件表示內部部署服務與密碼重設 Web 服務無法正確地通訊，來起始下架程序。 這可能是因為防火牆規則所造成，或是取得租使用者的授權權杖時發生問題。 若要修正此問題，請確定您沒有封鎖透過 443 或連到 https://ssprdedicatedsbprodncu.servicebus.windows.net 的輸出連線，且您要用來下架的 Azure Active Directory 管理帳戶並未同盟。|
| 32014| ServiceBusWarning| 此事件表示我們必須重試連線到租使用者的服務匯流排實例。 在正常情況下，這應該不會有問題，但如果您多次看到此事件，請考慮檢查服務匯流排的網路連線，尤其是如果它是高延遲或低頻寬的連線。|
| 32015| ReportServiceHealthError| 為了監視「密碼回寫」服務的健康情況，我們每隔 5 分鐘就會傳送一次活動訊號資料給我們的密碼重設 Web 服務。 這個事件表示在將此健全狀況資訊傳回到雲端 Web 服務時發生錯誤。 此健康狀態資訊不包含任何個人資料，而且只是一個統計資料和基本的服務統計資料，因此我們可以在雲端提供服務狀態資訊。|
| 33001| ADUnKnownError| 這個事件表示 Active Directory 傳回未知的錯誤。 如需詳細資訊，請檢查來自 ADSync 來源之事件的 Azure AD Connect 伺服器事件記錄。|
| 33002| ADUserNotFoundError| 這個事件表示在內部部署目錄中找不到嘗試重設或變更密碼的使用者。 當已在內部部署但未在雲端中刪除使用者時，就可能發生這個錯誤。 如果同步處理發生問題，也可能會發生此錯誤。請檢查您的同步記錄和最後幾個同步執行詳細資料，以取得詳細資訊。|
| 33003| ADMutliMatchError| 當密碼重設或變更要求源自雲端時，我們會使用 Azure AD Connect 設定程序期間所指定的雲端錨點，來判斷如何將該要求往回連結到內部部署環境中的使用者。 這個事件表示我們發現內部部署目錄中有兩位使用者具有相同的雲端錨點屬性。 如需詳細資訊，請檢查您的同步處理記錄，以及最後幾次執行的同步處理詳細資料。|
| 33004| ADPermissionsError| 此事件表示 Active Directory 管理代理程式 (ADMA) 服務帳戶沒有有問題的帳戶設定新密碼的適當許可權。 確定使用者樹系中的 ADMA 帳戶已在樹系中的所有物件上重設密碼許可權。 如需有關如何設定權限的詳細資訊，請參閱步驟 4：設定適當的 Active Directory 權限。 當使用者的屬性 AdminCount 設為 1 時，也會發生此錯誤。|
| 33005| ADUserAccountDisabled| 這個事件表示我們嘗試對內部部署中已停用的帳戶重設或變更密碼。 請啟用帳戶，然後再試一次此作業。|
| 33006| ADUserAccountLockedOut| 這個事件表示我們嘗試對內部部署中已鎖定的帳戶重設或變更密碼。 當使用者在短時間內嘗試進行變更或重設密碼作業太多次，就可能發生鎖定。 請解除鎖定帳戶，然後再試一次此作業。|
| 33007| ADUserIncorrectPassword| 這個事件表示使用者在執行密碼變更作業時，指定的目前密碼不正確。 請指定正確的目前密碼，然後再試一次。|
| 33008| ADPasswordPolicyError| 當密碼回寫服務嘗試在本機目錄上設定不符合密碼存留期、歷程記錄、複雜度或網域篩選需求的密碼時，就會發生此事件。 <br> <br> 如果您有最短的密碼使用期限，且最近在該時段內變更過密碼，就必須在到達網域中指定的使用期限後，才能再次變更密碼。 若要進行測試，最短使用期限應該設定為 0。 <br> <br> 如果已啟用密碼歷程記錄需求，則必須選取最近 N 次未用過的密碼，其中 N 是密碼歷程記錄設定。 如果您選取最近 N 次用過的密碼，則會在此案例中看到失敗。 若要進行測試，密碼歷程記錄應該設定為 0。 <br> <br> 如果您有密碼複雜度需求，則會在使用者嘗試變更或重設密碼時強制執行這些需求。 <br> <br> 如果您已啟用密碼篩選，而使用者選取的密碼不符合篩選準則，則重設或變更作業會失敗。|
| 33009| ADConfigurationError| 這個事件表示 Active Directory 的設定有問題，因此在將密碼回寫到內部部署目錄時發生問題。 請檢查 Azure AD Connect 電腦的應用程式事件記錄檔中是否有來自 ADSync 服務的訊息，以取得發生錯誤的詳細資訊。|

## <a name="azure-ad-forums"></a>Azure AD 論壇

如果您有關于 Azure AD 和自助式密碼重設的一般問題，您可以在 Azure Active Directory 的問題頁面中要求該社區提供 [Microsoft Q&](/answers/topics/azure-active-directory.html)的協助。 社群的成員包括工程師、產品經理、MVP 和夥伴 IT 專業人員。

## <a name="contact-microsoft-support"></a>連絡 Microsoft 支援

如果找不到問題的答案，我們的支援團隊一直都能進一步協助您。

為了正確地提供協助，我們會要求您在開啟案例時，提供盡可能詳細的資料。 這些詳細資料包含下列各項：

* **錯誤的一般描述**：錯誤為何？ 注意到何種行為？ 我們如何才能重現錯誤？ 請提供盡可能詳細的資料。
* **頁面**：您注意到錯誤時的所在頁面？ 盡可能包含 URL 和頁面的螢幕擷取畫面。
* **支援碼**：使用者看到錯誤時所產生的支援碼？
   * 若要找到支援碼，請重現錯誤，然後按一下畫面底部的 [支援碼] 連結，將所產生的 GUID 傳送給支援工程師。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="支援碼位於網頁瀏覽器視窗右下方。":::

  * 如果您所在的頁面底部沒有支援碼，請選取 F12，搜尋 SID 和 CID，然後將這兩個結果傳送給支援工程師。
* **日期、時間和時區**：請包含發生錯誤的精確日期和時間 (含時區)。
* **使用者識別碼**：看到錯誤的使用者是誰？ 例如，使用者 \@contoso.com。
   * 這是同盟使用者嗎？
   * 這是傳遞驗證使用者嗎？
   * 這是密碼雜湊同步使用者嗎？
   * 這是否僅限雲端的使用者？
* **授權**：使用者是否獲得指派 Azure AD 授權？
* **應用程式事件記錄**：如果您使用密碼回寫，而且錯誤位於您的內部部署基礎結構中，請包含來自 Azure AD Connect 伺服器的應用程式事件記錄壓縮複本。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 SSPR，請參閱 [運作方式： Azure AD 自助式密碼重設](concept-sspr-howitworks.md) ，或 [自助式密碼重設回寫在 Azure AD 中的運作](concept-sspr-writeback.md)方式。
