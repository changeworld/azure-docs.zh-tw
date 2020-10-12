---
title: Trusona 和 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure AD B2C 上將 Trusona 新增為身分識別提供者，以啟用無密碼 authentication。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0d5b369e1c143b3df4157329bcf7d3a3f7142d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87489464"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>將 Trusona 與 Azure Active Directory B2C 整合

Trusona 是獨立軟體廠商 (ISV) 提供者，可啟用無密碼 authentication、多重要素驗證和數位授權掃描，以協助保護登入的安全。 在本文中，您將瞭解如何將 Trusona 新增為 Azure AD B2C 中的身分識別提供者，以啟用無密碼 authentication。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。
* Trusona 的[試用帳戶](https://www.trusona.com/aadb2c)

## <a name="scenario-description"></a>案例描述

在此案例中，Trusona 會作為 Azure AD B2C 的身分識別提供者，以啟用無密碼 authentication。 下列元件組成方案：

* Azure AD B2C 合併登入和註冊原則
* Trusona 已新增至 Azure AD B2C 作為身分識別提供者
* 可下載的 Trusona 應用程式

![Trusona 架構圖表](media/partner-trusona/trusona-architecture-diagram.png)

| 步驟 | 說明 |
|------|------|
|1     | 使用者嘗試登入或註冊應用程式。 使用者會透過 Azure AD B2C 的註冊和登入原則進行驗證。 在註冊期間，會使用使用者先前在 Trusona 應用程式中驗證的電子郵件地址。     |
|2     | Azure B2C 會使用隱含流程將使用者重新導向至 Trusona OpenID Connect (OIDC) 識別提供者。     |
|3     | 針對以電腦為基礎的登入，Trusona 會顯示唯一、無狀態、動畫和動態 QR 代碼，以使用 Trusona 應用程式進行掃描。 針對以行動裝置為基礎的登入，Trusona 會使用「深層連結」來開啟 Trusona 應用程式。 這兩種方法會用於裝置和最終使用者探索。     |
|4     | 使用者會使用 Trusona 應用程式掃描顯示的 QR 代碼。     |
|5     | 使用者的帳戶可在 Trusona 雲端服務中找到，並已準備好驗證。     |
|6     | Trusona 雲端服務會透過傳送給 Trusona 應用程式的推播通知，對使用者發出驗證挑戰：<br>a. 系統會提示使用者輸入驗證挑戰。 <br> b. 使用者選擇接受或拒絕挑戰。 <br> c. 系統會要求使用者使用 OS 安全性 (例如，生物特徵辨識、密碼、PIN 或模式) ，以在安全記憶體保護區/受信任的執行環境中使用私密金鑰來確認並簽署挑戰。 <br> d. Trusona 應用程式會根據驗證的參數，產生動態的防重新執行承載。 <br> e. 整個回應是在安全記憶體保護區/受信任的執行環境中，以私密金鑰) 的第二次 (簽署，並傳回給 Trusona 雲端服務進行驗證。      |
|7     |  Trusona 雲端服務會使用 id_token 將使用者重新導向回起始應用程式。 Azure AD B2C 使用在識別提供者設定期間所設定的 Trusona 已發佈 OpenID 設定來驗證 id_token。    |
|  |  |

## <a name="onboard-with-trusona"></a>使用 Trusona 上線

1. 填寫 [表單](https://www.trusona.com/aadb2c) 以建立 Trusona 帳戶，然後開始使用。

2. 從 app store 下載 Trusona mobile 應用程式。 安裝應用程式並註冊您的電子郵件。

3. 透過軟體所傳送的安全「魔術連結」來驗證您的電子郵件。  

4. 移至 [Trusona 開發人員](https://dashboard.trusona.com) 的自助服務儀表板。

5. 選取 [ **我已準備就緒** ]，並使用您的 Trusona 應用程式進行驗證。

6. 從左側導覽窗格中，選擇 [ **OIDC**整合]。

7. 選取 [ **建立 OpenID Connect 整合**]。

8. 提供您選擇的 **名稱** ，並使用先前提供的網域資訊 (例如，Contoso) 在 [ **用戶端重新導向主機] 欄位**中。  

   > [!NOTE]
   > Azure Active Directory 的初始功能變數名稱會當做用戶端重新導向主機使用。

9. 遵循《 [Trusona 整合指南》](https://docs.trusona.com/integrations/aad-b2c-integration/)中的指示。 出現提示時，請使用初始功能變數名稱 (例如，Contoso) 在上一個步驟中參考。  

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="add-a-new-identity-provider"></a>新增身分識別提供者

> [!NOTE]
> 如果您還沒有租用戶，請[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到 Azure 訂用帳戶。

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。

4. 流覽至**Dashboard**  >  **Azure Active Directory B2C**身分  >  **識別提供者**的儀表板。

3. 選取 [ **識別提供者**]。

4. 選取 [新增]。

### <a name="configure-an-identity-provider"></a>設定身分識別提供者  

1. 選取 [**識別提供者類型**]  >  **OpenID Connect (預覽]) **。

2. 填寫表單以設定身分識別提供者：  

   | 屬性 | 值  |
   | :--- | :--- |
   | 中繼資料 URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | 用戶端識別碼 | 將會透過電子郵件傳送給您 Trusona |
   | 影響範圍 | OpenID 設定檔電子郵件 |
   | 回應類型 | Id_token |
   | 回應模式  | Form_post |

3. 選取 [確定]  。  

4. 選取 [ **對應此識別提供者的宣告**]。  

5. 填寫表單以對應身分識別提供者：

   | 屬性 | 值  |
   | :--- | :--- |
   | UserID | Sub  |
   | 顯示名稱 | 暱稱 |
   | 指定的名稱 | given_name |
   | Surname | Family_name |
   | 回應模式 | 電子郵件 |

6. 選取 **[確定]** 以完成新 OIDC 身分識別提供者的設定。

### <a name="create-a-user-flow-policy"></a>建立使用者流程原則

您現在應該會看到 Trusona 成為 B2C 身分識別提供者中所列的 **新 OpenID Connect 識別提供者** 。

1. 在您的 Azure AD B2C 租使用者中，選取 [ **原則**] 底下的 [ **使用者流程**]。

1. 選取 [ **新增使用者流程**]。

1. 選取 [ **註冊並登入**]，選取版本，然後選取 [ **建立**]。

1. 輸入原則的 **名稱** 。

1. 在 [ **識別提供者** ] 區段中，選取您新建立的 **Trusona 身分識別提供者**。

   > [!NOTE]
   > 因為 Trusona 本質上是多重要素，所以最好保持停用多重要素驗證。

1. 選取 [建立]****。

1. 在 [ **使用者屬性和宣告**] 下，選擇 [ **顯示更多**]。 在表單中，選取至少一個您在先前章節的身分識別提供者設定期間所指定的屬性。

1. 選取 [確定]  。  

### <a name="test-the-policy"></a>測試原則

1. 選取新建立的原則。

2. 選取 [執行使用者流程]。

3. 在表單中，輸入 [回復 URL]。

4. 選取 [執行使用者流程]。 您應該重新導向至 Trusona OIDC 閘道。 在 Trusona 閘道上，使用 Trusona 應用程式或使用 Trusona mobile SDK 的自訂應用程式，掃描顯示的安全 QR 代碼。

5. 掃描安全的 QR 代碼之後，您應該重新導向至您在步驟3中定義的回復 URL。

## <a name="next-steps"></a>接下來的步驟  

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](custom-policy-overview.md)

- [AAD B2C 中的自訂原則入門](custom-policy-get-started.md?tabs=applications)
