---
title: 已知問題-Azure 數位 Twins
description: 取得協助，以協助辨識和緩和 Azure 數位 Twins 的已知問題。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 549e1808a3b449f7d29b968cde76ef29391880b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100606"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 數位 Twins 的已知問題

本文提供與 Azure 數位 Twins 相關之已知問題的相關資訊。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell 中的「400用戶端錯誤：不正確的要求」

在中執行 Cloud Shell 的命令 *https://shell.azure.com* 可能會間歇性失敗，並出現錯誤「400用戶端錯誤： url 的要求不正確：」 http://localhost:50342/oauth2/token ，後面接著完整堆疊追蹤。

尤其是針對 Azure 數位 Twins，這會影響下列命令群組：
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>疑難排解步驟

若要解決此問題，您可以 `az login` 在 Cloud Shell 中重新執行命令，並完成後續的登入步驟。 在此之後，您應該可以重新執行此命令。

或者，您可以在 Azure 入口網站中開啟 [Cloud Shell] 窗格，並從該處完成 Cloud Shell 工作：

:::image type="content" source="media/includes/portal-cloud-shell.png" alt-text="已醒目提示 [Cloud Shell] 圖示的 Azure 入口網站檢視，而且 Cloud Shell 顯示在入口網站視窗底部" lightbox="media/includes/portal-cloud-shell.png":::

最後，另一個解決方法是在您的電腦上 [安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ，讓您可以在本機執行 Azure CLI 命令。 本機 CLI 不會遇到此問題。

### <a name="possible-causes"></a>可能的原因

這是 Cloud Shell 中已知問題的結果： [*從 Cloud Shell 取得權杖間歇性失敗，並出現400用戶端錯誤：不正確的要求*](https://github.com/Azure/azure-cli/issues/11749)。

這會顯示 Azure 數位 Twins 實例驗證權杖的問題，以及 Cloud Shell 的預設 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 型驗證。 執行的疑難排解步驟 `az login` 會將您切換出受控識別驗證，因此不會逐步執行此問題。

這不會影響或命令群組中的 Azure 數位 Twins 命令 `az dt` `az dt endpoint` ，因為它們會使用不同類型的驗證權杖 (ARM 型) ，而這對 Cloud Shell 的受控識別驗證沒有任何問題。

## <a name="missing-role-assignment-after-scripted-setup"></a>腳本安裝之後遺失角色指派

某些使用者可能會遇到 [*如何：設定實例和驗證 (腳本)*](how-to-set-up-instance-scripted.md)的角色指派部分問題。 腳本不會指出失敗，但 *Azure 數位 Twins 資料擁有* 者角色未成功指派給使用者，而此問題將會影響在途中建立其他資源的能力。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

若要判斷在執行腳本之後是否已成功設定您的角色指派，請依照安裝程式文章的 [*驗證使用者角色指派*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 一節中的指示進行。 如果您的使用者未以此角色顯示，此問題會影響您。

### <a name="troubleshooting-steps"></a>疑難排解步驟

若要解決此問題，您可以使用 CLI 或 Azure 入口網站手動設定您的角色指派。 

遵循下列指示：
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>可能的原因

針對使用個人 [Microsoft 帳戶 (MSA) ](https://account.microsoft.com/account)登入的使用者，您的使用者在這類命令中識別您的使用者主體識別碼可能與使用者的登入電子郵件不同，因此腳本難以探索及使用以正確指派角色。

## <a name="issue-with-interactive-browser-authentication"></a>互動式瀏覽器驗證的問題

當您在 Azure 數位 Twins 應用程式中使用 **[Azure 身分識別](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)程式庫** 的版本 **1.2.0** 來撰寫驗證碼時，您可能會遇到 [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)方法的問題。

這不是最新版本的程式庫。 最新版本是 **1.2.2** 。

下列文章中使用受影響的方法： 
* [*教學課程：撰寫用戶端應用程式的程式碼*](tutorial-code.md)
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)
* [*How to：使用 Azure 數位 Twins Api 和 Sdk*](how-to-use-apis-sdks.md)

當您嘗試在瀏覽器視窗中進行驗證時，此問題包括錯誤回應 "AuthenticationFailedException"。 瀏覽器視窗可能無法完全啟動，也可能會顯示為成功驗證使用者，而用戶端應用程式仍失敗，並出現錯誤。

### <a name="troubleshooting-steps"></a>疑難排解步驟

若要解決此問題，請更新您的應用程式以使用 `Azure.Identity` 版本 **1.2.2** 。 使用此版本的程式庫時，瀏覽器應該會如預期般載入和驗證。

### <a name="possible-causes"></a>可能的原因

這與程式庫最新版本的開啟問題有關 `Azure.Identity` (版本 **1.2.0** ) ： [*當使用 InteractiveBrowserCredential 時，無法進行驗證*](https://github.com/Azure/azure-sdk-for-net/issues/13940)。

如果您在 Azure 數位 Twins 應用程式中使用版本 **1.2.0** ，或將程式庫新增至您的專案，但未指定 (的版本，這也會預設為此最新版本) ，您將會看到此問題。

## <a name="next-steps"></a>下一步

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)