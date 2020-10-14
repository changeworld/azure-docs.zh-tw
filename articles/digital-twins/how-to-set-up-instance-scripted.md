---
title: 設定執行個體和驗證 (已編寫指令碼)
titleSuffix: Azure Digital Twins
description: 瞭解如何藉由執行自動化部署腳本來設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4e8e26f73aef155e4194ca7a4178827b6a7366fb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047415"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>設定 Azure 數位 Twins 實例和驗證 (腳本) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋 **設定新 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將會有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會執行可簡化程式的 [**自動化部署腳本** 範例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 來完成這些步驟。 
* 若要查看腳本在幕後執行的手動 CLI 步驟，請參閱這篇文章的 CLI 版本： [*如何：設定 (cli) 的實例和驗證 *](how-to-set-up-instance-cli.md)。
* 若要根據 Azure 入口網站來查看手動步驟，請參閱本文的入口網站版本： [*如何：設定實例和驗證 (入口網站) *](how-to-set-up-instance-portal.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="prerequisites-download-the-script"></a>必要條件：下載腳本

範例腳本是以 PowerShell 撰寫的。 它是 [**Azure 數位 Twins 範例**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，您可以藉由流覽至該範例連結並選取標題底下的 [ *下載 ZIP* ] 按鈕，將其下載到您的電腦。

這會將範例專案下載到您的電腦，做為 _**Azure_Digital_Twins_samples.zip**_。 流覽至您電腦上的資料夾，並將其解壓縮以解壓縮檔案。

在解壓縮的資料夾中，部署腳本位於_Azure_Digital_Twins_samples > 腳本 > **deploy.ps1** _。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>執行部署指令碼

本文使用 Azure 數位 Twins 程式碼範例，以半自動部署 Azure 數位 Twins 實例和必要的驗證。 它也可以用來作為撰寫您自己的腳本互動的起點。

以下是在 Cloud Shell 中執行部署腳本的步驟。
1. 移至瀏覽器中的 [Azure Cloud Shell](https://shell.azure.com/) 視窗。 使用下列命令登入：
    ```azurecli
    az login
    ```
    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。 否則，請在 *https://aka.ms/devicelogin* 開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。
 
2. 在 Cloud Shell 圖示列中，確認您的 Cloud Shell 設定為執行 PowerShell 版本。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗":::

1. 選取 [上傳/下載檔案] 圖示，然後選擇 [上傳]。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗":::

    流覽至您電腦上的_**deploy.ps1**_ 檔案 (_Azure_Digital_Twins_samples > 腳本 > **deploy.ps1** _) 並點擊 [開啟]。 這會將檔案上傳至 Cloud Shell，以便您可以在 [Cloud Shell] 視窗中執行該檔案。

4. `./deploy.ps1`在 Cloud Shell 視窗中傳送命令，以執行腳本。  (回想一下，若要貼到 Cloud Shell，您可以在 Windows 和 Linux 上使用 **Ctrl + shift + v** ，或在 macOS 上使用 **Cmd + shift + v** 。 您也可以使用滑鼠右鍵功能表。 ) 

    ```azurecli
    ./deploy.ps1
    ```

    當腳本透過自動化安裝步驟執行時，系統會要求您傳入下列值：
    * 針對實例：要使用的 Azure 訂用帳戶的訂用帳戶*識別碼*
    * 針對實例：您要部署實例的 *位置* 。 若要查看哪些區域支援 Azure 數位 Twins，請造訪 [*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 針對實例： *資源組* 名。 您可以使用現有的資源群組，或輸入要建立之新的名稱。
    * 針對實例： Azure 數位 Twins 實例的 *名稱* 。 新實例的名稱在訂用帳戶的區域內必須是唯一的 (也就是說，如果您的訂用帳戶在已使用您所選名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱) 。
    * 針對應用程式註冊：要與註冊相關聯的 *Azure AD 應用程式顯示名稱* 。 此應用程式註冊可讓您設定 [Azure 數位 Twins api](how-to-use-apis-sdks.md)的存取權限。 稍後，用戶端應用程式會向應用程式註冊進行驗證，因此會將設定的存取權限授與 Api。
    * 針對應用程式註冊： Azure AD 應用程式的 *Azure AD 應用程式回復 URL* 。 請使用 `http://localhost`。 腳本會為其設定 *公用用戶端/原生 (mobile & desktop) * URI。

此腳本會建立 Azure 數位 Twins 實例、為您的 Azure 使用者指派 *Azure 數位 Twins 擁有者 (預覽版) * 角色，以及設定 Azure AD 應用程式註冊以供用戶端應用程式使用。

>[!NOTE]
>目前已有腳本設定的 **已知問題** ，其中某些使用者 (特別是個人 [Microsoft 帳戶的使用者 (msa) ](https://account.microsoft.com/account)) 可能會發現 **未建立 _Azure 數位 Twins 擁有者 (Preview) _ 的角色指派**。
>
>您可以使用本文稍後的 [ [*驗證使用者角色指派*](#verify-user-role-assignment) ] 區段來驗證角色指派，並視需要使用 [Azure 入口網站](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)手動設定角色指派。
>
>如需此問題的詳細資訊，請參閱 [*疑難排解： Azure 數位 Twins 中的已知問題*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)。

以下是來自腳本的輸出記錄摘要：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

如果腳本順利完成，最後的列印結果將會顯示 `Deployment completed successfully` 。 否則，請解決錯誤訊息，然後重新執行腳本。 它會略過您已完成的步驟，並在您離開的那一點開始再次要求輸入。

腳本完成時，您現在已準備好可使用的 Azure 數位 Twins 實例來管理它，並已設定用戶端應用程式存取的許可權。

> [!NOTE]
> 腳本目前會將 Azure 數位 Twins 中的必要管理角色指派 (*Azure 數位 Twins 擁有者 (Preview) *) 與從 Cloud Shell 執行腳本的相同使用者。 如果您需要將此角色指派給即將管理實例的其他人，您現在可以透過 Azure 入口網站 ([指示](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([指示](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 。

## <a name="collect-important-values"></a>收集重要值

當您繼續使用 Azure 數位 Twins 實例時，您可能需要的腳本所設定的資源有數個重要的值。 在本節中，您會使用 [Azure 入口網站](https://portal.azure.com) 來收集這些值。 您應該將它們儲存在安全的位置，或返回此區段，以便稍後在需要時尋找它們。

如果其他使用者將針對該實例進行程式設計，您也應該與它們共用這些值。

### <a name="collect-instance-values"></a>收集實例值

在 [Azure 入口網站](https://portal.azure.com)中，在入口網站的搜尋列中搜尋您的實例名稱，以尋找您的 Azure 數位 Twins 實例。

選取它將會開啟實例的 *[總覽* ] 頁面。 請記下其 *名稱*、 *資源群組*和 *主機名稱*。 您稍後可能會需要這些以識別並連接到您的實例。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗":::

### <a name="collect-app-registration-values"></a>收集應用程式註冊值 

應用程式註冊有兩個重要值，稍後會需要這些值來 [撰寫 Azure 數位 Twins api 的用戶端應用程式驗證碼](how-to-authenticate-client.md)。 

若要尋找這些專案，請遵循 [此連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ，以流覽至 Azure 入口網站中的 Azure AD 應用程式註冊總覽頁面。 此頁面會顯示您的訂用帳戶中已建立的所有應用程式註冊。

您應該會在此清單中看到您剛才建立的應用程式註冊。 請選取它以開啟其詳細資料：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="顯示所選 PowerShell 版本的 Cloud Shell 視窗":::

記下**您**頁面上顯示的*應用程式 (用戶端) 識別碼*和*目錄 (租使用者) 識別碼*。 如果您不是要為用戶端應用程式撰寫程式碼的人員，則必須與將會與之人員共用這些值。

## <a name="verify-success"></a>確認是否成功

如果您想要確認由腳本所設定的資源和許可權的建立，您可以在 [Azure 入口網站](https://portal.azure.com)中查看這些資源和許可權。

如果您無法驗證任何步驟是否成功，請重試此步驟。 您可以使用 [Azure 入口網站](how-to-set-up-instance-portal.md) 或 [CLI](how-to-set-up-instance-cli.md) 指示，個別執行這些步驟。

### <a name="verify-instance"></a>驗證實例

若要確認您的實例已建立，請移至 Azure 入口網站中的 [Azure 數位 Twins 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 。 您可以藉由在入口網站的搜尋列中搜尋 *Azure 數位 Twins* 來自行前往此頁面。

此頁面會列出您所有的 Azure 數位 Twins 實例。 在清單中尋找新建立之實例的名稱。

如果驗證失敗，您可以使用 [入口網站](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) 或 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)重試建立實例。

### <a name="verify-user-role-assignment"></a>確認使用者角色指派

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 回想一下，腳本目前將這個必要的角色指派給從 Cloud Shell 執行腳本的相同使用者。 如果您需要將此角色指派給即將管理實例的其他人，您現在可以透過 Azure 入口網站 ([指示](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([指示](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 。

如果驗證失敗，您也可以使用 [入口網站](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)來重做自己的角色指派。

### <a name="verify-app-registration"></a>確認應用程式註冊

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

接下來，確認已在註冊時正確設定 Azure 數位 Twins 許可權設定。 若要這樣做，請從功能表列選取 [ *資訊清單* ]，以查看應用程式註冊的資訊清單程式碼。 滾動至程式碼視窗底部，然後在底下尋找這些欄位 `requiredResourceAccess` 。 這些值應該符合以下螢幕擷取畫面中的值：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

如果其中一或兩個驗證步驟都不成功，請使用 [入口網站](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) 或 [CLI](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) 指示重試建立應用程式註冊。

## <a name="other-possible-steps-for-your-organization"></a>您組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

使用 Azure 數位 Twins CLI 命令，在您的實例上測試個別 REST API 呼叫： 
* [az dt 參考](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [操作說明：*使用 Azure Digital Twins CLI*](how-to-use-cli.md)

或者，請參閱如何藉由撰寫用戶端應用程式的驗證碼，將用戶端應用程式連接至您的實例：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)