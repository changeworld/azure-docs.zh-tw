---
title: 將 API 從 Azure API 管理匯出到 Power Platform |Microsoft Docs
description: 了解如何從 API 管理將 API 匯出至 Power Platform。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 5814ff3909aa36ccfd9e14033f200fb4d3b8d32d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252847"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>將 API 從 Azure API 管理匯出到 Power Platform 

使用 Microsoft [Power Platform](https://powerplatform.microsoft.com) 的公民開發人員通常需要達到專業開發人員在 Azure 所開發並部署的商務功能。 [Azure API 管理](https://aka.ms/apimrocks)可讓專業開發人員將其後端服務發佈為 API，並輕鬆地將這些 API 匯出至 Power Platform (Power Apps 和 Power Automate)，作為公民開發人員取用的自訂連接器。 

本文會逐步引導您將 API 管理中的 API 匯出至 Power Platform。 

## <a name="prerequisites"></a>Prerequisites

+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)
+ 請確定您的 API 管理執行個體中有要匯出至 Power Platform 的 API
+ 確定您擁有 Power Apps 或 Power Automate [環境](/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="export-an-api"></a>匯出 API

1. 在 Azure 入口網站中，瀏覽至您的 APIM 服務，然後從功能表中選取 [API]  。
2. 按一下您要匯出之 API 旁的三個點圖示。 
3. 選取 [匯出]  。
4. 選取 [Power Apps 和 Power Automate]  。
5. 選擇要匯出 API 的環境。 
6. 提供顯示名稱，此名稱將用來作為自訂連接器的名稱。  
7. (選用) 如果 API 受到 OAuth 2.0 伺服器的保護，您也必須提供其他詳細資料，包括 `Client ID`、`Client secret`、`Authorization URL`、`Token URL` 和 `Refresh URL`。  
8. 選取 [匯出]  。 

匯出完成後，請瀏覽至您的 Power Apps 或 Power Automate 環境。 您會看到 API 作為自訂連接器。

## <a name="next-steps"></a>後續步驟

* [深入了解 Power Platform](https://powerplatform.microsoft.com/)
* [遵循教學課程，以了解 API 管理中的一般工作](./import-and-publish.md)
