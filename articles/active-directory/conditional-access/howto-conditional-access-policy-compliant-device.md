---
title: 條件訪問 - 需要相容的設備 - Azure 活動目錄
description: 創建自訂條件訪問策略以需要符合的設備
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295210"
---
# <a name="conditional-access-require-compliant-devices"></a>條件訪問：需要符合要求的設備

部署 Microsoft Intune 的組織可以使用從設備返回的資訊來識別符合合規性要求的設備，例如：

* 需要 PIN 解鎖
* 需要裝置加密
* 需要最小或最大作業系統版本
* 要求設備不是越獄或紮根

此策略合規性資訊將轉發到 Azure AD，其中條件訪問可以做出授予或阻止對資源的訪問的決定。 有關設備合規性策略的詳細資訊，請參閱"[在設備上設置規則"以允許使用 Intune 訪問組織中的資源](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

以下步驟將有助於創建條件訪問策略，要求訪問資源的設備標記為符合組織的 Intune 合規性策略。

1. 以全域管理員、安全管理員或條件訪問管理員的身份登錄到**Azure 門戶**。
1. 流覽到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選取 [新增原則]****。
1. 為您的策略指定一個名稱。 我們建議組織為其策略的名稱創建有意義的標準。
1. 在 **"分配"** 下，選擇 **"使用者"和"組**"
   1. 在 **"包括**"下，選擇**所有使用者**。
   1. 在 **"排除"** 下，選擇 **"使用者"和"組"，** 然後選擇組織的緊急訪問或碎玻璃帳戶。 
   1. 選擇 **"完成**"。
1. 在 **"雲應用"或"包括** > **"下**，選擇 **"所有雲應用**"。
   1. 如果必須從策略中排除特定應用程式，則可以從 **"選擇排除雲應用**"下的 **"排除**"選項卡中選擇這些應用程式，然後**選擇**。
   1. 選擇 **"完成**"。
1. 在**條件** > **用戶端應用（預覽）** 下，將 **"配置配置為** **"，** 然後選擇 **"完成**"。
1. 在 **"存取控制** > **授予"下**，選擇 **"要求設備標記為符合"。**
   1. 選取 [選取] ****。
1. 確認設置並將 **"啟用策略"** 設置為 **"打開**"。
1. 選擇 **"創建**"以啟用策略。

> [!NOTE]
> 即使您選擇 **"需要使用**上述步驟將設備標記為符合**所有使用者****和所有雲應用**"，您也可以將新設備註冊到 Intune。 **要求將設備標記為符合控制**不會阻止 Intune 註冊。 

### <a name="known-behavior"></a>已知行為

在 Windows 7 上，iOS、Android、macOS 和某些協力廠商 Web 瀏覽器 Azure AD 使用在設備註冊 Azure AD 時預配的用戶端憑證標識設備。 當使用者首次通過瀏覽器登錄時，系統會提示使用者選擇證書。 最終使用者必須先選擇此證書，然後才能繼續使用瀏覽器。

## <a name="next-steps"></a>後續步驟

[條件訪問通用策略](concept-conditional-access-policy-common.md)

[使用僅條件訪問報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件訪問"如果"工具類比登錄行為](troubleshoot-conditional-access-what-if.md)

[裝置合規性政策會與 Azure AD 一起運作](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
