---
title: 設定適用于 Windows 虛擬桌面的 Azure 多重要素驗證-Azure
description: 如何在 Windows 虛擬桌面中設定 Azure 多重要素驗證以提升安全性。
author: Heidilohr
ms.topic: how-to
ms.date: 10/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35af8191cfe237175cbd6669797d1744ac3ecd49
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92312659"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>啟用適用于 Windows 虛擬桌面的 Azure 多重要素驗證

>[!IMPORTANT]
> 如果您是從 Windows 虛擬桌面 (傳統) 檔流覽此頁面，請務必在完成後 [返回 Windows 虛擬桌面 (傳統) 檔](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) 。

適用于 windows 虛擬桌面的 Windows 用戶端是整合 Windows 虛擬桌面與本機電腦的絕佳選項。 但是，當您將 Windows 虛擬桌面帳戶設定為 Windows 用戶端時，您必須採取某些措施來保護自己和使用者的安全。

當您第一次登入時，用戶端會要求您提供使用者名稱、密碼和 Azure 多重要素驗證。 之後，當您下一次登入時，用戶端會從您的 Azure Active Directory (AD) 企業應用程式中記住您的權杖。 當 **您在提示** 輸入工作階段主機認證的提示時，您的使用者可以在重新開機用戶端之後登入，而不需要重新輸入其認證。

雖然記住認證很方便，但它也可以讓企業案例或個人裝置上的部署更不安全。 若要保護您的使用者，您可以確保用戶端會更頻繁地持續要求 Azure 多重要素驗證認證。 本文將說明如何設定 Windows 虛擬桌面的條件式存取原則，以啟用此設定。

## <a name="prerequisites"></a>先決條件

以下是您需要的入門入門：

- 指派包含 Azure Active Directory Premium P1 或 P2 的授權給使用者。
- 指派為群組成員之使用者的 Azure Active Directory 群組。
- 為您的所有使用者啟用 Azure 多重要素驗證。 如需如何執行此動作的詳細資訊，請參閱 [如何要求使用者使用雙步驟驗證](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

> [!NOTE]
> 下列設定也適用于 [Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

以下說明如何建立條件式存取原則，在連線至 Windows 虛擬桌面時需要多因素驗證：

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
2. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
3. 選取 [新增原則]。
4. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
5. 在 [指派]  底下，選取 [使用者和群組]  。
6. 在 [**包含**] 下，選取 [**選取使用者和群組**  >  **使用者和群組**] > 選擇您在 [[必要條件](#prerequisites)] 階段中建立的群組。
7. 選取 [完成]。
8. 在 [**雲端應用程式] 或 [動作**] 下  >  ** **，選取 [**選取應用程式**]。
9. 根據您所使用的 Windows 虛擬桌面版本，選取下列其中一個應用程式。
   
   - 如果您是使用 Windows 虛擬桌面 (傳統) ，請選擇下列應用程式：
       
       - **Windows 虛擬桌面** (應用程式識別碼 5a0aa725-4958-4b0c-80a9-34562e23f3b7) 
       - **Windows 虛擬桌面用戶端** (應用程式識別碼 fa4345a4-a730-4230-84a8-7d9651b86739) 可讓您在 web 用戶端上設定原則
       
        之後，直接跳到步驟11。

   - 如果您使用的是 Windows 虛擬桌面，請改為選擇此應用程式：
       
       -  **Windows 虛擬桌面** (應用程式識別碼 9cdead84-a844-4324-93f2-b2e6bb768d07) 
       
        之後，請移至步驟10。

   >[!IMPORTANT]
   > 請勿選取名為 Windows Virtual Desktop Azure Resource Manager Provider (50e95039-b200-4007-bc97-8d5790743a63) 的應用程式。 此應用程式只會用於抓取使用者摘要，而且不應該有多重要素驗證。
   > 
   > 如果您使用 Windows 虛擬桌面 (傳統) ，如果條件式存取原則會封鎖所有存取，而且只會排除 Windows 虛擬桌面應用程式識別碼，您可以在原則中新增應用程式識別碼9cdead84-a844-4324-93f2-b2e6bb768d07 來修正此問題。 若未新增此應用程式識別碼，將會封鎖 Windows 虛擬桌面 (傳統) 資源的摘要探索。

10. 移至 [**條件**  >  **用戶端應用程式**]，然後選取您要套用原則的位置：
    
    - 如果您想要將原則套用至 web 用戶端，請選取 [ **瀏覽器** ]。
    - 如果您想要將原則套用至其他用戶端，請選取 [行動裝置 **應用程式和桌面用戶端** ]。
    - 如果您想要將原則套用至所有用戶端，請同時選取這兩個核取方塊。
   
    > [!div class="mx-imgBorder"]
    > ![[用戶端應用程式] 頁面的螢幕擷取畫面。 使用者已選取 [行動裝置應用程式和桌面用戶端] 核取方塊。](media/select-apply.png)

11. 選取您的應用程式之後，請選擇 [ **選取**]，然後選取 [ **完成**]。

    > [!div class="mx-imgBorder"]
    > ![[雲端應用程式] 或 [動作] 頁面的螢幕擷取畫面。 Windows 虛擬桌面和 Windows 虛擬桌面用戶端應用程式會以紅色反白顯示。](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >若要尋找您想要選取之應用程式的應用程式識別碼，請移至 [ **企業應用程式** ]，然後從 [應用程式類型] 下拉式功能表中選取 [ **Microsoft 應用程式** ]。

12. 在 [**存取控制**  >  **授**與] 底下，選取 **[授與存取權**，**需要多重要素驗證**]，然後**選取**。
13. 在 [**存取控制**  >  **會話**] 下，選取 [登**入頻率**]，將值設定為您想要的提示間隔時間，然後選取 [**選取**]。 例如，將值設定為 **1** ，而單位為 **小時**，如果連接是在最後一小時之後啟動的，則需要多重要素驗證。
14. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
15. 選取 [ **建立** ] 以啟用您的原則。

## <a name="next-steps"></a>後續步驟

- [深入瞭解條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [深入瞭解使用者登入頻率](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
