---
title: 設定實例和驗證（已編寫腳本）
titleSuffix: Azure Digital Twins
description: 請參閱如何設定 Azure 數位 Twins 服務的實例，包括適當的驗證。 已編寫版本的腳本。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c601d01589a89667b630d04ddb19dfbe190cca9f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125768"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>設定 Azure 數位 Twins 實例和驗證（已編寫腳本）

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋**設定新的 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將擁有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會藉由執行可簡化程式的[**自動化部署腳本**範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)來完成這些步驟。 若要查看腳本在幕後執行的手動步驟，請參閱這篇文章的手動版本：[*如何：設定實例和驗證（手動）*](how-to-set-up-instance-manual.md)。

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="run-the-deployment-script"></a>執行部署指令碼

本文使用 Azure 數位 Twins 程式碼範例，以半自動部署 Azure 數位 Twins 實例和必要的驗證。 它也可用來做為撰寫您自己的腳本互動的起點。

範例腳本是以 PowerShell 撰寫。 這是[Azure 數位 Twins 範例](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，您可以流覽至該範例連結，然後選取標題底下的 [*下載 ZIP* ] 按鈕，將其下載到您的電腦。

在下載的範例資料夾中，部署腳本位於_Azure_Digital_Twins_samples.zip > 腳本 > **deploy.ps1** _。

以下是在 Cloud Shell 中執行部署腳本的步驟。
1. 移至瀏覽器中的[Azure Cloud Shell](https://shell.azure.com/)視窗。 使用此命令登入：
    ```azurecli-interactive
    az login
    ```
    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。 否則，請在 *https://aka.ms/devicelogin* 開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。
 
2. 登入之後，請查看 Cloud Shell 視窗圖示列。 選取 [上傳/下載檔案] 圖示，然後選擇 [上傳]。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="顯示上傳選項選取範圍的 Cloud Shell 視窗":::

    流覽至您電腦上的_**deploy.ps1**_ 檔案，然後按 [開啟]。 這會將檔案上傳至 Cloud Shell，讓您可以在 [Cloud Shell] 視窗中執行該檔案。

3. 藉由 `./deploy.ps1` 在 [Cloud Shell] 視窗中傳送命令來執行腳本。 當腳本透過自動化設定步驟執行時，系統會要求您傳遞下列值：
    * 針對實例：要使用之 Azure 訂用帳戶的訂用帳戶*識別碼*
    * 針對實例：您想要部署實例的*位置*。 若要查看哪些區域支援 Azure 數位 Twins，請造訪[*依區域提供的 azure 產品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 實例的：*資源組*名。 您可以使用現有的資源群組，或輸入一個新的名稱來建立。
    * 針對實例：您的 Azure 數位 Twins 實例的*名稱*。 新實例的名稱在區域內必須是唯一的（也就是說，如果該區域中的另一個 Azure 數位 Twins 實例已經使用您選擇的名稱，系統會要求您挑選不同的名稱）。
    * 應用程式註冊：要與註冊建立關聯的*Azure AD 應用程式顯示名稱*。 此應用程式註冊可讓您設定[Azure 數位 Twins api](how-to-use-apis-sdks.md)的存取權限。 之後，用戶端應用程式會對應用程式註冊進行驗證，因此會授與對 Api 所設定的存取權限。
    * 針對 [應用程式註冊]： Azure AD 應用程式的*Azure AD 應用程式回復 URL* 。 您可以使用 `http://localhost` 。

此腳本會建立 Azure 數位 Twins 實例、將實例上的*Azure 數位 Twins 擁有者（預覽）* 角色指派給您的 azure 使用者，並設定 Azure AD 應用程式註冊，供您的用戶端應用程式使用。

以下是腳本的輸出記錄摘錄：

:::image type="content" source="media/how-to-set-up-instance/deployment-script-output.png" alt-text="Cloud Shell 視窗，顯示透過執行部署腳本的輸入和輸出記錄" lightbox="media/how-to-set-up-instance/deployment-script-output.png":::

如果腳本順利完成，最後的列印結果會顯示 `Deployment completed successfully` 。 否則，請解決錯誤訊息，然後重新執行腳本。 它會略過您已完成的步驟，並在您離開的位置再次開始要求輸入。

腳本完成時，您現在已有 Azure 數位 Twins 實例準備就緒，並已設定許可權來進行管理。

## <a name="collect-important-values"></a>收集重要值

應用程式註冊中有兩個重要的值，稍後會在[針對 Azure 數位 Twins api 驗證用戶端應用程式](how-to-authenticate-client.md)時需要用到。 

若要尋找它們，請遵循[此連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)以流覽至 Azure 入口網站中的 Azure AD 應用程式註冊總覽頁面。 此頁面會顯示您的訂用帳戶中已建立的所有應用程式註冊。

您應該會在此清單中看到您剛才建立的應用程式註冊。 選取它以開啟其詳細資料：

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="應用程式註冊重要值的入口網站視圖":::

記**下頁面上**顯示的 [*應用程式（用戶端）識別碼*] 和 [*目錄（租使用者）] 識別碼*。 如果您不是要為用戶端應用程式撰寫程式碼的人員，您將需要與即將擁有的人員共用這些值。

## <a name="verify-success"></a>確認是否成功

如果您想要確認由腳本所設定的資源和許可權的建立，您可以在[Azure 入口網站](https://portal.azure.com)中查看這些專案。

### <a name="verify-instance"></a>驗證實例

若要確認您的實例已建立，請移至 Azure 入口網站中的 [ [Azure 數位 Twins] 頁面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)。 此頁面會列出您所有的 Azure 數位 Twins 實例。 在清單中尋找新建立之實例的名稱。

### <a name="verify-user-role-assignment"></a>驗證使用者角色指派

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

### <a name="verify-app-registration"></a>驗證應用程式註冊

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，請確認已正確設定註冊上的 Azure 數位 Twins 許可權設定。 若要這麼做，請從功能表列選取 [*資訊清單*]，以查看應用程式註冊的資訊清單代碼。 在程式碼視窗的底部，尋找中的這些欄位 `requiredResourceAccess` 。 值應符合下列螢幕擷取畫面中的值：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>貴組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

請參閱如何撰寫用戶端應用程式的驗證碼，將您的用戶端應用程式連接到您的實例：
* [*如何：撰寫應用程式驗證碼*](how-to-authenticate-client.md)
