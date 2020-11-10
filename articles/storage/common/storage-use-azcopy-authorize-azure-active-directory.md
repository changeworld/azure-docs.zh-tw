---
title: 使用 AzCopy & Azure Active Directory 授權存取 blob |Microsoft Docs
description: 您可以使用 Azure Active Directory (Azure AD) ，提供 AzCopy 作業的授權認證。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 89aab37b750a61bd21ba9af23875536a8cfbff4a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414415"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>使用 AzCopy 和 Azure Active Directory (Azure AD 授與 blob 的存取權) 

您可以使用 Azure AD 提供授權認證給 AzCopy。 如此一來，您就不需要將共用存取簽章 (SAS) 權杖附加至每個命令。 

首先，確認您的角色指派。 然後，選擇您想要授權的 _安全性主體_ 類型。 [使用者身分識別](../../active-directory/fundamentals/add-users-azure-active-directory.md)、[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)和[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)都是安全性主體的類型。

使用者身分識別是在 Azure AD 中具有身分識別的任何使用者。 這是要授權的最簡單安全性主體。 如果您打算在不需使用者互動的情況下執行的腳本內使用 AzCopy，則受控識別和服務主體是絕佳的選項。 受控識別較適用于從 Azure 虛擬機器 (VM) 執行的腳本，而服務主體較適合在內部部署執行的腳本。 

如需 AzCopy 的詳細資訊，請 [從 AzCopy 開始](storage-use-azcopy-v10.md)著手。

## <a name="verify-role-assignments"></a>確認角色指派

您需要的授權層級，取決於您是否打算上傳檔案或只下載檔案。

如果您只想要下載檔案，請確認已將 [儲存體 Blob 資料讀取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 角色指派給您的使用者身分識別、受控識別或服務主體。

如果您想要上傳檔案，請確認其中一個角色已指派給您的安全性主體：

- [儲存體 Blob 資料參與者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [儲存體 Blob 資料擁有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

這些角色可以指派給這些範圍中的安全性主體：

- 容器 (檔案系統) 
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要瞭解如何驗證和指派角色，請參閱 [使用 Azure 入口網站指派 Azure 角色以存取 blob 和佇列資料](./storage-auth-aad-rbac-portal.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)。

> [!NOTE]
> 請記住，Azure 角色指派最多可能需要五分鐘的時間來傳播。

如果您的安全性主體已新增至目標容器或目錄 (ACL) 的存取控制清單中，您就不需要將其中一個角色指派給安全性主體。 在 ACL 中，安全性主體需要目標目錄的寫入權限，以及容器和每個父目錄的 execute 許可權。

若要深入瞭解，請參閱 [Azure Data Lake Storage Gen2 中的存取控制模型](../blobs/data-lake-storage-access-control-model.md)。

## <a name="authorize-a-user-identity"></a>授權使用者身分識別

在確認您的使用者身分識別已獲得必要的授權層級之後，請開啟命令提示字元，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login
```

如果您收到錯誤，請嘗試包含儲存體帳戶所屬組織的租使用者識別碼。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]  按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

<a id="service-principal"></a>

## <a name="authorize-a-managed-identity"></a>授權受控識別

如果您打算在不需使用者互動的情況下執行的腳本內使用 AzCopy，且腳本 (VM) 從 Azure 虛擬機器執行，這是一個絕佳的選項。 使用此選項時，您不需要在 VM 上儲存任何認證。

您可以使用您已在 VM 上啟用的全系統受控識別，或使用您已指派給 VM 的使用者指派受控識別的用戶端識別碼、物件識別碼或資源識別碼，來登入您的帳戶。

若要深入瞭解如何啟用全系統受控識別，或建立使用者指派的受控識別，請參閱 [使用 Azure 入口網站在 VM 上設定 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)識別。

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>使用全系統受控識別進行授權

首先，請確定您已在 VM 上啟用全系統受控識別。 請參閱 [系統指派的受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。

然後，在命令主控台中輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>使用使用者指派的受控識別進行授權

首先，請確定您已在 VM 上啟用使用者指派的受控識別。 請參閱 [使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。

然後，在命令主控台中，輸入下列任何命令，然後按 ENTER 鍵。

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

將預留位置取代為 `<client-id>` 使用者指派受控識別的用戶端識別碼。

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

將預留位置取代為 `<object-id>` 使用者指派受控識別的物件識別碼。

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

將預留位置取代為 `<resource-id>` 使用者指派受控識別的資源識別碼。

## <a name="authorize-a-service-principal"></a>授權服務主體

如果您打算在不需使用者互動的情況下執行的腳本內使用 AzCopy，特別是在內部部署執行時，這是很好的選擇。 如果您打算在 Azure 中執行的 Vm 上執行 AzCopy，受控服務識別更容易管理。 若要深入瞭解，請參閱本文的「 [授權受控識別](#authorize-a-managed-identity) 」一節。

執行腳本之前，您必須以互動方式至少登入一次，才能提供 AzCopy 給服務主體的認證。  這些認證會儲存在安全和加密的檔案中，因此您的腳本不需要提供該機密資訊。

您可以使用用戶端密碼或使用與服務主體應用程式註冊相關聯之憑證的密碼，來登入您的帳戶。

若要深入瞭解如何建立服務主體，請參閱 [如何：使用入口網站來建立可存取資源 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。

若要深入瞭解服務主體的一般資訊，請參閱[Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md)。

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>使用用戶端秘密授權服務主體

首先，將 `AZCOPY_SPA_CLIENT_SECRET` 環境變數設定為服務主體應用程式註冊的用戶端密碼。

> [!NOTE]
> 請務必從命令提示字元設定此值，而不是在作業系統的環境變數設定中設定。 如此一來，此值只適用于目前的會話。

此範例示範如何在 PowerShell 中進行這項作業。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 請考慮使用如本範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。  

接著，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

將預留位置取代為 `<application-id>` 您服務主體應用程式註冊的應用程式識別碼。 將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>使用憑證授權服務主體

如果您想要使用自己的認證來進行授權，您可以將憑證上傳至您的應用程式註冊，然後使用該憑證來登入。

除了將憑證上傳至您的應用程式註冊之外，您還需要將憑證複本儲存至將執行 AzCopy 的電腦或 VM。 這份憑證複本應該在中。PFX 或。PEM 格式，而且必須包含私密金鑰。 私密金鑰應受密碼保護。 如果您使用的是 Windows，而且您的憑證只存在於憑證存放區中，請務必將該憑證匯出到 PFX 檔案， (包含私密金鑰) 。 如需指引，請參閱 [匯出-get-pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate)

接下來，將 `AZCOPY_SPA_CERT_PASSWORD` 環境變數設定為憑證密碼。

> [!NOTE]
> 請務必從命令提示字元設定此值，而不是在作業系統的環境變數設定中設定。 如此一來，此值只適用于目前的會話。

此範例說明如何在 PowerShell 中進行這項工作。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接著，輸入下列命令，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

將 `<path-to-certificate-file>` 預留位置取代為憑證檔案的相對或完整路徑。 AzCopy 會儲存此憑證的路徑，但不會儲存憑證的複本，因此請務必將該憑證保留在原處。 將預留位置取代為 `<tenant-id>` 儲存體帳戶所屬組織的租使用者識別碼。 若要尋找租使用者識別碼，請選取 Azure 入口網站中 **> 目錄識別碼 Azure Active Directory > 屬性** 。

> [!NOTE]
> 請考慮使用如本範例所示的提示。 如此一來，您的密碼就不會出現在主控台的命令歷程記錄中。 

<a id="managed-identity"></a>


## <a name="next-steps"></a>後續步驟

- 如需 AzCopy 的詳細資訊，請 [AzCopy 開始使用](storage-use-azcopy-v10.md)

- 如果您有任何疑問、問題或一般意見反應，請 [在 GitHub 頁面上](https://github.com/Azure/azure-storage-azcopy) 提交。