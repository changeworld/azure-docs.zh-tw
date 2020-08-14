---
title: 針對登入登錄進行疑難排解
description: 登入 Azure container registry 時常見問題的徵兆、原因和解決方式
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 8fbb96be8223001ac52db47788c31609e9b86e35
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227234"
---
# <a name="troubleshoot-registry-login"></a>針對登錄登入進行疑難排解

本文可協助您針對登入 Azure container registry 時可能遇到的問題進行疑難排解。 

## <a name="symptoms"></a>徵狀

可能包含下列一或多項：

* 無法使用 `docker login` 、 `az acr login` 或兩者登入登錄
* 無法登入登錄，且您收到錯誤 `unauthorized: authentication required` 或 `unauthorized: Application not registered with AAD`
* 無法登入登錄，且您收到 Azure CLI 錯誤 `Could not connect to the registry login server`
* 無法推送或提取映射，而且您收到 Docker 錯誤 `unauthorized: authentication required`
* 無法從 Azure Kubernetes Service、Azure DevOps 或其他 Azure 服務存取登錄
* 無法存取登錄，且您收到錯誤 `Error response from daemon: login attempt failed with status: 403 Forbidden` -請參閱針對登錄 [的網路問題進行疑難排解](container-registry-troubleshoot-access.md)
* 無法存取或查看 Azure 入口網站中的登錄設定，或使用 Azure CLI 管理登錄

## <a name="causes"></a>原因

* Docker 未在您的環境中正確設定- [解決方案](#check-docker-configuration)
* 登錄不存在或名稱不正確- [解決方案](#specify-correct-registry-name)
* 登錄認證無效- [解決方案](#confirm-credentials-to-access-registry)
* 認證未獲授權，無法進行推送、提取或 Azure Resource Manager 作業- [解決方案](#confirm-credentials-are-authorized-to-access-registry)
* 認證已過期- [解決方案](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>進一步診斷 

執行 [az acr check-health](/cli/azure/acr#az-acr-check-health) 命令，以取得登錄環境健全狀況的詳細資訊，並選擇性地存取目標登錄。 例如，診斷 Docker 設定錯誤或 Azure Active Directory 登入問題。 

如需命令範例，請參閱 [檢查 Azure container registry 的健全狀況](container-registry-check-health.md) 。 如果報告錯誤，請參閱 [錯誤參考](container-registry-health-error-reference.md) 和下列各節，以取得建議的解決方案。

> [!NOTE]
> 如果有防火牆或網路設定導致無法存取登錄，也可能會發生某些驗證或授權錯誤。 請參閱針對登錄 [的網路問題進行疑難排解](container-registry-troubleshoot-access.md)。

## <a name="potential-solutions"></a>可能的解決方案

### <a name="check-docker-configuration"></a>檢查 Docker 設定

大部分 Azure Container Registry 的驗證流程都需要本機 Docker 安裝，因此您可以使用登錄進行驗證以進行作業，例如推送和提取映射。 確認 docker CLI 用戶端和 daemon (Docker 引擎) 正在您的環境中執行。 您需要 Docker 用戶端18.03 版或更新版本。

相關連結：

* [驗證概觀](container-registry-authentication.md#authentication-options)
* [Container registry 常見問題](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>指定正確的登錄名稱

使用時 `docker login` ，請提供登錄的完整登入伺服器名稱，例如 *myregistry.azurecr.io*。 請確定您只使用小寫字母。 範例：

```console
docker login myregistry.azurecr.io
```

使用 [az acr login](/cli/azure/acr#az-acr-login) 搭配 Azure Active Directory 身分識別時，請先登 [入 Azure CLI](/cli/azure/authenticate-azure-cli)，然後指定登錄的 Azure 資源名稱。 資源名稱是建立登錄時提供的名稱，例如不含網域尾碼) 的 *myregistry* (。 範例：

```azurecli
az acr login --name myregistry
```

相關連結：

* [az acr login 成功，但 Docker 失敗，發生錯誤為：未經授權：需要驗證](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>確認認證以存取登錄

檢查您在案例中使用的認證是否有效，或由登錄擁有者提供給您。 一些可能的問題：

* 如果使用 Active Directory 服務主體，請務必在 Active Directory 租使用者中使用正確的認證：
  * 使用者名稱-服務主體應用程式識別碼 (也稱為 *用戶端識別碼*) 
  * 密碼-服務主體密碼 (也稱為 *用戶端密碼*) 
* 如果使用 Azure 服務（例如 Azure Kubernetes Service 或 Azure DevOps）來存取登錄，請確認您服務的登錄設定。
* 如果您 `az acr login` 使用選項執行 `--expose-token` ，這會在不使用 Docker daemon 的情況下啟用登錄登入，請確定您是使用使用者名稱進行驗證 `00000000-0000-0000-0000-000000000000` 。

相關連結：

* [驗證概觀](container-registry-authentication.md#authentication-options)
* [使用 Azure AD 進行個人登入](container-registry-authentication.md#individual-login-with-azure-ad)
* [使用服務主體登入](container-registry-auth-service-principal.md)
* [使用受控識別登入](container-registry-authentication-managed-identity.md)
* [使用存放庫範圍的權杖登入](container-registry-repository-scoped-permissions.md)
* [使用系統管理員帳戶登入](container-registry-authentication.md#admin-account)
* [Azure AD 驗證和授權錯誤代碼](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az-acr-login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>確認認證已獲授權可存取登錄

確認與認證相關聯的登入權利，例如 `AcrPull` 要從登錄提取映射的 RBAC 角色，或用 `AcrPush` 來推送映射的角色。 

在入口網站中或使用 Azure CLI 的登錄管理中，必須至少 `Reader` 要有角色才能執行 Azure Resource Manager 作業。

您或登錄擁有者必須在訂用帳戶中擁有足夠的許可權，才能新增或移除角色指派。

相關連結：

* [RBAC 角色和許可權-Azure Container Registry](container-registry-roles.md)
* [使用存放庫範圍的權杖登入](container-registry-repository-scoped-permissions.md)
* [使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)
* [使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)
* [建立新的應用程式祕密](../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)
* [Azure AD 驗證和授權碼](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>檢查認證未過期

權杖和 Active Directory 認證可能會在定義的期間後過期，以防止登錄存取。 若要啟用存取，可能需要重設或重新產生認證。

* 如果使用個別的 AD 身分識別、受控識別，或登錄登入的服務主體，則 AD 權杖會在3小時後到期。 再次登入登錄。  
* 如果使用具有過期用戶端密碼的 AD 服務主體，訂用帳戶擁有者或帳戶管理員必須重設認證或產生新的服務主體。
* 如果使用存放 [庫範圍的權杖](container-registry-repository-scoped-permissions.md)，登錄擁有者可能需要重設密碼或產生新的權杖。

相關連結：

* [重設服務主體認證](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [重新產生權杖密碼](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [使用 Azure AD 進行個人登入](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>進階疑難排解

如果已在登錄中啟用 [資源記錄的收集](container-registry-diagnostics-audit-logs.md) ，請檢查 ContainterRegistryLoginEvents 記錄。 此記錄會儲存驗證事件和狀態，包括傳入身分識別和 IP 位址。 查詢記錄檔中的登錄 [驗證失敗](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)。 

相關連結：

* [診斷評估與審核的記錄](container-registry-diagnostics-audit-logs.md)
* [Container registry 常見問題](container-registry-faq.md)
* [Azure Container Registry 的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>後續步驟

如果您沒有在這裡解決問題，請參閱下列選項。

* 其他登錄疑難排解主題包括：
  * [針對登錄的網路問題進行疑難排解](container-registry-troubleshoot-access.md)
  * [針對登錄效能進行疑難排解](container-registry-troubleshoot-performance.md)
* [社區支援](https://azure.microsoft.com/support/community/) 選項
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [開啟支援票證](https://azure.microsoft.com/support/create-ticket/) -根據您提供的資訊，可能會針對登錄中的驗證失敗執行快速診斷


