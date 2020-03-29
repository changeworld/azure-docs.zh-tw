---
title: 登錄到 Azure 筆記本預覽
description: 使用 Microsoft 帳戶或公司/學校帳戶，設定您的 Azure Notebook 使用者帳戶。
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646291"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Azure 筆記本預覽的使用者帳戶

不論您是否使用使用者帳戶登入，都可以使用 Azure Notebooks：

- 不登入時，您可以建立和執行 Notebooks，但無法將 Notebooks 或資料檔案保留在專案中。 例如，使用者收到可至 Azure Notebook 的連結，即可享用 Notebook 而不需要登入。
- 登入時，Azure Notebooks 會將所有專案保留在您的帳戶。 登入的使用者也會有使用者識別碼，使他們能與其他人共用其專案和 Notebooks。
- 當用於 Azure Notebooks 的帳戶也與 Azure 訂用帳戶相關聯時，可獲得其他權益，例如在更強大的伺服器上執行 Notebooks、建立私人 Notebooks，以及將 Notebooks 的權限授與個別使用者。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

需要 Microsoft 帳戶或「公司或學校」帳戶才能登入 Azure Notebooks。 當您選取 Notebooks 頁面右上方的 [登入]**** 命令時，會提示您輸入您的帳戶：

![Azure Notebooks 的登入命令](media/accounts/sign-in-command.png)

您在 Azure Notebooks 中執行的所有工作都與您用來登入的帳戶相關聯。 您[使用者設定檔](azure-notebooks-user-profile.md)中的每個帳戶也必須具有唯一的使用者識別碼。 如此一來，若您需要維護多組不同的專案和個別的身分識別集合，就可以使用不同的帳戶登入 Azure Notebooks。 例如，資料科學小組的每個成員可能會同時有各自的帳戶，以及用來向公司外部的人員展現工作成果的共用群組帳戶。 同樣地，講師可能會有維護其教學角色的帳戶，以及用於外部諮詢或開放原始碼工作的不同帳戶。

## <a name="microsoft-accounts"></a>Microsoft 帳戶

Microsoft 帳戶可用來登入數目不拘的 Microsoft 產品與服務，例如 Windows、Azure、outlook.com、OneDrive 及 XBox Live。 如果您可使用上述任何服務，很可能表示您已經有可以搭配 Azure Notebooks 使用的 Microsoft 帳戶。

如果您不確定，請在帳戶提示字元中，選取 [建立一個帳戶]**** 命令。 您可以使用任一提供者的任何電子郵件地址，建立新的 Microsoft 帳戶。

![建立新 Microsoft 帳戶的命令](media/accounts/create-new-microsoft-account.png)

> [!Note]
> 如果您嘗試使用已有帳戶與其關聯的電子郵件地址建立新的帳戶，您可能會看到「您無法使用公司或學校電子郵件地址註冊。 請使用例如 Gmail 或 Yahoo! 等個人電子郵件，或取得新的 Outlook 電子郵件」的訊息。 在此情況下，請嘗試使用工作電子郵件地址登入，不要建立新的帳戶。

預設會封鎖子女帳戶存取 Azure Notebooks。 以子女帳戶登入就會顯示錯誤，如下所示：

![嘗試使用子女帳戶登入時發生錯誤：發生錯誤，您的家長禁止您存取](media/accounts/child-account-error.png)

若要啟用存取，家長必須執行下列步驟：

1. 請造訪 `https://account.live.com/mk` 並使用家長帳戶登入。
1. 在有問題的子女區段中，選取 [管理子女對第三方應用程式的存取權]****。
1. 在下一個頁面上，選取 [啟用存取]****。
1. 下次使用該子女帳戶登入 Azure Notebooks 時，請在顯示的權限提示中選取 [是]****。

> [!Warning]
> 啟用 Azure Notebooks 的第三方應用程式存取權時，也會啟用其他所有協力廠商應用程式的存取權。 建議家長在啟用存取權時慎加考慮，並更密切監視其子女的活動。

## <a name="work-or-school-accounts"></a>工作或學校帳戶

由組織的系統管理員建立的公司或學校帳戶，可讓組織成員存取 Microsoft 雲端服務，例如 Office 365，以及作為在已加入網域的電腦上登入 Windows 的帳戶。 公司或學校帳戶通常會使用組織的電子郵件地址，例如 any-user@contoso.com。

Azure Notebooks 會收集或使用 (但不會揭露) 如帳戶的電子郵件地址與使用者的瀏覽器資訊等資訊，因此使用公司或學校帳戶登入 Azure Notebooks 可能需要系統管理員的同意。 (使用瀏覽器資料，根據最熱門的使用方式將功能最佳化。)

如果限制使用者不能自行同意，組織帳戶的系統管理員必須代表使用者表示同意。 在此情況下，使用者會看到「您無法存取此應用程式」的訊息：

![使用公司或學校帳戶時的「您無法存取此應用程式」訊息](media/accounts/consent-permissions-denied.png)

若要以系統管理員身分表示同意，請使用[系統管理員同意頁面](https://notebooks.azure.com/account/adminConsent)，引導您完成此程序。

## <a name="next-steps"></a>後續步驟  

> [!div class="nextstepaction"]
> [編輯您的設定檔與使用者識別碼](azure-notebooks-user-profile.md)
