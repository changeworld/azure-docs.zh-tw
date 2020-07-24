---
title: 針對伺服器的 Azure Arc 代理程式連線問題進行疑難排解
description: 本文說明如何針對在嘗試連線到服務時，由 Azure Arc 伺服器（預覽）所引發的連線機器代理程式，進行疑難排解並解決問題。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/20/2020
ms.topic: conceptual
ms.openlocfilehash: 46096e1f3f4266e9c070bd1d67f328241163126b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004540"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>針對連線的機器代理程式連接問題進行疑難排解

本文提供有關在嘗試為 Windows 或 Linux 設定適用于伺服器（預覽）的已連線機器代理程式時，疑難排解和解決可能發生之問題的資訊。 包括設定與服務的連線時的互動式和大規模安裝方法。 如需一般資訊，請參閱[伺服器的 Arc 總覽](./overview.md)。

## <a name="agent-verbose-log"></a>代理程式詳細資訊記錄檔

在遵循本文稍後所述的疑難排解步驟之前，您所需的最少資訊就是詳細記錄。 當使用 verbose （-v）引數時，它會包含**azcmagent**工具命令的輸出。 記錄檔會 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` 針對 Windows 和 Linux 寫入至 `/var/opt/azcmagent/log/azcmagent.log` 。

### <a name="windows"></a>Windows

以下是命令的範例，以在執行互動式安裝時，使用已連線的 Windows 電腦代理程式來啟用詳細資訊記錄。

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

以下是使用服務主體執行大規模安裝時，使用適用于 Windows 的已連線機器代理程式來啟用詳細資訊記錄的命令範例。

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

以下是命令的範例，以在執行互動式安裝時使用連線的電腦代理程式 for Linux 啟用詳細資訊記錄。

>[!NOTE]
>您必須具備 Linux 電腦的*根*存取權限，才能執行**azcmagent**。

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

以下是使用服務主體執行大規模安裝時，使用已連線的電腦代理程式 for Linux 啟用詳細資訊記錄的命令範例。

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>代理程式連線問題至服務

下表列出一些已知的錯誤，以及有關如何疑難排解和解決問題的建議。

|訊息 |錯誤 |可能的原因 |解決方案 |
|--------|------|---------------|---------|
|無法取得授權權杖裝置流程 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |無法連線到 `login.windows.net` 端點 | 確認端點的連線能力。 |
|無法取得授權權杖裝置流程 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy 或防火牆封鎖了對 `login.windows.net` 端點的存取。 | 驗證端點的連線能力，且防火牆或 proxy 伺服器不會封鎖它。 |
|無法取得授權權杖裝置流程  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | 在 [*系統重新開機] 上，群組原則物件電腦設定 \ 系統管理範本 \ 系統 \ 使用者設定檔 \ 刪除超過指定天數的使用者設定檔*已啟用。 | 確認已啟用 GPO，並將目標設為受影響的電腦。 如需進一步的詳細資料，請參閱註腳<sup>[1](#footnote1)</sup> 。 |
|無法從 SPN 取得授權權杖 |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy 或防火牆封鎖了對 `login.windows.net` 端點的存取。 |驗證端點的連線能力，且防火牆或 proxy 伺服器不會封鎖它。 |
|無法從 SPN 取得授權權杖 |`Invalid client secret is provided` |錯誤或不正確服務主體密碼。 |確認服務主體密碼。 |
| 無法從 SPN 取得授權權杖 |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |不正確的服務主體及/或租使用者識別碼。 |確認服務主體及/或租使用者識別碼。|
|取得 ARM 資源回應 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |錯誤的認證和/或許可權 |請確認您或服務主體是**Azure 已連線電腦**上線角色的成員。 |
|無法 AzcmagentConnect ARM 資源 |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |未註冊 Azure 資源提供者。 |註冊[資源提供者](./agent-overview.md#register-azure-resource-providers)。 |
|無法 AzcmagentConnect ARM 資源 |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxy 伺服器或防火牆封鎖對 `management.azure.com` 端點的存取。 |驗證端點的連線能力，且防火牆或 proxy 伺服器不會封鎖它。 |

<a name="footnote1"></a><sup>1</sup>如果啟用此 GPO 並套用至具有已連線機器代理程式的電腦，它會刪除與指定給*himds*服務的內建帳戶相關聯的使用者設定檔。 因此，它也會刪除用來與在本機憑證存放區中快取的服務通訊的驗證憑證30天。 在30天的限制之前，會嘗試更新憑證。 若要解決此問題，請遵循下列步驟來[取消註冊機器](manage-agent.md#unregister-machine)，然後再向執行的服務重新註冊該電腦 `azcmagent connect` 。

## <a name="next-steps"></a>後續步驟

如果在這裡沒有看到問題或無法解決問題，請嘗試下列其中一個管道以取得其他支援：

* 透過[Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-arc.html)取得 Azure 專家的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。

* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。