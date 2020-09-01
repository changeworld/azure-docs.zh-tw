---
title: 設定執行個體和驗證 (已編寫指令碼)
titleSuffix: Azure Digital Twins
description: 瞭解如何藉由執行自動化部署腳本來設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5d41a9b638ab023d045060e14488e91dca07b10f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181369"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>設定 Azure 數位 Twins 實例和驗證 (腳本) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋 **設定新 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將會有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會執行可簡化程式的 [**自動化部署腳本** 範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 來完成這些步驟。 
* 若要查看腳本在幕後執行的手動 CLI 步驟，請參閱這篇文章的 CLI 版本： [*如何：設定 (cli) 的實例和驗證 *](how-to-set-up-instance-cli.md)。
* 若要根據 Azure 入口網站來查看手動步驟，請參閱本文的入口網站版本： [*如何：設定實例和驗證 (入口網站) *](how-to-set-up-instance-portal.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>執行部署指令碼

本文使用 Azure 數位 Twins 程式碼範例，以半自動部署 Azure 數位 Twins 實例和必要的驗證。 它也可以用來作為撰寫您自己的腳本互動的起點。

範例腳本是以 PowerShell 撰寫的。 它是 [Azure 數位 Twins 範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，您可以藉由流覽至該範例連結並選取標題底下的 [ *下載 ZIP* ] 按鈕，將其下載到您的電腦。

在下載的範例資料夾中，部署腳本位於_Azure_Digital_Twins_samples.zip > 腳本 > **deploy.ps1** _。

以下是在 Cloud Shell 中執行部署腳本的步驟。
1. 移至瀏覽器中的 [Azure Cloud Shell](https://shell.azure.com/) 視窗。 使用下列命令登入：
    ```azurecli-interactive
    az login
    ```
    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。 否則，請在 *https://aka.ms/devicelogin* 開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。
 
2. 登入之後，請查看 Cloud Shell 視窗圖示列。 選取 [上傳/下載檔案] 圖示，然後選擇 [上傳]。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="顯示所選上傳選項的 Cloud Shell 視窗":::

    流覽至您電腦上的 _**deploy.ps1**_ 檔案，然後按 [開啟]。 這會將檔案上傳至 Cloud Shell，以便您可以在 [Cloud Shell] 視窗中執行該檔案。

3. `./deploy.ps1`在 Cloud Shell 視窗中傳送命令，以執行腳本。 當腳本透過自動化安裝步驟執行時，系統會要求您傳入下列值：
    * 針對實例：要使用的 Azure 訂用帳戶的訂用帳戶*識別碼*
    * 針對實例：您要部署實例的 *位置* 。 若要查看哪些區域支援 Azure 數位 Twins，請造訪 [*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 針對實例： *資源組* 名。 您可以使用現有的資源群組，或輸入要建立之新的名稱。
    * 針對實例： Azure 數位 Twins 實例的 *名稱* 。 新實例的名稱在訂用帳戶的區域內必須是唯一的 (也就是說，如果您的訂用帳戶在已使用您所選名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱) 。
    * 針對應用程式註冊：要與註冊相關聯的 *Azure AD 應用程式顯示名稱* 。 此應用程式註冊可讓您設定 [Azure 數位 Twins api](how-to-use-apis-sdks.md)的存取權限。 稍後，用戶端應用程式會向應用程式註冊進行驗證，因此會將設定的存取權限授與 Api。
    * 針對應用程式註冊： Azure AD 應用程式的 *Azure AD 應用程式回復 URL* 。 請使用 `http://localhost`。 腳本會為其設定 *公用用戶端/原生 (mobile & desktop) * URI。

此腳本會建立 Azure 數位 Twins 實例、為您的 Azure 使用者指派 *Azure 數位 Twins 擁有者 (預覽版) * 角色，以及設定 Azure AD 應用程式註冊以供用戶端應用程式使用。

以下是來自腳本的輸出記錄摘要：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="顯示透過執行部署腳本的輸入和輸出記錄的 Cloud Shell 視窗" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

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

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="從實例的總覽頁面中反白顯示重要值":::

### <a name="collect-app-registration-values"></a>收集應用程式註冊值 

應用程式註冊有兩個重要值，稍後會需要這些值來 [撰寫 Azure 數位 Twins api 的用戶端應用程式驗證碼](how-to-authenticate-client.md)。 

若要尋找這些專案，請遵循 [此連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ，以流覽至 Azure 入口網站中的 Azure AD 應用程式註冊總覽頁面。 此頁面會顯示您的訂用帳戶中已建立的所有應用程式註冊。

您應該會在此清單中看到您剛才建立的應用程式註冊。 請選取它以開啟其詳細資料：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="入口網站查看應用程式註冊的重要值":::

記下**您**頁面上顯示的*應用程式 (用戶端) 識別碼*和*目錄 (租使用者) 識別碼*。 如果您不是要為用戶端應用程式撰寫程式碼的人員，則必須與將會與之人員共用這些值。

## <a name="verify-success"></a>確認是否成功

如果您想要確認由腳本所設定的資源和許可權的建立，您可以在 [Azure 入口網站](https://portal.azure.com)中查看這些資源和許可權。

### <a name="verify-instance"></a>驗證實例

若要確認您的實例已建立，請移至 Azure 入口網站中的 [Azure 數位 Twins 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 。 此頁面會列出您所有的 Azure 數位 Twins 實例。 在清單中尋找新建立之實例的名稱。

### <a name="verify-user-role-assignment"></a>確認使用者角色指派

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 回想一下，腳本目前將這個必要的角色指派給從 Cloud Shell 執行腳本的相同使用者。 如果您需要將此角色指派給即將管理實例的其他人，您現在可以透過 Azure 入口網站 ([指示](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([指示](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 。
>
> 如果腳本設定有任何問題，您也可以使用入口網站或 CLI 來重做您自己的角色指派。

### <a name="verify-app-registration"></a>確認應用程式註冊

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，請確認已在註冊時正確設定 Azure 數位 Twins 許可權設定。 若要這樣做，請從功能表列選取 [ *資訊清單* ]，以查看應用程式註冊的資訊清單程式碼。 滾動至程式碼視窗底部，然後在底下尋找這些欄位 `requiredResourceAccess` 。 這些值應該符合以下螢幕擷取畫面中的值：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>您組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

瞭解如何藉由撰寫用戶端應用程式的驗證碼，將用戶端應用程式連線至您的實例：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)
