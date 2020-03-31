---
title: 在 Azure AD 標識保護中類比風險檢測
description: 瞭解如何在身份保護中類比風險檢測
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886931"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>類比身份保護中的風險檢測

管理員可能需要類比其環境中的風險，以便完成以下項：

- 通過類比風險檢測和漏洞來填充身份保護環境中的資料。
- 設置基於風險的條件訪問策略，並測試這些策略的影響。

本文為您提供了類比以下風險檢測類型的步驟：

- 匿名 IP 位址（簡單）
- 不熟悉的登錄屬性（中等）
- 非典型旅行（困難）

無法以安全方式類比其他風險檢測。

有關每個風險檢測的詳細資訊，請參閱文章"[什麼是風險](concept-identity-protection-risks.md)"。

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
- 啟用 Azure 多重要素驗證。

**若要模擬從不熟悉的位置登入，請執行下列步驟**：

1. 使用測試帳戶登錄時，通過未通過 MFA 質詢，從而失敗多重要素驗證 （MFA） 質詢。
2. 使用新的 VPN 導航並[https://myapps.microsoft.com](https://myapps.microsoft.com)輸入測試帳戶的憑據。

登入將會在 10 - 15 分鐘內顯示於 [身分識別保護] 儀表板上。

## <a name="atypical-travel"></a>非慣用登入位置

類比非典型旅行條件很困難，因為演算法使用機器學習來清除誤報，例如來自熟悉設備的非典型旅行，或目錄中其他使用者使用的 VPN 登錄。 此外，該演算法需要使用者 14 天的登錄歷史記錄和 10 個登錄名，然後才能開始生成風險檢測。 由於複雜的機器學習模型和上述規則，以下步驟很可能不會導致風險檢測。 您可能希望為多個 Azure AD 帳戶複製這些步驟以類比此檢測。

**要類比非典型旅行風險檢測，執行以下步驟**：

1. 使用標準瀏覽器導航到[https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 輸入要生成非典型旅行風險檢測的帳戶的憑據。
3. 變更您的使用者代理程式。 可以從開發人員工具 （F12） 更改 Microsoft Edge 中的使用者代理。
4. 變更您的 IP 位址。 您可以通過使用 VPN、Tor 載入項或在 Azure 中在不同的資料中心創建新虛擬機器來更改 IP 位址。
5. 登錄以[https://myapps.microsoft.com](https://myapps.microsoft.com)使用與前一次登錄後相同的憑據，並在前一次登錄後幾分鐘內登錄。

登入會在 2-4 小時內顯示於 [身分識別保護] 儀表板上。

## <a name="testing-risk-policies"></a>測試風險策略

本節為您提供了測試使用者的步驟以及文章"[如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md)"中創建的登錄風險策略。

### <a name="user-risk-policy"></a>使用者風險原則

若要測試使用者風險安全性原則，請執行下列步驟：

1. 導航到[Azure 門戶](https://portal.azure.com)。
1. 流覽到**Azure 活動目錄** > **安全** > **概述**。
1. 選擇 **"配置使用者風險策略**"。
   1. 在**分配**下
      1. **使用者**- 如果限制推出，請選擇**所有使用者**或**選擇個人和組**。
         1. 可以選擇將使用者從策略中排除。
      1. **條件** - **使用者風險**微軟的建議是將此選項設置為**高**。
   1. 在**控制下**
      1. **訪問**- 微軟的建議是**允許訪問**和**要求密碼更改**。
   1. **強制關閉策略** -  ** **
   1. **保存**- 此操作將返回到 **"概述"** 頁。
1. 例如，通過多次類比其中一個風險檢測來提升測試帳戶的使用者風險。
1. 等待幾分鐘，然後驗證使用者的風險是否升高。 否則，類比使用者的更多風險檢測。
1. 返回到風險策略並將 **"強制策略**"設置為 **"打開**"並**保存**策略更改。
1. 現在，您可以通過使用具有較高風險級別的使用者登錄來測試基於使用者風險的條件訪問。

### <a name="sign-in-risk-security-policy"></a>登入風險安全性原則

若要測試登入風險原則，請執行下列步驟：

1. 導航到[Azure 門戶](https://portal.azure.com)。
1. 流覽到**Azure 活動目錄** > **安全** > **概述**。
1. 選擇**配置登錄風險策略**。
   1. 在**分配**下
      1. **使用者**- 如果限制推出，請選擇**所有使用者**或**選擇個人和組**。
         1. 可以選擇將使用者從策略中排除。
      1. **條件** - **登錄風險**微軟的建議是將此選項設置為**中和以上**。
   1. 在**控制下**
      1. **訪問**- 微軟的建議是**允許訪問**和**要求多重要素驗證**。
   1. **執行策略** -  ** **
   1. **保存**- 此操作將返回到 **"概述"** 頁。
1. 現在，您可以通過使用風險會話（例如，通過使用 Tor 瀏覽器）登錄來測試基於登錄風險的條件訪問。 

## <a name="next-steps"></a>後續步驟

- [什麼是風險？](concept-identity-protection-risks.md)

- [如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
