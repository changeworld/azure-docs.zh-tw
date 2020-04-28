---
title: 使用適用于 B2B Azure AD 的 AD FS 來設定直接同盟
description: 瞭解如何設定 AD FS 做為直接同盟的身分識別提供者，讓來賓可以登入您的 Azure AD 應用程式
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272836"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>範例：與 Active Directory 同盟服務的直接同盟（AD FS）（預覽）
|     |
| --- |
| 直接同盟是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文說明如何使用 Active Directory 同盟服務（AD FS），以 SAML 2.0 或 WS-送出的身分識別提供者來設定[直接同盟](direct-federation.md)。 若要支援直接同盟，必須在識別提供者上設定某些屬性和宣告。 為了說明如何設定直接同盟的識別提供者，我們將使用 Active Directory 同盟服務（AD FS）做為範例。 我們將示範如何將 AD FS 同時設定為 SAML 身分識別提供者和 WS-ADDRESSING 身分識別提供者。

> [!NOTE]
> 本文說明如何設定 SAML 和 WS-ADDRESSING 的 AD FS，以供說明之用。 針對 AD FS 身分識別提供者的直接同盟整合，建議使用 WS-ADDRESSING 做為通訊協定。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>設定 SAML 2.0 直接同盟的 AD FS
Azure AD B2B 可以設定為與使用 SAML 通訊協定與下列特定需求的身分識別提供者同盟。 為了說明 SAML 設定步驟，本節說明如何設定 SAML 2.0 的 AD FS。 

若要設定直接同盟，必須在識別提供者的 SAML 2.0 回應中收到下列屬性。 這些屬性可以藉由連結至線上 Security Token Service XML 檔案或手動輸入來設定。 [建立測試 AD FS 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步驟12說明如何尋找 AD FS 端點，或如何產生中繼資料 URL （例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`）。 

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的簽發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

下列宣告必須在識別提供者所簽發的 SAML 2.0 權杖中進行設定：


|屬性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一節說明如何使用 AD FS 來設定必要的屬性和宣告，以作為 SAML 2.0 身分識別提供者的範例。

### <a name="before-you-begin"></a>開始之前

開始此程式之前，必須先設定 AD FS 伺服器並正常運作。 如需設定 AD FS 伺服器的說明，請參閱[在 Azure 虛擬機器上建立測試 AD FS 3.0 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>新增宣告描述

1. 在您的 AD FS 伺服器上，選取 [**工具** > ] [**AD FS 管理**]。
2. 在流覽窗格中，選取 [**服務** > 宣告**描述**]。
3. 在 [**動作**] 底下，選取 [**新增宣告描述**]。
4. 在 [**新增宣告描述**] 視窗中，指定下列值：

   - **顯示名稱**：持續性識別碼
   - 宣告**識別碼**：`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 選取 [**在同盟中繼資料中發佈此宣告描述] 核取方塊，做為此 federation service 可接受**的宣告類型。
   - 選取 [**在同盟中繼資料中發佈此宣告描述] 核取方塊，做為此 federation service 可傳送**的宣告類型。

5. 按一下 [確定]  。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>新增信賴憑證者信任和宣告規則

1. 在 AD FS 伺服器上，移至 [**工具** > ] [**AD FS 管理**]。
2. 在流覽窗格中，選取 [**信任關係** > ] [信賴憑證者**信任**]。
3. 在 [**動作**] 底下，選取 [**新增信賴**憑證者信任]。 
4. 在 [為**選取資料來源**新增信賴憑證者信任] 嚮導中，使用 [匯**入在線上或區域網路上發佈的信賴憑證者相關資料**] 選項。 指定此同盟中繼資料 URL https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml-。 保留其他預設選項。 選取 [關閉]  。
5. [**編輯宣告規則**] 嚮導隨即開啟。
6. 在 [**編輯宣告規則**] 中，選取 [**新增規則**]。 在 **[選擇規則類型**] 中，選取 [**以宣告方式傳送 LDAP 屬性**]。 選取 [下一步]  。
7. 在 [**設定宣告規則**] 中，指定下列值： 

   - 宣告**規則名稱**：電子郵件宣告規則 
   - **屬性存放區**： Active Directory 
   - **LDAP 屬性**：電子郵件地址 
   - **傳出宣告類型**：電子郵件地址

8. 選取 [完成]  。
9. [**編輯宣告規則**] 視窗會顯示新的規則。 按一下 [套用]  。 
10. 按一下 [確定]  。  

### <a name="create-an-email-transform-rule"></a>建立電子郵件轉換規則
1. 移至 [**編輯宣告規則**]，然後按一下 [**新增規則**]。 在 **[選擇規則類型**] 選取 [**轉換傳入**宣告]，然後按 **[下一步]**。 
2. 在 [**設定宣告規則**] 中，指定下列值： 

   - 宣告**規則名稱**：電子郵件轉換規則 
   - **傳入宣告類型**：電子郵件地址 
   - **傳出宣告類型**：名稱識別碼 
   - **外寄名稱 ID 格式**：持續性識別碼 
   - 選取 [**傳遞所有宣告值**]。

3. 按一下 [完成]  。 
4. [**編輯宣告規則**] 視窗會顯示新的規則。 按一下 [套用]  。 
5. 按一下 [確定]  。 AD FS 伺服器現在已設定為使用 SAML 2.0 通訊協定的直接同盟。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>設定 WS-ADDRESSING 直接同盟的 AD FS 
Azure AD B2B 可以設定為與使用 WS-ADDRESSING 通訊協定的身分識別提供者同盟，並具有下列特定需求。 目前，兩個 WS-ADDRESSING 提供者已通過 Azure AD 的相容性測試，包括 AD FS 和 Shibboleth。 在這裡，我們將使用 Active Directory 同盟服務（AD FS）做為 WS-ADDRESSING 身分識別提供者的範例。 如需在與 Azure AD 的 WS-ADDRESSING 相容提供者之間建立信賴憑證者信任的詳細資訊，請下載 Azure AD 身分識別提供者相容性檔。

若要設定直接同盟，必須在來自識別提供者的 WS-送訊息中收到下列屬性。 這些屬性可以藉由連結至線上 Security Token Service XML 檔案或手動輸入來設定。 [建立測試 AD FS 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步驟12說明如何尋找 AD FS 端點，或如何產生中繼資料 URL （例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`）。
 
|屬性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|適用對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |合作夥伴 IdP 的簽發者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 所簽發的 WS-送出權杖所需的宣告：

|屬性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一節說明如何使用 AD FS 做為 WS-ADDRESSING 身分識別提供者的範例，來設定必要的屬性和宣告。

### <a name="before-you-begin"></a>開始之前
開始此程式之前，必須先設定 AD FS 伺服器並正常運作。 如需設定 AD FS 伺服器的說明，請參閱[在 Azure 虛擬機器上建立測試 AD FS 3.0 實例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>新增信賴憑證者信任和宣告規則 
1. 在 AD FS 伺服器上，移至 [**工具** > ] [**AD FS 管理**]。 
1. 在流覽窗格中，選取 [**信任關係** > ] [信賴憑證者**信任**]。 
1. 在 [**動作**] 底下，選取 [**新增信賴**憑證者信任]。  
1. 在 [新增信賴憑證者信任] 中，針對 [**選取資料來源**]，使用 [匯**入在線上或區域網路上發佈的信賴憑證者相關資料**] 選項。 指定此同盟中繼資料 URL `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`：。  保留其他預設選項。 選取 [關閉]  。
1. [**編輯宣告規則**] 嚮導隨即開啟。 
1. 在 [**編輯宣告規則**] 中，選取 [**新增規則**]。 在 **[選擇規則類型**] 中，選取 [**使用自訂規則傳送宣告**]。 選取 [下一步]  。 
1. 在 [**設定宣告規則**] 中，指定下列值：

   - 宣告**規則名稱**：問題不彈性識別碼  
   - **自訂規則**：`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 選取 [完成]  。 
1. [**編輯宣告規則**] 視窗會顯示新的規則。 按一下 [套用]  。  
1. 在相同的 [**編輯宣告規則**] 中，選取 [**新增規則**]。 在 [ **Cohose 規則類型**] 中，選取 [**以宣告方式傳送 LDAP 屬性**]。 選取 [下一步]  。
1. 在 [**設定宣告規則**] 中，指定下列值： 

   - 宣告**規則名稱**：電子郵件宣告規則  
   - **屬性存放區**： Active Directory  
   - **LDAP 屬性**：電子郵件地址  
   - **傳出宣告類型**：電子郵件地址 

1.  選取 [完成]  。 
1.  [**編輯宣告規則**] 視窗會顯示新的規則。 按一下 [套用]  。  
1.  按一下 [確定]  。 AD FS 伺服器現在已設定為使用 WS-送出的直接同盟。

## <a name="next-steps"></a>後續步驟
接下來，您會在 Azure AD 入口網站中或使用 PowerShell，在[Azure AD 中設定直接同盟](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)。 
