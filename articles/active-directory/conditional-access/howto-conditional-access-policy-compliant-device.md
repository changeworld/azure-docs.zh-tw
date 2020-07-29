---
title: 條件式存取-需要符合規範的裝置-Azure Active Directory
description: 建立自訂的條件式存取原則，以要求符合規範的裝置
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c65b4ede6f4851418bf17d42db5b3215dafa9234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83995168"
---
# <a name="conditional-access-require-compliant-devices"></a>條件式存取：需要符合規範的裝置

已部署 Microsoft Intune 的組織可以使用其裝置所傳回的資訊，來識別符合合規性需求的裝置，例如：

* 需要 PIN 才能解除鎖定
* 需要裝置加密
* 需要最低或最高的作業系統版本
* 要求裝置未越獄或根目錄

此原則合規性資訊會轉送到 Azure AD，條件式存取可以在其中決定是否要授與或封鎖資源的存取權。 如需裝置相容性原則的詳細資訊，請參閱在[裝置上設定規則，以允許使用 Intune 存取組織中的資源](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

下列步驟將協助建立條件式存取原則，以要求存取資源的裝置必須標示為符合您組織的 Intune 合規性政策。

1. 以全域管理員、安全性系統管理員或條件式存取管理員的身分，登入 **Azure 入口網站**。
1. 瀏覽至 [Azure Active Directory] > [安全性] > [條件式存取]。
1. 選取 [新增原則]。
1. 為您的原則命名。 我們建議組織針對其原則的名稱建立有意義的標準。
1. 在 [指派] 底下，選取 [使用者和群組]
   1. 在 [包含] 下，選取 [所有使用者]。
   1. 在 [排除] 底下選取 [使用者和群組]，然後選擇組織的緊急存取或急用帳戶。 
   1. 選取 [完成] 。
1. 在 [**雲端應用程式] 或 [動作**] 底下  >  ** **，選取 [**所有雲端應用程式**]。
   1. 如果您必須從原則中排除特定應用程式，您可以從 [**選取排除的雲端應用程式**] 底下的 [**排除**] 索引標籤中選擇，然後選擇 [**選取**]
   1. 選取 [完成] 。
1. 在 [條件] > [用戶端應用程式 (預覽)] 中，將 [設定] 設定為 [是]，然後選取 [完成]。
1. 在 [**存取控制**]  >  **[授**與] 底下，選取 [**需要將裝置標示為符合規範**]。
   1. 選取 [選取] 。
1. 確認您的設定，並將 [啟用原則] 設定為 [開啟]。
1. 選取 [建立] 以建立以啟用您的原則。

> [!NOTE]
> 您可以使用上述步驟，將您的新裝置註冊至 Intune，即使您選取 [**需要將裝置標示為符合規範**]**所有使用者**和**所有雲端應用程式**。 [**要求裝置必須標記為相容**] 控制項不會封鎖 Intune 註冊。 

### <a name="known-behavior"></a>已知的行為

在 Windows 7、iOS、Android、macOS 和一些協力廠商網頁瀏覽器上，Azure AD 使用向 Azure AD 註冊裝置時所布建的用戶端憑證來識別裝置。 當使用者第一次透過瀏覽器登入時，系統會提示使用者選取憑證。 終端使用者必須選取此憑證，才能繼續使用瀏覽器。

## <a name="next-steps"></a>後續步驟

[條件式存取一般原則](concept-conditional-access-policy-common.md)

[使用條件式存取報告專用模式判斷影響](howto-conditional-access-report-only.md)

[使用條件式存取 What If 工具模擬登入行為](troubleshoot-conditional-access-what-if.md)

[裝置合規性政策會與 Azure AD 一起運作](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
