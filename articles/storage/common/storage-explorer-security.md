---
title: Azure 儲存體總管安全性指南 |Microsoft Docs
description: Azure 儲存體總管的安全性指引
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783754"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure 儲存體總管安全性指南

Microsoft Azure 儲存體總管可讓您輕鬆地在 Windows、macOS 和 Linux 上安全地使用 Azure 儲存體的資料。 藉由遵循這些指導方針，您可以確保您的資料保持受保護。

## <a name="general"></a>一般

- **一律使用最新版的儲存體總管。** 儲存體總管版本可能包含安全性更新。 保持最新狀態有助於確保一般安全性。
- **只連接到您信任的資源。** 您從不受信任來源下載的資料可能是惡意的，將資料上傳至不受信任的來源可能會導致遺失或遭竊的資料。
- **盡可能使用 HTTPS。** 儲存體總管預設會使用 HTTPS。 某些案例可讓您使用 HTTP，但 HTTP 只能做為最後的手段。
- **確定只將所需的許可權提供給需要他們的人員。** 授與任何人存取您資源的許可權時，請避免過度寬鬆。
- **執行重要作業時請務必小心。** 某些作業（例如刪除和覆寫）無法復原，而且可能會造成資料遺失。 執行這些作業之前，請確定您使用的是正確的資源。

## <a name="choosing-the-right-authentication-method"></a>選擇正確的驗證方法

儲存體總管提供各種方式來存取您的 Azure 儲存體資源。 無論您選擇哪種方法，以下是我們的建議。

### <a name="azure-ad-authentication"></a>Azure AD 驗證

存取 Azure 儲存體資源最簡單且最安全的方式，就是使用您的 Azure 帳戶登入。 登入使用 Azure AD 驗證，可讓您：

- 授與特定使用者和群組的存取權。
- 隨時撤銷特定使用者和群組的存取權。
- 強制執行存取條件，例如需要多重要素驗證。

建議盡可能使用 Azure AD 驗證。

本節說明可用來保護您的儲存體資源的兩種 Azure AD 技術。

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure 角色型存取控制 (Azure RBAC)

[Azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 可讓您對 azure 資源進行更細緻的存取控制。 您可以從 Azure 入口網站管理 Azure 角色和許可權。

儲存體總管支援 Azure RBAC 對儲存體帳戶、Blob 和佇列的存取。 如果您需要存取檔案共用或資料表，您必須指派 Azure 角色來授與許可權，以列出儲存體帳戶金鑰。

#### <a name="access-control-lists-acls"></a>存取控制清單 (ACL)

[ (acl 的存取控制清單) ](../blobs/data-lake-storage-access-control.md) 可讓您控制 ADLS Gen2 blob 容器中的檔案和資料夾層級存取。 您可以使用儲存體總管來管理 Acl。

### <a name="shared-access-signatures-sas"></a>共用存取簽章 (SAS)

如果您無法使用 Azure AD authentication，建議使用共用存取簽章。 使用共用存取簽章，您可以：

- 為安全的資源提供匿名有限的存取權。
- 如果是從共用存取原則產生 (SAP) ，請立即撤銷 SAS。

不過，使用共用存取簽章時，您無法：

- 限制誰可以使用 SAS。 具有有效 SAS 的任何人都可以使用有效的 SAS。
- 如果不是從共用存取原則產生 (SAP) 的 SAS，請撤銷 SAS。

在儲存體總管中使用 SAS 時，建議您遵循下列指導方針：

- **限制 SAS 權杖和 Uri 的散發。** 只將 SAS 權杖和 Uri 散發給受信任的個人。 限制 SAS 散發可減少 SAS 可能被誤用的可能性。
- **只使用來自您信任之實體的 SAS 權杖和 Uri。**
- **盡可能在產生 SAS 權杖和 Uri 時，使用 (SAP) 的共用存取原則。** 以共用存取原則為基礎的 SAS 較單純的 SAS 更為安全，因為可以藉由刪除 SAP 來撤銷 SAS。
- **產生具有基本資源存取權和許可權的權杖。** 最基本許可權會限制 SAS 誤用時可能發生的損毀。
- **產生只在必要時才有效的權杖。** 短期 SAS 的存留期特別重要，因為沒有任何方法可以在產生後撤銷它們。

> [!IMPORTANT]
> 共用 SAS 權杖和 Uri 以進行疑難排解時（例如在服務要求或錯誤報表中），一律會至少修訂 SAS 的簽章部分。

### <a name="storage-account-keys"></a>儲存體帳戶金鑰

儲存體帳戶金鑰會授與不受限制地存取儲存體帳戶內的服務和資源。 基於這個理由，建議您限制金鑰的使用以存取儲存體總管中的資源。 請改用 Azure RBAC 功能或 SAS 來提供存取權。

某些 Azure 角色會授與取得儲存體帳戶金鑰的許可權。 具有這些角色的人員可以有效地規避 Azure RBAC 授與或拒絕的許可權。 除非必要，否則不建議您授與此許可權。

儲存體總管會嘗試使用儲存體帳戶金鑰（如果有的話）來驗證要求。 您可以在 ( **服務 > 儲存體帳戶** 的設定中停用這項功能，> 停用) 的金鑰使用方式。 某些功能不支援 Azure RBAC，例如使用傳統儲存體帳戶。 這類功能仍然需要金鑰，而且不受此設定影響。

如果您必須使用金鑰來存取儲存體資源，建議您遵循下列指導方針：

- **請勿與任何人共用您的帳戶金鑰。**
- **將您的儲存體帳戶金鑰（例如密碼）處理。** 如果您必須讓金鑰可供存取，請使用安全儲存解決方案，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

> [!NOTE]
> 如果您認為儲存體帳戶金鑰已被錯誤共用或散發，您可以從 Azure 入口網站為您的儲存體帳戶產生新的金鑰。

### <a name="public-access-to-blob-containers"></a>Blob 容器的公用存取

儲存體總管可讓您修改 Azure Blob 儲存體容器的存取層級。 非私用 blob 容器可讓任何人匿名讀取這些容器中的資料。

啟用 blob 容器的公用存取時，建議使用下列指導方針：

- **請勿啟用 blob 容器的公用存取，其中可能包含任何可能的敏感性資料。** 請確定您的 blob 容器是所有私用資料的免費。
- **請勿將任何可能的敏感性資料上傳至具有 Blob 或容器存取權的 blob 容器。** 

## <a name="next-steps"></a>後續步驟

- [安全性建議](../blobs/security-recommendations.md)