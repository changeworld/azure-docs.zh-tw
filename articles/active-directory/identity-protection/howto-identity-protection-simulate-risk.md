---
title: 模擬 Azure AD Identity Protection 中的風險偵測
description: 瞭解如何模擬 Identity Protection 中的風險偵測
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "72886931"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>模擬 Identity Protection 中的風險偵測

系統管理員可能想要在環境中模擬風險，以便完成下列專案：

- 藉由模擬風險偵測和弱點，在身分識別保護環境中填入資料。
- 設定以風險為基礎的條件式存取原則，並測試這些原則的影響。

本文提供模擬下列風險偵測類型的步驟：

- 匿名 IP 位址（簡單）
- 不熟悉的登入屬性（適中）
- 非典型旅遊（艱難）

其他風險偵測無法以安全的方式模擬。

如需每個風險偵測的詳細資訊，請參閱[什麼是風險](concept-identity-protection-risks.md)。

## <a name="anonymous-ip-address"></a>匿名 IP 位址

要完成下列程序，您必須使用：

- [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)，用以模擬匿名 IP 位址。 如果您的組織對 Tor 瀏覽器的使用有所限制，您可能需要使用虛擬機器。
- 尚未註冊 Azure 多重要素驗證的測試帳戶。

**若要模擬從匿名 IP 登入，請執行下列步驟**：

1. 使用 [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)，瀏覽至 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
2. 輸入您要在 [從匿名 IP 位址登入] **** 報告中顯示之帳戶的認證。

登入將會在 10 - 15 分鐘內顯示於 [身分識別保護] 儀表板上。 

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登入屬性

若要模擬不熟悉的位置，您必須從測試帳戶未曾用來登入的位置和裝置進行登入。

下列程序會使用新建立的：

- VPN 連線，用以模擬新位置。
- 虛擬機器，用以模擬新裝置。

要完成下列程序，您必須使用符合下列條件的使用者帳戶：

- 至少有 30 天的登入歷程記錄。
- 已啟用 Azure 多重要素驗證。

**若要模擬從不熟悉的位置登入，請執行下列步驟**：

1. 使用測試帳戶登入時，不會通過 MFA 挑戰，導致多重要素驗證（MFA）的挑戰失敗。
2. 使用新的 VPN，流覽至[https://myapps.microsoft.com](https://myapps.microsoft.com)並輸入您測試帳戶的認證。

登入將會在 10 - 15 分鐘內顯示於 [身分識別保護] 儀表板上。

## <a name="atypical-travel"></a>非慣用登入位置

模擬非典型的旅遊條件並不容易，因為演算法會使用機器學習來得費勁篩選出誤報，例如來自熟悉裝置的非典型旅遊，或從目錄中其他使用者所使用的 Vpn 登入。 此外，此演算法需要14天的登入歷程記錄和10個使用者登入，才會開始產生風險偵測。 由於有複雜的機器學習模型和上述規則，因此下列步驟可能不會導致風險偵測。 您可能想要為多個 Azure AD 帳戶複寫這些步驟，以模擬此偵測。

**若要模擬非典型的旅遊風險偵測，請執行下列步驟**：

1. 使用標準瀏覽器，流覽至[https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 輸入您要為其產生非典型旅遊風險偵測的帳號憑證。
3. 變更您的使用者代理程式。 您可以從開發人員工具（F12）變更 Microsoft Edge 中的使用者代理程式。
4. 變更您的 IP 位址。 您可以使用 VPN、Tor 附加元件，或在不同資料中心的 Azure 中建立新的虛擬機器，來變更您的 IP 位址。
5. 在前次登[https://myapps.microsoft.com](https://myapps.microsoft.com)入之後的幾分鐘內，使用與之前相同的認證登入。

登入會在 2-4 小時內顯示於 [身分識別保護] 儀表板上。

## <a name="testing-risk-policies"></a>測試風險原則

本節提供測試使用者的步驟，以及[如何：設定和啟用風險原則](howto-identity-protection-configure-risk-policies.md)一文中建立的登入風險原則。

### <a name="user-risk-policy"></a>使用者風險原則

若要測試使用者風險安全性原則，請執行下列步驟：

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
1. 流覽至**Azure Active Directory** > **安全性** > **總覽**。
1. 選取 [**設定使用者風險原則**]。
   1. 在 [**指派**] 底下
      1. **使用者**-選擇 [**所有使用者**] **，或選取個人和群組**（如果您要限制首度發行）。
         1. （選擇性）您可以選擇從原則中排除使用者。
      1. **條件** - **使用者風險**Microsoft 的建議是將此選項設定為 [**高**]。
   1. 在**控制項**底下
      1. **存取**-Microsoft 的建議是**允許存取**，而且**需要變更密碼**。
   1. **強制執行原則** -  ** **
   1. **儲存**-此動作會讓您回到 [**總覽**] 頁面。
1. 藉由提高測試帳戶的使用者風險，例如，模擬幾次其中一項風險偵測。
1. 等候幾分鐘，然後確認您的使用者已提升風險。 如果不是，則會為使用者模擬更多的風險偵測。
1. 返回您的風險原則，並將 [**強制執行原則**] 設定為 [**開啟**]，然後**儲存**您的原則變更。
1. 您現在可以使用具有提高許可權的風險層級的使用者登入，以測試以使用者風險為基礎的條件式存取。

### <a name="sign-in-risk-security-policy"></a>登入風險安全性原則

若要測試登入風險原則，請執行下列步驟：

1. 流覽至 [ [Azure 入口網站](https://portal.azure.com)]。
1. 流覽至**Azure Active Directory** > **安全性** > **總覽**。
1. 選取 [**設定登入風險原則**]。
   1. 在 [**指派**] 底下
      1. **使用者**-選擇 [**所有使用者**] **，或選取個人和群組**（如果您要限制首度發行）。
         1. （選擇性）您可以選擇從原則中排除使用者。
      1. **條件** - 登**入風險**Microsoft 的建議是將此選項設定為 [**中] 和 [以上**]。
   1. 在**控制項**底下
      1. **存取**-Microsoft 的建議是**允許存取**，而且**需要多重要素驗證**。
   1. **強制執行原則** -  ** **
   1. **儲存**-此動作會讓您回到 [**總覽**] 頁面。
1. 您現在可以使用有風險的會話登入來測試登入風險型條件式存取（例如，藉由使用 Tor 瀏覽器）。 

## <a name="next-steps"></a>後續步驟

- [有什麼風險？](concept-identity-protection-risks.md)

- [如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
