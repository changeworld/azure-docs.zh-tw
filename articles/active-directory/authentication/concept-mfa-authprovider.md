---
title: Azure 多因素驗證提供者-Azure Active Directory
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
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309909"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何時使用 Azure Multi-Factor Authentication Provider

> [!IMPORTANT]
> 自 2018 年 9 月 1 日起，不再建立新的驗證提供者。 現有的驗證提供者可能會繼續使用和更新，但不再可能進行遷移。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

依預設，擁有 Azure Active Directory 和 Office 365 使用者的全域管理員可以使用雙步驟驗證。 不過，如果您想要充分利用[進階功能](howto-mfa-mfasettings.md)，則應該購買完整版的 Azure Multi-Factor Authentication (MFA)。

Azure Multi-Factor Auth Provider 可讓**沒有授權**的使用者能夠充分利用 Azure Multi-Factor Authentication 提供的功能。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK 的相關注意事項

請注意，該 SDK 已遭取代，只會繼續運作到 2018 年 11 月 14 日為止。 在這個時間之後，對 SDK 的呼叫將會失敗。

## <a name="what-is-an-mfa-provider"></a>什麼是 MFA Provider？

驗證提供者的類型有兩種，差異在於您 Azure 訂用帳戶的收費方式。 每次驗證選項會計算一個月中對您的租用戶執行之驗證數目。 如果您有許多使用者偶爾才會進行驗證，最好使用此選項。 每位使用者選項會計算您的租用戶中一個月執行雙步驟驗證之個人數目。 如果您的某些使用者已擁有授權，但是需要將 MFA 擴充至您授權限制之外的更多使用者，最好使用此選項。

## <a name="manage-your-mfa-provider"></a>管理 MFA Provider

建立 MFA 提供者之後，您就無法變更使用量模型 (依據已啟用的使用者或依據驗證)。

如果您購買的授權已足夠讓啟用 MFA 的所有使用者使用，您可以一併刪除 MFA 提供者。

如果您的 MFA 提供者未連結至 Azure AD 租用戶，或是您將新的 MFA 提供者連結至不同 Azure AD 租用戶，則使用者設定和組態選項並不會進行轉移。 此外，現有 Azure MFA 伺服器也需要使用 MFA 提供者產生的啟用認證重新啟動。

### <a name="removing-an-authentication-provider"></a>移除驗證提供者

> [!CAUTION]
> 刪除驗證提供者時，不會有任何確認。 選取 [**刪除**] 是永久的程式。

您可以在**Azure 入口網站** > **Azure Active Directory** > **安全性** > **MFA** > **提供者**中找到驗證提供者。 按一下列出的提供者，以查看與該提供者相關聯的詳細資料和設定。

移除驗證提供者之前，請記下您在提供者中設定的任何自訂設定。 決定哪些設定需要從您的提供者遷移至一般 MFA 設定，並完成這些設定的遷移。 

連結到提供者的 Azure MFA server 必須使用**Azure 入口網站** > **Azure Active Directory** > **安全性** > **MFA** > **伺服器設定**底下產生的認證重新開機。 在重新開機之前，您必須從環境中 Azure `\Program Files\Multi-Factor Authentication Server\Data\` MFA server 上的目錄中刪除下列檔案：

- caCert
- cert
- groupCACert
- 群組索引鍵
- groupName
- licenseKey
- pkey

![從 Azure 入口網站刪除驗證提供者](./media/concept-mfa-authprovider/authentication-provider-removal.png)

當您確認所有設定都已遷移之後，您可以流覽至 [ **Azure 入口網站** > **Azure Active Directory** > **安全性** > **MFA** > **提供者**]，然後選取省略號 **...** ，然後選取 [**刪除**]。

> [!WARNING]
> 刪除驗證提供者將會刪除與該提供者相關聯的任何報告資訊。 在刪除您的提供者之前，您可能會想要儲存活動報告。

> [!NOTE]
> 具有舊版 Microsoft Authenticator 應用程式和 Azure MFA Server 的使用者可能需要重新註冊其應用程式。

## <a name="next-steps"></a>後續步驟

[進行 Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
