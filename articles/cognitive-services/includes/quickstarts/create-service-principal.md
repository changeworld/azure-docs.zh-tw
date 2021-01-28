---
title: 建立 Azure 服務主體
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947393"
---
## <a name="create-an-azure-service-principal"></a>建立 Azure 服務主體

若要讓您的應用程式與您的 Azure 帳戶互動，您需要 Azure 服務主體來管理權限。 依照[建立 Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0)中的指示進行。

建立服務主體時，您會看到服務主體有一個祕密值、一個識別碼和一個應用程式識別碼。 將應用程式識別碼和祕密儲存至暫存位置，以供後續步驟使用。
