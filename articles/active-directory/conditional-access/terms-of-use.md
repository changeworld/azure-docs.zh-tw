---
title: 使用規定-Azure Active Directory |Microsoft Docs
description: 在取得存取權之前，請先使用 Azure Active Directory 使用規定將資訊呈現給員工或來賓。
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 12/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e64b0af455ab1f84653093b26654530ee3dfab
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232772"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 使用規定

Azure AD 使用規定原則提供了一種簡單的方法，讓組織可用來向使用者呈現資訊。 此呈現可確保使用者看到合法或合規性需求的相關免責聲明。 本文說明如何開始使用使用條款 (ToU) 原則。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概觀影片

下列影片提供使用規定原則的快速總覽。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

如需其他影片，請參閱：
- [如何在 Azure Active Directory 中部署使用規定原則](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何在 Azure Active Directory 中推出使用規定原則](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>使用規定可以做些什麼？

Azure AD 使用規定原則具有下列功能：

- 要求員工或來賓在取得存取權之前，先接受您的使用條款原則。
- 要求員工或來賓在取得存取權之前，在每個裝置上接受您的使用規定原則。
- 要求員工或來賓依照週期性排程接受您的使用條款原則。
- 要求員工或來賓在 Azure AD Multi-Factor Authentication (MFA) 註冊安全性資訊之前，先接受您的使用規定原則。
- 在 Azure AD 自助式密碼重設 (SSPR) 中註冊安全性資訊之前，要求員工先接受您的使用規定原則。
- 針對您組織中的所有使用者，提供一般的使用條款原則。
- 根據使用者屬性顯示特定的使用規定原則 (例如 醫生與護士或國內員工與國際員工，透過使用[動態群組](../enterprise-users/groups-dynamic-membership.md)完成)。
- 在存取高業務影響應用程式（例如 Salesforce）時，提供特定的使用條款原則。
- 以不同的語言呈現使用條款原則。
- 列出誰已接受或未接受您的使用規定原則。
- 協助符合隱私權法規。
- 顯示適用于合規性和 audit 的使用規定原則活動記錄。
- 使用 [Microsoft Graph api](/graph/api/resources/agreement?view=graph-rest-beta) (目前為預覽) 來建立和管理使用規定原則。

## <a name="prerequisites"></a>必要條件

若要使用及設定 Azure AD 的使用規定原則，您必須具備下列條件：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。
   - 如果您沒有其中任何一個訂用帳戶，您可以[取得 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[啟用 Azure AD Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下列其中一個管理帳戶，將用於您要設定的目錄：
   - 全域管理員
   - 安全性系統管理員
   - 條件式存取管理員

## <a name="terms-of-use-document"></a>使用規定文件

Azure AD 使用規定原則會使用 PDF 格式來呈現內容。 此 PDF 檔案可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。 若要支援行動裝置上的使用者，建議在 PDF 中使用 24 點的字型大小。

## <a name="add-terms-of-use"></a>新增使用規定

當您完成使用規定原則檔之後，請使用下列程式將其新增。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 Azure。
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

    ![條件式存取-使用規定分頁](./media/terms-of-use/tou-blade.png)

1. 按一下 [新增規定]。

    ![用來指定使用條款設定的新使用規定窗格](./media/terms-of-use/new-tou.png)

1. 在 [ **名稱** ] 方塊中，輸入將在 Azure 入口網站中使用之使用條款原則的名稱。
1. 在 [顯示名稱] 方塊中，輸入使用者在登入時會看見的標題。
1. 針對 **使用規定檔**，請流覽至您已完成的使用條款原則 PDF，然後選取它。
1. 選取您的使用規定原則檔的語言。 [語言] 選項可讓您上傳多個使用規定原則，各有不同的語言。 終端使用者將會看到的使用規定原則版本會以其瀏覽器喜好設定為基礎。
1. 若要要求終端使用者在接受使用規定原則之前先加以查看，請設定 [ **要求使用者將使用規定擴充** 至 **開啟**]。
1. 若要要求使用者在其所存取的每個裝置上接受您的使用規定原則，請設定 [**要求使用者在每個裝置****上** 同意]。 如果啟用此選項，使用者可能需要安裝其他應用程式。 如需詳細資訊，請參閱 [依裝置使用的使用條款](#per-device-terms-of-use)。
1. 如果您想要在排程上讓使用規定原則同意過期，請將 [ **同意過期** ] 設定為 [ **開啟**]。 設定為 [開啟] 時，系統會顯示兩個額外的排程設定。

    ![使同意設定過期，以設定開始日期、頻率和持續時間](./media/terms-of-use/expire-consents.png)

1. 使用 [ **開始時間** ] 和 [ **頻率** ] 設定來指定使用規定原則過期的排程。 下表會顯示幾個範例設定的結果：

   | 到期生效時間 | 頻率 | 結果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 從今天開始，使用者必須接受使用規定原則，然後每個月重新接受一次。 |
   | 未來的日期  | 每月 | 從今天開始，使用者必須接受使用規定原則。 抵達未來的日期時，同意將會到期，且使用者必須於每個月重新接受。  |

   例如，如果您將 [到期生效時間] 設定為 [1 月 1 日]，並將 [頻率] 設定為 [每月]，則下列兩個使用者所遇到的到期方式將如下所示：

   | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 在 **reacceptance 需要 (天之前使用持續時間)** 設定來指定使用者必須重新接受使用規定原則的天數。 這可讓使用者遵循自己的排程。 例如，如果您將期間設定為 **30** 天，則下列兩個使用者所遇到的到期方式將如下所示：

   | 使用者 | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   您可以使用 **過期的同意** 和 **持續時間，在 reacceptance 需要 (天)** 設定一起使用，但通常您會使用其中一個。

1. 在 [ **條件式存取**] 下，使用 [強制搭配條件式 **存取原則範本** ] 清單來選取要強制使用 [使用規定] 原則的範本。

    ![用來選取原則範本的 [條件式存取] 下拉式清單](./media/terms-of-use/conditional-access-templates.png)

   | 範本 | 描述 |
   | --- | --- |
   | **所有來賓的雲端應用程式存取權** | 系統會為所有來賓和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 一旦建立此項，您可能必須登出再登入。 |
   | **所有使用者的雲端應用程式存取權** | 將會針對所有使用者和所有雲端應用程式建立條件式存取原則。 此原則會影響 Azure 入口網站。 一旦建立，您就必須登出再登入。 |
   | **自訂原則** | 選取將套用此使用規定原則的使用者、群組和應用程式。 |
   | **稍後建立條件式存取原則** | 此使用規定原則會在建立條件式存取原則時，出現在 grant control 清單中。 |

   >[!IMPORTANT]
   >條件式存取原則控制 (包括使用規定原則) 不支援對服務帳戶強制執行。 建議您排除條件式存取原則中的所有服務帳戶。

    自訂條件式存取原則可對特定雲端應用程式或使用者群組啟用細微的使用規定原則。 如需詳細資訊，請參閱 [快速入門：在存取雲端應用程式之前，必須先接受使用](require-tou.md)規定。

1. 按一下頁面底部的 [新增] 。

    如果您已選取自訂的條件式存取範本，則會出現新的畫面，讓您建立自訂的條件式存取原則。

   ![如果您選擇自訂條件式存取原則範本，則新的條件式存取窗格](./media/terms-of-use/custom-policy.png)

   您現在應該會看到新的使用規定原則。

   ![使用規定分頁中列出的新使用規定](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>檢視已接受和已拒絕的人員報表

[使用規定] 刀鋒視窗會顯示已接受和已拒絕的使用者計數。 這些計數和已接受/已拒絕的人員都會儲存在使用條款原則的存留期內。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。

    ![使用規定的分頁清單中，列出已接受和已拒絕的使用者節目數目](./media/terms-of-use/view-tou.png)

1. 在 [使用規定] 原則中，按一下 [已 **接受** ] 或 [已 **拒絕** ] 下的數位，以查看使用者的目前狀態。

    ![使用規定同意] 窗格中列出已接受的使用者](./media/terms-of-use/accepted-tou.png)

1. 若要檢視個別使用者的歷程記錄，請按一下省略符號 (**...**)，然後按一下 [檢視記錄]。

    ![使用者的查看歷程記錄內容功能表](./media/terms-of-use/view-history-menu.png)

   在 [檢視記錄] 窗格中，您可以看見所有接受、拒絕及到期的歷程記錄。

   ![[View History] 窗格會列出使用者的歷程記錄接受、拒絕和到期日](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>檢視 Azure AD 稽核記錄

如果您想要查看其他活動，Azure AD 使用規定原則包含 audit 記錄檔。 使用者每次同意時都會觸發稽核記錄事件，並將儲存 **30 天**。 您可以在入口網站中檢視這些記錄或將其下載為 .csv 檔案。

若要開始使用 Azure AD 稽核記錄，請使用下列程序：

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。
1. 選取使用規定原則。
1. 按一下 [檢視稽核記錄]。

    ![已反白顯示 View audit logs 選項的使用規定分頁](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 稽核記錄畫面上，您可以使用提供的清單來篩選資訊，以找出特定的稽核記錄資訊。

    您也可以按一下 [下載]，將資訊下載成 .csv 檔案，以在本機中使用。

   ![Azure AD audit 記錄畫面清單日期、目標原則、起始者和活動](./media/terms-of-use/audit-logs-tou.png)

   如果您按一下某個記錄，螢幕會出現具有額外活動詳細資料的窗格。

   ![記錄的活動詳細資料，顯示活動、活動狀態、起始者、目標原則](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用者的使用規定如下

一旦建立並強制執行使用規定原則之後，使用者（範圍內的使用者）在登入期間將會看到下列畫面。

![使用者登入時顯示的範例使用規定](./media/terms-of-use/user-tou.png)

使用者可以查看使用條款原則，並視需要使用按鈕來放大和縮小。

![使用縮放按鈕來查看使用規定](./media/terms-of-use/zoom-buttons.png)

下列畫面顯示如何在行動裝置上查看使用條款原則。

![使用者登入行動裝置時所顯示的範例使用規定](./media/terms-of-use/mobile-tou.png)

使用者只需要接受使用規定原則一次，在後續登入時不會再看到使用規定原則。

### <a name="how-users-can-review-their-terms-of-use"></a>使用者可以如何審核其使用條款

使用者可以使用下列程式來檢查並查看他們已接受的使用條款原則。

1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角，按一下您的名稱並選取 [設定檔]。

    ![開啟使用者窗格的 MyApps 網站](./media/terms-of-use/tou14.png)

1. 在您的設定檔頁面上，按一下 [檢閱使用規定]。

    ![使用者的設定檔頁面面，顯示 [評論使用規定] 連結](./media/terms-of-use/tou13a.png)

1. 從該處，您可以複習您已接受的使用規定原則。

## <a name="edit-terms-of-use-details"></a>編輯使用條款詳細資料

您可以編輯部分使用規定原則的詳細資料，但無法修改現有的檔。 下列程序說明如何編輯詳細資料。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。
1. 選取您要編輯的使用條款原則。
1. 按一下 [編輯使用規定]。
1. 在 [編輯使用規定] 窗格中，您可以變更下列各項：
    - **名稱** -這是未與終端使用者共用的 ToU 內部名稱
    - **顯示名稱** –這是使用者在觀看 ToU 時可以看到的名稱
    - **要求使用者展開使用** 規定–將此設為 **On** ，會在接受使用規定原則檔之前，強制終端使用者先展開這些條款。
    -  (Preview) 您可以 **更新現有的使用** 規定檔
    - 您可以將語言新增至現有的 ToU

   如果有您想要變更的其他設定（例如 PDF 檔），要求使用者在每部裝置上同意、讓同意到期、在 reacceptance 之前的期間或條件式存取原則，您必須建立新的使用規定原則。

    ![編輯顯示不同的語言選項 ](./media/terms-of-use/edit-terms-use.png)

1. 完成之後，按一下 [ **儲存** ] 以儲存您的變更。

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>更新現有使用條款的版本或 pdf

1.  登入 Azure 並流覽至 [使用規定](https://aka.ms/catou)
2.  選取您要編輯的使用條款原則。
3.  按一下 [編輯使用規定]。
4.  針對您想要更新新版本的語言，請按一下 [動作] 資料行底下的 [ **更新** ]。

    ![編輯使用規定窗格，顯示名稱和展開選項](./media/terms-of-use/edit-terms-use.png)

5.  在右側窗格中，上傳新版本的 pdf
6.  如果您想要要求使用者在下次登入時接受這個新版本，也有一個切換選項 **需要重新接受** 。 如果您需要使用者重新接受，下一次嘗試存取您的條件式存取原則中定義的資源時，系統會提示他們接受此新版本。 如果您不需要使用者進行重新接受，其先前的同意將會維持最新狀態，而且只有未事先同意或同意過期的新使用者才會看到新版本。

    ![醒目提示 [編輯使用條款重新接受] 選項](./media/terms-of-use/re-accept.png)

7.  當您上傳新的 pdf 並決定重新接受之後，請按一下窗格底部的 [新增]。
8.  您現在會在 [檔] 資料行底下看到最新版本。

## <a name="view-previous-versions-of-a-terms-of-use"></a>查看使用中的舊版使用條款

1.  登入 Azure 並瀏覽至 https://aka.ms/catou 上的 [使用規定]。
2.  選取您要查看其版本歷程記錄的使用條款原則。
3.  按一下 **語言和版本歷程記錄**
4.  按一下 [ **查看先前的版本]。**

    ![檔詳細資料，包括語言版本](./media/terms-of-use/document-details.png)

5.  您可以按一下檔的名稱以下載該版本

## <a name="see-who-has-accepted-each-version"></a>查看誰已接受每個版本

1.  登入 Azure 並瀏覽至 https://aka.ms/catou 上的 [使用規定]。
2.  若要查看目前已接受的使用者，請在您想要的 ToU 中，按一下 [已 **接受** ] 資料行底下的數位。
3.  根據預設，下一個頁面會顯示每位使用者接受 ToU 的目前狀態。
4.  如果您想要查看先前的同意事件，您可以從 [**目前狀態**] 下拉式清單中選取 [**全部**]。 現在您可以在每個版本的詳細資料中查看每個使用者的事件，以及發生什麼事。
5.  或者，您可以從 [ **版本**  ] 下拉式清單中選取特定版本，以查看誰已接受該特定版本。


## <a name="add-a-terms-of-use-language"></a>新增使用規定語言

下列程式說明如何新增使用規定語言。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。
1. 選取您要編輯的使用條款原則。
1. 按一下 [**編輯詞彙**]
1. 按一下頁面底部的 [ **新增語言** ]。
1. 在 [新增使用規定語言] 窗格中，上傳當地語系化的 PDF 並選取的語言。

    ![使用規定選取並顯示 [詳細資料] 窗格中的 [語言] 索引標籤](./media/terms-of-use/select-language.png)

1. 按一下 [新增語言]。
1. 按一下 [儲存]

1. 按一下 [新增] 來新增語言。

## <a name="per-device-terms-of-use"></a>每一裝置的使用規定

[ **要求使用者在每個裝置上同意** ] 設定可讓您要求使用者在其存取的每個裝置上接受您的使用規定原則。 終端使用者將需要在 Azure AD 中註冊其裝置。 註冊裝置時，裝置識別碼會用來強制執行每個裝置上的使用規定原則。

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
- 不支援「Intune 註冊」應用程式。 確定它已從任何需要使用規定原則的條件式存取原則中排除。
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

您可以使用下列程式來刪除舊的使用條款原則。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]。
1. 選取您要移除的使用條款原則。
1. 按一下 [刪除規定]。
1. 出現詢問您是否要繼續的訊息時，請按一下 [是]。

    ![要求確認以刪除使用條款的訊息](./media/terms-of-use/delete-tou.png)

   您應該不會再看到使用規定原則。

## <a name="deleted-users-and-active-terms-of-use"></a>已刪除的使用者和使用中的使用條款

根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。 已永久刪除使用者，將會從使用中的使用條款原則中移除該使用者的後續資料。 與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="policy-changes"></a>原則變更

條件式存取原則會立即生效。 當此情況發生時，系統管理員便會開始看到「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。

> [!IMPORTANT]
> 如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
>
> - 使用規定原則的條件式存取原則已啟用
> - 或建立第二個使用規定原則

## <a name="b2b-guests"></a>B2B 來賓

大部分的組織都有一個流程，可讓員工同意其組織的使用規定原則和隱私權聲明。 但在 Azure AD 企業對企業 (B2B) 來賓透過 SharePoint 或 Teams 新增的情況下，您要如何強制執行相同的同意程序呢？ 使用條件式存取和使用規定原則，您可以直接對 B2B 來賓使用者強制執行原則。 在邀請兌換流程中，使用者會看到使用規定原則。 這項支援目前只能預覽。

使用規定原則只會在使用者在 Azure AD 中有來賓帳戶時顯示。 SharePoint Online 目前有特定的 [外部共用收件者體驗](/sharepoint/what-s-new-in-sharing-in-targeted-release) ，可共用不需要使用者擁有來賓帳戶的檔或資料夾。 在此情況下，不會顯示使用條款原則。

![[使用者和群組] 窗格-已核取 [包含所有來賓使用者的索引標籤] 選項](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>雲端應用程式的支援

使用規定原則可以用於不同的雲端應用程式，例如 Azure 資訊保護和 Microsoft Intune。 這項支援目前只能預覽。

### <a name="azure-information-protection"></a>Azure 資訊保護

您可以為 Azure 資訊保護應用程式設定條件式存取原則，並在使用者存取受保護的檔時要求使用規定原則。 這將會在使用者第一次存取受保護檔之前觸發使用規定原則。

![已選取 Microsoft Azure 資訊保護應用程式的 [雲端應用程式] 窗格](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 註冊

您可以為 Microsoft Intune 的註冊應用程式設定條件式存取原則，並在 Intune 中註冊裝置之前要求使用規定原則。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的規定解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

![已選取 Microsoft Intune 應用程式的 [雲端應用程式] 窗格](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> [每一裝置的使用](#per-device-terms-of-use)規定不支援 Intune 註冊應用程式。

## <a name="frequently-asked-questions"></a>常見問題集

**問：當使用規定啟用時，我無法使用 PowerShell 進行登入。**<br />
答：只有在以互動方式進行驗證時，才能接受使用規定。

**問：如何? 查看使用者是否已接受使用規定？**<br />
答：在 [使用規定] 刀鋒視窗上，按一下 [接受] 底下的數字。 您也可以檢視或搜尋 Azure AD 稽核記錄中的接受活動。 如需詳細資訊，請參閱檢視已接受和已拒絕的人員報表和[檢視 Azure AD 稽核記錄](#view-azure-ad-audit-logs)。

**問：資訊會儲存多久時間？**<br />
答：使用規定報表中的使用者計數和已接受/已拒絕的使用者會儲存在使用規定的存留期內。 Azure AD 稽核記錄會儲存 30 天。

**問：為什麼我在使用規定報表與 Azure AD audit 記錄檔中看到不同數目的同意？**<br />
答：使用規定報表會儲存在該使用規定原則的存留期內，而 Azure AD audit 記錄會儲存30天。 此外，[使用規定] 報告只會顯示使用者目前的同意狀態。 例如，如果使用者拒絕然後接受，則 [使用規定] 報告只會顯示該使用者的 [接受]。 如果您需要查看歷程記錄，您可以使用 Azure AD 稽核記錄。

**問：如果我編輯使用規定原則的詳細資料，是否需要使用者重新接受？**<br />
答：否，如果系統管理員編輯使用規定原則的詳細資料 (名稱、顯示名稱、要求使用者展開或新增語言) ，則不需要使用者重新接受新的條款。

**問：我可以更新現有的使用條款原則檔嗎？**<br />
答：目前您無法更新現有的使用條款原則檔。 若要變更使用條款原則檔，您將必須建立新的使用規定原則實例。

**問：如果超連結是在使用規定原則 PDF 檔中，使用者是否可以按一下它們？**<br />
答：是，終端使用者可以選取其他頁面的超連結，但不支援檔中的區段連結。 此外，從 Azure AD MyApps/我的帳戶入口網站存取時，使用原則 Pdf 的超連結無法運作。

**問：使用規定原則是否可支援多種語言？**<br />
A：是。 目前有108種不同的語言，系統管理員可以設定單一使用規定原則。 系統管理員可以上傳多個 PDF 文件，並以相對應的語言 (最多 108 個) 標記那些文件。 當使用者登入時，我們會查看其瀏覽器語言偏好設定，並顯示相符的文件。 如果沒有相符項目，我們將會顯示預設文件，也就是第一個上傳的文件。

**問：何時觸發使用規定原則？**<br />
答：登入體驗期間會觸發使用規定原則。

**問：我可以將使用規定原則設為目標的應用程式為何？**<br />
答：您可以使用新式驗證，在企業應用程式上建立條件式存取原則。 如需詳細資訊，請參閱[企業應用程式](./../manage-apps/view-applications-portal.md)。

**問：是否可以將多個使用規定原則新增至指定的使用者或應用程式？**<br />
答：是，藉由建立以這些群組或應用程式為目標的多個條件式存取原則。 如果使用者落在多個使用規定原則的範圍內，則會一次接受一項使用規定原則。

**問：如果使用者拒絕使用規定原則，會發生什麼事？**<br />
答：使用者會遭封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。

**問：是否可以辦法取消接受先前接受的使用規定原則？**<br />
答：您可以 [複習先前接受的使用規定原則](#how-users-can-review-their-terms-of-use)，但目前沒有方法可以辦法取消接受。

**問：如果同時使用 Intune 條款及條件會發生什麼事？**<br />
答：如果您已設定 Azure AD 使用規定和 [Intune 條款及條件](/intune/terms-and-conditions-create)，則使用者必須接受這兩者。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的條款解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

**問：使用規定服務會使用哪些端點進行驗證？**<br />
答：使用規定會利用下列端點進行驗證： https://tokenprovider.termsofuse.identitygovernance.azure.com 和 https://account.activedirectory.windowsazure.com 。 如果您的組織具有可供註冊之 Url 的允許清單，您必須將這些端點新增至您的允許清單，以及用於登入 Azure AD 端點。

## <a name="next-steps"></a>後續步驟

- [快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)
