---
title: 為 B2B - Azure AD 設置與 AD FS 的直接聯合
description: 瞭解如何將 AD FS 設置為直接聯合的標識提供程式，以便來賓可以登錄到 Azure AD 應用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272836"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>示例：與活動目錄聯合服務 （AD FS） 的直接聯合（預覽）
|     |
| --- |
| 直接聯合是 Azure 活動目錄的公共預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介紹如何使用 Active 目錄聯合服務 （AD FS） 作為 SAML 2.0 或 WS-Fed 標識提供程式設置[直接聯合](direct-federation.md)。 要支援直接聯合，必須在標識提供程式配置某些屬性和聲明。 為了說明如何為直接聯合配置標識提供程式，我們將使用 Active 目錄聯合服務 （AD FS） 作為示例。 我們將演示如何將 AD FS 設置為 SAML 標識提供程式和 WS-Fed 標識提供程式。

> [!NOTE]
> 本文介紹如何為 SAML 和 WS-Fed 設置 AD FS 以進行說明。 對於標識提供程式為 AD FS 的直接聯合集成，我們建議使用 WS-Fed 作為協定。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>為 SAML 2.0 直接聯合配置 AD FS
Azure AD B2B 可以配置為與使用 SAML 協定且具有下面列出的特定要求的標識提供程式聯合。 為了說明 SAML 配置步驟，本節演示如何為 SAML 2.0 設置 AD FS。 

要設置直接聯合，必須在標識提供程式的 SAML 2.0 回應中接收以下屬性。 可以通過連結到線上安全權杖服務 XML 檔或手動輸入它們來配置這些屬性。 [在創建測試 AD FS 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步驟 12 描述了如何查找 AD FS 終結點或如何生成中繼資料`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`URL，例如 。 

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的頒發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

需要在標識提供程式頒發的 SAML 2.0 權杖中配置以下聲明：


|屬性  |值  |
|---------|---------|
|名稱 ID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一節將說明如何使用 AD FS 作為 SAML 2.0 標識提供程式的示例配置所需的屬性和聲明。

### <a name="before-you-begin"></a>開始之前

在開始此過程之前，必須設置 AD FS 伺服器並正常運行。 有關設置 AD FS 伺服器的説明，請參閱[在 Azure 虛擬機器上創建測試 AD FS 3.0 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>添加聲明說明

1. 在 AD FS 伺服器上，選擇 **"工具** > **AD FS"管理**。
2. 在功能窗格中，選擇 **"服務** > **聲明說明**"。
3. 在 **"操作"** 下，選擇 **"添加索賠說明**"。
4. 在 **"添加聲明描述"** 視窗中，指定以下值：

   - **顯示名稱**： 持久識別碼
   - **聲明識別碼**：`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 選擇聯合**中繼資料中"發佈此聲明說明"核取方塊，作為此識別身分同盟服務可以接受的聲明類型**。
   - 選擇聯合**中繼資料中"發佈此聲明說明"核取方塊，作為此識別身分同盟服務可以發送的聲明類型**。

5. 按一下 [確定]****。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加依賴方信任和索賠規則

1. 在 AD FS 伺服器上，轉到**工具** > **AD FS 管理**。
2. 在功能窗格中，選擇**信任關係** > **依賴方信任**。
3. 在 **"操作"** 下，選擇 **"添加依賴方信任**"。 
4. 在 **"選擇資料來源**"的添加依賴方信任嚮導中，使用"**導入有關連線或本地網路上發佈的依賴方的資料"** 選項。 指定此聯合中繼資料 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml。 保留其他預設選擇。 選取 [關閉]****。
5. 將打開 **"編輯聲明規則"** 嚮導。
6. 在 **"編輯聲明規則"** 嚮導中，選擇 **"添加規則**"。 在**選擇規則類型**中，選擇 **"發送 LDAP 屬性作為聲明**"。 選取 [下一步]****。
7. 在 **"配置聲明規則"中**，指定以下值： 

   - **聲明規則名稱**：電子郵件聲明規則 
   - **屬性存儲**： 活動目錄 
   - **LDAP 屬性**： 電子郵件地址 
   - **傳出聲明類型**： 電子郵件地址

8. 選取 [完成]****。
9. "**編輯聲明規則"** 視窗將顯示新規則。 按一下 [套用]****。 
10. 按一下 [確定]****。  

### <a name="create-an-email-transform-rule"></a>創建電子郵件轉換規則
1. 轉到 **"編輯索賠規則**"，然後按一下"**添加規則**"。 在 **"選擇規則類型**"中，選擇 **"轉換傳入聲明**"，然後按一下"**下一步**"。 
2. 在 **"配置聲明規則"中**，指定以下值： 

   - **聲明規則名稱**：電子郵件轉換規則 
   - **傳入聲明類型**：電子郵件地址 
   - **傳出聲明類型**：名稱 ID 
   - **傳出名稱 ID 格式**： 持久識別碼 
   - 選擇 **"傳遞所有聲明值**"。

3. 按一下 **[完成]**。 
4. "**編輯聲明規則"** 視窗將顯示新規則。 按一下 [套用]****。 
5. 按一下 [確定]****。 AD FS 伺服器現在配置為使用 SAML 2.0 協定進行直接聯合。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>為 WS-Fed 直接聯合配置 AD FS 
Azure AD B2B 可以配置為與使用 WS-Fed 協定且具有下面列出的特定要求的標識提供程式聯合。 目前，兩個 WS-Fed 提供程式已測試與 Azure AD 的相容性，包括 AD FS 和 Shibboleth。 在這裡，我們將使用活動目錄聯合服務 （AD FS） 作為 WS-Fed 標識提供程式的示例。 有關使用 Azure AD 在符合 WS-Fed 的提供程式之間建立依賴方信任的詳細資訊，請下載 Azure AD 標識提供程式相容性文檔。

要設置直接聯合，必須在來自標識提供程式的 WS-Fed 消息中接收以下屬性。 可以通過連結到線上安全權杖服務 XML 檔或手動輸入它們來配置這些屬性。 [在創建測試 AD FS 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步驟 12 描述了如何查找 AD FS 終結點或如何生成中繼資料`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`URL，例如 。
 
|屬性  |值  |
|---------|---------|
|被動要求者端點     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的頒發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 頒發的 WS-Fed 權杖所需的聲明：

|屬性  |值  |
|---------|---------|
|不可改變ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一節將說明如何使用 AD FS 作為 WS-Fed 標識提供程式的示例配置所需的屬性和聲明。

### <a name="before-you-begin"></a>開始之前
在開始此過程之前，必須設置 AD FS 伺服器並正常運行。 有關設置 AD FS 伺服器的説明，請參閱[在 Azure 虛擬機器上創建測試 AD FS 3.0 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加依賴方信任和索賠規則 
1. 在 AD FS 伺服器上，轉到**工具** > **AD FS 管理**。 
1. 在功能窗格中，選擇**信任關係** > **依賴方信任**。 
1. 在 **"操作"** 下，選擇 **"添加依賴方信任**"。  
1. 在添加依賴方信任嚮導中，對於**選擇資料來源**，請使用選項 **"導入有關連線或本地網路上發佈的依賴方的資料**"。 指定此聯合中繼資料 URL： `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他預設選擇。 選取 [關閉]****。
1. 將打開 **"編輯聲明規則"** 嚮導。 
1. 在 **"編輯聲明規則"** 嚮導中，選擇 **"添加規則**"。 在 **"選擇規則類型**"中，選擇**使用自訂規則發送聲明**。 選取 [下一步]**。 
1. 在 **"配置聲明規則"中**，指定以下值：

   - **聲明規則名稱**：發出不可改變的 ID  
   - **自訂規則**：`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 選取 [完成]****。 
1. "**編輯聲明規則"** 視窗將顯示新規則。 按一下 [套用]****。  
1. 在同一 **"編輯聲明規則"** 嚮導中，選擇 **"添加規則**"。 在**Cohose 規則類型中**，選擇 **"發送 LDAP 屬性作為聲明**"。 選取 [下一步]****。
1. 在 **"配置聲明規則"中**，指定以下值： 

   - **聲明規則名稱**：電子郵件聲明規則  
   - **屬性存儲**： 活動目錄  
   - **LDAP 屬性**： 電子郵件地址  
   - **傳出聲明類型**： 電子郵件地址 

1.  選取 [完成]****。 
1.  "**編輯聲明規則"** 視窗將顯示新規則。 按一下 [套用]****。  
1.  按一下 [確定]****。 AD FS 伺服器現在配置為使用 WS-Fed 進行直接聯合。

## <a name="next-steps"></a>後續步驟
接下來，您將在 Azure AD 門戶中或使用 PowerShell[配置直接聯合](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)。 
