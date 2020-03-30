---
title: Azure 多因素身份檢查器提供者 - Azure 活動目錄
description: 何時應使用驗證提供者搭配 Azure MFA？
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275e5ab394b54960a2340848152741762b28f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269375"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何時使用 Azure Multi-Factor Authentication Provider

依預設，擁有 Azure Active Directory 和 Office 365 使用者的全域管理員可以使用雙步驟驗證。 不過，如果您想要充分利用[進階功能](howto-mfa-mfasettings.md)，則應該購買完整版的 Azure Multi-Factor Authentication (MFA)。

Azure Multi-Factor Auth Provider 可讓**沒有授權**的使用者能夠充分利用 Azure Multi-Factor Authentication 提供的功能。

> [!NOTE]
> 自 2018 年 9 月 1 日起，不再建立新的驗證提供者。 現有身份檢查器提供者可以繼續使用和更新，但遷移不再可能。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK 的相關注意事項

請注意，該 SDK 已遭取代，只會繼續運作到 2018 年 11 月 14 日為止。 在這個時間之後，對 SDK 的呼叫將會失敗。

## <a name="what-is-an-mfa-provider"></a>什麼是 MFA Provider？

驗證提供者的類型有兩種，差異在於您 Azure 訂用帳戶的收費方式。 每次驗證選項會計算一個月中對您的租用戶執行之驗證數目。 如果您有許多使用者偶爾才會進行驗證，最好使用此選項。 每位使用者選項會計算您的租用戶中一個月執行雙步驟驗證之個人數目。 如果您的某些使用者已擁有授權，但是需要將 MFA 擴充至您授權限制之外的更多使用者，最好使用此選項。

## <a name="manage-your-mfa-provider"></a>管理 MFA Provider

建立 MFA 提供者之後，您就無法變更使用量模型 (依據已啟用的使用者或依據驗證)。

如果您購買的授權已足夠讓啟用 MFA 的所有使用者使用，您可以一併刪除 MFA 提供者。

如果您的 MFA 提供者未連結至 Azure AD 租用戶，或是您將新的 MFA 提供者連結至不同 Azure AD 租用戶，則使用者設定和組態選項並不會進行轉移。 此外，現有 Azure MFA 伺服器也需要使用 MFA 提供者產生的啟用認證重新啟動。

### <a name="removing-an-authentication-provider"></a>刪除身份檢查器提供者

> [!CAUTION]
> 刪除身份檢查器提供者時沒有確認。 選擇 **"刪除**"是一個永久過程。

身份檢查器提供者可以在**Azure 門戶** > **Azure 活動目錄** > **安全** > **MFA** > **提供程式中找到**。 按一下列出的提供程式以查看與該提供程式關聯的詳細資訊和配置。

在刪除身份檢查器提供者之前，請注意提供程式中配置的任何自訂設置。 決定需要從供應商遷移到常規 MFA 設置的設置，並完成這些設置的遷移。 

連接到提供程式的 Azure MFA 伺服器需要使用**Azure 門戶** > **Azure 活動目錄** > **安全** > **MFA** > **伺服器設置**下生成的憑據重新啟動。 在重新啟動之前，必須從環境中 Azure MFA`\Program Files\Multi-Factor Authentication Server\Data\`伺服器上的目錄中刪除以下檔：

- 卡塞爾特
- cert
- 組CACert
- 組鍵
- groupName
- 許可證金鑰
- 皮基

![從 Azure 門戶中刪除身份檢查器提供者](./media/concept-mfa-authprovider/authentication-provider-removal.png)

確認已遷移所有設置後，可以流覽到 Azure**門戶** > **Azure 活動目錄** > **安全** >  ** ** **MFA** > **提供程式**並選擇省略號 **...**

> [!WARNING]
> 刪除身份檢查器提供者將刪除與該提供程式關聯的任何報告資訊。 您可能需要在刪除提供程式之前保存活動報告。

> [!NOTE]
> 具有舊版本的 Microsoft 身份驗證器應用和 Azure MFA 伺服器的使用者可能需要重新註冊其應用。

## <a name="next-steps"></a>後續步驟

[進行 Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
