---
title: 在入口網站中建立 Azure AD 應用程式 & 服務主體
titleSuffix: Microsoft identity platform
description: 建立新的 Azure Active Directory 應用程式 & 服務主體，以使用 Azure Resource Manager 中的角色型存取控制來管理資源的存取權。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: c5f65adfe401f2f6e99234d08b8e8dabeff7d5db
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264111"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>操作說明：使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體

本文說明如何建立可與角色型存取控制搭配使用的新 Azure Active Directory （Azure AD）應用程式和服務主體。 如果您擁有需要存取或修改資源的程式碼，則可以建立應用程式的身分識別。 此身分識別就是所謂的服務主體。 然後，您可以將必要的權限指派給服務主體。 本文說明如何使用入口網站來建立服務主體。 其中著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。 您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。

> [!IMPORTANT]
> 若不要建立服務主體，可考慮使用 Azure 資源的受控識別作為應用程式識別碼。 如果您的程式碼在支援受控識別的服務上執行，並存取支援 Azure AD authentication 的資源，則受控識別會是較好的選項。 若要深入了解 Azure 資源的受控識別，包含目前哪些服務支援該功能，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。

## <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

讓我們直接跳到建立身分識別。 如果您遇到問題，請檢查[必要的權限](#required-permissions)，以確定您的帳戶可以建立身分識別。

1. 透過 [Azure 入口網站](https://portal.azure.com)登入您的 Azure 帳戶。
1. 選取 **Azure Active Directory**。
1. 選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 為應用程式命名。 選取支援的帳戶類型，以決定可以使用應用程式的人員。 在 [重新**導向 URI**] 下，針對您想要建立的應用程式類型選取 [ **Web** ]。 輸入要在其中傳送存取權杖的 URI。 您無法建立[原生應用程式](../manage-apps/application-proxy-configure-native-client-application.md)的認證。 您無法將該類型使用於自動化應用程式。 設定值之後，選取 [註冊]。

   ![輸入應用程式的名稱](./media/howto-create-service-principal-portal/create-app.png)

您已建立 Azure AD 應用程式和服務主體。

## <a name="assign-a-role-to-the-application"></a>將角色指派給應用程式

若要存取您訂用帳戶中的資源，您必須將角色指派給應用程式。 決定哪個角色可提供應用程式的適當權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 在 Azure 入口網站中，選取您想要指派應用程式的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請在 [**首頁**] 上搜尋並選取 [**訂閱**]，或選取 [**訂閱**]。

   ![例如，在訂用帳戶範圍指派角色](./media/howto-create-service-principal-portal/select-subscription.png)

1. 選取指派應用程式時作為對象的特定訂用帳戶。

   ![選取要指派的訂用帳戶](./media/howto-create-service-principal-portal/select-one-subscription.png)

   如果您未看見所尋找的訂用帳戶，請選取 [全域訂閱篩選]。 確定您想要的訂用帳戶已針對入口網站選取。

1. 選取 [存取控制 (IAM)]。
1. 選取 [新增角色指派]。
1. 選取您想要將應用程式指派給哪個角色。 例如，若要允許應用程式執行諸如**重新開機**、**啟動**和**停止**實例等動作，請選取 [**參與者**] 角色。  根據預設，請閱讀更多[可用角色](../../role-based-access-control/built-in-roles.md)的相關資訊，Azure AD 應用程式不會顯示在可用的選項中。 若要尋找您的應用程式，請搜尋名稱並加以選取。

   ![選取要指派給應用程式的角色](./media/howto-create-service-principal-portal/select-role.png)

1. 選取 [儲存] 以完成角色指派。 您會在具有該範圍之角色的使用者清單中看到您的應用程式。

您的服務主體已設定。 您可以開始使用它來執行指令碼或應用程式。 下節說明如何取得以程式設計方式登入時所需的值。

## <a name="get-values-for-signing-in"></a>取得值以便登入

以程式設計方式登入時，您需要將租用戶識別碼與驗證要求一起傳送。 您也需要應用程式識別碼和驗證金鑰。 若要取得這些值，請使用下列步驟︰

1. 選取 **Azure Active Directory**。
1. 在 Azure AD 中，從 [應用程式註冊] 選取您的應用程式。
1. 複製目錄（租使用者）識別碼，並將它儲存在您的應用程式代碼中。

    ![複製目錄 (租用戶) 識別碼並將它儲存在您的應用程式程式碼中。](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。

   ![複製應用程式 (用戶端) 識別碼](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>憑證和秘密
Daemon 應用程式可以使用兩種形式的認證來驗證 Azure AD：憑證和應用程式秘密。  我們建議使用憑證，但您也可以建立新的應用程式密碼。

### <a name="upload-a-certificate"></a>上傳憑證

您可以使用現有的憑證（如果有的話）。  （選擇性）您可以建立自我簽署憑證，*僅供測試之*用。 開啟 PowerShell 並使用下列參數執行[SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) ，以在您電腦的使用者憑證存放區中建立自我簽署憑證： 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

使用 [[管理使用者憑證](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)] mmc 嵌入式管理單元，從 Windows [控制台] 中，將此憑證匯出至檔案。

1. 從 [**開始**] 功能表中選取 [**執行**]，然後輸入**certmgr.msc**。

   目前使用者的 [憑證管理員] 工具隨即出現。

1. 若要查看您的憑證，請在左窗格中的 [**憑證-目前的使用者**] 底下，展開 [**個人**] 目錄。
1. 以滑鼠右鍵按一下您建立的憑證，選取 所有工作 **-> 匯出**。
1. 依照 [憑證匯出嚮導] 進行。  匯出私密金鑰、指定憑證檔案的密碼，以及匯出至檔案。

若要上傳憑證：

1. 選取 **Azure Active Directory**。
1. 在 Azure AD 中，從 [應用程式註冊] 選取您的應用程式。
1. 選取 [憑證和秘密]。
1. 選取 [**上傳憑證**]，然後選取憑證（現有的憑證或您匯出的自我簽署憑證）。

    ![選取 [上傳憑證]，然後選取您想要新增的憑證](./media/howto-create-service-principal-portal/upload-cert.png)

1. 選取 [新增]。

在應用程式註冊入口網站中向應用程式註冊憑證之後，您必須啟用用戶端應用程式程式碼以使用憑證。

### <a name="create-a-new-application-secret"></a>建立新的應用程式祕密

如果您選擇不使用憑證，您可以建立新的應用程式密碼。

1. 選取 [憑證和秘密]。
1. 選取 [用戶端秘密] -> [新增用戶端密碼]。
1. 提供秘密的描述及持續時間。 完成時，選取 [新增]。

   儲存用戶端秘密之後，就會顯示用戶端秘密的值。 複製此值，因為您稍後將無法取出金鑰。 您將會提供具有應用程式識別碼的金鑰值，以應用程式的身分登入。 將金鑰值儲存在應用程式可擷取的地方。

   ![複製秘密值，因為您之後無法取得此值](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>設定資源的存取原則
請記住，您可能需要設定應用程式需要存取之資源的額外許可權。 例如，您也必須[更新金鑰保存庫的存取原則](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies)，讓您的應用程式存取金鑰、秘密或憑證。  

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的金鑰保存庫，然後選取 **存取原則**。  
1. 選取 [**新增存取原則**]，然後選取您想要授與應用程式的金鑰、密碼和憑證許可權。  選取您先前建立的服務主體。
1. 選取 [**新增**] 以新增存取原則，然後按一下 [**儲存**] 以認可您的變更。
    ![新增存取原則](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>所需的權限

您必須有足夠的許可權，才能向您的 Azure AD 租使用者註冊應用程式，並將 Azure 訂用帳戶中的角色指派給應用程式。

### <a name="check-azure-ad-permissions"></a>檢查 Azure AD 權限

1. 選取 **Azure Active Directory**。
1. 記下您的角色。 如果您具有 [使用者] 角色，則必須確定非系統管理員可以註冊應用程式。

   ![尋找您的角色。 如果您是使用者，請確定非系統管理員可以註冊應用程式](./media/howto-create-service-principal-portal/view-user-info.png)

1. 在左窗格中，選取 [**使用者設定**]。
1. 檢查 [應用程式註冊] 設定。 此值只能由系統管理員設定。 若設定為 [是]，則 Azure AD 租用戶中的任何使用者都可以註冊應用程式。

如果應用程式註冊設定設為 [否]，則只有具備系統管理員角色的使用者才能註冊這些類型的應用程式。 若要了解 Azure AD 中可用的系統管理員角色及賦予每個角色的特定權限，請參閱[可用的角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)和[角色權限](../users-groups-roles/directory-assign-admin-roles.md#role-permissions)。 如果您的帳戶已獲指派使用者角色，但應用程式註冊設定僅限於系統管理員使用者，請要求系統管理員為您指派其中一個可建立和管理應用程式註冊層面的管理員角色，或讓使用者註冊均.

### <a name="check-azure-subscription-permissions"></a>檢查 Azure 訂用帳戶權限

在您的 Azure 訂用帳戶中，您的帳戶必須具有 `Microsoft.Authorization/*/Write` 的存取權，才能將角色指派給 AD 應用程式。 此動作是[擁有者](../../role-based-access-control/built-in-roles.md#owner)角色或[使用者存取系統管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色來授與。 如果您的帳戶已獲指派「**參與者**」角色，則您沒有足夠的許可權。 當您嘗試將角色指派給服務主體時，將會收到錯誤。

若要檢查訂用帳戶權限：

1. 搜尋並選取 [**訂閱**]，或選取**首頁** **上的**[訂用帳戶]。

   ![搜尋](./media/howto-create-service-principal-portal/select-subscription.png)

1. 選取您想要在其中建立服務主體的訂用帳戶。

   ![選取要指派的訂用帳戶](./media/howto-create-service-principal-portal/select-one-subscription.png)

   如果您未看見所尋找的訂用帳戶，請選取 [全域訂閱篩選]。 確定您想要的訂用帳戶已針對入口網站選取。

1. 選取 [我的權限]。 然後，選取 [按一下這裡以詳細檢視此訂用帳戶的完整存取權]。

   ![選取您想要在其中建立服務主體的訂用帳戶](./media/howto-create-service-principal-portal/view-details.png)

1. 選取 [**角色指派**] 中的 [ **view** ] 以查看您指派的角色，並判斷您是否有足夠的許可權可將角色指派給 AD 應用程式。 如果沒有，請洽詢訂用帳戶管理員，將您新增至「使用者存取系統管理員」角色。 在下圖中，會將「擁有者」角色指派給使用者，這表示該使用者具有足夠的許可權。

   ![此範例顯示使用者已獲指派擁有者角色](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>後續步驟

* 若要了解如何指定安全性原則，請參閱 [Azure 角色型存取控制](../../role-based-access-control/role-assignments-portal.md)。  
* 如需可授與或拒絕使用者的可用動作清單，請參閱 [Azure Resource Manager 資源提供者作業](../../role-based-access-control/resource-provider-operations.md)。
