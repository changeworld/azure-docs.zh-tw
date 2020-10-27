---
author: baanders
description: 包含 Azure Digital Twins 教學課程的檔案 - 設定範例專案
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff44e4ae45f8b7c6bff3685522440ca7118f337e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347082"
---
## <a name="configure-the-sample-project"></a>設定範例專案

接下來，請設定將和您 Azure Digital Twins 執行個體互動的範例用戶端應用程式。

在您的機器上瀏覽至先前從 [*Azure Digital Twins 端對端範例*](/samples/azure-samples/digital-twins-samples/digital-twins-samples)下載的檔案 (如果尚未解壓縮，請將其解壓縮)。

在資料夾內，瀏覽至 _AdtSampleApp_ 。 在 Visual Studio 2019 中開啟 _**AdtE2ESample.sln**_ 。 

在 Visual Studio 中，選取 [SampleClientApp] > [appsettings]  檔案，以在編輯視窗中加以開啟。 這會作為預先設定的 JSON 檔案，其中具備執行專案所需的設定變數。

在檔案本文中，將 `instanceUrl` 變更為您的 Azure Digital Twins 執行個體 *hostName* URL (開頭包含 *https://* ，如下所示)。

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

儲存並關閉檔案。 

接下來，設定 *.json* 檔案，使其在您建置 *SampleClientApp* 時複製到輸出目錄。 若要進行此操作，請以滑鼠右鍵選取 *appsettings.json* 檔案，然後選擇 [屬性]。 在 [屬性] 偵測器中，將 [複製到輸出目錄] 屬性的值變更為 [有更新時才複製]。

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Visual Studio 視窗的摘要，顯示 [方案總管] 窗格，其中醒目提示 appsettings.json，而且 [屬性] 窗格的 [複製到輸出目錄] 屬性設為 [有更新時才複製]" border="false":::

在 Visual Studio 中將 _**AdtE2ESample**_ 專案保持在開啟狀態，以繼續在教學課程中使用該專案。

