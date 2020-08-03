---
author: baanders
description: 包含 Azure Digital Twins 教學課程的檔案 - 設定範例專案
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ad9eb95c9e3a21cd33a367cc864a4d791888afb9
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124543"
---
## <a name="configure-the-sample-project"></a>設定範例專案

接下來，請設定將和您 Azure Digital Twins 執行個體互動的範例用戶端應用程式。 如果您尚未下載範例專案，請立即從 [*Azure Digital Twins 範例*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) 登陸頁面下載該專案，方法是選取標題底下的 [下載 ZIP] 按鈕。

巡覽至下載到您電腦上的檔案並解壓縮。

在解壓縮後的資料夾內，瀏覽至 _AdtSampleApp/_ 。 在 Visual Studio 2019 中開啟 _**AdtE2ESample.sln**_。 

在 Visual Studio 中，使用 [方案總管] 窗格來建立 _SampleClientApp > **serviceConfig.json.TEMPLATE**_ 檔案的複本 (您可以使用滑鼠右鍵選取的功能表來複製和貼上)。 將複本重新命名為 *serviceConfig.json*。 這會作為預先設定的 JSON 檔案，其中具備執行專案所需的設定變數。

選取 *serviceConfig.json* 檔案，以在編輯視窗中將其開啟。 將 `tenantId` 變更為您的「目錄識別碼」，將 `clientId` 變更為您的「應用程式識別碼」，並將 `instanceUrl` 變更為您的 Azure Digital Twins 執行個體 *hostName* URL (開頭包含 *https://* ，如下所示)。

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

儲存並關閉檔案。 

接下來，設定 *serviceConfig.json* 檔案，使其在您建置 *SampleClientApp* 時複製到輸出目錄。 若要進行此操作，請以滑鼠右鍵選取 *serviceConfig.json* 檔案，然後選擇 [屬性]。 在 [屬性] 偵測器中，將 [複製到輸出目錄] 屬性的值變更為 [有更新時才複製]。

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Visual Studio 視窗的摘要，顯示 [方案總管] 窗格，其中醒目提示 serviceConfig.json，而且 [屬性] 窗格的 [複製到輸出目錄] 屬性設為 [有更新時才複製]" border="false":::

在 Visual Studio 中將 _**AdtE2ESample**_ 專案保持在開啟狀態，以繼續在教學課程中使用該專案。

