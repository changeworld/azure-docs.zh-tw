---
title: 從封裝執行您的 Azure Functions
description: 讓 Azure Functions 執行階段藉由掛接部署套件檔案 (內含函式應用程式的專案檔) 來執行函式。
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79365266"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>從套件檔案執行 Azure Functions

在 Azure 中，您可以直接從函式應用程式中的部署套件檔案執行函式。 另一個選項是將您的檔案部署在函式應用程式的 `d:\home\site\wwwroot` 目錄中。

本文說明從套件執行函式的優點。 文中也會說明如何在函式應用程式中啟用這項功能。

> [!IMPORTANT]
> 將函式部署至高階[方案](functions-scale.md#premium-plan)中的 Linux 函式應用程式時，您應該一律從封裝檔案執行，並[使用 Azure Functions Core Tools 發佈應用程式](functions-run-local.md#project-file-deployment)。

## <a name="benefits-of-running-from-a-package-file"></a>從套件檔案執行的優點
  
從套件檔案執行有幾項優點：

+ 減少檔案複製鎖定問題的風險。
+ 可以部署到生產應用程式 (透過重新啟動)。
+ 您可以確定在應用程式中執行的檔案。
+ 提升 [Azure Resource Manager 部署](functions-infrastructure-as-code.md)的效能。
+ 可縮短冷啟動時間，特別是針對具有大型 npm 套件樹狀結構的 JavaScript 函式。

如需詳細資訊，請參閱[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>讓函式能夠從套件執行

若要讓函式應用程式能夠從套件執行，您只需要將 `WEBSITE_RUN_FROM_PACKAGE` 設定新增至函式應用程式的設定。 `WEBSITE_RUN_FROM_PACKAGE` 設定可具有下列其中一個值：

| 值  | 說明  |
|---------|---------|
| **`1`**  | 建議用於 Windows 上執行的函數應用程式。 從函式應用程式中 `d:\home\data\SitePackages` 資料夾內的套件檔案執行。 如果不是[使用 zip deploy 進行部署](#integration-with-zip-deployment)，此選項會要求資料夾也要有名`packagename.txt`為的檔案。 此檔案只會包含資料夾內套件檔案的名稱，且不含任何空白字元。 |
|**`<URL>`**  | 所要執行的特定套件檔案所在的位置。 使用 Blob 儲存體時，請搭配使用私用容器與[共用存取簽章 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 來讓 Functions 執行階段能夠存取套件。 您可以使用 [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)將套件檔案上傳至 Blob 儲存體帳戶。 當您指定 URL 時，您也必須在發行更新的封裝之後[同步處理觸發](functions-deployment-technologies.md#trigger-syncing)程式。 |

> [!CAUTION]
> 在 Windows 上執行函數應用程式時，[外部 URL] 選項會產生較差的冷啟動效能。 將您的函式應用程式部署到 Windows 時`WEBSITE_RUN_FROM_PACKAGE` ， `1`您應該將設為，並使用 zip 部署進行發佈。

下圖顯示的函式應用程式已設定為要從裝載於 Azure Blob 儲存體的 .zip 檔案來執行：

![WEBSITE_RUN_FROM_ZIP 應用程式設定](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前只支援 .zip 套件檔案。

## <a name="integration-with-zip-deployment"></a>與 zip 部署的整合

[zip 部署][Zip deployment for Azure Functions]是 Azure App Service 的一項功能，可讓您將函式應用程式專案部署至 `wwwroot` 目錄。 專案會封裝為 .zip 部署檔案。 您可以使用相同的 API 將套件部署至 `d:\home\data\SitePackages` 資料夾。 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定值若為 `1`，zip 部署 API 就會將套件複製到 `d:\home\data\SitePackages` 資料夾，而不會將檔案解壓縮到 `d:\home\site\wwwroot`。 它也會建立 `packagename.txt` 檔案。 重新開機之後，封裝會掛接至`wwwroot`做為唯讀檔案系統。 如需 zip 部署的詳細資訊，請參閱 [Azure Functions 的 zip 部署](deployment-zip-push.md)。

## <a name="adding-the-website_run_from_package-setting"></a>新增 WEBSITE_RUN_FROM_PACKAGE 設定

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>疑難排解

- [從封裝執行`wwwroot` ] 會變成隻讀，因此當您將檔案寫入此目錄時，將會收到錯誤。
- 不支援 Tar 和 gzip 格式。
- 這項功能不會使用本機快取來撰寫。
- 若要改善冷啟動效能，請使用本機 Zip 選項（`WEBSITE_RUN_FROM_PACKAGE`= 1）。
- 從封裝執行與部署自訂選項（`SCM_DO_BUILD_DURING_DEPLOYMENT=true`）不相容，部署期間將會忽略組建步驟。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
