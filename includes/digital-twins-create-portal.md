---
title: 包含檔案
description: 包含檔案
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75895483"
---
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取首頁側邊的導覽列，然後選取 [+ 建立資源]  。 

   [![展開首頁側邊的導覽列，然後選取 [+ 建立資源]](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. 搜尋 **Digital Twins**，然後選取 [Digital Twins]  。 

   [![用於建立新 Digital Twins 執行個體的選取項目](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   或者，選取 [物聯網]  ，然後選取 [Digital Twins (預覽)]  。

1. 選取 [建立]  以啟動部署程序。

   [![建立並確認資源的部署](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. 在 [Digital Twins]  窗格中，輸入下列資訊：
   * **資源名稱**：為 Digital Twins 執行個體建立獨一無二的名稱。
   * **訂用帳戶**：選擇您要用來建立此 Digital Twins 執行個體的訂用帳戶。 
   * **資源群組**：選取或建立 Digital Twins 執行個體的[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)。
   * **位置**：選取最接近裝置的位置。

     [![已輸入資訊的 Digital Twins 窗格](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. 檢閱 Digital Twins 資訊，然後選取 [建立]  。 Digital Twins 執行個體可能需要幾分鐘的時間才會建立好。 您可以在 [通知]  窗格中監視進度。

1. 開啟 Digital Twins 執行個體的 [概觀]  窗格。 請記下 [管理 API]  底下的連結。 **管理 API** URL 的格式為： 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   此 URL 會帶您前往您的執行個體所適用的 Azure Digital Twins REST API 文件。 請閱讀[如何使用 Azure Digital Twins Swagger](../articles/digital-twins/how-to-use-swagger.md)，以了解如何閱讀及使用此 API 文件。 複製**管理 API** URL 並修改為此格式： 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   應用程式會使用修改後的 URL 作為基底 URL 來存取執行個體。 將這個修改後的 URL 複製到暫存檔。 您在下一節中將會用到此 URL。

   [![管理 API 概觀](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)