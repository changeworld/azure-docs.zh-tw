---
title: 條件存取 - 管理員需要 MFA - Azure 的動作目錄
description: 建立自訂條件存取原則,要求管理員執行多重身份驗證
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90e8a8b0926575b5a40a8c0ca7820e31827434ec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755204"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>條件存取:需要管理員 MFA

分配管理許可權的帳戶是攻擊者的目標。 在這些帳戶上需要多重身份驗證 (MFA) 是降低這些帳戶被洩露風險的簡便方法。

Microsoft 建議您至少需要以下角色的 MFA:

* 計費管理員
* 條件式存取系統管理員
* Exchange 系統管理員
* 全域管理員
* 說明台(密碼)管理員
* 密碼管理員
* 安全性系統管理員
* SharePoint 管理員
* 使用者管理員

組織可以選擇在他們認為合適的位置包括或排除角色。

## <a name="user-exclusions"></a>使用者排除

條件存取策略是功能強大的工具,我們建議將以下帳戶排除在策略之外:

* **緊急訪問**或**破玻璃**帳戶,以防止租戶範圍的帳戶鎖定。 在不太可能的情況下,所有管理員都鎖定在租戶之外,您的緊急訪問管理帳戶可用於登錄到租戶,採取措施恢復訪問許可權。
   * 有關詳細資訊,請參閱[" 在 Azure AD 中管理緊急訪問帳戶](../users-groups-roles/directory-emergency-access.md)「一文。
* **服務帳戶****和服務主體**,如 Azure AD 連接同步帳戶。 服務帳戶是非與任何特定使用者關聯的非互動式帳戶。 它們通常由允許以程式設計方式訪問應用程式的後端服務使用,但也用於登錄系統以進行管理。 應排除類似這樣的服務帳戶,因為無法以程式設計方式完成 MFA。 條件訪問不會阻止服務主體進行的呼叫。
   * 如果您的組織在指令碼或程式碼中使用這些帳戶，請考慮將它們取代為[受管理的身分識別](../managed-identities-azure-resources/overview.md)。 作為臨時解決方法,可以從基線策略中排除這些特定帳戶。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

以下步驟將幫助創建條件訪問策略,以要求分配的管理角色執行多重身份驗證。

1. 以全域管理員、安全管理員或條件存取管理員的身份登入**Azure 門戶**。
1. 瀏覽到**Azure 的目錄** > **安全** > **條件存取**。
1. 選取 [新增原則]****。
1. 為您的策略指定一個名稱。 我們建議組織為其策略的名稱創建有意義的標準。
1. 在 **"配置'** 下,選擇 **"使用者"和"組**"
   1. 在 **「包括**」下,選擇**目錄角色(預覽),** 並至少選擇以下角色:
      * 驗證系統管理員
      * 計費管理員
      * 條件式存取系統管理員
      * Exchange 系統管理員
      * 全域管理員
      * 服務台管理員
      * 密碼管理員
      * 安全性系統管理員
      * SharePoint 管理員
      * 使用者管理員
   1. 在 **「排除」** 下,選擇 **「使用者」和「組」,** 然後選擇組織的緊急訪問或碎玻璃帳戶。 
   1. 選取 [完成]  。
1. 在 **"雲應用"或"操作** > **包括**"下,選擇"**所有雲應用****",然後選擇"完成**"。
1. 在**條件** > **客戶端應用(預覽)** 下,將 **「設定配置為** **」,** 然後選擇 **「完成**」。
1. 在 **「存取控制** > **授予」下**,選擇 **「授予存取**」,「**需要多重身份驗證**」,然後選擇 **「選擇」。**
1. 確認設置並將 **「啟用策略」** 設定為 **「打開**」。
1. 選擇 **「建立**」以啟用策略。

## <a name="next-steps"></a>後續步驟

[條件存取一般原則](concept-conditional-access-policy-common.md)

[使用僅條件存取報告模式確定影響](howto-conditional-access-report-only.md)

[使用條件存取「如果」工具模擬登入行為](troubleshoot-conditional-access-what-if.md)
