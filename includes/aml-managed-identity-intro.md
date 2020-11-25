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
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025440"
---
 Azure Machine Learning 計算叢集也支援 [受控](../articles/active-directory/managed-identities-azure-resources/overview.md) 識別，以驗證對 Azure 資源的存取，而不在您的程式碼中包含認證。 受控身分識別有兩種：

* **系統指派的受控識別** 會直接在 Azure Machine Learning 計算叢集上啟用。 系統指派的身分識別生命週期會直接系結至計算叢集。 如果刪除計算叢集，Azure 會自動清除 Azure AD 中的認證和身分識別。
* **使用者指派的受控識別** 是透過 Azure 受控識別服務提供的獨立 Azure 資源。 您可以將使用者指派的受控識別指派給多個資源，而且只要您需要，它就會持續保存。