---
title: 使用規定-Azure Active Directory |Microsoft Docs
description: 在取得存取權之前，請先使用 Azure Active Directory 使用規定將資訊呈現給員工或來賓。
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3781a87bde283de3b798f840274db1dd5ea3ac7e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366491"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 使用規定

Azure AD 使用規定提供一種簡單的方法，讓組織可用來向使用者呈現資訊。 此呈現可確保使用者看到合法或合規性需求的相關免責聲明。 本文說明如何開始使用使用條款。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概觀影片

下列影片提供使用規定的快速總覽。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

如需其他影片，請參閱：
- [如何在 Azure Active Directory 中部署使用規定](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何在 Azure Active Directory 中推出使用規定](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>使用規定可以做些什麼？

Azure AD 使用規定具有下列功能：

- 要求員工或來賓在取得存取權之前，先接受您的使用規定。
- 要求員工或來賓在取得存取權之前，先在每個裝置上接受您的使用規定。
- 要求員工或來賓依據週期性排程接受您的使用規定。
- 要求員工或來賓在 Azure Multi-Factor Authentication (MFA) 註冊安全性資訊之前，先接受您的使用規定。
- 要求員工在 Azure AD 自助式密碼重設 (SSPR) 中註冊安全性資訊之前，先接受您的使用規定。
- 提供組織中所有使用者的一般使用條款。
- 根據使用者屬性提出特定使用規定 (例如 醫生與護士或國內員工與國際員工，透過使用[動態群組](../enterprise-users/groups-dynamic-membership.md)完成)。
- 在存取高業務影響應用程式（例如 Salesforce）時，提供特定的使用條款。
- 以不同的語言呈現使用條款。
- 列出已接受或未接受使用規定的人員。
- 協助符合隱私權法規。
- 顯示使用規定活動的記錄，以進行合規性和審核。
- 使用 [Microsoft Graph api](/graph/api/resources/agreement?view=graph-rest-beta) 來建立和管理使用規定 (目前為預覽) 。

## <a name="prerequisites"></a>先決條件

若要使用及設定 Azure AD 使用規定，您必須具備：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。
   - 如果您沒有其中任何一個訂用帳戶，您可以[取得 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[啟用 Azure AD Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下列其中一個管理帳戶，將用於您要設定的目錄：
   - 全域管理員
   - 安全性系統管理員
   - 條件式存取系統管理員

## <a name="terms-of-use-document"></a>使用規定文件

Azure AD 使用規定使用 PDF 格式來呈現內容。 此 PDF 檔案可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。 若要支援行動裝置上的使用者，建議在 PDF 中使用 24 點的字型大小。

## <a name="add-terms-of-use"></a>新增使用規定

當您完成使用規定檔之後，請使用下列程式將它加入。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 Azure。
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。

   ![條件式存取-使用規定分頁](./media/terms-of-use/tou-blade.png)

1. 按一下 [新增規定]****。

   ![用來指定使用條款設定的新使用規定窗格](./media/terms-of-use/new-tou.png)

1. 在 [ **名稱** ] 方塊中，輸入將在 Azure 入口網站中使用之使用條款的名稱。
1. 在 [顯示名稱]**** 方塊中，輸入使用者在登入時會看見的標題。
1. 針對 **使用規定檔**，請流覽至您已完成的使用規定 PDF 並加以選取。
1. 選取您的使用規定檔的語言。 語言選項可讓您上傳多個使用規定，各有不同的語言。 終端使用者會看到的使用規定版本將以其瀏覽器喜好設定為基礎。
1. 若要要求**使用者在接受**之前先查看使用條款，請設定 **[要求使用者展開使用**規定]。
1. 若要要求使用者在其所存取的每個裝置上接受您的使用規定，請設定 [**要求使用者在每個裝置****上**同意]。 如果啟用此選項，使用者可能需要安裝其他應用程式。 如需詳細資訊，請參閱 [依裝置使用的使用條款](#per-device-terms-of-use)。
1. 如果您想要在排程上讓使用規定過期同意，請將 [ **同意過期** ] 設定為 [ **開啟**]。 設定為 [開啟] 時，系統會顯示兩個額外的排程設定。

   ![使同意設定過期，以設定開始日期、頻率和持續時間](./media/terms-of-use/expire-consents.png)

1. 使用 [ **開始時間** ] 和 [ **頻率** ] 設定來指定使用規定到期的排程。 下表會顯示幾個範例設定的結果：

   | 到期生效時間 | 頻率 | 結果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 從今天開始，使用者必須接受使用規定，然後重新接受每個月。 |
   | 未來的日期  | 每月 | 從今天開始，使用者必須接受使用規定。 抵達未來的日期時，同意將會到期，且使用者必須於每個月重新接受。  |

   例如，如果您將 [到期生效時間] 設定為 [1 月 1 日]****，並將 [頻率] 設定為 [每月]****，則下列兩個使用者所遇到的到期方式將如下所示：

   | User | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用 [ **重新接受之前的持續時間] 需要 (天) ** 設定來指定使用者必須重新接受使用規定之前的天數。 這可讓使用者遵循自己的排程。 例如，如果您將期間設定為 **30** 天，則下列兩個使用者所遇到的到期方式將如下所示：

   | User | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   您可以同時使用 [到期同意]**** 和 [需要重新接受之前的期間 (天)]**** 設定，但通常您只會使用其中一個。

1. 在 [ **條件式存取**] 下，使用 [強制搭配條件式 **存取原則範本** ] 清單來選取要強制使用使用條款的範本。

   ![用來選取原則範本的 [條件式存取] 下拉式清單](./media/terms-of-use/conditional-access-templates.png)

   | 範本 | 描述 |
   | --- | --- |
   | **所有來賓的雲端應用程式存取權** | 系統會為所有來賓和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您可能需要登出然後再登入。 |
   | **所有使用者的雲端應用程式存取權** | 將會針對所有使用者和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 建立此原則之後，您必須登出然後再登入。 |
   | **自訂原則** | 選取將套用此使用條款的使用者、群組和應用程式。 |
   | **稍後建立條件式存取原則** | 此使用規定會在建立條件式存取原則時出現在授與控制清單中。 |

   >[!IMPORTANT]
   >條件式存取原則控制 (包括使用規定) 不支援對服務帳戶強制執行。 建議您排除條件式存取原則中的所有服務帳戶。

    自訂條件式存取原則可對特定雲端應用程式或使用者群組啟用細微的使用規定。 如需詳細資訊，請參閱 [快速入門：在存取雲端應用程式之前，必須先接受使用](require-tou.md)規定。

1. 按一下 [建立]。

   如果您已選取自訂的條件式存取範本，則會出現新的畫面，讓您建立自訂的條件式存取原則。

   ![如果您選擇自訂條件式存取原則範本，則新的條件式存取窗格](./media/terms-of-use/custom-policy.png)

   您現在應該會看到新的使用規定。

   ![使用規定分頁中列出的新使用規定](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>檢視已接受和已拒絕的人員報表

[使用規定] 刀鋒視窗會顯示已接受和已拒絕的使用者計數。 這些計數和已接受/已拒絕的人員都會儲存在使用規定的存留期內。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。

   ![使用規定的分頁清單中，列出已接受和已拒絕的使用者節目數目](./media/terms-of-use/view-tou.png)

1. 針對使用規定，請按一下 [已 **接受** ] 或 [已 **拒絕** ] 下的數位，以查看使用者的目前狀態。

   ![使用規定同意] 窗格中列出已接受的使用者](./media/terms-of-use/accepted-tou.png)

1. 若要檢視個別使用者的歷程記錄，請按一下省略符號 (**...**)，然後按一下 [檢視記錄]****。

   ![使用者的查看歷程記錄內容功能表](./media/terms-of-use/view-history-menu.png)

   在 [檢視記錄] 窗格中，您可以看見所有接受、拒絕及到期的歷程記錄。

   ![[View History] 窗格會列出使用者的歷程記錄接受、拒絕和到期日](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>檢視 Azure AD 稽核記錄

如果您想要查看其他活動，Azure AD 使用規定包含 audit 記錄。 使用者每次同意時都會觸發稽核記錄事件，並將儲存 **30 天**。 您可以在入口網站中檢視這些記錄或將其下載為 .csv 檔案。

若要開始使用 Azure AD 稽核記錄，請使用下列程序：

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選取使用規定。
1. 按一下 [檢視稽核記錄]****。

   ![已反白顯示 View audit logs 選項的使用規定分頁](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 稽核記錄畫面上，您可以使用提供的清單來篩選資訊，以找出特定的稽核記錄資訊。

   您也可以按一下 [下載]****，將資訊下載成 .csv 檔案，以在本機中使用。

   ![Azure AD audit 記錄畫面清單日期、目標原則、起始者和活動](./media/terms-of-use/audit-logs-tou.png)

   如果您按一下某個記錄，螢幕會出現具有額外活動詳細資料的窗格。

   ![記錄的活動詳細資料，顯示活動、活動狀態、起始者、目標原則](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用者的使用規定如下

一旦建立並強制執行使用條款之後，使用者（範圍內的使用者）在登入期間將會看到下列畫面。

![使用者登入時顯示的範例使用規定](./media/terms-of-use/user-tou.png)

使用者可以查看使用規定，並在必要時使用按鈕來放大和縮小。

![使用縮放按鈕來查看使用規定](./media/terms-of-use/zoom-buttons.png)

下列畫面顯示使用規定在行動裝置上的外觀。

![使用者登入行動裝置時所顯示的範例使用規定](./media/terms-of-use/mobile-tou.png)

使用者只需要接受使用規定一次，在後續登入時不會再看到使用規定。

### <a name="how-users-can-review-their-terms-of-use"></a>使用者可以如何審核其使用條款

使用者可以使用下列程式來檢查並查看他們已接受的使用條款。

1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角，按一下您的名稱並選取 [設定檔]****。

   ![開啟使用者窗格的 MyApps 網站](./media/terms-of-use/tou14.png)

1. 在您的設定檔頁面上，按一下 [檢閱使用規定]****。

   ![使用者的設定檔頁面面，顯示 [評論使用規定] 連結](./media/terms-of-use/tou13a.png)

1. 您可以在此檢閱您已接受的使用規定。

## <a name="edit-terms-of-use-details"></a>編輯使用條款詳細資料

您可以編輯使用規定的一些詳細資料，但無法修改現有的檔。 下列程序說明如何編輯詳細資料。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選取您要編輯的使用條款。
1. 按一下 [編輯使用規定]****。
1. 在 [編輯使用規定] 窗格中，變更名稱、顯示名稱，或要求使用者展開值。

   如果有您想要變更的其他設定（例如 PDF 檔），要求使用者在每部裝置上同意、讓同意過期、在 reacceptance 之前的持續時間或條件式存取原則，您必須建立新的使用規定。

   ![編輯使用規定窗格，顯示名稱和展開選項](./media/terms-of-use/edit-tou.png)

1. 按一下 [確定] 儲存變更。

   儲存您的變更後，使用者將不需要重新接受這些編輯後的規定。

## <a name="add-a-terms-of-use-language"></a>新增使用規定語言

下列程式說明如何新增使用規定語言。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選取您要編輯的使用條款。
1. 在 [詳細資料] 窗格中，按一下 [語言]**** 索引標籤。

   ![使用規定選取並顯示 [詳細資料] 窗格中的 [語言] 索引標籤](./media/terms-of-use/languages-tou.png)

1. 按一下 [新增語言]****。
1. 在 [新增使用規定語言] 窗格中，上傳當地語系化的 PDF 並選取的語言。

   ![使用上傳當地語系化 Pdf 的選項新增使用規定語言窗格](./media/terms-of-use/language-add-tou.png)

1. 按一下 [新增]**** 來新增語言。

## <a name="per-device-terms-of-use"></a>每一裝置的使用規定

[ **要求使用者在每個裝置上同意** ] 設定可讓您要求使用者在其存取的每個裝置上接受您的使用規定。 終端使用者將需要在 Azure AD 中註冊其裝置。 註冊裝置時，會使用裝置識別碼來強制執行每個裝置上的使用規定。

以下是支援的平台與軟體清單。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **原生應用程式** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome (搭配擴充功能)** | 是 | 是 | 是 |  |

每一裝置的使用規定具有下列限制：

- 一部裝置只能加入至單一租用戶。
- 使用者必須具有加入其裝置的權限。
- 不支援「Intune 註冊」應用程式。 確定它已從任何需要使用規定的條件式存取原則中排除。
- 不支援 Azure AD B2B 使用者。

如果使用者的裝置未加入，他們將會收到要求加入其裝置的訊息。 使用者的體驗將取決於平台和軟體。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 裝置

如果使用者是使用 Windows 10 和 Microsoft Edge，他們將會收到類似下列的訊息以[加入其裝置](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)。

![Windows 10 和 Microsoft Edge 訊息，指出您的裝置必須註冊](./media/terms-of-use/per-device-win10-edge.png)

如果他們是使用 Chrome，系統將會提示他們安裝 [Windows 10 Accounts 擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) \(英文\)。

### <a name="register-an-ios-device"></a>註冊 iOS 裝置

如果使用者使用 iOS 裝置，系統會提示他們安裝 [Microsoft Authenticator 應用程式](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)。

### <a name="register-an-android-device"></a>註冊 Android 裝置

如果使用者使用 Android 裝置，系統會提示他們安裝 [Microsoft Authenticator 應用程式](https://play.google.com/store/apps/details?id=com.azure.authenticator)。

### <a name="browsers"></a>瀏覽器

如果使用者是使用不支援的瀏覽器，系統將會提示他們使用其他瀏覽器。

![指出您裝置必須註冊的訊息，但不支援瀏覽器](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>刪除使用規定

您可以使用下列程式來刪除舊的使用規定。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選取您想要移除的使用規定。
1. 按一下 [刪除規定]****。
1. 出現詢問您是否要繼續的訊息時，請按一下 [是]****。

   ![要求確認以刪除使用條款的訊息](./media/terms-of-use/delete-tou.png)

   您應該不會再看到使用規定。

## <a name="deleted-users-and-active-terms-of-use"></a>已刪除的使用者和使用中的使用條款

根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。 已永久刪除使用者，將會從作用中的使用規定中移除該使用者的後續相關資料。 與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="policy-changes"></a>原則變更

條件式存取原則會立即生效。 當此情況發生時，系統管理員便會開始看到「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。

> [!IMPORTANT]
> 如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
>
> - 使用規定啟用條件式存取原則
> - 或建立第二個使用條款

## <a name="b2b-guests-preview"></a>B2B 來賓 (預覽)

大部分的組織都有一個流程，可讓員工同意其組織的使用規定和隱私權聲明。 但在 Azure AD 企業對企業 (B2B) 來賓透過 SharePoint 或 Teams 新增的情況下，您要如何強制執行相同的同意程序呢？ 您可以使用條件式存取和使用規定，直接對 B2B 來賓使用者強制執行原則。 在邀請兌換流程中，使用者會看到使用規定。 這項支援目前只能預覽。

使用規定只會在使用者於 Azure AD 中具有來賓帳戶的情況下顯示。 SharePoint Online 目前有特定的 [外部共用收件者體驗](/sharepoint/what-s-new-in-sharing-in-targeted-release) ，可共用不需要使用者擁有來賓帳戶的檔或資料夾。 在此情況下，不會顯示使用規定。

![[使用者和群組] 窗格-已核取 [包含所有來賓使用者的索引標籤] 選項](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>針對雲端應用程式的支援 (預覽)

使用規定可以用於不同的雲端應用程式，例如 Azure 資訊保護和 Microsoft Intune。 這項支援目前只能預覽。

### <a name="azure-information-protection"></a>Azure 資訊保護

您可以為 Azure 資訊保護應用程式設定條件式存取原則，並且在使用者存取受保護的檔時需要使用規定。 這將會在使用者第一次存取受保護檔之前觸發使用規定。

![已選取 Microsoft Azure 資訊保護應用程式的 [雲端應用程式] 窗格](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 註冊

您可以為 Microsoft Intune 的註冊應用程式設定條件式存取原則，並在 Intune 中註冊裝置之前要求使用規定。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的規定解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

![已選取 Microsoft Intune 應用程式的 [雲端應用程式] 窗格](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> [每一裝置的使用](#per-device-terms-of-use)規定不支援 Intune 註冊應用程式。

## <a name="frequently-asked-questions"></a>常見問題集

**問：如何? 查看使用者是否已接受使用規定？**<br />
答：在 [使用規定] 刀鋒視窗上，按一下 [接受]**** 底下的數字。 您也可以檢視或搜尋 Azure AD 稽核記錄中的接受活動。 如需詳細資訊，請參閱檢視已接受和已拒絕的人員報表和[檢視 Azure AD 稽核記錄](#view-azure-ad-audit-logs)。

**問：資訊會儲存多久時間？**<br />
答：使用規定報表中的使用者計數和已接受/已拒絕的使用者會儲存在使用規定的存留期內。 Azure AD 稽核記錄會儲存 30 天。

**問：為什麼我在使用規定報表與 Azure AD audit 記錄檔中看到不同數目的同意？**<br />
答：使用規定報表會儲存在該使用規定的存留期內，而 Azure AD 審核記錄會儲存30天。 此外，[使用規定] 報告只會顯示使用者目前的同意狀態。 例如，如果使用者拒絕然後接受，則 [使用規定] 報告只會顯示該使用者的 [接受]。 如果您需要查看歷程記錄，您可以使用 Azure AD 稽核記錄。

**問：如果我編輯使用規定的詳細資料，是否需要使用者重新接受？**<br />
答：否，如果系統管理員編輯使用規定的詳細資料 (名稱、顯示名稱、要求使用者展開或新增語言) ，則不需要使用者重新接受新的條款。

**問：我是否可以更新現有的使用規定檔？**<br />
答：目前您無法更新現有的使用規定檔。 若要變更使用規定檔，您將必須建立新的使用規定實例。

**問：如果超連結是在使用規定 PDF 檔中，使用者是否可以按一下這些文字？**<br />
答：是，終端使用者可以選取其他頁面的超連結，但不支援檔中的區段連結。 此外，從 Azure AD MyApps/我的帳戶入口網站存取時，使用 Pdf 的超連結無法運作。

**問：使用規定是否支援多種語言？**<br />
A：是。 目前有108種不同的語言，系統管理員可以設定單一使用規定。 系統管理員可以上傳多個 PDF 文件，並以相對應的語言 (最多 108 個) 標記那些文件。 當使用者登入時，我們會查看其瀏覽器語言偏好設定，並顯示相符的文件。 如果沒有相符項目，我們將會顯示預設文件，也就是第一個上傳的文件。

**問：何時觸發使用規定？**<br />
答：登入體驗期間即會觸發使用規定。

**問：哪些應用程式也可以將使用規定設為目標？**<br />
答：您可以使用新式驗證，在企業應用程式上建立條件式存取原則。 如需詳細資訊，請參閱[企業應用程式](./../manage-apps/view-applications-portal.md)。

**問：是否可以將多個使用規定新增至指定的使用者或應用程式？**<br />
答：是，藉由建立以這些群組或應用程式為目標的多個條件式存取原則。 如果使用者落在多個使用規定的範圍內，則會一次接受一項使用規定。

**問：如果使用者拒絕使用規定，會發生什麼事？**<br />
答：使用者會遭封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。

**問：是否可以辦法取消接受先前接受的使用規定？**<br />
答：您可以 [複習先前接受的使用](#how-users-can-review-their-terms-of-use)規定，但目前沒有方法可以辦法取消接受。

**問：如果同時使用 Intune 條款及條件會發生什麼事？**<br />
答：如果您已設定 Azure AD 使用規定和 [Intune 條款及條件](/intune/terms-and-conditions-create)，則使用者必須接受這兩者。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的條款解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

**問：使用規定服務會使用哪些端點進行驗證？**<br />
答：使用規定會利用下列端點進行驗證： https://tokenprovider.termsofuse.identitygovernance.azure.com 和 https://account.activedirectory.windowsazure.com 。 如果您的組織具有可供註冊之 Url 的允許清單，您將需要將這些端點新增至您的允許清單，以及用於登入的 Azure AD 端點。

## <a name="next-steps"></a>後續步驟

- [快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)