---
title: 使用條款 - Azure 活動目錄 |微軟文檔
description: 開始使用 Azure 活動目錄使用條款在獲取存取權限之前向員工或來賓顯示資訊。
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480958"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure 活動目錄的使用條款

Azure AD 的使用條款提供了一種簡單的方法，組織可以使用該方法向最終使用者顯示資訊。 此呈現可確保使用者看到合法或合規性需求的相關免責聲明。 本文介紹如何開始使用條款。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概觀影片

以下視頻提供了使用條款的快速概述。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

如需其他影片，請參閱：
- [如何在 Azure 活動目錄中部署使用條款](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何在 Azure 活動目錄中推出使用條款](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>使用條款可以做什麼？

Azure AD 使用條款具有以下功能：

- 要求員工或客人在獲得存取權限之前接受您的使用條款。
- 要求員工或客人在獲取存取權限之前接受您在每台設備上的使用條款。
- 要求員工或客人定期接受您的使用條款。
- 要求員工或客人在 Azure 多重要素驗證 （MFA） 中註冊安全資訊之前接受您的使用條款。
- 要求員工在 Azure AD 自助服務密碼重設 （SSPR） 中註冊安全資訊之前接受您的使用條款。
- 為組織中的所有使用者提供一般使用條款。
- 根據使用者屬性顯示特定的使用條款（例如。 醫生與護士或國內員工與國際員工，透過使用[動態群組](../users-groups-roles/groups-dynamic-membership.md)完成)。
- 在訪問高業務影響應用程式（如 Salesforce）時提供特定的使用條款。
- 以不同語言提供使用條款。
- 列出誰已經或尚未接受您的使用條款。
- 協助符合隱私權法規。
- 顯示符合性和審核的使用條款活動日誌。
- 使用[Microsoft 圖形 API（](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement)當前預覽版）創建和管理使用條款。

## <a name="prerequisites"></a>Prerequisites

要使用和配置 Azure AD 的使用條款，您必須具有：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。
   - 如果您沒有其中任何一個訂用帳戶，您可以[取得 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[啟用 Azure AD Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下列其中一個管理帳戶，將用於您要設定的目錄：
   - 全域管理員
   - 安全性系統管理員
   - 條件式存取系統管理員

## <a name="terms-of-use-document"></a>使用規定文件

Azure AD 的使用條款使用 PDF 格式來顯示內容。 此 PDF 檔案可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。 若要支援行動裝置上的使用者，建議在 PDF 中使用 24 點的字型大小。

## <a name="add-terms-of-use"></a>新增使用規定

完成使用條款文檔後，請使用以下過程添加它。

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 Azure。
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。

   ![條件訪問 - 使用條款邊欄選項卡](./media/terms-of-use/tou-blade.png)

1. 按一下 [新增規定]****。

   ![用於指定使用條款設置的新使用期限窗格](./media/terms-of-use/new-tou.png)

1. 在 **"名稱"** 框中，輸入將在 Azure 門戶中使用的使用條款的名稱。
1. 在 [顯示名稱]**** 方塊中，輸入使用者在登入時會看見的標題。
1. 對於**使用條款文檔**，流覽到您最終確定的使用條款 PDF 並選擇它。
1. 選擇使用條款文檔的語言。 語言選項可讓您上傳多個使用規定，各有不同的語言。 終端使用者會看到的使用規定版本將以其瀏覽器喜好設定為基礎。
1. 要要求最終使用者在接受之前查看使用條款，請使用 "**要求使用者"將使用條款擴展到** **On**。
1. 要求最終使用者接受您在從其訪問的每個設備上的使用條款，請將 **"要求使用者在每台設備上的同意"** 設置為**On**。 如果啟用了此選項，使用者可能需要安裝其他應用程式。 有關詳細資訊，請參閱[每個設備的使用條款](#per-device-terms-of-use)。
1. 如果要在計畫上過期使用條款同意，請使用 "**過期同意"** 設置為**On**。 設定為 [開啟] 時，系統會顯示兩個額外的排程設定。

   ![過期同意設置以設置開始日期、頻率和持續時間](./media/terms-of-use/expire-consents.png)

1. 使用 **"開始過期**"和 **"頻率**"設置指定使用期限到期的計畫。 下表會顯示幾個範例設定的結果：

   | 到期生效時間 | 頻率 | 結果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 從今天開始，使用者必須接受使用條款，然後每月重新接受。 |
   | 未來的日期  | 每月 | 從今天開始，使用者必須接受使用條款。 抵達未來的日期時，同意將會到期，且使用者必須於每個月重新接受。  |

   例如，如果您將 [到期生效時間] 設定為 [1 月 1 日]****，並將 [頻率] 設定為 [每月]****，則下列兩個使用者所遇到的到期方式將如下所示：

   | User | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用**重新接受要求（天）前的持續時間**設置指定使用者必須重新接受使用條款前的天數。 這可讓使用者遵循自己的排程。 例如，如果您將期間設定為 **30** 天，則下列兩個使用者所遇到的到期方式將如下所示：

   | User | 第一次接受日期 | 第一次到期日期 | 第二次到期日期 | 第三次到期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   您可以同時使用 [到期同意]**** 和 [需要重新接受之前的期間 (天)]**** 設定，但通常您只會使用其中一個。

1. 在 **"條件訪問**"下，使用 **"使用條件訪問強制"策略範本**清單來選擇範本以強制執行使用條款。

   ![條件訪問下拉清單以選擇策略範本](./media/terms-of-use/conditional-access-templates.png)

   | [範本] | 描述 |
   | --- | --- |
   | **所有來賓的雲端應用程式存取權** | 將為所有來賓和所有雲應用創建條件訪問策略。 此原則會影響 Azure 入口網站。 建立此原則之後，您可能需要登出然後再登入。 |
   | **所有使用者的雲端應用程式存取權** | 將為所有使用者和所有雲應用創建條件訪問策略。 此原則會影響 Azure 入口網站。 建立此原則之後，您必須登出然後再登入。 |
   | **自訂原則** | 選擇此使用條款將應用於的使用者、組和應用。 |
   | **稍後創建條件訪問策略** | 創建條件訪問策略時，此使用條款將顯示在授予控制清單中。 |

   >[!IMPORTANT]
   >條件訪問策略控制項（包括使用條款）不支援對服務帳戶實施。 我們建議從條件訪問策略中排除所有服務帳戶。

    自訂條件訪問策略支援精細的使用條款，下一個歸為特定的雲應用程式或使用者組。 有關詳細資訊，請參閱[快速入門：在訪問雲應用之前需要接受使用條款](require-tou.md)。

1. 按一下 **[建立]**。

   如果選擇了自訂條件訪問範本，則會顯示一個新螢幕，允許您創建自訂條件訪問策略。

   ![如果選擇自訂條件訪問策略範本，則新建條件訪問窗格](./media/terms-of-use/custom-policy.png)

   現在，您應該會看到您的新使用條款。

   ![使用條款欄選項卡中列出的新使用條款](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>檢視已接受和已拒絕的人員報表

[使用規定] 刀鋒視窗會顯示已接受和已拒絕的使用者計數。 這些計數和接受/拒絕的計數在使用條款的生命週期記憶體儲。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。

   ![使用條款刀片列出使用者顯示的數量已被接受和拒絕](./media/terms-of-use/view-tou.png)

1. 有關使用條款，請按一下 **"已接受**"或 **"已拒絕**"下的數位以查看使用者的目前狀態。

   ![使用條款同意窗格列出已接受的使用者](./media/terms-of-use/accepted-tou.png)

1. 若要檢視個別使用者的歷程記錄，請按一下省略符號 (**...**)，然後按一下 [檢視記錄]****。

   ![查看使用者的歷史記錄內容功能表](./media/terms-of-use/view-history-menu.png)

   在 [檢視記錄] 窗格中，您可以看見所有接受、拒絕及到期的歷程記錄。

   ![查看歷史記錄窗格列出歷史記錄接受、拒絕和過期的使用者](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>檢視 Azure AD 稽核記錄

如果要查看其他活動，Azure AD 的使用條款包括稽核記錄。 使用者每次同意時都會觸發稽核記錄事件，並將儲存 **30 天**。 您可以在入口網站中檢視這些記錄或將其下載為 .csv 檔案。

若要開始使用 Azure AD 稽核記錄，請使用下列程序：

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選擇使用條款。
1. 按一下 [檢視稽核記錄]****。

   ![突出顯示的"查看稽核記錄"選項的使用條款邊欄選項卡](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 稽核記錄畫面上，您可以使用提供的清單來篩選資訊，以找出特定的稽核記錄資訊。

   您也可以按一下 [下載]****，將資訊下載成 .csv 檔案，以在本機中使用。

   ![Azure AD 稽核記錄螢幕清單日期、目標策略、啟動項和活動](./media/terms-of-use/audit-logs-tou.png)

   如果您按一下某個記錄，螢幕會出現具有額外活動詳細資料的窗格。

   ![顯示活動、活動狀態（由啟動）啟動的日誌的活動詳細資訊，目標策略](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用者使用條款的外觀

創建並強制執行使用條款後，處於作用域中的使用者將在登錄期間看到以下螢幕。

![使用者登錄時出現的示例使用條款](./media/terms-of-use/user-tou.png)

使用者可以查看使用條款，並在必要時使用按鈕進行放大和縮小。

![使用縮放按鈕的使用條款視圖](./media/terms-of-use/zoom-buttons.png)

下面的螢幕顯示了行動裝置上的使用條款的外觀。

![當使用者在行動裝置上登錄時顯示的示例使用條款](./media/terms-of-use/mobile-tou.png)

使用者只需接受一次使用條款，並且不會在後續登錄中再次看到使用條款。

### <a name="how-users-can-review-their-terms-of-use"></a>使用者如何查看其使用條款

使用者可以使用以下過程查看並查看他們接受的使用條款。

1. 登錄到[https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角，按一下您的名稱並選取 [設定檔]****。

   ![打開使用者的窗格的"我的應用"網站](./media/terms-of-use/tou14.png)

1. 在您的設定檔頁面上，按一下 [檢閱使用規定]****。

   ![顯示"審閱使用條款"連結的使用者的設定檔頁面](./media/terms-of-use/tou13a.png)

1. 您可以在此檢閱您已接受的使用規定。

## <a name="edit-terms-of-use-details"></a>編輯使用條款詳細資訊

您可以編輯使用條款的一些詳細資訊，但不能修改現有文檔。 下列程序說明如何編輯詳細資料。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選擇要編輯的使用條款。
1. 按一下 [編輯使用規定]****。
1. 在 [編輯使用規定] 窗格中，變更名稱、顯示名稱，或要求使用者展開值。

   如果要更改其他設置（如 PDF 文檔），則要求使用者在每台設備上同意、過期同意、重新接受前的持續時間或條件訪問策略，則必須創建新的使用條款。

   ![編輯顯示名稱和展開選項的使用條款窗格](./media/terms-of-use/edit-tou.png)

1. 按一下 [儲存]**** 以儲存您的變更。

   儲存您的變更後，使用者將不需要重新接受這些編輯後的規定。

## <a name="add-a-terms-of-use-language"></a>添加使用語言術語

以下過程介紹如何添加使用語言的術語。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選擇要編輯的使用條款。
1. 在 [詳細資料] 窗格中，按一下 [語言]**** 索引標籤。

   ![所選使用條款並在詳細資訊窗格中顯示"語言"選項卡](./media/terms-of-use/languages-tou.png)

1. 按一下 [新增語言]****。
1. 在 [新增使用規定語言] 窗格中，上傳當地語系化的 PDF 並選取的語言。

   ![添加使用語言窗格的術語與上傳當地語系化 PDF 的選項](./media/terms-of-use/language-add-tou.png)

1. 按一下 [新增]**** 來新增語言。

## <a name="per-device-terms-of-use"></a>每個設備的使用條款

**通過要求使用者在每台設備設置上同意**，您可以要求最終使用者接受您在所訪問的每個設備上的使用條款。 最終使用者將需要在 Azure AD 中註冊其設備。 註冊設備時，裝置識別碼 用於強制執行每台設備的使用條款。

以下是支援的平台與軟體清單。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **原生應用程式** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome (搭配擴充功能)** | 是 | 是 | 是 |  |

每個設備的使用條款具有以下約束：

- 一部裝置只能加入至單一租用戶。
- 使用者必須具有加入其裝置的權限。
- 不支援「Intune 註冊」應用程式。
- 不支援 Azure AD B2B 使用者。

如果使用者的裝置未加入，他們將會收到要求加入其裝置的訊息。 使用者的體驗將取決於平台和軟體。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 裝置

如果使用者是使用 Windows 10 和 Microsoft Edge，他們將會收到類似下列的訊息以[加入其裝置](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)。

![Windows 10 和 Microsoft 邊緣 - 指示您的設備必須註冊的消息](./media/terms-of-use/per-device-win10-edge.png)

如果他們是使用 Chrome，系統將會提示他們安裝 [Windows 10 Accounts 擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) \(英文\)。

### <a name="register-an-ios-device"></a>註冊 iOS 設備

如果使用者正在使用 iOS 設備，系統將提示他們安裝 Microsoft[身份驗證器應用](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)。

### <a name="register-an-android-device"></a>註冊 Android 設備

如果使用者正在使用 Android 設備，系統將提示他們安裝 Microsoft[身份驗證器應用](https://play.google.com/store/apps/details?id=com.azure.authenticator)。

### <a name="browsers"></a>瀏覽器

如果使用者是使用不支援的瀏覽器，系統將會提示他們使用其他瀏覽器。

![必須註冊指示您的設備，但不支援瀏覽器的消息](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>刪除使用規定

您可以使用以下過程刪除舊使用條款。

1. 登入 Azure 並瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 上的 [使用規定]****。
1. 選取您想要移除的使用規定。
1. 按一下 [刪除規定]****。
1. 出現詢問您是否要繼續的訊息時，請按一下 [是]****。

   ![要求確認刪除使用條款的消息](./media/terms-of-use/delete-tou.png)

   您不應再看到您的使用條款。

## <a name="deleted-users-and-active-terms-of-use"></a>已刪除的使用者和活動使用條款

根據預設，已刪除的使用者會在 Azure AD 中以已刪除的狀態保留 30 天，在這段期間內，系統管理員可以視需要加以還原。 經過 30 天後，該使用者將永久刪除。 此外，全域管理員可以使用 Azure Active Directory 入口網站，在該時段結束之前明確地[永久刪除最近刪除的使用者](../fundamentals/active-directory-users-restore.md)。 一個使用者已被永久刪除，有關該使用者的後續資料將從活動使用條款中刪除。 與已刪除的使用者有關的稽核資訊仍會保留在稽核記錄中。

## <a name="policy-changes"></a>原則變更

條件訪問策略將立即生效。 當此情況發生時，系統管理員便會開始看到「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。

> [!IMPORTANT]
> 如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
>
> - 根據使用條款啟用條件訪問策略
> - 或建立第二個使用條款

## <a name="b2b-guests-preview"></a>B2B 來賓 (預覽)

大多陣列織都有一個流程，讓員工同意其組織的使用條款和隱私權聲明。 但在 Azure AD 企業對企業 (B2B) 來賓透過 SharePoint 或 Teams 新增的情況下，您要如何強制執行相同的同意程序呢？ 使用條件訪問和使用條款，可以直接對 B2B 來賓使用者強制實施策略。 在邀請兌換流程期間，向使用者顯示使用條款。 這項支援目前只能預覽。

使用規定只會在使用者於 Azure AD 中具有來賓帳戶的情況下顯示。 SharePoint Online 目前具有[臨時外部共用收件者體驗](/sharepoint/what-s-new-in-sharing-in-targeted-release)，用於共用文件或不需要使用者具有來賓帳戶的資料夾。 在這種情況下，不會顯示使用條款。

![使用者和組窗格 - 包含選中所有來賓使用者選項的選項卡](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>針對雲端應用程式的支援 (預覽)

使用規定可以用於不同的雲端應用程式，例如 Azure 資訊保護和 Microsoft Intune。 這項支援目前只能預覽。

### <a name="azure-information-protection"></a>Azure 資訊保護

可以為 Azure 資訊保護應用配置條件訪問策略，並在使用者訪問受保護的文檔時需要使用條款。 這將在使用者首次訪問受保護文檔之前觸發使用條款。

![選擇 Microsoft Azure 資訊保護應用的雲應用窗格](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 註冊

您可以為 Microsoft Intune 註冊應用配置條件訪問策略，並要求在 Intune 中註冊設備之前使用條款。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的規定解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

![雲應用窗格，選擇 Microsoft Intune 應用](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> [對於每台設備的使用條款](#per-device-terms-of-use)，不支援 Intune 註冊應用。

## <a name="frequently-asked-questions"></a>常見問題集

**問：如何查看使用者何時/是否接受使用條款？**<br />
答：在 [使用規定] 刀鋒視窗上，按一下 [接受]**** 底下的數字。 您也可以檢視或搜尋 Azure AD 稽核記錄中的接受活動。 如需詳細資訊，請參閱檢視已接受和已拒絕的人員報表和[檢視 Azure AD 稽核記錄](#view-azure-ad-audit-logs)。

**問：資訊會儲存多久時間？**<br />
答：使用者在使用條款報告中計數，接受/拒絕者在使用條款的生命週期記憶體儲。 Azure AD 稽核記錄會儲存 30 天。

**問：為什麼在使用條款報告與 Azure AD 稽核記錄不同時看到不同數量的同意？**<br />
答：使用條款報告存儲為該使用條款的存留期，而 Azure AD 稽核記錄存儲 30 天。 此外，使用條款報告僅顯示使用者當前同意狀態。 例如，如果使用者拒絕並接受，則使用條款報告將僅顯示該使用者的接受。 如果您需要查看歷程記錄，您可以使用 Azure AD 稽核記錄。

**問：如果我編輯使用條款的詳細資訊，是否需要使用者再次接受？**<br />
答：不，如果管理員編輯使用條款的詳細資訊（名稱、顯示名稱、要求使用者展開或添加語言），則不需要使用者重新接受新術語。

**問：我可以更新現有的使用條款文檔嗎？**<br />
答：目前無法更新現有使用條款文檔。 要更改使用條款文檔，您必須創建新的使用條款實例。

**問：如果超連結在 PDF 文檔的使用條款中，最終使用者能否按一下它們？**<br />
答：是的，最終使用者可以選擇指向其他頁面的超連結，但不支援指向文檔中部分的連結。

**問：使用條款是否可以支援多種語言？**<br />
答： 會。 目前，管理員可以為單個使用條款配置 108 種不同的語言。 系統管理員可以上傳多個 PDF 文件，並以相對應的語言 (最多 108 個) 標記那些文件。 當使用者登入時，我們會查看其瀏覽器語言偏好設定，並顯示相符的文件。 如果沒有相符項目，我們將會顯示預設文件，也就是第一個上傳的文件。

**問：何時觸發使用條款？**<br />
答：登入體驗期間即會觸發使用規定。

**問：哪些應用程式也可以將使用規定設為目標？**<br />
答：您可以使用現代身份驗證在企業應用程式上創建條件訪問策略。 如需詳細資訊，請參閱[企業應用程式](./../manage-apps/view-applications-portal.md)。

**問：是否可以向給定使用者或應用添加多個使用條款？**<br />
答：是的，通過創建針對這些組或應用程式的多個條件訪問策略。 如果使用者屬於多個使用條款的範圍，則他們一次接受一個使用條款。

**問：如果使用者拒絕使用條款，會發生什麼情況？**<br />
答：使用者會遭封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。

**問：是否可以不接受以前接受的使用條款？**<br />
答：您可以[查看以前接受的使用條款](#how-users-can-review-their-terms-of-use)，但目前沒有一種不接受的方法。

**問：如果同時使用 Intune 條款及條件會發生什麼事？**<br />
答：如果已配置 Azure AD 使用條款和[Intune 條款及條件](/intune/terms-and-conditions-create)，則使用者必須接受這兩種條款及條件。 如需詳細資訊，請參閱[為您組織的部落格文章選擇正確的條款解決方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) \(英文\)。

**問：使用服務條款用於身份驗證的終結點是什麼？**<br />
答：使用條款使用以下終結點進行身份驗證：https://tokenprovider.termsofuse.identitygovernance.azure.com和https://account.activedirectory.windowsazure.com。 如果您的組織具有用於註冊的 URL 的允許清單，則需要將這些終結點添加到允許清單中，以及用於登錄的 Azure AD 終結點。

## <a name="next-steps"></a>後續步驟

- [快速入門：必須接受使用規定才可存取雲端應用程式](require-tou.md)
- [Azure 活動目錄中的條件訪問最佳做法](best-practices.md)
