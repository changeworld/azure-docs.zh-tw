---
title: 登錄後，應用頁面上將顯示錯誤訊息 |微軟文檔
description: 當應用返回錯誤訊息時，如何解決 Azure AD 登錄的問題。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185485"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>應用頁在使用者登錄後顯示錯誤訊息

在這種情況下，Azure 活動目錄 （Azure AD） 會對使用者進行簽名。 但是，應用程式會顯示一條錯誤訊息，並且不會讓使用者完成登錄流。 問題是應用不接受 Azure AD 發出的回應。

應用不接受 Azure AD 的回應的原因有多種。 如果錯誤訊息沒有明確標識回應中缺少的內容，請嘗試以下操作：

-   如果應用是 Azure AD 庫，請驗證您是否按照[如何調試 Azure AD 中基於 SAML 的單一登入](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)中的步驟。

-   使用[Fiddler](https://www.telerik.com/fiddler)等工具捕獲 SAML 請求、回應和權杖。

-   將 SAML 回應發送給應用供應商，並詢問他們缺少什麼。

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 回應中缺少屬性

要在 Azure AD 配置中添加將在 Azure AD 回應中發送的屬性，請按照以下步驟操作：

1. 打開[**Azure 門戶**](https://portal.azure.com/)，以全域管理員或共同管理員身份登錄。

2. 在左側功能窗格的頂部，選擇 **"所有服務**"以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE]
   > 如果看不到所需的應用，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為單次登錄配置的應用程式。

7. 載入應用後，在功能窗格中選擇 **"單一登入**"。

8. 在 **"使用者屬性**"部分中，選擇 **"查看並編輯所有其他使用者屬性**"。 在這裡，您可以更改使用者登錄時要在 SAML 權杖中發送到應用的屬性。

   若要新增屬性︰

   1. 選擇 **"添加屬性**"。 輸入**名稱**，並從下拉清單中選擇 **"值**"。

   1.  選取 [儲存]****。 您將在表中看到新屬性。

9. 儲存組態。

   下次使用者登錄到應用時，Azure AD 將在 SAML 回應中發送新屬性。

## <a name="the-app-doesnt-identify-the-user"></a>應用不標識使用者

登錄到應用失敗，因為 SAML 回應缺少角色等屬性。 或者它失敗，因為應用需要**NameID（** 使用者識別碼）屬性的不同格式或值。

如果使用[Azure AD 自動使用者預配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)來創建、維護和刪除應用中的使用者，請驗證使用者是否已預配到 SaaS 應用。 有關詳細資訊，請參閱[未將使用者預配到 Azure AD 庫應用程式](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>將屬性添加到 Azure AD 應用配置

若要變更使用者識別碼的值，請遵循下列步驟︰

1. 打開[**Azure 門戶**](https://portal.azure.com/)，以全域管理員或共同管理員身份登錄。

2. 選擇左側功能窗格頂部**的所有服務**以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE]
   > 如果看不到所需的應用，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為 SSO 配置的應用。

7. 載入應用後，在功能窗格中選擇 **"單一登入**"。

8. 在 **"使用者屬性**"下，從**使用者識別碼**下拉清單中選擇使用者的唯一識別碼。

## <a name="change-the-nameid-format"></a>更改 NameID 格式

如果應用程式需要**NameID（** 使用者識別碼）屬性的其他格式，請參閱編輯[nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid)以更改 NameID 格式。

Azure AD 根據所選值或應用在 SAML AuthRequest 中請求的格式選擇**NameID**屬性（使用者識別碼）的格式。 有關詳細資訊，請參閱[單一登入 SAML 協定的](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)"NameID 策略"部分。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>應用需要為 SAML 回應使用不同的簽名方法

要更改 SAML 權杖的哪些部分由 Azure AD 進行數位簽章，請按照以下步驟操作：

1. 打開[Azure 門戶](https://portal.azure.com/)，以全域管理員或共同管理員身份登錄。

2. 選擇左側功能窗格頂部**的所有服務**以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用清單。

   > [!NOTE]
   > 如果看不到所需的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為單次登錄配置的應用程式。

7. 載入應用程式後，在功能窗格中選擇 **"單一登入**"。

8. 在**SAML 簽章憑證**下，選擇 **"顯示高級證書簽章設定**"。

9. 從以下選項中選擇應用期望的 **"簽名"** 選項：

   * **簽署 SAML 回應**
   * **簽署 SAML 回應和斷言**
   * **簽署 SAML 斷言**

   下次使用者登錄到應用時，Azure AD 將簽署所選 SAML 回應的部分。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>應用程式期望SHA-1簽名演算法

預設情況下，Azure AD 使用最安全的演算法對 SAML 權杖進行簽名。 我們建議您不要將簽名演算法更改為*SHA-1，* 除非應用需要 SHA-1。

若要變更簽署演算法，請遵循下列步驟：

1. 打開[Azure 門戶](https://portal.azure.com/)，以全域管理員或共同管理員身份登錄。

2. 選擇左側功能窗格頂部**的所有服務**以打開 Azure AD 副檔名。

3. 在篩選器搜索框中鍵入**Azure 活動目錄**，然後選擇**Azure 活動目錄**。

4. 在 Azure AD 功能窗格中選擇**企業應用程式**。

5. 選擇 **"所有應用程式**"以查看應用程式的清單。

   > [!NOTE]
   > 如果看不到所需的應用程式，請使用 **"所有應用程式清單**"頂部的 **"篩選器"** 控制項。 將 **"顯示"** 選項設置為"所有應用程式"。

6. 選擇要為單次登錄配置的應用。

7. 載入應用後，從應用左側的功能窗格中選擇 **"單一登入**"。

8. 在**SAML 簽章憑證**下，選擇 **"顯示高級證書簽章設定**"。

9. 選擇**SHA-1**作為**簽名演算法**。

   下次使用者登錄到應用時，Azure AD 將使用 SHA-1 演算法對 SAML 權杖進行簽名。

## <a name="next-steps"></a>後續步驟
[如何調試 Azure AD 中應用程式中基於 SAML 的單一登入](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
