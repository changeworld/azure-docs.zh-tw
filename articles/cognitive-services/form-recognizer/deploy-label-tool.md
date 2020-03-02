---
title: 如何部署表單辨識器範例圖章工具
titleSuffix: Azure Cognitive Services
description: 瞭解您可以部署表單辨識器範例圖章工具的不同方式，以協助進行監督式學習。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207869"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署範例標籤工具

表單辨識器範例圖章工具是在 Docker 容器中執行的應用程式。 它提供有用的 UI，可讓您用來手動標記表單檔以供監督學習之用。 [[使用標籤定型](./quickstarts/label-tool.md)] 快速入門會示範如何在您的本機電腦上執行此工具，這是最常見的案例。 

本指南將說明您可以部署和執行範例標籤工具的替代方式。 

## <a name="deploy-with-azure-container-instances"></a>使用 Azure 容器實例進行部署

您可以在 Docker web 應用程式容器中執行 [標籤] 工具。 首先，在 Azure 入口網站上[建立新的 Web 應用程式資源](https://ms.portal.azure.com/#create/Microsoft.WebSite)。 在表單中填入您的訂用帳戶和資源群組詳細資料。 在必要欄位中輸入下列資訊：
* **發佈**： Docker 容器
* **操作**System： Linux

在下一個頁面上，針對 Docker 容器設定填入下欄欄位：

* **選項**：單一容器
* **影像來源**： Azure Container Registry
* **存取類型**：公用
* **影像和標記**： mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

接下來的步驟是選擇性的。 當您的應用程式完成部署之後，您可以執行它，並在線上存取標籤工具。

### <a name="connect-to-azure-ad-for-authorization"></a>連接到 Azure AD 以進行授權

我們建議您將 web 應用程式連線至 Azure Active Directory （AAD），讓只有具備您認證的人可以登入並使用應用程式。 請遵循[設定您的 App Service 應用程式](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)以連線至 AAD 中的指示。

## <a name="next-steps"></a>後續步驟

回到 [[使用標籤定型](./quickstarts/label-tool.md)] 快速入門，以瞭解如何使用此工具來手動標示定型資料，並執行監督式學習。
