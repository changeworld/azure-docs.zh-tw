---
title: 向 Azure Key Vault 進行驗證
description: 了解如何向 Azure Key Vault 進行驗證
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: aaaeec3a20088cad447ab0e8034d12ef3268fae3
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793226"
---
# <a name="authenticate-to-azure-key-vault"></a>向 Azure Key Vault 進行驗證

## <a name="overview"></a>概觀

Azure Key Vault 是一種秘密管理解決方案，可讓您集中儲存應用程式秘密，並控制其散發。 Azure Key Vault 不需要在應用程式中儲存認證。 您的應用程式可以向金鑰保存庫進行驗證，以擷取所需的認證。 本文件將涵蓋向金鑰保存庫進行驗證的基本概念。

本文件將協助您了解金鑰保存庫驗證的運作方式。 本文件將說明驗證流程、示範如何授與金鑰保存庫的存取權，以及包含一個教學課程，教導如何從範例 python 應用程式擷取金鑰保存庫中儲存的秘密。

本文件將涵蓋：

* 重要概念
* 安全性主體註冊
* 了解 Key Vault 驗證流程
* 授與服務主體存取 Key Vault 的權限
* 教學課程 (Python)

## <a name="key-concepts"></a>重要概念

### <a name="azure-active-directory-concepts"></a>Azure Active Directory 概念

* Azure Active Directory (AAD) - Azure Active Directory (Azure AD) 是 Microsoft 的雲端型身分識別和存取管理服務，可協助您的員工登入及存取資源

* 角色定義 - 角色定義是權限集合。  AAD 具有標準角色 (擁有者、參與者或讀者)，其中包含在 Azure 資源上執行讀取、寫入和刪除等作業的權限層級。 角色也可以是自訂定義，而這些自訂定義是由具有特定細微權限的使用者所建立。

* 應用程式註冊 - 當您註冊 Azure AD 應用程式時，Azure AD 租用戶中會建立兩個物件︰應用程式物件和服務主體物件。 將應用程式物件視為應用程式的全域代表 (用於所有租用戶)，而將服務主體看做是本機代表 (用於特定租用戶)。

### <a name="security-principal-concepts"></a>安全性主體概念

* 安全性主體 - 安全性主體是一個物件，代表要求存取 Azure 資源的使用者、群組、服務主體或受控識別。

* 使用者 - 在 Azure Active Directory 中具有設定檔的個人。

* 群組 - 在 Azure Active Directory 中建立的一組使用者。 當您將角色指派給群組時，該群組內的所有使用者都具有該角色。

* 服務主體 - 應用程式或服務用來存取特定 Azure 資源的安全性身分識別。 您可以將其視為應用程式的使用者身分識別 (使用者名稱和密碼或憑證)。

* 受控識別 - 在 Azure Active Directory 中由 Azure 自動管理的身分識別。

* 物件識別碼 - Azure AD 所產生的唯一識別碼，會在其初始佈建期間繫結至服務主體。

## <a name="security-principal-registration"></a>安全性主體註冊

1. 管理員會在 Azure Active Directory 中註冊使用者或應用程式 (服務主體)。

2. 管理員會建立 Azure Key Vault 並設定存取原則 (ACL)。

3. (選擇性) 管理員會設定 Azure Key Vault 防火牆。

![IMAGE](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>了解 Key Vault 驗證流程

1. 服務主體會進行呼叫，以向 AAD 進行驗證，這種情況可能會以數種方式發生：
    * 使用者可以藉由使用者名稱和密碼登入 Azure 入口網站。
    * 應用程式會使用用戶端識別碼，並將用戶端密碼或用戶端憑證呈現給 AAD
    * Azure 資源 (例如虛擬機器) 具有指派的 MSI，並會聯繫 IMDS REST 端點以取得存取權杖。

2. 如果向 AAD 進行驗證成功，則會將 OAuth 權杖授與服務主體。
3. 服務主體會呼叫 Key Vault。
4. Azure Key Vault 防火牆決定是否允許呼叫。
    * 案例 1：Key Vault 防火牆已停用，可從公用網際網路觸達金鑰保存庫的公用端點 (URI)。 允許呼叫。
    * 案例 2：呼叫者是 Azure Key Vault 信任的服務。 如果選取此選項，某些 Azure 服務可以略過金鑰保存庫防火牆。 [Key Vault 信任的服務清單](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * 案例 3：呼叫者會依 IP 位址、虛擬網路或服務端點列示在 Azure Key Vault 防火牆中。
    * 案例 4：呼叫者可以透過設定的私人連結連線觸達 Azure Key Vault。
    * 案例 5：呼叫者未獲授權，且會傳回禁止回應。
5. Key Vault 會呼叫 AAD 來驗證服務主體的存取權杖。
6. Key Vault 會檢查服務主體是否有足夠的存取原則權限來執行要求的作業，在此範例中，作業是取得祕密。
7. Key Vault 會將祕密提供給服務主體。

![IMAGE](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>授與服務主體存取 Key Vault 的權限

1. 如果您還沒有服務主體，請建立一個。 [建立服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. 在 Azure Key Vault IAM 設定中，將角色指派新增至您的服務主體。 您可以新增預先指派的擁有者、參與者或讀者角色。 也可以為您的服務主體建立自訂角色。 您應遵循最低權限的主體，並只提供服務主體所需的最小存取權。 
3.  設定金鑰保存庫防火牆。 您可以讓金鑰保存庫防火牆保持停用狀態，並允許從公用網際網路存取 (較不安全，但更容易設定)。 也可以限制只能存取特定 IP 範圍、服務端點、虛擬網路或私人端點 (更安全)。
4.  為您的服務主體新增存取原則，這是您服務主體可在金鑰保存庫上執行的作業清單。 您應該使用最低權限的主體，並限制服務主體可以執行的作業。 不過，如果您未提供足夠的權限，您的服務主體將會遭到拒絕存取。

## <a name="tutorial"></a>教學課程

在本教學課程中，您將了解如何設定服務主體，以向 key vault 進行驗證並擷取秘密。 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>第 1 部分：在 Azure 入口網站中建立服務主體

1. 登入 Azure 入口網站
1. 搜尋 Azure Active Directory
1. 按一下 [應用程式註冊] 索引標籤
1. 按一下 [+ 新增註冊]
1. 建立服務主體的名稱
1. 選取 [註冊]

此時您有已註冊的服務主體。 您可以選取 [應用程式註冊] 來檢視此服務主體。 現在會將用戶端識別碼 GUID 指派給您的服務主體，請將此視為您服務主體的「使用者名稱」。 現在，我們需要為您的服務主體建立「密碼」，您可以使用用戶端密碼或用戶端憑證。 請注意，使用用戶端密碼進行驗證並不安全，而且應該僅用於測試目的。 本教學課程將說明如何使用用戶端憑證。

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>第 2 部分：為您的服務主體建立用戶端憑證

1. 建立憑證

    * 選項 1：使用 [OpenSSL](https://www.openssl.org/) 建立憑證 (僅供測試之用，請不要在生產環境中使用自我簽署憑證)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * 選項 2：使用金鑰保存庫建立憑證。 [在 Azure Key Vault 中建立憑證](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. 以 PEM 格式下載憑證
1. 登入 Azure 入口網站，然後瀏覽至 Azure Active Directory
1. 按一下 [應用程式註冊]
1. 選取您在第 1 部分中建立的服務主體。
1. 按一下服務主體的 [憑證和祕密] 索引標籤
1. 使用 [上傳憑證] 按鈕來上傳憑證

### <a name="part-3-configure-an-azure-key-vault"></a>第 3 部分：設定 Azure Key Vault

1. 建立 Azure Key Vault [連結](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)

2. 設定 Key Vault IAM 權限
    1. 瀏覽至您的金鑰保存庫
    1. 選取 [存取控制 (IAM)] 索引標籤
    1. 按一下 [新增角色指派]
    1. 從下拉式清單中選取 [參與者] 角色
    1. 輸入所建立服務主體的名稱或用戶端識別碼
    1. 按一下 [檢視角色指派]，確認您的服務主體已列出

3. 設定 Key Vault 存取原則權限
    1. 瀏覽至您的金鑰保存庫
    1. 選取 [設定] 底下的 [存取原則] 索引標籤
    1. 選取 [+ 新增存取原則] 連結
    1. 在 [秘密權限] 下拉式清單中，勾選 [取得] 和 [列出] 權限。
    1. 依名稱或用戶端識別碼選取您的服務主體。
    1. 選取 [新增]
    1. 選取 [儲存]

4. 在您的金鑰保存庫中建立秘密。
    1. 瀏覽至您的金鑰保存庫
    1. 按一下 [設定] 底下的 [秘密] 索引標籤
    1. 選取 [+ 產生/匯入]
    1. 建立祕密的名稱，在此範例中，我會將祕密命名為 "test"
    1. 建立祕密的值，在此範例中，我會設定 "password123" 一值

現在，從本機電腦執行程式碼時，您可以藉由呈現用戶端識別碼和憑證的路徑，藉以取得存取權杖，向金鑰保存庫進行驗證。

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>第 4 部分：從應用程式中的 Azure Key Vault 擷取祕密 (Python)

使用下列程式碼範例，測試您的應用程式是否可以使用您設定的服務主體，從金鑰保存庫中擷取秘密。 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![IMAGE](../media/authentication-3.png)


## <a name="next-steps"></a>後續步驟

1. 了解如何對金鑰保存庫驗證錯誤進行疑難排解。 [Key Vault 疑難排解指南](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
