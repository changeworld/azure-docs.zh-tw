---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173880"
---
## <a name="set-up-the-development-environment"></a>設定開發環境

本節會逐步引導您設定開發環境。 內容包括建立 ASP.NET MVC 應用程式、新增已連接的服務連線、新增控制器，以及指定必要的命名空間指示詞。

### <a name="create-an-aspnet-mvc-app-project"></a>建立 ASP.NET MVC 應用程式專案

1. 開啟 Visual Studio。

1. 從主功能表中 **，選取** > [檔案] [**新增** > ] [**專案**]。

1. 在 [新增專案]**** 對話方塊中，選取 [Web]**** > [ASP.NET Web 應用程式 (.NET Framework)]****。 在 [名稱]**** 欄位中，指定 [StorageAspNet]****。 選取 [確定]  。

    ![[新增專案] 對話方塊的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. 在 [新增 ASP.NET Web 應用程式]**** 對話方塊中，選取 [MVC]****，然後選取 [確定]****。

    ![[新增 ASP.NET Web 應用程式] 對話方塊的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用已連線服務來連線到 Azure 儲存體帳戶

1. 在 [方案總管]**** 中，於專案上按一下滑鼠右鍵。

1. 從內容功能表中，選取 [**加入** > **已連接服務**]。

1. 在 [已連線的服務]**** 對話方塊中，選取 [使用 Azure 儲存體的雲端儲存體]****。

    ![[已連線的服務] 對話方塊的螢幕擷取畫面](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. 在 [Azure 儲存體]**** 對話方塊中，選取要用於本教學課程的 Azure 儲存體帳戶。 若要建立新的 Azure 儲存體帳戶，請選取 [建立新的儲存體帳戶]****，然後完成表單。 在選取現有儲存體帳戶或建立新的儲存體帳戶之後，選取 [新增]****。 Visual Studio 會將 Azure 儲存體 NuGet 套件和儲存體連接字串安裝到 **Web.config**。

1. 在**方案總管**中，以滑鼠右鍵按一下 [相依性]，選擇 [**管理 nuget 套件** **]**，然後將 nuget 套件參考新增至最新版本的 ConfigurationManager。

> [!TIP]
> 若要了解如何使用 [Azure 入口網站](https://portal.azure.com)來建立儲存體帳戶，請參閱[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。
>
> 您也可以使用 [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、[Azure CLI](../articles/storage/common/storage-azure-cli.md) 或 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 來建立儲存體帳戶。
