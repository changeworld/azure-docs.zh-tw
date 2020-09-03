---
title: 教學課程：在 Azure AD Connect 中將 PHS 設定為 AD FS 的備份 | Microsoft Docs
description: 示範如何開啟密碼雜湊同步作為 AD FS 的備份。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9cd70d534cebf35938285208dc76bd4a6d94533
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279001"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>教學課程：在 Azure AD Connect 中將 PHS 設定為 AD FS 的備份

下列教學課程將逐步說明如何設定密碼雜湊同步，作為 AD FS 的備份和容錯移轉。  本文件也將示範在 AD FS 失敗或無法使用的情況下，如何啟用密碼雜湊同步作為主要驗證方法。

>[!NOTE] 
>雖然這些步驟通常是在緊急狀況或中斷的情況下執行的，但仍建議您測試這些步驟，並在發生中斷狀況之前確認您的程序。

>[!NOTE]
>如果您無法存取 Azure AD Connect 伺服器，或該伺服器無法存取網際網路，您可以連絡 [Microsoft 支援服務](https://support.microsoft.com/en-us/contactus/)，以協助您切換至 Azure AD 端。

## <a name="prerequisites"></a>Prerequisites
本教學課程的建置基礎為[教學課程：將單一 AD 樹系環境與雲端建立同盟](tutorial-federation.md)，必須先加以完成，才能嘗試進行本教學課程。  如果您尚未完成該教學課程，請先加以完成，再嘗試執行本文件中的步驟。

>[!IMPORTANT]
>切換至 PHS 之前，您應先建立 AD FS 環境的備份。  您可以使用 [AD FS 快速還原工具](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool)完成此作業。

## <a name="enable-phs-in-azure-ad-connect"></a>在 Azure AD Connect 中啟用 PHS
我們現在已有使用同盟的 Azure AD Connect 環境，因此第一個步驟就是開啟密碼雜湊同步，並允許 Azure AD Connect 同步處理這些雜湊。

執行下列動作：

1.  按兩下在桌面上建立的 Azure AD Connect 圖示
2.  按一下 [設定]  。
3.  在 [其他工作] 頁面上選取 [自訂同步處理選項]  ，然後按 [下一步]  。
4.  輸入全域管理員的使用者名稱和密碼。  先前的教學課程已在[這裡](tutorial-federation.md#create-a-global-administrator-in-azure-ad)建立此帳戶。
5.  在 [連線您的目錄]  畫面中，按 [下一步]  。
6.  在 [網域與 OU 篩選]  畫面上，按 [下一步]  。
7.  在 [選用功能]  畫面上勾選 [密碼雜湊同步處理]  ，然後按 [下一步]  。
![選取](media/tutorial-phs-backup/backup1.png)</br>
8.  在 [已可設定]  畫面上，按一下 [設定]  。
9.  在設定完成後，按一下 [結束]  。
10. 就這麼簡單！  大功告成。  密碼雜湊同步處理即將執行，如果 AD FS 無法使用，就可以作為備份。

## <a name="switch-to-password-hash-synchronization"></a>切換至密碼雜湊同步處理
現在，我們將說明如何切換至密碼雜湊同步處理。 在開始之前，請考慮應該在哪些情況下進行切換。 不要為了暫存的原因 (例如網絡中斷、輕微的 AD FS 問題，或影響使用者子網路的問題) 進行切換。 如果由於修正問題的時間太長而決定進行切換，請執行以下作業：

> [!IMPORTANT]
> 請注意，將密碼雜湊同步處理至 Azure AD 需要一些時間。  這表示最多可能需要 3 小時，才能完成同步處理並開始使用密碼雜湊進行驗證。

1. 按兩下在桌面上建立的 Azure AD Connect 圖示
2.  按一下 [設定]  。
3.  選取 [變更使用者登入]  ，然後按 [下一步]  。
![變更](media/tutorial-phs-backup/backup2.png)</br>
4.  輸入全域管理員的使用者名稱和密碼。  先前的教學課程已在[這裡](tutorial-federation.md#create-a-global-administrator-in-azure-ad)建立此帳戶。
5.  在 [使用者登入]  畫面上選取 [密碼雜湊同步處理]  ，並勾選 [請勿轉換使用者帳戶]  方塊。  
6.  保留依預設選取的 [啟用單一登入]  ，然後按 [下一步]  。
7.  在 [啟用單一登入]  畫面上，按 [下一步]  。
8.  在 [已可設定]  畫面上，按一下 [設定]  。
9.  設定完成後，請按一下 [結束]  。
10. 使用者現在已可使用其密碼來登入 Azure 和 Azure 服務。

## <a name="test-signing-in-with-one-of-our-users"></a>使用我們其中一個使用者來測試登入

1. 瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 使用我們在新租用戶中建立的使用者來登入。  您必須使用下列格式登入：(user@domain.onmicrosoft.com)。 透過該使用者在內部部署用來登入的密碼登入。</br>
   ![Verify](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>切換回同盟
現在，我們將說明如何切換回同盟。  若要這麼做，請執行下列步驟：

1.  按兩下在桌面上建立的 Azure AD Connect 圖示
2.  按一下 [設定]  。
3.  選取 [變更使用者登入]  ，然後按 [下一步]  。
4.  輸入全域管理員的使用者名稱和密碼。  這是在先前的教學課程中建立於[這裡](tutorial-federation.md#create-a-global-administrator-in-azure-ad)的帳戶。
5.  在 [使用者登入]  畫面上，選取 [與 AD FS 同盟]  ，然後按 [下一步]  。  
6. 在 [網域系統管理員認證] 頁面上，輸入 contoso\Administrator 使用者名稱與密碼，然後按一下 [下一步]  。
7. 在 [AD FS 伺服器陣列] 畫面上，按 [下一步]  。
8. 在 [Azure AD 網域]  畫面上，從下拉式清單中選取網域，然後按 [下一步]  。
9. 在 [已可設定]  畫面上，按一下 [設定]  。
10. 設定完成後，請按 [下一步]  。
![設定](media/tutorial-phs-backup/backup4.png)</br>
11. 在 [驗證同盟連線能力]  畫面上，按一下 [確認]  。  您可能需要設定 DNS 記錄 (新增 A 和 AAAA 記錄)，此作業才能順利完成。
![Verify](media/tutorial-phs-backup/backup5.png)</br>
12. 按一下 [結束]  。

## <a name="reset-the-ad-fs-and-azure-trust"></a>重設 AD FS 與 Azure 的信任
現在，我們必須重設 AD FS 與 Azure 之間的信任。

1.  按兩下在桌面上建立的 Azure AD Connect 圖示
2.  按一下 [設定]  。
3.  選取 [管理同盟]  ，然後按 [下一步]  。
4.  選取 [重設 Azure AD 信任]  ，然後按 [下一步]  。
![重設](media/tutorial-phs-backup/backup6.png)</br>
5.  在 [連線至 Azure AD]  畫面上，輸入全域管理員的使用者名稱和密碼。
6.  在 [連線至 AD FS]  畫面上，輸入 contoso\Administrator 使用者名稱與密碼，然後按 [下一步]  。
7.  在 [憑證]  畫面上，按 [下一步]  。

## <a name="test-signing-in-with-one-of-our-users"></a>使用我們其中一個使用者來測試登入

1.  瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 使用我們在新租用戶中建立的使用者來登入。  您必須使用下列格式登入：(user@domain.onmicrosoft.com)。 透過該使用者在內部部署用來登入的密碼登入。
![Verify](media/tutorial-password-hash-sync/verify1.png)

您現在已成功設定混合式身分識別環境，可用來測試及熟悉 Azure 的功能。

## <a name="next-steps"></a>後續步驟


- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [密碼雜湊同步處理](how-to-connect-password-hash-synchronization.md)