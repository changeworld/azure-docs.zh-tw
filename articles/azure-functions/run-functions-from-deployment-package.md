---
title: 從包運行 Azure 函數
description: 讓 Azure Functions 執行階段藉由掛接部署套件檔案 (內含函式應用程式的專案檔) 來執行函式。
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365266"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>從套件檔案執行 Azure Functions

在 Azure 中，您可以直接從函式應用程式中的部署套件檔案執行函式。 另一個選項是將您的檔案部署在函式應用程式的 `d:\home\site\wwwroot` 目錄中。

本文說明從套件執行函式的優點。 文中也會說明如何在函式應用程式中啟用這項功能。

> [!IMPORTANT]
> 在[高級計畫中](functions-scale.md#premium-plan)將函數部署到 Linux 函數應用時，應始終從包檔運行並使用[Azure 函數核心工具發佈應用](functions-run-local.md#project-file-deployment)。

## <a name="benefits-of-running-from-a-package-file"></a>從套件檔案執行的優點
  
從套件檔案執行有幾項優點：

+ 減少檔案複製鎖定問題的風險。
+ 可以部署到生產應用程式 (透過重新啟動)。
+ 您可以確定在應用程式中執行的檔案。
+ 提升 [Azure Resource Manager 部署](functions-infrastructure-as-code.md)的效能。
+ 可縮短冷啟動時間，特別是針對具有大型 npm 套件樹狀結構的 JavaScript 函式。

有關詳細資訊，請參閱[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>讓函式能夠從套件執行

若要讓函式應用程式能夠從套件執行，您只需要將 `WEBSITE_RUN_FROM_PACKAGE` 設定新增至函式應用程式的設定。 `WEBSITE_RUN_FROM_PACKAGE` 設定可具有下列其中一個值：

| 值  | 描述  |
|---------|---------|
| **`1`**  | 建議用於在 Windows 上運行的函數應用。 從函式應用程式中 `d:\home\data\SitePackages` 資料夾內的套件檔案執行。 如果不[部署 zip 部署](#integration-with-zip-deployment)，此選項要求資料夾還必須具有名為 的檔。 `packagename.txt` 此檔案只會包含資料夾內套件檔案的名稱，且不含任何空白字元。 |
|**`<URL>`**  | 所要執行的特定套件檔案所在的位置。 使用 Blob 儲存體時，請搭配使用私用容器與[共用存取簽章 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 來讓 Functions 執行階段能夠存取套件。 您可以使用 [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)將套件檔案上傳至 Blob 儲存體帳戶。 指定 URL 時，還必須在發佈更新的包後[同步觸發器](functions-deployment-technologies.md#trigger-syncing)。 |

> [!CAUTION]
> 在 Windows 上運行函數應用時，外部 URL 選項會產生較差的冷啟動性能。 將函數應用部署到 Windows 時，應設置為`WEBSITE_RUN_FROM_PACKAGE`，`1`併發布 zip 部署。

下圖顯示的函式應用程式已設定為要從裝載於 Azure Blob 儲存體的 .zip 檔案來執行：

![WEBSITE_RUN_FROM_ZIP 應用程式設定](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前只支援 .zip 套件檔案。

## <a name="integration-with-zip-deployment"></a>與 zip 部署的整合

[zip 部署][Zip deployment for Azure Functions]是 Azure App Service 的一項功能，可讓您將函式應用程式專案部署至 `wwwroot` 目錄。 專案會封裝為 .zip 部署檔案。 您可以使用相同的 API 將套件部署至 `d:\home\data\SitePackages` 資料夾。 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定值若為 `1`，zip 部署 API 就會將套件複製到 `d:\home\data\SitePackages` 資料夾，而不會將檔案解壓縮到 `d:\home\site\wwwroot`。 它也會建立 `packagename.txt` 檔案。 重新開機後，包將作為唯讀檔案系統裝載`wwwroot`到。 如需 zip 部署的詳細資訊，請參閱 [Azure Functions 的 zip 部署](deployment-zip-push.md)。

## <a name="adding-the-website_run_from_package-setting"></a>新增 WEBSITE_RUN_FROM_PACKAGE 設定

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>疑難排解

- 運行從包使`wwwroot`唯讀，所以你會收到一個錯誤，當寫入此目錄的檔。
- 不支援焦油和 gzip 格式。
- 此功能不與本機快取組成。
- 為提高冷啟動性能，請使用本地 Zip 選項 （#1）。`WEBSITE_RUN_FROM_PACKAGE`
- 運行從包與部署自訂選項 （`SCM_DO_BUILD_DURING_DEPLOYMENT=true`）， 生成步驟將在部署期間被忽略。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的持續部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
