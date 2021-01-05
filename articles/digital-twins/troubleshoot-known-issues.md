---
title: 已知問題-Azure 數位 Twins
description: 取得協助，以協助辨識和緩和 Azure 數位 Twins 的已知問題。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q3
ms.openlocfilehash: d0c26255e6d9d35d51390ed2b432b9c5dc9ab2be
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862459"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 數位 Twins 的已知問題

本文提供與 Azure 數位 Twins 相關之已知問題的相關資訊。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的「400用戶端錯誤：不正確的要求」

**問題描述：** 在中執行 Cloud Shell 的命令 *https://shell.azure.com* 可能會間歇性失敗，並出現錯誤「400用戶端錯誤： url 的要求不正確：」 http://localhost:50342/oauth2/token ，後面接著完整堆疊追蹤。

| 這對我有何影響？ | 原因 | 解決方案 |
| --- | --- | --- |
| 在 &nbsp; Azure &nbsp; 數位 &nbsp; Twins 中，這會影響下列命令群組：<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | 這是 Cloud Shell 中已知問題的結果： [*從 Cloud Shell 取得權杖間歇性失敗，並出現400用戶端錯誤：不正確的要求*](https://github.com/Azure/azure-cli/issues/11749)。<br><br>這會顯示 Azure 數位 Twins 實例驗證權杖的問題，以及 Cloud Shell 的預設 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 型驗證。 <br><br>這不會影響或命令群組中的 Azure 數位 Twins 命令 `az dt` `az dt endpoint` ，因為它們會根據 Azure Resource Manager) 使用不同類型的驗證權杖 (，這對 Cloud Shell 的受控身分識別驗證沒有任何問題。 | 解決此問題的方法之一是 `az login` 在 Cloud Shell 中重新執行命令，並完成後續的登入步驟。 這會將您的會話切換出受控識別驗證，以避免根本問題。 在此之後，您應該可以重新執行此命令。<br><br>或者，您可以在 Azure 入口網站中開啟 Cloud Shell 窗格，並從該處完成 Cloud Shell 工作。<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Azure 入口網站圖示列中 Cloud Shell 圖示的影像" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>最後，另一個解決方法是在您的電腦上 [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ，讓您可以在本機執行 Azure CLI 命令。 本機 CLI 不會遇到此問題。 |


## <a name="missing-role-assignment-after-scripted-setup"></a>腳本安裝之後遺失角色指派

**問題描述：** 某些使用者可能會遇到 [*如何：設定實例和驗證 (腳本)*](how-to-set-up-instance-scripted.md)的角色指派部分問題。 腳本不會指出失敗，但 *Azure 數位 Twins 資料擁有* 者角色未成功指派給使用者，而此問題將會影響在途中建立其他資源的能力。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| 這對我有何影響？ | 原因 | 解決方案 |
| --- | --- | --- |
| 若要判斷在執行腳本之後是否已成功設定您的角色指派，請依照安裝程式文章的 [*驗證使用者角色指派*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 一節中的指示進行。 如果您的使用者未以此角色顯示，此問題會影響您。 | 針對使用個人 [Microsoft 帳戶 (MSA) ](https://account.microsoft.com/account)登入的使用者，您的使用者在這類命令中識別您的使用者主體識別碼可能與使用者的登入電子郵件不同，因此腳本難以探索及使用以正確指派角色。 | 若要解決此問題，您可以使用 [CLI 指示](how-to-set-up-instance-cli.md#set-up-user-access-permissions) 或 [Azure 入口網站指示](how-to-set-up-instance-portal.md#set-up-user-access-permissions)，手動設定您的角色指派。 |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Azure 上的互動式瀏覽器驗證問題。身分識別1.2。0

**問題描述：** 當您在 Azure 數位 Twins 應用程式中使用 **[Azure 身分識別](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)程式庫** 的版本 **1.2.0** 來撰寫驗證碼時，您可能會遇到 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)方法的問題。 在瀏覽器視窗中嘗試進行驗證時，這會顯示為 "AuthenticationFailedException" 的錯誤回應。 瀏覽器視窗可能無法完全啟動，也可能會顯示為成功驗證使用者，而用戶端應用程式仍失敗，並出現錯誤。

| 這對我有何影響？ | 原因 | 解決方案 |
| --- | --- | --- |
| &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 下列文章中使用受影響的方法：<br><br>[*教學課程：撰寫用戶端應用程式的程式碼*](tutorial-code.md)<br><br>[*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)<br><br>[*How to：使用 Azure 數位 Twins Api 和 Sdk*](how-to-use-apis-sdks.md) | 有些使用者在程式庫的版本 **1.2.0** 中發生此問題 `Azure.Identity` 。 | 若要解決此問題，請更新您的應用程式以使用較 [新版本](https://www.nuget.org/packages/Azure.Identity) 的 `Azure.Identity` 。 更新程式庫版本之後，瀏覽器應該會如預期般載入和驗證。 |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Azure 上的預設 Azure 認證驗證問題。身分識別1.3。0

**問題描述：** 當您在 Azure 數位 Twins 應用程式中使用 **[Azure 身分識別](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)程式庫** 的版本 **1.3.0** 來撰寫驗證碼時，您可能會遇到這些檔中許多範例所使用的 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true)方法問題。當程式碼嘗試進行驗證時，這會顯示為「Azure AuthenticationFailedException： SharedTokenCacheCredential authentication 失敗」的錯誤回應。

| 這對我有何影響？ | 原因 | 解決方案 |
| --- | --- | --- |
| DefaultAzureCredential 用於大部分包含驗證的檔範例中。 如果您使用 DefaultAzureCredential 撰寫驗證程式代碼，並使用程式庫的版本 1.3.0 `Azure.Identity` ，這可能會影響您。 | 使用 DefaultAzureCredential 搭配程式庫的版本 **1.3.0** 時，會出現此問題 `Azure.Identity` 。 | 若要解決此問題，請將您的應用程式切換為使用的 [版本 1.2.2](https://www.nuget.org/packages/Azure.Identity/1.2.2) `Azure.Identity` 。 變更程式庫版本之後，驗證應該會如預期般成功。 |

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)