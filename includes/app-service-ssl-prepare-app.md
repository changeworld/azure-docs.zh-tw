---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: dd73ac372a21a32eac0c742a7f0f525db2d1a4fe
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92169090"
---
## <a name="prepare-your-web-app"></a>準備您的 Web 應用程式

若要為您的 App Service 應用程式建立自訂安全性繫結或啟用用戶端憑證，您的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須使用 **基本** 、 **標準** 、 **進階** 或 **隔離** 層。 在此步驟中，您要確定 Web 應用程式在支援的定價層。

### <a name="sign-in-to-azure"></a>登入 Azure

開啟 [Azure 入口網站](https://portal.azure.com)。

### <a name="navigate-to-your-web-app"></a>瀏覽至您的 Web 應用程式

搜尋並選取 [應用程式服務]  。

![選取 [應用程式服務]](./media/app-service-ssl-prepare-app/app-services.png)

在 [應用程式服務]  頁面上，選取您的 Web 應用程式名稱。

![Azure 入口網站中 [應用程式服務] 頁面的螢幕擷取畫面，其中顯示所有執行中 Web 應用程式的清單並醒目提示清單中的第一個應用程式。](./media/app-service-ssl-prepare-app/select-app.png)

您已經位於 Web 應用程式的管理頁面上。  

### <a name="check-the-pricing-tier"></a>檢查定價層

在 Web 應用程式頁面的左側導覽中，捲動到 [設定]  區段，然後選取 [擴大 (App Service 方案)]  。

![相應增加功能表](./media/app-service-ssl-prepare-app/scale-up-menu.png)

請檢查以確定您的 Web 應用程式不在 **F1** 或 **D1** 層中。 系統會以深藍色方塊醒目顯示 Web 應用程式目前的層。

![檢查定價層](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

**F1** 或 **D1** 層中不支援自訂 SSL。 如果您需要擴大，請遵循下一節中的步驟來進行。 否則，請關閉 [擴大]  頁面，並略過[擴大 App Service 方案](#scale-up-your-app-service-plan)一節。

### <a name="scale-up-your-app-service-plan"></a>擴大您的 App Service 方案

選取任何非免費層 ( **B1** 、 **B2** **B3** 或「生產」類別中的任何一層)。  如需其他選項，請按一下 [查看其他選項]  。

按一下 [套用]  。

![選擇定價層](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

當您看見下列通知時，表示擴充作業已完成。

![擴大通知](./media/app-service-ssl-prepare-app/scale-notification.png)

