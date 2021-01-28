---
title: OATH 權杖驗證方法-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用 OATH 權杖來協助改善和保護登入事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9276fca62e96395150c9545b8f4dcb5c8c0afb87
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954237"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Azure Active Directory OATH 權杖中的驗證方法

OATH TOTP (時間型單次密碼) 是一項開放標準，可指定單次密碼 (OTP) 程式碼的產生方式。 OATH TOTP 可使用軟體或硬體來實作，以產生程式碼。 Azure AD 不支援 OATH HOTP，這是不同的程式碼產生標準。

## <a name="oath-software-tokens"></a>OATH 軟體權杖

軟體 OATH 權杖通常是應用程式，例如 Microsoft Authenticator 應用程式和其他驗證器應用程式。 Azure AD 會產生秘密金鑰或種子，其會輸入至應用程式並用來產生每個 OTP。

在準備進行推播通知時，Authenticator 應用程式會自動產生代碼，讓使用者即使在其裝置沒有連線能力的情況下也有備份。 也可以使用利用 OATH TOTP 來產生代碼的第三方應用程式。

有些 OATH TOTP 硬體權杖可程式化，這表示其不會隨附預先編寫的秘密金鑰或種子。 您可使用從軟體權杖安裝流程取得的秘密金鑰或種子來設定這些可程式化的硬體權杖。 客戶可以向他們所選的廠商購買這些權杖，並在其廠商的設定程序中使用秘密金鑰或種子。

## <a name="oath-hardware-tokens-preview"></a>OATH 硬體權杖 (預覽)

Azure AD 支援使用 OATH-TOTP SHA-1 權杖，其每隔 30 或 60 秒重新整理一次程式碼。 客戶可以向他們所選的廠商購買這些權杖。

OATH TOTP 硬體權杖通常會隨附在權杖中預先編寫的秘密金鑰或種子。 如下列步驟所述，這些金鑰必須輸入到 Azure AD 中。 祕密金鑰限制為 128 個字元，可能會與所有權杖不相容。 秘密金鑰只能包含字元 *a-z* 或 *A-Z* 和數字 *1-7*，而且必須以 *Base32* 編碼。

在軟體權杖設定流程中也可使用 Azure AD 來設定可重新植入的可程式化 OATH TOTP 硬體權杖。

OATH 硬體權杖已作為公開預覽的一部分支援。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![將 OATH 權杖上傳到 MFA OATH 權杖刀鋒視窗](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

一旦取得權杖之後，必須以逗點分隔值的形式上傳， (CSV) 檔案格式，包括 UPN、序號、秘密金鑰、時間間隔、製造商和型號，如下列範例所示：

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列。

將格式正確設為 CSV 檔案後，系統管理員可接著登入 Azure 入口網站，瀏覽至 [Azure Active Directory] > [安全性] > [MFA] > [OATH 權杖]，並上傳所產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 選取 [重新整理] 按鈕來取得目前的狀態。 如果檔案中有任何錯誤，您可下載 CSV 檔案，其中列出您需要解決的任何錯誤。 所下載 CSV 檔案中的欄位名稱與上傳的版本不同。  

一旦解決任何錯誤後，系統管理員可接著針對權杖選取 [啟動] 並輸入權杖上顯示的 OTP，以啟動每個金鑰。 每隔5分鐘可啟用最多200個 OATH 權杖。 

使用者可設定最多 5 個 OATH 硬體權杖或驗證器應用程式 (例如 Microsoft Authenticator 應用程式) 的組合，以在任何時間點使用。

## <a name="next-steps"></a>下一步

深入瞭解如何使用 [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)來設定驗證方法。
