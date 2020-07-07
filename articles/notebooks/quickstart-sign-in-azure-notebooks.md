---
title: 登入 Azure Notebooks 預覽
description: 快速登入 Azure Notebooks 預覽並設定使用者識別碼，如此可讓您存取己儲存的專案並與他人共用 Notebook。
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 8a2d9836cd005ab11106c569929a6776d605da50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85830412"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>快速入門：登入 Azure Notebooks 預覽並設定使用者識別碼

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

雖然您不用登入就可以檢視 Azure Notebooks，但是您必須登入以執行 Notebook、存取已儲存的專案和 Notebook，以及與其他人共用您的 Notebook。

## <a name="sign-in"></a>登入

1. 選取 [notebooks.azure.com](https://notebooks.azure.com/) 右上方的 [登入]  。

    ![Azure Notebooks 登入命令的位置](media/accounts/sign-in-command.png)

1. 出現提示時，請輸入 Microsoft 帳戶或工作或學校帳戶的電子郵件地址，然後選取 [下一步]  。 帳戶類型會在[您的 Azure Notebooks 使用者帳戶](azure-notebooks-user-account.md)上說明。 如果您沒有 Microsoft 帳戶，或想要申請一個專門用於 Azure Notebooks 的帳戶，請選取 [建立帳戶]  ：

    ![在登入提示中建立新的 Microsoft 帳戶命令](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > 如果您嘗試使用已有帳戶與其關聯的電子郵件地址建立新的帳戶，您可能會看到「您無法使用公司或學校電子郵件地址註冊。 請使用例如 Gmail 或 Yahoo! 等個人電子郵件，或取得新的 Outlook 電子郵件」的訊息。 在此情況下，請嘗試使用工作電子郵件地址登入，不要建立新的帳戶。

1. 在系統提示時輸入您的密碼。

1. 如果您是第一次登入，Azure Notebooks 會要求存取您帳戶的權限。 選取 [是]  以繼續進行：

    ![帳戶權限提示](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>設定使用者識別碼

1. 在第一次登入時，您會獲得指派暫時使用者識別碼，例如 "anon-idrca3"。 只要您的使用者識別碼開頭是 "anon-"，Azure Notebooks 就會提示您建立自己的識別碼。 您的使用者識別碼會在您取得以共用專案和 Notebook 的任何 URL 中使用，因此請選擇唯一且有意義的識別碼。

    ![提示輸入 Azure Notebooks 的使用者識別碼](media/accounts/create-user-id.png)

    如果您選取 [不用了，謝謝]  ，Azure Notebooks 會持續在您每一次登入時提示您輸入使用者識別碼。 您也可以隨時在您的[使用者設定檔](azure-notebooks-user-profile.md)中設定使用者識別碼。

1. 成功登入之後，Azure Notebooks 會將您導覽至公用設定檔頁面，您可以在其中選取 [編輯設定檔資訊]  以填入其餘資訊 (如需詳細資訊，請參閱[您的設定檔和使用者識別碼](azure-notebooks-user-profile.md))：

    ![Azure Notebooks 設定檔頁面的初始檢視](media/accounts/profile-page-new.png)

> [!NOTE]
> 如果您看到「使用者識別碼已有人使用」的訊息，請嘗試其他識別碼。 使用者識別碼在所有 Azure Notebooks 帳戶中都是獨一無二的，而 Azure Notebooks 也會保留特定的使用者識別碼，例如 Microsoft 品牌名稱。

## <a name="sign-out"></a>登出

若要登出，請在頁面右上方選取您的使用者名稱，然後選取 [登出]  ：

![Azure Notebooks 登出命令的位置](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：建立及共用 Notebook](quickstart-create-share-jupyter-notebook.md)
