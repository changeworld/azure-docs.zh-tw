---
title: Azure Active Directory 入口網站中的錯誤碼 |Microsoft Docs
description: 登入活動報告錯誤碼參考。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: da2d598c7bb6d7b06e57dd497d1e2aebf1b63694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898898"
---
# <a name="sign-in-activity-report-error-codes"></a>登入活動報告錯誤碼 

利用[使用者登入報告](concept-sign-ins.md)所提供的資訊，您可以找到下列問題的解答︰

- 誰登入我的應用程式？
- 已登入哪些應用程式？
- 哪些登入失敗？為什麼？

登入失敗時，您會看到失敗的對應錯誤碼。 本文會列出錯誤碼與其描述，以及建議的適用行動方針。 

## <a name="how-can-i-display-failed-sign-ins"></a>如何顯示失敗的登入？ 

在 [ [Azure 入口網站](https://portal.azure.com)] 功能表上，選取 [ **Azure Active Directory**]，或搜尋並從任何頁面選取 [ **Azure Active Directory** ]。

![選取 Azure Active Directory](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

在 [**監視**] 底下，選取 [登**入**] 以開啟登[入報告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)。

![登入活動](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "登入活動")

篩選報告以顯示所有失敗的登入，其做法是從 [登入狀態] 下拉式方塊中選取 [失敗]。

![登入活動](./media/reference-sign-ins-error-codes/06.png "登入活動")

從篩選後的清單中選取一個項目，就會開啟 [活動詳細資料：登入] 刀鋒視窗。 此檢視提供有關失敗登入事件的其他資料，包括 [登入錯誤碼] 和 [失敗原因]。

![登入活動](./media/reference-sign-ins-error-codes/05.png "登入活動")

您也可以使用[報告 API](concept-reporting-api.md)，以程式設計方式存取登入資料。

## <a name="error-codes"></a>錯誤碼


|錯誤|說明|
|---|---|
|16000|這是內部實作詳細資料，而不是錯誤狀況。 您可以放心地忽略此參考。|
|20001|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|20012|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|20033|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|40008|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|40009|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|40014|同盟識別提供者有問題。 請連絡 IDP 以解決此問題。|
|50000|登入服務發生問題。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)以解決此問題。|
|50001|此租用戶中找不到服務主體名稱。 如果租用戶的管理員尚未安裝此應用程式，或如果在目錄中找不到資源主體或為無效，也可能會發生此錯誤。|
|50002|登入失敗，因為租用戶上的 Proxy 存取受限。 如果該租用戶原則是您自有的，您可以變更受限租用戶的設定以修正此問題。|
|50003|登入失敗，因為遺漏簽署金鑰或憑證。 這可能是因為應用程式中未設定任何簽署金鑰。 請參閱下列文件所述的解決方式：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)。 如果問題持續發生，請連絡應用程式擁有者或應用程式管理員。|
|50005|使用者嘗試透過條件式存取原則，從目前不支援的平臺登入裝置。|
|50006| 簽章驗證失敗，因為簽章無效。 請參閱下列文件所述的解決方式：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery)。 如果問題持續發生，請連絡應用程式擁有者或應用程式管理員。|
|50007|找不到此應用程式的夥伴加密憑證。 請向 Microsoft [開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)以修正此問題。|
|50008|權杖中的 SAML 判斷提示遺漏或設定不正確。 請連絡同盟提供者。|
|50010|應用程式的對象 URI 驗證失敗，因為未設定權杖對象。 請連絡應用程式擁有者以解決問題。|
|50011|回覆地址遺漏、設定不正確或不符合針對應用程式所設定的回覆地址。 請嘗試使用下列文件所列出的解決方式：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application)。 如果問題持續發生，請連絡應用程式擁有者或應用程式管理員。|
|50012| 這是指出驗證失敗的一般錯誤訊息。 發生的原因可能是要求中遺失或無效的認證或宣告。 確認傳送的要求中包含正確的認證和宣告。 |
|50013|判斷提示因為各種原因而無效。 例如，權杖簽發者不符合其有效時間範圍內的 API 版本、權杖已過期或格式不正確，或判斷提示中的重新整理權杖不是主要的重新整理權杖。|
|50017|憑證驗證失敗，原因如下：<ul><li>在受信任的憑證清單中找不到發行憑證</li><li>找不到預期的 CrlSegment</li><li>在受信任的憑證清單中找不到發行憑證</li><li>在設定差異 CRL 發佈點時未指定對應的 CRL 發佈點</li><li>由於逾時問題而無法擷取有效的 CRL 區段</li><li>無法下載 CRL</li></ul>請連絡租用戶管理員。|
|50020|由於以下其中一個原因，使用者未獲得授權。<ul><li>使用者嘗試以含 v1 端點的 MSA 帳戶來登入</li><li>使用者不存在於租用戶中。</li></ul> 請連絡應用程式擁有者。|
|50027|JWT 權杖無效，原因如下：<ul><li>未包含 nonce 宣告、子宣告</li><li>主旨識別碼不相符</li><li>idToken 宣告中的宣告重複</li><li>未預期的簽發者</li><li>未預期的對象</li><li>不在其有效時間範圍內 </li><li>權杖格式不正確</li><li>來自簽發者的外部 ID 權杖未能通過簽章驗證。</li></ul>請連絡應用程式擁有者|
|50029|URI 無效 - 網域名稱包含無效字元。 請連絡租用戶管理員。|
|50034|使用者不存在於目錄中。 請連絡租用戶管理員。|
|50042|原則中遺漏產生成對識別碼所需的 salt。 請連絡租用戶管理員。|
|50048|主體與用戶端判斷提示中的簽發者宣告不符。 請連絡租用戶管理員。|
|50050|要求格式不正確。 請連絡應用程式擁有者。|
|50053|帳戶遭到鎖定，因為使用者嘗試使用不正確的使用者識別碼或密碼登入太多次。|
|50055|無效的密碼，或輸入的密碼過期。|
|50056|密碼無效或為 Null - 存放區中沒有此使用者的密碼。|
|50057|使用者帳戶已停用。 系統管理員已停用該帳戶。|
|50058|應用程式嘗試執行無訊息登入，但使用者不得以無訊息模式登入。 應用程式需要啟動互動式流程，讓使用者可選擇登入。 請連絡應用程式擁有者。|
|50059|使用者不存在於目錄中。 請連絡租用戶管理員。|
|50061|登出要求無效。 請連絡應用程式擁有者。|
|50072|使用者需要註冊雙因素驗證 (互動式)。|
|50074|使用者未通過 MFA 檢查。|
|50076|使用者未通過 MFA 檢查 (非互動式)。|
|50078|呈現的多重要素驗證已過期，您必須重新整理您的多因素驗證才能存取。|
|50079|使用者需要註冊雙因素驗證 (非互動式登入)。|
|50085|重新整理權杖需要社交 IDP 登入。 讓使用者嘗試以其使用者名稱和密碼重新登入。|
|50089|流程權杖過期 - 驗證失敗。 讓使用者嘗試以其使用者名稱和密碼重新登入。|
|50097|需要裝置驗證。 因為 DeviceId 或 DeviceAltSecId 宣告為 **Null**，或沒有與裝置識別碼對應的裝置存在，所以可能發生這種情形。|
|50099|JWT 簽章無效。 請連絡應用程式擁有者。|
|50105|未將登入的使用者指派給所登入應用程式的角色。 請將使用者指派給應用程式。 如需詳細資訊：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|所要求的同盟領域物件不存在。 請連絡租用戶管理員。|
|50120|JWT 標頭有問題。 請連絡租用戶管理員。|
|50124|宣告轉換所包含的輸入參數無效。 請連絡租用戶管理員以更新原則。|
|50125|因為密碼重設或密碼註冊項目，登入作業已中斷。|
|50126|使用者名稱、密碼無效，或內部部署使用者名稱或密碼無效。|
|50127|使用者必須安裝訊息代理程式應用程式來存取此內容。|
|50128|網域名稱無效 - 在要求中找不到租用戶識別資訊，或任何提供的認證均未隱含租用戶識別資訊。|
|50129|裝置為加入工作場所網路 - 註冊裝置需**加入工作場所網路**。|
|50130|宣告值無法解譯為已知驗證方法。|
|50131|用於各種條件式存取錯誤。 例如： 不正確的 Windows 裝置狀態，要求因為可疑的活動、存取原則和安全性原則決策而遭到封鎖。|
|50132|認證已遭撤銷，原因如下：<ul><li>SSO 成品無效或過期</li><li>工作階段對應用程式來說不夠新</li><li>已傳送無訊息登入要求，但使用者的 Azure AD 工作階段無效或過期。</li></ul>|
|50133|工作階段因為到期或近期密碼變更而無效。|
|50135|帳戶有風險，因此必須變更密碼。|
|50136|將 MSA 工作階段重新導向至應用程式 - 偵測到單一 MSA 工作階段。 |
|50140|由於在使用者登入時出現「讓我保持登入」插斷，所以發生此錯誤。 請使用相互關聯識別碼、要求識別碼和錯誤碼[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得更多詳細資料。 |
|50143|工作階段不符 - 工作階段無效，原因是由於資源不同而導致使用者租用戶與網域提示不符。請使用相互關聯識別碼、要求識別碼和錯誤碼 [開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得更多詳細資料。|
|50144|使用者的 Active Directory 密碼已到期。 為使用者產生新密碼，或讓終端使用者使用自助式重設工具。|
|50146|在設定此應用程式時，必須使用應用程式專屬的簽署金鑰。 在設定應用程式時未使用這樣的金鑰，或該金鑰已過期或尚未生效。 請連絡應用程式擁有者。|
|50148|code_verifier 與 PKCE 的授權要求中所提供的 code_challenge 不符。 聯絡應用程式開發人員。 |
|50155|此使用者的裝置驗證失敗。|
|50158|不符合外部安全性挑戰。|
|50161|外部提供者所傳送的宣告不足，或是遺漏向外部提供者所要求的宣告。|
|50166|無法將要求傳送給宣告提供者。|
|50169|此領域不是當前服務命名空間所設定的領域。|
|50172|外部宣告提供者未獲得核准。 請連絡租用戶管理員。|
|50173|需要全新的驗證權杖。 讓使用者使用全新的認證再次登入。|
|50177|未針對傳遞使用者支援外部挑戰。|
|50178|未針對傳遞使用者支援工作階段控制。|
|50180|需要 Windows 整合式驗證。 為租用戶啟用無縫 SSO。|
|50181|登入期間與 OTP 相關的失敗。 |
|50201|當使用者提供其他資訊時，將會在登入期間向使用者顯示此訊息提示中斷。|
|51001|所顯示的網域提示沒有內部部署安全性識別碼 - 內部部署 UPN。|
|51004|目錄中不存在使用者帳戶。|
|51006|需要 Windows 整合式驗證。 使用者使用透過宣告遺失的會話權杖登入。 請要求使用者重新登入。|
|52004|使用者尚未同意存取 LinkedIn 資源。 |
|53000|條件式存取原則需要相容的裝置，但裝置不相容。 讓使用者向 Intune 等獲得核准的 MDM 提供者註冊其裝置。|
|53001|條件式存取原則需要已加入網域的裝置，但該裝置尚未加入網域。 請使用者使用已加入網域的裝置。|
|53002|使用的應用程式不是條件式存取的已核准應用程式。 使用者所使用的應用程式，必須是已核准使用的應用程式清單其中一個，才能取得存取權。|
|53003|因為條件式存取原則，所以已封鎖存取。|
|53004|使用者必須先完成多重要素驗證註冊程序，才能存取此內容。 使用者應該註冊多重要素驗證。|
|53032|已封鎖帳戶，因為 Azure AD Identity Protection 的原則。|
|65001|應用程式 X 沒有存取應用程式 Y 的權限，或已撤銷此權限。 或者，使用者或系統管理員尚未同意使用識別碼為 X 的應用程式。針對此使用者和資源傳送互動式授權要求。 或者，使用者或系統管理員尚未同意使用識別碼為 X 的應用程式。將授權要求傳送給租用戶管理員，以代表應用程式 Y 對資源 Z 採取行動。|
|65004|使用者不同意存取應用程式。 讓使用者重試登入，並同意應用程式|
|65005|應用程式所需資源存取清單不包含資源可探索的應用程式，或用戶端應用程式已要求存取未在其所需資源存取清單中指定的資源，或 Graph 服務傳回不正確的要求或找不到資源。 如果應用程式支援 SAML，則可能是您在設定應用程式時所使用的識別碼 (實體) 不正確。 請嘗試使用下列連結中針對 SAML 所列出的解決方案：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|授與無效，原因如下：<ul><li>所要求 SAML 2.0 判斷提示的主體確認方法無效</li><li>V2 上不支援應用程式 OnBehalfOf 流程</li><li>未使用工作階段金鑰簽署主要的重新整理權杖</li><li>外部重新整理權杖無效</li><li>所取得的存取權授與，是不同租用戶的。</li></ul>|
|70001|在名為 Y 的租用戶中找不到名為 X 的應用程式。如果租用戶系統管理員尚未安裝識別碼為 X 的應用程式，或租用戶中的任何使用者尚未同意使用此應用程式，便可能發生此錯誤。 可能是針對應用程式所設定的識別碼值不正確，或將驗證要求傳送給錯誤的租用戶。|
|70002|應用程式傳回的用戶端認證無效。 請連絡應用程式擁有者。|
|70003|應用程式傳回的授與類型不受支援。 請連絡應用程式擁有者。|
|70004|應用程式傳回的重新導向 URI 無效。 用戶端所指定的重新導向位址，與已設定的位址或 OIDC 核准清單上的位址全都不符。 請連絡應用程式擁有者。|
|70005|應用程式傳回的回應類型不受支援，原因如下：<ul><li>未對應用程式啟用 'token' 回應類型</li><li>'id_token' 回應類型需要 'OpenID' 範圍 - 在編碼的 wctx 中包含不受支援的 OAuth 參數值</li></ul>請連絡應用程式擁有者。|
|70007|應用程式在要求權杖時傳回的 'response_mode' 值不受支援。 請連絡應用程式擁有者。|
|70008|所提供的授權碼或重新整理權杖已過期或已撤銷。 請讓使用者重試登入。|
|70011|應用程式所要求的範圍無效。 請連絡應用程式擁有者。|
|70012|在驗證 MSA (取用者) 使用者時，發生伺服器錯誤。 重試登入時，如果問題持續發生，請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|驗證碼無效，因為使用者針對裝置碼流程所輸入的使用者代碼錯誤。 授權未獲核准。|
|70019|驗證碼過期。 請讓使用者重試登入。|
|70037|所提供的挑戰回應不正確。 已拒絕遠端驗證工作階段。|
|70043|Azure 條件式存取會話管理會強制會話到期|
|70044|Azure 條件式存取會話管理會強制會話到期|
|75001|SAML 訊息繫結期間發生錯誤。|
|75003|應用程式傳回了與未支援繫結相關的錯誤 (無法透過 HTTP POST 以外的繫結傳送 SAML 通訊協定回應)。 請連絡應用程式擁有者。|
|75005|Azure AD 不支援應用程式為單一登入傳送的 SAML 要求。 請連絡應用程式擁有者。|
|75008|應用程式所提出的要求遭拒，因為 SAML 要求有未預期的目的地。 請連絡應用程式擁有者。|
|75011|使用者用來向服務進行驗證的驗證方法，與所要求的驗證方法不符。 請連絡應用程式擁有者。|
|75016|SAML2 驗證要求所具有的 NameIdPolicy 無效。 請連絡應用程式擁有者。|
|80001|驗證代理程式無法連線至 Active Directory。 請確定能看到使用者登入要求處理 DC、且已加入網域的機器上，已安裝驗證代理程式。|
|80002|內部錯誤。 密碼驗證要求已超時。我們無法將驗證要求傳送給內部的混合式身分識別服務。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關錯誤的詳細資料。|
|80003|驗證代理程式收到無效的回應。 嘗試對 Active Directory 內部部署進行驗證時，發生未知錯誤。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關錯誤的詳細資料。|
|80005|驗證代理程式：處理驗證代理程式所傳來的回應時，發生未知錯誤。 請[開啟支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關錯誤的詳細資料。|
|80007|驗證代理程式無法驗證使用者的密碼。|
|80010|驗證代理程式無法連線將密碼解密。 |
|80011|驗證代理程式無法擷取加密金鑰。|
|80012|使用者在允許的時間外 (此值是在 AD 中指定的) 嘗試登入。|
|80013|無法完成驗證嘗試，因為執行驗證代理程式的機器和 AD 之間有時間差異。 請修正時間同步問題|
|80014|驗證代理程式已超時。開啟包含錯誤碼、相互關聯識別碼和日期時間的[支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)，以取得有關此錯誤的詳細資料。|
|81001|使用者的 Kerberos 票證太大。 如果使用者位於過多群組，以致於 Kerberos 票證包含過多群組成員資格，便會發生此錯誤。 降低使用者的群組成員資格，並再試一次。|
|81005|驗證套件不受支援。|
|81007|租用戶未啟用無縫 SSO。|
|81012|這不是錯誤狀況。 這表示嘗試登入 Azure AD 的使用者與登入裝置的使用者不同。 您可以放心地忽略記錄中此錯誤碼。|
|90010|基於各種不同的原因而不支援該要求。 例如，該要求是使用不支援的要求方法 (僅支援 POST 方法)，或不支援所要求的權杖簽章演算法。 請連絡應用程式開發人員。|
|90014| 遺漏通訊協定訊息的必要欄位，請連絡應用程式擁有者。 如果您是應用程式擁有者，請確定擁有登入要求的所有必要參數。 |
|90051| 不正確委派 Token。 指定了不正確國家/地區雲端識別碼（{cloudId}）。|
|90072| 必須先在租用戶中將帳戶新增為外部使用者。 登出後再使用不同的 Azure AD 帳戶登入。|
|90094| 應用程式要求的許可權不允許登入的使用者同意，且使用者已被封鎖。 |
|90095| 應用程式要求的許可權不允許登入的使用者同意，且使用者已顯示為系統[管理員同意要求](../manage-apps/configure-admin-consent-workflow.md)表單。 |
|500011| 在名為 <tenant ID>的租使用者中找不到名為 <site address> 的資源主體。 如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 您可能會將驗證要求傳送至錯誤的租用戶。|
|500021| 租使用者受到公司 proxy 的限制。 拒絕資源存取。|
|500121| 驗證在強式驗證要求期間失敗。|
|500133| Assert 陳述式不在其有效時間範圍內。 先確認存取權杖未過期，再將它用於使用者 Assert 陳述式，或要求新權杖。|
|530021|應用程式不符合條件式存取已核准的應用程式需求。|
|530032|受到安全性原則封鎖。| 
|700016|在目錄 ' {tenantName} ' 中找不到識別碼為 ' {appIdentifier} ' 的應用程式。 如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 您可能已將驗證要求傳送給錯誤的租使用者。|
|900432|跨雲端要求中不支援機密用戶端。|
|5000811|無法驗證 SAML 權杖簽章。 簽署金鑰識別碼不符合任何有效的已註冊金鑰。|
|7000215|提供的用戶端密碼無效。|
|7000218|要求主體必須包含下列參數： ' client_assertion ' 或 ' client_secret '。|


## <a name="next-steps"></a>後續步驟

* [登入報告概觀](concept-sign-ins.md)
* [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)
