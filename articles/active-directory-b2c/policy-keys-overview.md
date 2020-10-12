---
title: 原則金鑰總覽-Azure Active Directory B2C
description: 瞭解可在 Azure Active Directory B2C 中用來簽署和驗證權杖、用戶端密碼、憑證和密碼的加密原則金鑰類型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8019c049d830df0c2f3301a450eed60145c8eab3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570429"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的原則金鑰總覽

Azure Active Directory B2C (Azure AD B2C) 以原則金鑰的形式儲存秘密和憑證，以與它所整合的服務建立信任關係。 這些信任包含：

- 外部識別提供者
- 使用[REST API 服務](restful-technical-profile.md)連接
- 權杖簽署和加密

 本文討論 Azure AD B2C 所使用的原則金鑰的相關須知。

> [!NOTE]
> 目前，原則金鑰的設定僅限於 [自訂原則](active-directory-b2c-get-started-custom.md) 。

您可以設定秘密和憑證，以在 [ **原則金鑰** ] 功能表底下的 Azure 入口網站中建立服務之間的信任。 金鑰可以是對稱或非對稱的。 *對稱* 式加密或私密金鑰加密是用來加密和解密資料的共用密碼。 *非對稱* 式密碼編譯（或稱公開金鑰加密）是一種密碼編譯系統，使用的金鑰組是由與信賴憑證者應用程式共用的公開金鑰所組成，以及僅 Azure AD B2C 已知的私密金鑰。

## <a name="policy-keyset-and-keys"></a>原則索引鍵集和金鑰

Azure AD B2C 中原則金鑰的最上層資源是索引 **鍵集** 容器。 每個索引鍵集至少包含一個 **金鑰**。 索引鍵具有下列屬性：

| 屬性 |  必要 | 備註 |
| --- | --- |--- |
| `use` | 是 | 使用方式：識別公開金鑰的預期用途。 加密資料 `enc` ，或驗證資料的簽章 `sig` 。|
| `nbf`| 否 | 啟用日期和時間。 |
| `exp`| 否 | 到期日期和時間。 |

建議您根據 PKI 標準來設定金鑰啟用和到期值。 基於安全性或原則的考慮，您可能需要定期輪替這些憑證。 例如，您可能會有每年輪替所有憑證的原則。

若要建立金鑰，您可以選擇下列其中一種方法：

- **手動** -使用您定義的字串來建立秘密。 秘密是對稱金鑰。 您可以設定啟用日期和到期日。
- 已**產生**-自動產生金鑰。 您可以設定啟用日期和到期日。 有兩個選項：
  - **Secret** -產生對稱金鑰。
  - **RSA** - (非對稱金鑰) 產生金鑰組。
- **上傳** -上傳憑證或 PKCS12 金鑰。 憑證必須包含 (非對稱金鑰) 的私用和公開金鑰。

## <a name="key-rollover"></a>金鑰變換

基於安全性考慮，Azure AD B2C 可以定期變換金鑰，或在發生緊急狀況時立即變換金鑰。 任何與 Azure AD B2C 整合的應用程式、身分識別提供者或 REST API 都應該準備好處理金鑰變換事件，無論發生的頻率為何。 否則，如果您的應用程式或 Azure AD B2C 嘗試使用過期的金鑰來執行密碼編譯作業，則登入要求會失敗。

如果 Azure AD B2C 索引鍵集有多個索引鍵，則根據下列準則，一次只能有一個使用中的金鑰：

- 金鑰啟用是以 **啟用日期**為基礎。
  - 金鑰會依啟用日期以遞增順序排序。 未來的啟用日期之後的金鑰會顯示在清單中較低的位置。 沒有啟用日期的金鑰位於清單底部。
  - 當目前的日期和時間大於金鑰的啟用日期時，Azure AD B2C 將會啟動金鑰，並停止使用先前的作用中金鑰。
- 當目前金鑰的到期時間已過，且金鑰容器包含的新金鑰具有有效的 *非之前* 和 *到期* 時間時，新的金鑰將會自動變成使用中狀態。
- 當目前金鑰的到期時間已過，且 *金鑰容器不* 包含有效但 *不早* 于 *到期* 時間的新金鑰，Azure AD B2C 將無法使用過期的金鑰。 Azure AD B2C 將會在自訂原則的相依元件內引發錯誤訊息。 若要避免這個問題，您可以建立不含啟用和到期日的預設金鑰做為安全的網路。
- 當 [JwtIssuer 技術設定檔](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile)中參考金鑰時，OpenId connect 知名設定端點的金鑰端點 (JWKS URI) 會反映金鑰容器中設定的金鑰。 使用 OIDC 程式庫的應用程式將會自動提取此中繼資料，以確保它使用正確的金鑰來驗證權杖。 如需詳細資訊，請瞭解如何使用 [Microsoft 驗證程式庫](https://docs.microsoft.com/azure/active-directory/develop/msal-b2c-overview)，此程式庫一律會自動提取最新的權杖簽署金鑰。

## <a name="policy-key-management"></a>原則金鑰管理

若要取得金鑰容器內目前的活動金鑰，請使用 Microsoft Graph API [getActiveKey](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey) 端點。

新增或刪除簽署和加密金鑰：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面的 [原則] 下，選取 [識別體驗架構]。
1. 選取 **原則金鑰** 
    1. 若要加入新的機碼， **請選取 [新增]**。
    1. 若要移除新的金鑰，請選取金鑰，然後選取 [ **刪除**]。 若要刪除金鑰，請輸入要刪除之金鑰容器的名稱。 Azure AD B2C 將會刪除金鑰，並使用後置 .bak 來建立金鑰的複本。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用 Microsoft Graph 來自動化 [金鑰集](microsoft-graph-operations.md#trust-framework-policy-keyset) 和 [原則金鑰](microsoft-graph-operations.md#trust-framework-policy-key) 部署。







