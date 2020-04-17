---
title: 沒有密碼安全金鑰登入(預覽) - Azure 的目錄
description: 使用 FIDO2 安全金鑰(預覽)為 Azure AD 啟用無密碼安全金鑰登入
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066ab7892bed6e7505e7ee114ff37a7850ef5c9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450916"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>開啟無密碼安全金鑰登入(預覽)

對於目前使用密碼且具有共用 PC 環境的企業,安全密鑰為工作人員提供了一種無縫的身份驗證方式,而無需輸入使用者名或密碼。 安全密鑰提高了工作人員的工作效率,並提高了安全性。

本文檔重點介紹啟用基於安全密鑰的無密碼身份驗證。 在本文末尾,您將能夠使用 FIDO2 安全金鑰使用 Azure AD 帳戶登入到基於 Web 的應用程式。

|     |
| --- |
| FIDO2 安全金鑰是 Azure 活動目錄的公共預覽功能。 有關預覽的詳細資訊,請參閱 Microsoft [Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>需求

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [組合安全資訊註冊預覽](concept-registration-mfa-sspr-combined.md)
- 相容[FIDO2 安全金鑰](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 版本 1809 或更高版本*

要使用安全金鑰登錄到 Web 應用和服務,您必須具有支援 WebAuthN 協定的瀏覽器。 其中包括微軟邊緣、Chrome、火狐和Safari。

## <a name="prepare-devices-for-preview"></a>準備裝置進行預覽

使用試用的 Azure AD 加入設備必須運行 Windows 10 版本 1809 或更高版本。 最好的體驗是在 Windows 10 版本 1903 或更高版本中。

混合 Azure AD 加入的設備必須運行 Windows 10 內部人員生成 18945 或更新。

## <a name="enable-passwordless-authentication-method"></a>開啟無密碼認證方法

### <a name="enable-the-combined-registration-experience"></a>開啟組合註冊體驗

無密碼身份驗證方法的註冊功能依賴於組合註冊功能。 按照文章中的步驟[啟用組合安全資訊註冊(預覽),](howto-registration-mfa-sspr-combined.md)以啟用合併註冊。

### <a name="enable-fido2-security-key-method"></a>開啟 FIDO2 安全金鑰方法

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽到**Azure 活動目錄** > **安全** > **身份驗證方法** > **身份驗證方法策略(預覽)。**
1. 在**FIDO2 安全金鑰**的方法下,選擇以下選項:
   1. **開啟**- 是或 否
   1. **目標**- 所有使用者或選擇使用者
1. **保存**配置。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全金鑰的使用者註冊和管理

1. 瀏覽[https://myprofile.microsoft.com](https://myprofile.microsoft.com)到 。
1. 如果尚未登錄,請登錄。
1. 按下 **「安全資訊**」。
   1. 如果用戶已註冊了至少一個 Azure 多重身份驗證方法,則可以立即註冊 FIDO2 安全密鑰。
   1. 如果他們沒有至少註冊一個 Azure 多重身份驗證方法,則必須添加一個。
1. 以按下 **「新增」方法**並選擇**安全金鑰**,添加 FIDO2 安全密鑰。
1. 選擇**USB 裝置**或 NFC**裝置**。
1. 準備好您的密鑰並選擇 **「下一步**」。
1. 將顯示一個框,要求使用者為您的安全密鑰創建/輸入 PIN,然後對密鑰執行所需的手勢,即生物識別或觸摸。
1. 使用者將返回到組合註冊體驗,並要求為密鑰提供有意義的名稱,以便用戶可以標識哪個具有多個名稱。 按 [下一步]  。
1. 按下 **「完成」** 以完成此過程。

## <a name="sign-in-with-passwordless-credential"></a>使用無密碼認證

在下面的示例中,使用者已預配其FIDO2安全金鑰。 用戶可以選擇在 Windows 10 版本 1809 或更高版本的受支援的瀏覽器內使用 FIDO2 安全密鑰在 Web 上登錄。

![安全金鑰登入微軟邊緣](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>容錯排除及回饋

如果您想在預覽此功能時共用反饋或遇到問題,請使用以下步驟透過 Windows 回饋中心應用共用:

1. 啟動**反饋中心**並確保您已登錄。
1. 根據以下類別提交回饋:
   - 類別:安全和隱私
   - 子類別: FIDO
1. 要捕捉紀錄,請使用選項**重新建立問題**

## <a name="known-issues"></a>已知問題

### <a name="security-key-provisioning"></a>安全金鑰預先

公共預覽版中不提供安全密鑰的管理員預配和取消預配。

### <a name="upn-changes"></a>UPN 變更

我們正在努力支援一項功能,該功能允許在混合 Azure AD 聯接和 Azure AD 聯接裝置上更改 UPN。 如果使用者的 UPN 發生更改,您無法再修改 FIDO2 安全密鑰以考慮更改。 解決方法是重置設備,用戶必須重新註冊。

## <a name="next-steps"></a>後續步驟

[FIDO2 安全金鑰 Windows 10 登入](howto-authentication-passwordless-security-key-windows.md)

[開啟本地端的 FIDO2 認證](howto-authentication-passwordless-security-key-on-premises.md)

[瞭解有關裝置註冊的資訊](../devices/overview.md)

[深入了解 Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
