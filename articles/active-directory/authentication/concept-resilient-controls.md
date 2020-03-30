---
title: 創建彈性存取控制管理原則 - Azure AD
description: 本文件提供有關組織所應採用策略的指引，這些策略可提供靈活彈性，以在發生未預期的中斷情況期間降低鎖定風險
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908729"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>使用 Azure Active Directory 來建立具彈性的存取控制管理策略

>[!NOTE]
> 本文件所含的資訊代表 Microsoft Corporation 在發行日當下對問題的看法。 由於 Microsoft 必須因應不斷變化的市場狀況，因此不應將此解讀為 Microsoft 方面所作出的承諾，且 Microsoft 也無法保證在發行日之後所提出任何資訊的正確性。

組織如果倚賴單一存取控制措施 (例如多重要素驗證 (MFA) 或單一網路位置) 來保護其 IT 系統，當該單一存取控制措施變成無法使用或設定不正確時，就容易在存取其應用程式和資源時發生失敗。 例如，天然災害可能造成大區段的電信基礎設施或公司網路無法供使用。 這類中斷情況可能導致使用者和系統管理員無法登入。

本文件藉由下列案例提供有關組織所應採用策略的指引，這些策略可提供靈活彈性，以在發生未預期的中斷情況期間降低鎖定風險：

 1. 組織可以藉由實作風險降低策略或應變計劃來提升其靈活彈性，以**在中斷情況發生前**降低鎖定風險。
 2. 組織可以藉由備妥風險降低策略和應變計劃，**在中斷情況發生期間**繼續存取他們所選擇的應用程式和資源。
 3. 組織應確定保留**在中斷情況發生後**及回復所實作之任何應變措施前的資訊 (例如記錄)。
 4. 組織如果尚未實作預防策略或替代計劃，可以實作**緊急選項**來處理中斷情況。

## <a name="key-guidance"></a>主要指引

本文件中有四個重點：

* 使用緊急存取帳戶來避免系統管理員鎖定。
* 使用條件訪問 （CA） 而不是每個使用者 MFA 實現 MFA。
* 通過使用多個條件訪問 （CA） 控制項來緩解使用者鎖定。
* 為每個使用者佈建多個驗證方法或對等的方法，以降低使用者鎖定風險。

## <a name="before-a-disruption"></a>在中斷情況發生前

在處理可能發生的存取控制問題方面，降低發生實際中斷情況的風險必定是組織的首要任務。 降低風險包括針對實際事件做規劃，以及實作策略來確保存取控制措施和作業在中斷情況發生期間不受影響。

### <a name="why-do-you-need-resilient-access-control"></a>為何需要具彈性的存取控制措施？

 身分識別是存取應用程式和資源之使用者的控制平面。 您的身分識別系統可控制哪些使用者及在哪些條件下 (例如存取控制措施或驗證需求)，使用者可以存取應用程式。 當因為未預期的情況導致使用者無法滿足一或多個驗證或存取控制需求以進行驗證時，組織可能會發生下列其中一種或同時發生兩種問題：

* **管理員鎖定：** 管理員無法管理租戶或服務。
* **使用者鎖定：** 使用者無法訪問應用或資源。

### <a name="administrator-lockout-contingency"></a>系統管理員鎖定應變措施

為了將系統管理員對您租用戶的存取權解除鎖定，您應該建立緊急存取帳戶。 這些緊急存取帳戶 (也稱為「急用」** 帳戶) 可允許在無法使用一般特殊權限帳戶存取程序時，進行存取來管理 Azure AD 設定。 依照[緊急存取帳戶建議]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)，應該至少建立兩個緊急存取帳戶。

### <a name="mitigating-user-lockout"></a>降低使用者鎖定風險

 要降低使用者鎖定的風險，請使用具有多個控制項的條件訪問策略，以便使用者選擇如何訪問應用和資源。 例如，藉由讓使用者能夠選擇使用 MFA 進行登入****、從受控裝置進行登入**或**從公司網路進行登入，當其中一個存取控制措施無法供使用時，使用者便還有其他選項來繼續工作。

#### <a name="microsoft-recommendations"></a>Microsoft 建議

在組織的現有條件訪問策略中包含以下訪問控制項：

1. 為倚賴不同通訊通道的每個使用者佈建多個驗證方法，例如 Microsoft Authenticator 應用程式 (網際網路型)、OATH 權杖 (於裝置上產生) 及 SMS (電話)。
2. 在 Windows 10 裝置上部署「Windows Hello 企業版」以直接從裝置登入滿足 MFA 需求。
3. 透過 [Azure AD 混合式聯結](https://docs.microsoft.com/azure/active-directory/devices/overview)或 [Microsoft Intune 受控裝置](https://docs.microsoft.com/intune/planning-guide)使用受信任的裝置。 受信任的裝置將可改善使用者體驗，因為受信任的裝置本身無須向使用者提出 MFA 挑戰，即可滿足原則的增強式驗證需求。 而在註冊新裝置時，以及從非受信任裝置存取應用程式或資源時，則會需要 MFA。
4. 使用 Azure AD 身分識別保護風險型原則來取代固定的 MFA 原則，以在使用者或登入有風險時防止存取。

>[!NOTE]
> 風險型原則會要求要有 [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) 授權。

下列範例說明若要提供具彈性的存取控制措施以便讓使用者存取其應用程式和資源，您必須建立哪些原則。 在此範例中，您將需要一個安全性群組 **AppUsers** (含有您要授與存取權的目標使用者)、一個名為 **CoreAdmins** 的群組 (含有核心系統管理員)，以及一個名為 **EmergencyAccess** 的群組 (含有緊急存取帳戶)。
**AppUsers** 中選取的使用者只要是從受信任的裝置進行存取或提供增強式驗證 (例如 MFA)，此範例原則集便會對他們授與所選應用程式的存取權。 此範例原則集會排除緊急帳戶和核心系統管理員。

**CA 風險降低原則集：**

* 策略 1：阻止對目標群組外部人員的訪問
  * 使用者和組：包括所有使用者。 排除 AppUsers、CoreAdmins 及 EmergencyAccess
  * 雲應用：包括所有應用
  * 條件：（無）
  * 授予控制：塊
* 策略 2：授予需要 MFA 或受信任設備的應用使用者存取權限。
  * 使用者和組：包括應用使用者。 排除 CoreAdmins 和 EmergencyAccess
  * 雲應用：包括所有應用
  * 條件：（無）
  * 授予控制：授予存取權限，需要多重要素驗證，要求設備符合要求。 對於多個控制項：需要其中一個選定的控制項。

### <a name="contingencies-for-user-lockout"></a>使用者鎖定的應變措施

或者，您的組織也可以建立應變原則。 若要建立應變原則，您必須定義商務持續性、營運成本、財務成本及安全性風險之間的取捨準則。 例如，您可以只針對一部分使用者、一部分應用程式、一部分用戶端或一部分位置，啟用應變原則。 應變原則可在沒有實作任何風險降低方法的情況下，於中斷情況發生期間，讓系統管理員和使用者能夠存取應用程式和資源。
了解您在中斷情況發生期間的暴露情形，不僅有助於降低您的風險，也是您規劃程序中不可或缺的一部分。 若要建立您的應變計劃，請先判斷您組織的下列業務需求：

1. 提前確定任務關鍵型應用：即使風險/安全狀況較低，您也必須允許哪些應用訪問？ 請為這些應用程式建立一份清單，並確定您的其他專案關係人 (業務、安全性、法務、領導階層) 都同意如果所有存取控制措施都消失，這些應用程式仍然必須繼續執行。 您可能最後會產生下列類別：
   * **類別 1 任務關鍵性應用程式**：無法使用的時間不能超過幾分鐘的應用程式，例如直接影響組織營收的應用程式。
   * **類別 2 重要應用程式**：企業需要在幾小時內可供存取的應用程式。
   * **類別 3 低優先順序的應用程式**：可以承受發生幾天中斷情況的應用程式。
2. 針對類別 1 和 2 中的應用程式，Microsoft 建議您與先規劃要允許的存取層級類型：
   * 您想要允許完整存取，還是受限的工作階段 (例如限制下載)？
   * 您是否想要允許存取應用程式的部分而非整個應用程式？
   * 您是否想要允許資訊工作者存取而封鎖系統管理員存取，直到存取控制措施還原為止？
3. 針對這些應用程式，Microsoft 同樣建議您規劃將刻意開放及關閉的存取途徑：
   * 您是否想要只允許透過瀏覽器存取，而封鎖可儲存離線資料的豐富型用戶端？
   * 您是否想要只允許公司網路內的使用者存取，而將外部使用者封鎖？
   * 您是否想要只在中斷情況發生期間，才允許來自特定國家/地區或區域的存取？
   * 您想要讓應變原則 (尤其是適用於任務關鍵性應用程式的原則) 在替代存取控制措施無法供使用時失敗還是成功？

#### <a name="microsoft-recommendations"></a>Microsoft 建議

應急條件訪問策略是一種**禁用的策略**，它省略了 Azure MFA、協力廠商 MFA、基於風險或基於設備的控制項。 而當您的組織決定啟用應變計劃時，系統管理員則可以啟用該原則，並停用一般的控制型原則。

>[!IMPORTANT]
> 當已備妥應變計劃時，停用會對使用者強制執行安全性的原則將會降低安全性狀態，即使只是暫時停用也一樣。

* 如果一個認證類型或一個存取控制機制發生中斷會影響對您應用程式的存取，請設定一組遞補原則。 請設定一個狀態為停用且要求以「加入網域」作為控制措施的原則，用來作為需要協力廠商 MFA 提供者的作用中原則備份。
* 藉由依循[密碼指引](https://aka.ms/passwordguidance) \(英文\) 白皮書中的做法，降低不要求使用 MFA 時不良執行者猜測密碼的風險。
* 部署 [Azure AD 自助密碼重設 (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) 和 [Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy)，以確保使用者不會使用一般密碼和您選擇禁止的字詞。
* 使用在未達到特定驗證層級時，會限制應用程式內的存取而不直接切換回完整存取的原則。 例如：
  * 設定一個將受限工作階段宣告傳送給 Exchange 和 SharePoint 的備份原則。
  * 如果您的組織使用 Microsoft Cloud App Security，請考慮切換回會牽涉 MCAS 的原則，然後 MCAS 就會允許唯讀存取而非上傳。
* 為您的原則命名，以確定在中斷期間可以輕易找到這些原則。 請在原則名稱中包含下列元素：
  * 原則的標籤號碼**。
  * 用來指出此原則僅供緊急狀況使用的文字。 例如：**在 EMERGENCY 中啟用**
  * 適用的*中斷情況*。 例如：**在 MFA 中斷期間**
  * ** 以序號顯示您必須啟動原則的順序。
  * 適用的應用程式**。
  * 適用的控制項**。
  * 所需的條件**。
  
應變原則的這個命名標準將顯示如下： 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

以下示例：**示例 A - 用於恢復對任務關鍵型協作應用的訪問的應急 CA 策略**是典型的企業應急方案。 在此案例中，組織通常會針對所有 Exchange Online 和 SharePoint Online 存取要求使用 MFA，而在此情況下，中斷係指客戶的 MFA 提供者發生中斷狀況 (不論是 Azure MFA、內部部署 MFA 提供者，還是協力廠商 MFA)。 此原則可降低此中斷狀況的風險，方法是允許特定的目標使用者從受信任的 Windows 裝置存取這些應用程式，但只有在從受信任的公司網路存取應用程式時才允許。 它也會將緊急帳戶和核心系統管理員從這些限制中排除。 目標使用者將得以存取 Exchange Online 和 SharePoint Online，而其他使用者仍將因中斷而無法存取應用程式。 此範例將需要一個具名的網路位置 **CorpNetwork** 和一個安全性群組 **ContingencyAccess** (含有目標使用者)、一個名為 **CoreAdmins** 的群組 (含有核心系統管理員)，以及一個名為 **EmergencyAccess** 的群組 (含有緊急存取帳戶)。 此應變措施需要四個原則來提供所需的存取權。 

**範例 A - 用以還原對任務關鍵性共同作業應用程式之存取權的應變 CA 原則：**

* 策略 1：需要域加入的設備進行交換和共用點
  * 名稱： EM001 - 緊急啟用：MFA 中斷[1/4] - 交換共用點 - 需要混合 Azure AD 聯接
  * 使用者和組：包括應急訪問。 排除 CoreAdmins 和 EmergencyAccess
  * 雲應用程式：線上交換和線上共用點
  * 條件： 任何
  * 授予控制：需要加入域
  * 狀態： 已禁用
* 策略 2：阻止 Windows 以外的平臺
  * 名稱： EM002 - 緊急啟用：MFA 中斷[2/4] - 交換共用點 - 阻止訪問，但 Windows 除外
  * 使用者和組：包括所有使用者。 排除 CoreAdmins 和 EmergencyAccess
  * 雲應用程式：線上交換和線上共用點
  * 條件：設備平臺包括所有平臺，不包括 Windows
  * 授予控制：塊
  * 狀態： 已禁用
* 策略 3：阻止公司網路以外的網路
  * 名稱： EM003 - 緊急啟用：MFA 中斷[3/4] - 交換共用點 - 阻止訪問，但公司網路除外
  * 使用者和組：包括所有使用者。 排除 CoreAdmins 和 EmergencyAccess
  * 雲應用程式：線上交換和線上共用點
  * 條件：位置包括任何位置，不包括公司網路
  * 授予控制：塊
  * 狀態： 已禁用
* 策略 4：顯式阻止 EAS
  * 名稱： EM004 - 緊急啟用：MFA 中斷[4/4] - 交換 - 所有使用者的模組 EAS
  * 使用者和組：包括所有使用者
  * 雲應用：包括線上交換
  * 條件：用戶端應用：交換活動同步
  * 授予控制：塊
  * 狀態： 已禁用

啟用順序：

1. 將 ContingencyAccess、CoreAdmins 及 EmergencyAccess 從現有的 MFA 原則中排除。 確認 ContingencyAccess 中的使用者可以存取 SharePoint Online 和 Exchange Online。
2. 啟用策略 1：驗證不在排除組中的域加入設備上的使用者能夠訪問連線交換和連線共用點。 確認在排除群組中的使用者可以從任何裝置存取 SharePoint Online 和 Exchange。
3. 啟用策略 2：驗證不在排除組中的使用者無法從其行動裝置訪問 SharePoint 連線和連線交換。 確認在排除群組中的使用者可以從任何裝置 (Windows/iOS/Android) 存取 SharePoint 和 Exchange。
4. 啟用策略 3：驗證不在排除組中的使用者無法從公司網路訪問 SharePoint 和 Exchange，即使使用域加入的電腦也是如此。 確認在排除群組中的使用者可以從任何網路存取 SharePoint 和 Exchange。
5. 啟用策略 4：驗證所有使用者無法從行動裝置上的本機郵件應用程式獲取 Exchange Online。
6. 停用 SharePoint Online 和 Exchange Online 的現有 MFA 原則。

在這個接下來的範例 (**範例 B - 用以允許對 Salesforce 進行行動存取的應變 CA 原則**) 中，會還原一個商務應用程式的存取權。 在此案例中，客戶通常會要求只有當其銷售員工使用符合規範的裝置時，才允許他們從行動裝置存取 Salesforce (已針對使用 Azure AD 進行單一登入做設定)。 而在此情況下，中斷係指評估裝置合規性時發生問題，而中斷狀況發生在銷售小組需要存取 Salesforce 以完成交易的敏感時間。 這些應變原則將授與關鍵使用者從行動裝置存取 Salesforce 的權限，以便讓他們能夠繼續完成交易而不會中斷業務。 在此範例中，**SalesforceContingency** 包含所有需要保留存取權的銷售員工，而 **SalesAdmins** 則包含必要的 Salesforce 系統管理員。

**範例 B - 應變 CA 原則：**

* 策略 1：阻止不在銷售應急團隊中的每個人
  * 名稱： EM001 - 緊急啟用：設備合規性中斷[1/2] - 銷售人員 - 阻止除銷售隊伍緊急之外的所有使用者
  * 使用者和組：包括所有使用者。 排除 SalesAdmins 和 SalesforceContingency
  * 雲應用：銷售隊伍。
  * 條件：無
  * 授予控制：塊
  * 狀態： 已禁用
* 策略 2：阻止銷售團隊訪問移動以外的任何平臺（以減少攻擊的表面區域）
  * 名稱： EM002 - 支援 EMERGENCY： 設備合規性中斷[2/2] - 銷售人員 - 阻止除 iOS 和安卓之外的所有平臺
  * 使用者和組：包括銷售隊伍應急。 排除 SalesAdmins
  * 雲應用：銷售隊伍
  * 條件：設備平臺包括所有平臺，不包括 iOS 和安卓
  * 授予控制：塊
  * 狀態： 已禁用

啟用順序：

1. 將 SalesAdmins 和 SalesforceContingency 從 Salesforce 的現有裝置合規性原則中排除。 確認 SalesforceContingency 群組中的使用者可以存取 Salesforce。
2. 啟用策略 1：驗證 Sales 應急計畫外部的使用者無法訪問 Salesforce。 確認 SalesAdmins 和 SalesforceContingency 中的使用者可以存取 Salesforce。
3. 啟用策略 2：驗證 Sales 應急組中的使用者無法從其 Windows/Mac 筆記本電腦訪問 Salesforce，但仍可以從其行動裝置訪問。 確認 SalesAdmin 仍可從任何裝置存取 Salesforce。
4. 停用 Salesforce 的現有裝置合規性原則。

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>即使您已同盟或使用傳遞驗證，仍部署密碼雜湊同步

如果下列條件成立，也可能發生使用者鎖定：

- 您的組織將混合式身分識別解決方案與傳遞驗證或同盟搭配使用。
- 您的內部部署身分識別系統 (例如 Active Directory、AD FS 或相依元件) 無法供使用。 
 
若要更具彈性，您的組織應該[啟用密碼雜湊同步](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)，因為它可讓您在內部部署身分識別系統無法運作時，[切換成使用密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin)。

#### <a name="microsoft-recommendations"></a>Microsoft 建議
 不論您組織使用同盟還是傳遞驗證，都使用 Azure AD Connect 精靈來啟用密碼雜湊同步。

>[!IMPORTANT]
> 您無須將使用者從同盟驗證轉換成受控驗證，即可使用密碼雜湊同步。

## <a name="during-a-disruption"></a>在中斷情況發生期間

如果您選擇了實作風險降低計劃，將能夠在發生單一存取控制措施中斷的情況時，自動安然渡過。 不過，如果您選擇了建立應變計劃，則能夠在存取控制措施中斷情況發生期間，啟用您的應變原則：

1. 啟用授與目標使用者從特定網路存取特定應用程式之權限的應變原則。
2. 停用一般控制型原則。

### <a name="microsoft-recommendations"></a>Microsoft 建議

依據在中斷情況發生期間所使用的風險降低措施或應變措施而定，您的組織可能可以僅使用密碼來授與存取權。 沒有防護措施是相當大的安全性風險，必須謹慎衡量。 組織必須：

1. 作為變更控制策略的一部分，記載每個變更和先前狀態，如此才能在存取控制措施完全正常運作後，立即復原您實作的所有應變措施。
2. 假設惡意執行者會在您已停用 MFA 時，嘗試透過密碼噴濺或網路釣魚來獲取密碼。 此外，不良執行者也可能已經有先前未授與任何資源存取權的密碼，而可在這段期間內嘗試使用。 針對關鍵使用者 (例如主管)，您可以藉由在停用其 MFA 之前重設其密碼，來局部降低此風險。
3. 封存所有登入活動以識別在停用 MFA 的期間，哪些人存取了哪些資源。
4. [在此視窗期間報告的所有風險檢測進行會審](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。

## <a name="after-a-disruption"></a>在中斷情況發生後

在造成中斷情況的服務還原之後，將隨著所啟用應變計劃進行的變更復原。 

1. 啟用一般原則
2. 停用您的應變原則。 
3. 將您在中斷情況發生期間所進行和記載的任何其他變更復原。
4. 如果您使用了緊急存取帳戶，請記得重新產生認證，並在您的緊急存取帳戶程序中一併實際保護新的認證詳細資料。
5. 繼續對可疑活動中斷後[報告的所有風險檢測進行會審](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。
6. [使用 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) 來撤銷所有已簽發的重新整理權杖，以將一組使用者設為目標。 對於在中斷情況發生期間所使用的特殊權限帳戶來說，撤銷所有重新整理權杖是很重要的，此做法將強制他們重新驗證並符合所還原原則的控制措施。

## <a name="emergency-options"></a>緊急選項

 如果發生緊急情況，並且您的組織以前未實施緩解或應急計畫，則請按照[使用者鎖定備用的意外情況](#contingencies-for-user-lockout)部分中的建議進行操作，如果這些建議已經使用條件訪問策略來強制實施 MFA。
如果組織使用個別使用者 MFA 舊版原則，則您可以考慮使用下列替代方案：

1. 如果您有公司網路連出 IP 位址，您可以將它們新增為受信任的 IP，以只針對公司網路啟用驗證。
   1. 如果您沒有輸出 IP 位址的目錄，或是必須啟用公司網路內部及外部的存取權，您可以透過指定 0.0.0.0/1 與 128.0.0.0/1，將整個 IPv4 位址空間新增為受信任的 IP。

>[!IMPORTANT]
 > 如果將受信任的 IP 位址擴展到取消阻止訪問，將不會生成與 IP 位址關聯的風險檢測（例如，不可能的旅行或不熟悉的位置）。

>[!NOTE]
 > 只有在使用 [Azure AD Premium 授權](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)的情況下，才能為 Azure MFA 設定[受信任的 IP](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings)。

## <a name="learn-more"></a>深入了解

* [Azure AD 驗證文件](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [在 Azure AD 中管理緊急存取系統管理帳戶](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [在 Azure 活動目錄中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [如何設定混合式 Azure Active Directory 已聯結裝置](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Windows Hello 企業版部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [密碼指引 - Microsoft 研究](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf) \(英文\)
* [Azure 活動目錄條件訪問中有哪些條件？](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Azure 活動目錄條件訪問中有哪些訪問控制項？](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
