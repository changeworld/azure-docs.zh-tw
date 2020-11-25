---
title: 建立 Azure 服務主體
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097633"
---
## <a name="create-an-azure-service-principal"></a>建立 Azure 服務主體

若要讓您的應用程式與您的 Azure 帳戶互動，您需要 Azure 服務主體來管理權限。 依照[建立 Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)中的指示進行。

建立服務主體時，您會看到服務主體有一個祕密值、一個識別碼和一個應用程式識別碼。 將應用程式識別碼和祕密儲存至暫存位置，以供後續步驟使用。