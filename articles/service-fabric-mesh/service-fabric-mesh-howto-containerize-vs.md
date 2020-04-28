---
title: 將 Service Fabric Mesh 的現有 .NET 應用程式容器化
description: 將 Service Fabric 網格容器協調流程支援新增至使用完整 .NET framework 的 ASP.NET 和主控台專案。
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75462045"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>將 Service Fabric Mesh 的現有 .NET 應用程式容器化

本文說明如何為現有的 .NET 應用程式新增 Service Fabric Mesh 容器協調流程支援。

在 Visual Studio 2017 中，您可以為使用完整 .NET Framework 的 ASP.NET 和主控台專案新增容器化支援。

> [!NOTE]
> 目前不支援 .NET **Core** 專案。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，您可以在開始前[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 請確定您已[設定開發環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。 這包括安裝 Service Fabric 執行階段、SDK、Docker、Visual Studio 2017 和建立本機叢集。

## <a name="open-an-existing-net-app"></a>開啟現有的 .NET 應用程式

開啟要新增容器協調流程支援的應用程式。

如果您想要嘗試使用範例，您可以使用 [eShop](https://github.com/MikkelHegn/ContainersSFLab) 程式碼範例。 本文的其餘部分將假設我們會使用該專案，雖然您可以將這些步驟套用到您自己的專案。

取得 **eShop** 專案的複本：

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

在下載取得後，請在 Visual Studio 2017 中開啟 **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**。

## <a name="add-container-support"></a>新增容器支援
 
使用 Service Fabric Mesh 工具，為現有的 ASP.NET 或主控台專案新增容器協調流程支援，如下所示：

在 Visual Studio 方案總管中，以滑鼠右鍵按一下專案名稱 (在範例中為 **eShopLegacyWebForms**)，然後選擇 [新增]**** > [容器協調器支援]****。
[新增容器協調器支援]**** 對話方塊隨即出現。

![Visual Studio 的新增容器協調器對話方塊](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

從下拉式清單中選擇 [Service Fabric Mesh]****，然後按一下 [確定]****。

工具接著會確認 Docker 已安裝、將 Dockerfile 新增至您的專案，然後為您的專案提取 Docker 映像。  
Service Fabric Mesh 應用程式專案會新增至您的解決方案。 其中包含您的 Mesh 發佈設定檔和組態檔。 專案的名稱與您的專案名稱相同，結尾處會加上 'Application'，例如 **eShopLegacyWebFormsApplication**。 

在新的 Mesh 專案中，您會看到兩個您應注意的資料夾：
- **應用程式資源**，其中包含 YAML 檔案用以說明其他 Mesh 資源，例如網路。
- **服務資源**，其中包含 service.yaml 檔案，用以說明您的應用程式在部署後應如何執行。

在容器協調流程支援新增至您的應用程式後，您可以按 **F5**，對您在本機 Service Fabric Mesh 叢集上的 .NET 應用程式進行偵錯。 以下是在 Mesh Service Fabric 叢集上執行的 eShop ASP.NET 應用程式： 

![eShop 應用程式](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

現在，您可以將應用程式發佈至 Azure Service Fabric Mesh。

## <a name="next-steps"></a>後續步驟

請了解如何將應用程式發佈至 Service Fabric Mesh：[教學課程 - 部署 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)