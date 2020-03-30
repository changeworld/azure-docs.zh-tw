---
title: 運行狀況檢查的錯誤引用
description: 在 Azure 容器註冊表中運行 az acr 檢查運行狀況診斷命令時發現的錯誤代碼和可能的解決方案
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289136"
---
# <a name="health-check-error-reference"></a>運行狀況檢查錯誤引用

以下是[有關 az acr 檢查運行狀況][az-acr-check-health]命令返回的錯誤代碼的詳細資訊。 對於每個錯誤，都會列出可能的解決方案。

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

此錯誤表示找不到 CLI 的 Docker 用戶端。 因此，不會運行以下其他檢查：查找 Docker 版本、評估 Docker 守護進程狀態以及運行 Docker 拉取命令。

*潛在解決方案*：安裝 Docker 用戶端;將 Docker 路徑添加到系統變數。

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

此錯誤表示 Docker 守護進程狀態不可用，或者無法使用 CLI 到達該狀態。 因此，Docker 操作（如`docker login`和`docker pull`） 通過 CLI 不可用。

*潛在解決方案*：重新開機 Docker 守護進程，或驗證是否已正確安裝。

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

此錯誤表示 CLI 無法運行命令`docker --version`。

*潛在解決方案*：嘗試手動運行命令，確保您具有最新的 CLI 版本，並調查錯誤訊息。

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

此錯誤表示 CLI 無法將示例圖像拉到您的環境。

*潛在解決方案*：驗證拉映射所需的所有元件是否正常運行。

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

此錯誤意味著 CLI 找不到 Helm 用戶端，這排除了其他 Helm 操作。

*潛在解決方案*：驗證是否安裝了 Helm 用戶端，以及其路徑是否添加到系統內容變數中。

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

此錯誤表示 CLI 無法確定已安裝的 Helm 版本。 如果使用的 Azure CLI 版本（或 Helm 版本）已過時，則可能發生此情況。

*潛在解決方案*：更新到最新的 Azure CLI 版本或推薦的 Helm 版本;手動運行該命令並調查錯誤訊息。

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

此錯誤表示給定註冊表登錄伺服器的 DNS 已 ping，但未回應，這意味著它不可用。 這可能表示某些連接問題。 或者，註冊表可能不存在，使用者可能沒有註冊表的許可權（以正確檢索其登錄伺服器），或者目標注冊表位於與 Azure CLI 中使用的雲不同的雲中。

*潛在解決方案*：驗證連接;驗證註冊表的拼寫，並且該註冊表存在;驗證使用者是否具有正確的許可權，以及註冊表的雲是否與 Azure CLI 中使用的雲相同。

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

此錯誤表示給定註冊表的挑戰終結點回應為 403 禁止 HTTP 狀態。 此錯誤意味著使用者無法訪問註冊表，很可能是由於虛擬網路配置。 要查看當前配置的防火牆規則，請使用`az acr show --query networkRuleSet --name <registry>`運行 。

*潛在解決方案*：刪除虛擬網路規則，或將當前用戶端 IP 位址添加到允許的清單。

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

此錯誤意味著目標注冊表的挑戰終結點未發出質詢。

*潛在解決方案*：一段時間後再試一次。 如果錯誤仍然存在，則在 中https://aka.ms/acr/issues打開問題。

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

此錯誤意味著目標注冊表的挑戰終結點發出質詢，但註冊表不支援 Azure 活動目錄身份驗證。

*潛在解決方案*：嘗試其他方式進行身份驗證，例如，使用管理員憑據。 如果使用者需要使用 Azure 活動目錄進行身份驗證，請打開https://aka.ms/acr/issues問題。

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

此錯誤表示註冊表登錄伺服器沒有使用刷新權杖進行回應，因此拒絕訪問目標注冊表。 如果使用者對註冊表沒有正確的許可權，或者 Azure CLI 的使用者憑據陳舊，則可能發生此錯誤。

*潛在解決方案*：驗證使用者是否對註冊表具有正確的許可權;運行`az login`以刷新許可權、權杖和憑據。

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

此錯誤表示註冊表登錄伺服器沒有使用訪問權杖進行回應，因此拒絕對目標注冊表的訪問。 如果使用者對註冊表沒有正確的許可權，或者 Azure CLI 的使用者憑據陳舊，則可能發生此錯誤。

*潛在解決方案*：驗證使用者是否對註冊表具有正確的許可權;運行`az login`以刷新許可權、權杖和憑據。

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

此錯誤表示用戶端無法建立到容器註冊表的安全連線。 如果您正在運行或使用代理伺服器，通常會發生此錯誤。

*潛在解決方案*：有關在代理後面工作的更多資訊，[請參閱此處](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)。

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

此錯誤表示 CLI 找不到給定註冊表的登錄伺服器，並且未找到當前雲的預設尾碼。 如果註冊表不存在、使用者對註冊表沒有正確的許可權、註冊表的雲和當前的 Azure CLI 雲不匹配，或者 Azure CLI 版本已過時，則可能發生此錯誤。

*潛在解決方案*：驗證拼寫是否正確，以及註冊表是否存在;驗證使用者對註冊表具有正確的許可權，並且註冊表的雲與 CLI 環境匹配;將 Azure CLI 更新到最新版本。

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

此錯誤表示 CLI 與當前安裝的 Docker/公證版本不相容。 嘗試手動更換 Docker 安裝的公證用戶端來解決此問題，將公證.exe 版本降級到早于 0.6.0 的版本。

## <a name="next-steps"></a>後續步驟

有關檢查註冊表運行狀況的選項，請參閱檢查 Azure[容器註冊表的運行狀況](container-registry-check-health.md)。

有關 Azure 容器註冊表的常見問題和其他已知問題，請參閱[常見問題解答](container-registry-faq.md)。





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
