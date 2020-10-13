---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147372"
---
 Azure Machine Learning 計算叢集也支援 [受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 識別，以驗證對 Azure 資源的存取，而不在您的程式碼中包含認證。 受控身分識別有兩種：

* **系統指派的受控識別**會直接在 Azure Machine Learning 計算叢集上啟用。 系統指派的身分識別生命週期會直接系結至計算叢集。 如果刪除計算叢集，Azure 會自動清除 Azure AD 中的認證和身分識別。
* **使用者指派的受控識別**是透過 Azure 受控識別服務提供的獨立 Azure 資源。 您可以將使用者指派的受控識別指派給多個資源，而且只要您需要，它就會持續保存。