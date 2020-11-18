---
title: 在 Azure AD Identity Protection 中模擬風險偵測
description: 瞭解如何在 Identity Protection 中模擬風險偵測
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835895"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>在 Identity Protection 中模擬風險偵測

系統管理員可能會想要模擬其環境中的風險，以便完成下列專案：

- 藉由模擬風險偵測和弱點，在 Identity Protection 環境中填入資料。
- 設定以風險為基礎的條件式存取原則，並測試這些原則的影響。

本文將為您提供模擬下列風險偵測類型的步驟：

- 匿名 IP 位址 (簡單) 
- 不熟悉的登入屬性 (中等) 
- 非慣用移動 (很難) 

其他風險偵測無法以安全的方式模擬。

有關每個風險偵測的詳細資訊，請參閱一文， [什麼是風險](concept-identity-protection-risks.md)。

## <a name="anonymous-ip-address"></a>匿名 IP 位址

要完成下列程序，您必須使用：

- [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)，用以模擬匿名 IP 位址。 如果您的組織對 Tor 瀏覽器的使用有所限制，您可能需要使用虛擬機器。
- 尚未針對 Azure AD Multi-Factor Authentication 註冊的測試帳戶。

**若要模擬從匿名 IP 登入，請執行下列步驟**：

1. 使用 [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)，瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
2. 輸入您要在 [從匿名 IP 位址登入]  報告中顯示之帳戶的認證。

登入將會在 10 - 15 分鐘內顯示於 [身分識別保護] 儀表板上。 

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登入屬性

若要模擬不熟悉的位置，您必須從測試帳戶未曾用來登入的位置和裝置進行登入。

下列程序會使用新建立的：

- VPN 連線，用以模擬新位置。
- 虛擬機器，用以模擬新裝置。

要完成下列程序，您必須使用符合下列條件的使用者帳戶：

- 至少有 30 天的登入歷程記錄。
- Azure AD Multi-Factor Authentication 已啟用。

**若要模擬從不熟悉的位置登入，請執行下列步驟**：

1. 使用您的測試帳戶登入時，若未通過 MFA 挑戰， (MFA) 挑戰的多重要素驗證將會失敗。
2. 使用您的新 VPN，流覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com) 並輸入您的測試帳號憑證。

登入將會在 10 - 15 分鐘內顯示於 [身分識別保護] 儀表板上。

## <a name="atypical-travel"></a>非慣用登入位置

模擬非典型的移動條件很困難，因為演算法使用機器學習服務來難排除誤報，例如從熟悉的裝置進行非典型的移動，或從目錄中其他使用者使用的 Vpn 登入。 此外，此演算法在開始產生風險偵測之前，需要14天的登入歷程記錄和10位使用者登入。 由於有複雜的機器學習模型和以上的規則，因此下列步驟有可能不會導致風險偵測。 您可能會想要將這些步驟複寫到多個 Azure AD 帳戶，以模擬此偵測。

**若要模擬非典型的旅遊風險偵測，請執行下列步驟**：

1. 使用標準瀏覽器，流覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。  
2. 輸入您想要為其產生非典型旅遊風險偵測之帳戶的認證。
3. 變更您的使用者代理程式。 您可以在 Microsoft Edge 中，從 [開發人員工具] (F12) 變更使用者代理程式。
4. 變更您的 IP 位址。 您可以使用 VPN、Tor 附加元件，或在 Azure 中于不同的資料中心建立新的虛擬機器，來變更您的 IP 位址。
5. 使用與之前相同的認證登入， [https://myapps.microsoft.com](https://myapps.microsoft.com) 然後在前一次登入之後的幾分鐘內登入。

登入會在 2-4 小時內顯示於 [身分識別保護] 儀表板上。

## <a name="testing-risk-policies"></a>測試風險原則

本節提供您測試使用者的步驟，以及在文章中建立的登入風險原則， [如何：設定和啟用風險原則](howto-identity-protection-configure-risk-policies.md)。

### <a name="user-risk-policy"></a>使用者風險原則

若要測試使用者風險安全性原則，請執行下列步驟：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 **Azure Active Directory**  >  **安全性**  >  **總覽**。
1. 選取 [ **設定使用者風險原則**]。
   1. 在 **指派** 下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
      1. **條件**  - **使用者風險** Microsoft 的建議是將此選項設定為 [**高**]。
   1. 在 **控制項** 下
      1. **存取** -Microsoft 的建議是 **允許存取** ，而且 **需要變更密碼**。
   1. **強制執行原則**  - **關閉**
   1. **儲存** -此動作會讓您回到 [ **總覽** ] 頁面。
1. 以提高測試帳戶的使用者風險，例如，模擬其中一個風險偵測數次。
1. 等候幾分鐘，然後確認使用者的風險已提高。 如果不是，請為使用者模擬更多風險偵測。
1. 返回您的風險原則，並將 [ **強制執行原則** ] 設定為 [ **開啟** ]，並 **儲存** 原則變更。
1. 您現在可以使用具有較高風險層級的使用者登入，以測試使用者風險型條件式存取。

### <a name="sign-in-risk-security-policy"></a>登入風險安全性原則

若要測試登入風險原則，請執行下列步驟：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 **Azure Active Directory**  >  **安全性**  >  **總覽**。
1. 選取 [ **設定登入風險原則**]。
   1. 在 **指派** 下
      1. **使用者** -選擇 [ **所有使用者** ] **，或選取 [個人和群組** ] 以限制您的首度發行。
         1. （選擇性）您可以選擇將使用者從原則中排除。
      1. **條件**  - 登 **入風險** Microsoft 的建議是將此選項設定為 [**中] 或更高** 的版本。
   1. 在 **控制項** 下
      1. **存取** -Microsoft 的建議是 **允許存取** ，而且 **需要多重要素驗證**。
   1. **強制執行原則**  - **開啟**
   1. **儲存** -此動作會讓您回到 [ **總覽** ] 頁面。
1. 您現在可以使用具風險的會話登入來測試登入風險型條件式存取 (例如，使用 Tor 瀏覽器) 。 

## <a name="next-steps"></a>後續步驟

- [什麼是風險？](concept-identity-protection-risks.md) (機器翻譯)

- [如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
