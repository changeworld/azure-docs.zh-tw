---
title: 健全狀況檢查的錯誤參考
description: 在 Azure Container Registry 中執行 az acr check-health 診斷命令所發現問題的錯誤碼和可能的解決方案
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289136"
---
# <a name="health-check-error-reference"></a>健全狀況檢查錯誤參考

以下是[az acr check-health][az-acr-check-health]命令所傳回之錯誤碼的詳細資料。 針對每個錯誤，會列出可能的解決方案。

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

此錯誤表示找不到適用于 CLI 的 Docker 用戶端。 因此，不會執行下列額外的檢查：尋找 Docker 版本、評估 Docker daemon 狀態，以及執行 Docker pull 命令。

*可能的解決方案*：安裝 Docker 用戶端;將 Docker 路徑新增至系統變數。

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

此錯誤表示 Docker daemon 狀態為 [無法使用]，或無法使用 CLI 來達到。 因此，透過 CLI 無法使用 Docker 作業（ `docker login`例如`docker pull`和）。

*可能的解決方案*：重新開機 Docker daemon，或驗證是否已正確安裝。

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

此錯誤表示 CLI 無法執行命令`docker --version`。

*可能的解決方案*：請嘗試手動執行命令，確定您有最新的 CLI 版本，並調查錯誤訊息。

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

此錯誤表示 CLI 無法將範例映射提取到您的環境。

*可能的解決方案*：驗證提取映射所需的所有元件都正常運作。

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

此錯誤表示 CLI 找不到 Helm 用戶端，這會排除其他 Helm 作業。

*可能的解決方案*：請確認已安裝 Helm 用戶端，而且其路徑已加入系統內容變數中。

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

此錯誤表示 CLI 無法判斷已安裝的 Helm 版本。 如果所使用的 Azure CLI 版本（或 Helm 版本）已過時，就可能發生這種情況。

*可能的解決方案*：更新至最新的 Azure CLI 版本或建議的 Helm 版本;手動執行命令，並調查錯誤訊息。

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

此錯誤表示已 ping 到指定登錄登入伺服器的 DNS，但沒有回應，這表示它無法使用。 這可能表示某些連接問題。 或者，登錄可能不存在、使用者可能沒有登錄的許可權（若要正確地抓取其登入伺服器），或目標登錄所在的雲端與 Azure CLI 所用的不同。

*可能的解決方案*：驗證連線能力;確認登錄的拼寫和登錄是否存在;請確認使用者具有適當的許可權，而且登錄的雲端與 Azure CLI 中使用的相同。

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

此錯誤表示指定登錄的挑戰端點回應了403禁止的 HTTP 狀態。 此錯誤表示使用者無法存取登錄，很可能是因為虛擬網路設定。 若要查看目前設定的防火牆規則， `az acr show --query networkRuleSet --name <registry>`請執行。

*可能的解決方案*：移除虛擬網路規則，或將目前的用戶端 IP 位址新增至允許清單。

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

此錯誤表示目標登錄的挑戰端點不會發出挑戰。

*可能的解決方案*：稍後再試一次。 如果錯誤持續發生，請在https://aka.ms/acr/issues中開啟問題。

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此錯誤表示目標登錄的挑戰端點發出挑戰，但登錄不支援 Azure Active Directory 驗證。

*可能的解決方案*：嘗試以不同的方式驗證，例如使用系統管理員認證。 如果使用者需要使用 Azure Active Directory 進行驗證，請在https://aka.ms/acr/issues中開啟問題。

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此錯誤表示登錄登入伺服器沒有回應重新整理權杖，因此拒絕存取目標登錄。 如果使用者沒有登錄的正確許可權，或 Azure CLI 的使用者認證已過時，就會發生此錯誤。

*可能的解決方案*：確認使用者是否擁有登錄的正確許可權;執行`az login`以重新整理許可權、權杖和認證。

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此錯誤表示登錄登入伺服器未以存取權杖回應，因此已拒絕對目標登錄的存取。 如果使用者沒有登錄的正確許可權，或 Azure CLI 的使用者認證已過時，就會發生此錯誤。

*可能的解決方案*：確認使用者是否擁有登錄的正確許可權;執行`az login`以重新整理許可權、權杖和認證。

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

此錯誤表示用戶端無法建立與容器登錄的安全連線。 如果您執行的是或使用 proxy 伺服器，通常就會發生此錯誤。

*可能的解決方案*：您可以在[這裡找到](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)有關在 proxy 後方工作的詳細資訊。

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

此錯誤表示 CLI 找不到指定登錄的登入伺服器，而且目前的雲端找不到預設尾碼。 如果登錄不存在，如果使用者沒有適當的登入權利、如果登錄的雲端和目前的 Azure CLI 雲端不相符，或 Azure CLI 版本已過時，就會發生此錯誤。

*可能的解決方案*：請確認拼寫是否正確，以及登錄是否存在;請確認使用者具有登錄的正確許可權，且登錄和 CLI 環境的雲端符合;將 Azure CLI 更新為最新版本。

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

此錯誤表示 CLI 與目前安裝的 Docker/Notary 版本不相容。 請嘗試以手動方式取代 Docker 安裝的 Notary 用戶端來解決此問題，以將您的 notary 版本降級至0.6.0 之前的版本。

## <a name="next-steps"></a>後續步驟

如需檢查登錄健康狀態的選項，請參閱[檢查 Azure container registry 的健全狀況](container-registry-check-health.md)。

如需有關 Azure Container Registry 的常見問題和其他已知問題，請參閱[常見問題](container-registry-faq.md)。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
