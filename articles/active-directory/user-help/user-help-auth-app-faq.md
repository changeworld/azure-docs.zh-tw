---
title: 有關 Microsoft 身份驗證器應用&答案的問題 - Azure AD
description: 有關 Microsoft 身份驗證應用和雙重驗證的常見問題和答案 （FAQ）。
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 86b2311c31e5f291968773ac2e34ebc4f6a92003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284002"
---
# <a name="frequently-asked-questions-faqs-about-the-microsoft-authenticator-app"></a>有關 Microsoft 身份驗證器應用的常見問題 （FAQ）

本文將回答有關 Microsoft Authenticator 應用程式的常見問題。 如果您沒有看到您問題的解答，請移至 [Microsoft 驗證器應用程式論壇](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。

Microsoft Authenticator 應用程式取代了 Azure Authenticator 應用程式，當您使用 Azure Multi-Factor Authentication 時，建議使用此應用程式。 Microsoft Authenticator 應用程式適用於 [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 和 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

## <a name="frequently-asked-questions"></a>常見問題集

| 問題 | 解決方法 |
| -------- | -------- |
| 我可以在 Android 微軟身份驗證器上截取 OTP 代碼的螢幕截圖嗎？ | 預設情況下，當獲取身份驗證器的螢幕截圖時，將隱藏所有 OTP 代碼以更好地保護我們的使用者，因此，根據 Microsoft 身份驗證器 Android 版本 6.2003.1704，將隱藏所有 OTP 代碼。 如果使用者希望查看螢幕截圖中的 OTP 代碼或允許其他應用捕獲身份驗證器的螢幕，則可以通過在身份驗證器應用中啟用"螢幕捕獲"設置，然後重新開機應用來執行此操作。 |
| Authenticator 會為我儲存何種資料，而我該如何刪除它？ | Microsoft Authenticator 應用程式會收集三種類型的資訊：<ul><li>您新增帳戶時提供的帳戶資訊。 這項資料可以藉由移除帳戶來移除。</li><li>診斷日誌資料僅保留在應用中，直到您選擇 **"發送日誌**"**應用的説明功能表**以向 Microsoft 發送日誌。 這些日誌檔包含個人資料，如您的電子郵件地址（如），alain@contoso.com伺服器或 IP 位址和設備資料（如設備名稱和作業系統版本），個人資料僅限於説明解決應用問題所必需的資訊。 您可以隨時在應用程式中檢視這些記錄檔，以查看所收集的資訊。 如果您傳送記錄檔，驗證應用程式的工程師就可以使用此資訊來對客戶回報問題進行疑難排解。</li><li>非個人可識別的使用資料，例如"啟動添加帳戶流/成功添加帳戶"或"已批准的通知"。 這項資料是我們的工程決策中不可或缺的一部分，可協助我們判斷哪些功能對您而言很重要，以及要透過應用程式更新形式來改善的地方。 作為應用使用者，您會在第一次啟動應用時看到此資料收集的通知，並被告知可以在應用的 **"設置"** 頁上將其關閉。 您可以隨時啟用或停用此設定。</li></ul> |
| 應用程式中的程式碼有什麼作用？ | 打開 Microsoft 身份驗證器應用時，您將看到添加的帳戶為磁貼。<li>在 iOS 設備上，您的工作或學校帳戶以及您的個人 Microsoft 帳戶在帳戶的全屏視圖中將可以看到六位或八位數位（通過點擊帳戶磁貼訪問）。<br><br>![應用程式中的 [帳戶] 畫面](./media/user-help-auth-app-faq/auth-app-accounts.png)<li>對於 iOS 設備上的其他帳戶和 Android 設備上的所有帳戶，您將在應用的"帳戶"頁中看到六位或八位數位。 您將使用這些驗證碼來驗證您就是您所說的人員。 使用使用者名和密碼登錄後，您將鍵入與該帳戶關聯的驗證碼。 例如，如果您正在登錄 Contoso 帳戶，並且正在使用 iOS 設備，則點擊帳戶磁貼，然後使用驗證碼驗證您的身份。 如果您是凱蒂登錄到 Outlook 帳戶，則遵循相同的步驟。<br><br>![點擊應用中的帳戶磁貼後](./media/user-help-auth-app-faq/katy-signin.png)<br><br>點擊 Contoso 帳戶磁貼後，Katy 在全屏視圖中看到驗證碼，然後輸入 895823 完成登錄。<br><br>![應用程式中的驗證碼螢幕](./media/user-help-auth-app-faq/verification-code.png) |
| 為什麼驗證碼旁邊的數字會持續倒數計時？ | 您可能會看到 30 秒的計時器在作用中的驗證碼旁邊倒數計時。 此計時器讓您永遠不會使用同一個驗證碼登入兩次。 與密碼不同，我們不希望您記得此號碼。 其概念是，只有可存取您手機的人才會知道您的驗證碼。 |
| 為什麼我的帳戶磚會呈現灰色？ | 有些組織要求 Microsoft Authenticator 應用程式透過單一登入運作，並保護組織資源。 在此情況下，就無法使用帳戶進行雙步驟驗證，而且帳戶會顯示為灰色或非作用中狀態。 此類型的帳戶通常稱為「訊息代理程式」帳戶。 |
| 什麼是裝置註冊？ | 您的組織可能希望您註冊設備，以便他們瞭解設備是否正在訪問安全資源，如檔和應用程式。 它們還可能打開條件訪問，以減少意外訪問這些資源的風險。 您可以在 **"設置"** 中取消註冊設備，但可能會失去對 Outlook 中的電子郵件、OneDrive 中的檔的存取權限，並且您將失去使用電話登錄的功能。 |
| 我是否需要連線到網際網路或我的網路，以取得並使用驗證碼？ | 驗證碼不需要您位於網際網路上或連線到資料，因此您不需要使用手機服務來登入。 此外，由於應用程式會在您關閉它之後立即停止執行，因此，它將不會耗盡您的電池。 |
| 我只有在開啟應用程式時才會收到通知。 不會在應用程式關閉時收到通知。 | 如果您會收到通知，但不會收到警示，即使已開啟響鈴，還是應該檢查您的應用程式設定。 請確定應用程式已開啟來針對通知使用音效或震動。 如果您完全不會收到通知，則應檢查下列各項：<ul><li>您的電話處於「請勿打擾」還是無訊息模式？ 這些模式讓應用程式無法傳送通知。</li><li>您可以收到來自其他應用程式的通知嗎？ 如果收不到，可能就是手機上的網路連線問題，或是來自 Android 或 Apple 的通知通道問題。 您可以透過手機設定嘗試解決網路連線問題，但您可能需要與服務提供者聯繫，以協助解決 Android 或 Apple 通知通道問題。</li><li>您可以在應用程式上收到某些帳戶的通知，但收不到其他帳戶的通知嗎？ 如果是，請從您的應用程式移除有問題的帳戶、再次新增它以允許通知，然後查看是否能解決問題。</li></ul>如果您嘗試了上述所有步驟，但仍發生問題，建議您傳送記錄檔以進行診斷。 開啟應用程式、移至 [說明]****，然後選取 [傳送記錄]****。 在那之後，移至 [Microsoft Authenticator 應用程式論壇](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) \(英文\)，讓我們了解您遇到的問題，以及到目前為止已採取的步驟。 |
| 我正在應用程式中使用驗證碼，但該如何切換到推播通知？ | 您可以針對公司或學校帳戶 (如果已由您的系統管理員開啟) 或個人 Microsoft 帳戶設定這類通知，但通知將不適用於 Google 或 Facebook 之類的協力廠商帳戶。<br><br>若要將您的個人帳戶切換到通知，您將必須使用該帳戶重新註冊裝置。 移至 [新增帳戶]****，選取 [個人 Microsoft 帳戶]****，然後以使用者名稱和密碼登入。<br><br>您的組織會決定是否要針對您的公司或學校帳戶允許單鍵通知，如此一來，您的組織可能會關閉此功能。|
|通知適用於非 Microsoft 帳戶嗎 | 不適用，通知只適用於 Microsoft 帳戶與 Azure Active Directory 帳戶。 如果您的公司或學校使用 Azure AD 帳戶，他們可能會關閉此功能。 |
| 我取得新的裝置或已從備份還原我的裝置。 我要如何重新設定 Microsoft Authenticator 應用程式中的帳戶？ | 如果您正在運行 iOS 或 Android 設備，並且如果您在舊設備上打開了**雲備份**，則可以使用舊備份在新設備上恢復帳戶憑據。 如需詳細資訊，請參閱[使用 Microsoft Authenticator 應用程式來備份和復原帳戶認證](user-help-auth-app-backup-recovery.md)一文。 |
| 我遺失了我的裝置，或已移動至新的裝置。 如何確定通知不會繼續傳送到舊裝置？ | 將 Microsoft Authenticator 應用程式新增至新的裝置並不會自動移除舊裝置上的應用程式。 縱使從舊裝置上刪除應用程式也不能完全刪除。 您必須從舊裝置中刪除應用程式，並告訴 Microsoft 或您的組織捨棄舊裝置，然後從您的帳戶中將裝置取消註冊。<ul><li>**從使用個人 Microsoft 帳戶的裝置中移除應用程式。** 轉到[帳戶安全](https://account.microsoft.com/security) 頁面的兩步驗證區域，然後選擇關閉舊設備的驗證。</li><li>**從使用公司或學校 Microsoft 帳戶的裝置中移除應用程式。** 轉到[MyApps](https://myapps.microsoft.com/)頁面的兩步驗證區域或組織的自訂門戶，然後選擇關閉舊設備的驗證。</li></ul> |
| 如何從應用程式移除帳戶？ | <ul><li>**Ios。** 點擊要從應用中刪除的帳戶的帳戶磁貼以載入帳戶的全屏視圖。 點擊 **"刪除帳戶"** 選項從應用中刪除帳戶。</li><li>**Android。** 從主畫面中選取功能表按鈕，然後選取 [編輯帳戶]****。 點選帳戶名稱旁邊的 [X]****。</li></ul>如果已向您的組織註冊您的裝置，您可能需要完成額外的步驟才能移除您的帳戶。 在這些裝置上，Microsoft Authenticator 應用程式會自動註冊為裝置系統管理員。 如果您想要完全解除安裝應用程式，則需要先在應用程式設定中取消註冊應用程式。 |
| 為什麼應用程式要求這麼多權限？ | 下面是可能需要訪問的許可權的完整清單，以及應用如何使用這些許可權。 您看到的特定許可權將取決於您擁有的電話類型。<ul><li>**相機。** 當您新增公司、學校或非 Microsoft 帳戶時，可用來掃描 QR 代碼。</li><li>**連絡人和手機。** 該應用程式需要此許可權，以便它可以搜索手機上的現有工作或學校 Microsoft 帳戶並將其添加到應用，從而有助於確保您的帳戶正常工作。</li><li>**短信。** 用來確定您的電話號碼符合記錄上的號碼。 當您第一次使用個人 Microsoft 帳戶登入時。 我們會將簡訊傳送到先前下載應用程式的手機，其中會包含 6-8 個數字的驗證碼。 我們會在簡訊中找到這個驗證碼，而不是要求您找到它，並將它輸入應用程式中。</li><li>**透過其他應用程式進行。** 用來驗證您身分識別的通知也會顯示在任何其他可能正在執行的應用程式上。</li><li>**從網際網路接收資料。** 需要有此權限，才能傳送通知。</li><li>**防止手機進入休眠。** 如果您向組織註冊裝置，則組織可以在您的手機上變更這個原則。</li><li>**控制震動。** 您可以選擇是否要在收到驗證身分識別的通知時震動。</li><li>**使用指紋硬體。** 驗證身分識別時，部分公司和學校帳戶需要額外的 PIN。 為了簡化此程序，我們可讓您使用指紋，而不是輸入 PIN。</li><li> **檢視網路連線。** 當您新增 Microsoft 帳戶時，應用程式需要網路/網際網路連線。</li><li>**讀取儲存體的內容**。 只有在您透過應用程式設定回報技術問題時，才會使用這個權限。 會收集您儲存體中的部分資訊來診斷問題。</li><li>**完整的網路存取權。** 需要有此權限，才能傳送驗證您身分識別的通知。</li><li>**在啟動時執行。** 如果您重新啟動手機，此權限可確保您會繼續收到驗證身分識別的通知。</li></ul> |
| 為什麼 Microsoft Authenticator 應用程式可讓您不需解除鎖定裝置就能核准要求？ | 您不需解除鎖定裝置來核准驗證要求，因為您只需要證明您帶著您的電話。 雙步驟驗證需要證明兩件事 - 一件是您知道的，另一件則是您擁有的。 您知道的就是密碼。 您擁有的是您的手機（使用 Microsoft 身份驗證器應用設置並註冊為 MFA 證明。因此，讓電話和批准請求符合第二個身份驗證因素的條件。 |
| 為什麼在 Apple Watch 上打開 Microsoft 身份驗證器應用時，我的所有帳戶都未顯示？ | Microsoft Authenticator 應用程式只支援使用 Microsoft 個人或是學校或公司帳戶搭配 Apple Watch 附屬應用程式上的推播通知。 對於其他帳戶（如 Google 或 Facebook），您必須在手機上打開身份驗證器應用才能查看驗證碼。 |
| 為什麼我無法批准或拒絕 Apple Watch 上的通知？ | 首先，請確保您已升級到 Microsoft 身份驗證器應用，iPhone 上的版本為 6.0.0 或更高版本。 在那之後，在您的 Apple Watch 上開啟 Microsoft Authenticator 隨附應用程式，然後使用位於任何帳戶下方的 [設定]**** 按鈕來尋找它們。 您必須完成該設定程序，才能核准那些帳戶的通知。 |
| 我收到蘋果手錶和我的手機之間的通信錯誤。 我能做什麼來進行疑難排解？ | 若在您的 Watch 跟手機完成通訊之前，畫面先進入睡眠，會出現此項錯誤。<br><br><b>如果在設定期間發生這個情況：</b><br>試著重新設定，確保在過程完成之前，您的 Watch 都沒有進入睡眠。 同時請開啟您手機上的 APP，在出現任何提示時進行回應。<br><br>如果您的手機和手錶仍然無法通信，您可以嘗試以下操作：<ol><li>在 iPhone 上強制結束 Microsoft Authenticator 手機應用程式並再次開啟。</li><li>在 Apple Watch 上強制結束隨附應用程式。<ol><li> 在 Watch 上開啟 Microsoft Authenticator 隨附應用程式</li><li>按住側邊按鈕，直到 [關機]**** 畫面出現為止。</li><li>放開側邊按鈕，然後按住 Digital Crown 以強制結束作用中的應用程式。</li></ol></li><li>針對您的手機和 Watch 關閉藍芽和 Wi-Fi，然後重新開啟它們。</li><li>重新啟動您的 iPhone 和 Watch。</li></ol><b>如果嘗試批准通知時發生這種情況：</b><br>下次您在 Apple Watch 上確認通知時，確保畫面不要進入睡眠，直到完成要求且聽見提示確認成功之音效。 |
| 為什麼適用于 Apple Watch 的 Microsoft 身份驗證器配套應用程式無法同步或顯示在我的手錶上？ | 如果應用未顯示在手錶上，請嘗試以下操作： <ol><li>確定您的 Watch 正在執行 watchOS 4.0 或更新版本。</li><li>再次同步處理您的 Watch。</li></ol> |
| 我的 Apple Watch 附屬應用程式當機。 可以將我的當機記錄傳送給您，讓您可以進行調查嗎？ | 您首先必須確保您已選擇與我們共用您的分析。 如果您是 TestFlight 使用者，則已註冊。 否則，您可以移至 [設定] > [隱私權] > [分析]****，然後同時選取 [共用 iPhone 與 Watch 分析]**** 和 [與應用程式開發人員共用]**** 選項。<br><br>註冊之後，您可以嘗試重現當機狀況，如此一來，您的當機記錄就會自動傳送給我們以利調查。 但是，如果您無法重現崩潰，您可以手動複製日誌檔並將其發送給我們。<ol><li>在手機上開啟 Watch 應用程式、移至 [設定] > [一般]****，然後按一下 [複製 Watch 分析]****。</li><li>在 [設定] > [隱私權] > [分析] > [分析資料]**** 下方尋找對應的當機，然後手動複製整個文字。</li><li>在手機上開啟 Microsoft Authenticator 應用程式，將複製的文字貼到 [傳送記錄]**** 頁面上的 [與應用程式開發人員共用]**** 文字方塊中。</li></ol> |
| 什麼是應用程式鎖定功能，其如何協助提升我使用裝置時的安全性？ | 若要讓單次密碼、應用程式資訊和應用程式設定更加安全，您可以在 Microsoft Authenticator 應用程式中開啟應用程式鎖定功能。 從 Microsoft 身份驗證器應用的 **"設置"** 螢幕打開應用鎖定，這意味著每次打開 Microsoft 身份驗證器應用時，系統都會要求您使用 PIN 或生物識別進行身份驗證。 此功能提供了額外的保護，您在 Microsoft 身份驗證器應用中批准通知的方式不會更改。<br><br>**注意**<br>由於設備註冊可能發生在 Microsoft 身份驗證器應用之外的其他位置（如在公司門戶應用中或在 Android 帳戶設置中）進行，因此不能保證應用鎖定會阻止使用者訪問 Microsoft身份驗證器應用。 |
| 為什麼我會收到關於帳戶活動的通知？ | 為了讓您更了解個人 Microsoft 帳戶的現況，我們會將活動通知傳送至您的 Microsoft Authenticator 應用程式。 這些通知會發生變更後立即顯示，以利確保您的安全性。 我們之前透過電子郵件和簡訊傳送這些通知，而現在已擴充為包括應用程式。 如需這些活動通知的詳細資訊，請參閱[若您的帳戶出現異常登入，將會如何](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)。 要更改接收通知的位置，請登錄"[哪裡可以與您聯繫您的帳戶的非關鍵帳戶提醒](https://account.live.com/SecurityNotifications/Update)頁面"。 |
| 使用 iOS 附帶的預設郵件應用登錄到我的工作或學校帳戶時，系統會提示 Microsoft 身份驗證器應用提供安全驗證資訊。 輸入該資訊並返回到郵件應用後，我收到錯誤。 我該怎麼辦？ | 這最有可能發生的原因是，您的登錄和郵件應用發生在兩個不同的應用中，從而導致初始後臺登錄過程停止工作並失敗。 要嘗試解決此問題，我們建議您在登錄到郵件應用時選擇螢幕右下角的**Safari**圖示。 通過移動到 Safari，整個登錄過程發生在單個應用中，從而允許您成功登錄到應用。 |
| 我的一次性密碼 （OTP） 代碼不起作用。 我該怎麼辦？ | 確保設備上的日期和時間正確且正在自動同步。 如果日期和時間錯誤或不同步，代碼將無法工作。 |
| Windows 10 移動作業系統于 2019 年 12 月被棄用。 Windows 移動作業系統上的 Microsoft 身份驗證器是否會也被棄用？ | 2020 年 2 月 28 日之後，所有 Windows 移動作業系統上的 Microsoft 身份驗證器應用將不受支援。 使用者將沒有資格在上述日期後收到任何新的更新的應用程式。 2020 年 2 月 28 日之後，目前支援在所有 Windows Mobile 作業系統上使用 Microsoft 身份驗證器進行身份驗證的 Microsoft 服務將開始停用其支援。 為了對 Microsoft 服務進行身份驗證，我們強烈建議所有使用者在此日期之前切換到備用身份驗證機制。 |

## <a name="next-steps"></a>後續步驟

- 如果您在獲取個人 Microsoft 帳戶的驗證碼時遇到問題，請參閱[Microsoft 帳戶安全資訊&驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文的**故障排除驗證碼問題**部分。

- 如果您需要雙步驟驗證的詳細資訊，請參閱[對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)

- 如果需要有關安全資訊的詳細資訊，請參閱[安全資訊（預覽）概述](user-help-security-info-overview.md)

- 如果您無法在此處找到解答，我們希望能夠聆聽您的意見。 請移至 [Microsoft Authenticator 應用程式論壇 (英文)](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) 張貼您的問題並從社群取得協助，或在此頁面上留下您的意見。
