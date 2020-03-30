---
title: 需要來自不受信任的網路的 MFA - Azure 活動目錄
description: 瞭解如何在 Azure 活動目錄 （Azure AD） 中配置條件訪問策略，以便從不受信任的網路進行訪問嘗試。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379995"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>如何：需要 MFA 才能從具有條件訪問的不受信任的網路進行訪問   

Azure Active Directory (Azure AD) 可讓使用者從任何位置單一登入到裝置、應用程式和服務。 您的使用者不僅可以從組織的網路存取雲端應用程式，還可以從任何未受信任的網際網路位置存取。 從未受信任的網路存取的常見最佳做法是要求多重要素驗證 (MFA)。

本文為您提供了配置條件訪問策略所需的資訊，該策略需要 MFA 才能從不受信任的網路進行訪問。 

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉以下各項： 

- Azure AD 條件訪問[的基本概念](overview.md) 
- 在 Azure 門戶中配置條件訪問策略的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>案例描述

在從您組織的網路登入的情況下，若要達到安全性和生產力之間的最佳平衡，只要求密碼應該就足夠了。 不過，對於來自不受信任之網路位置的存取，登入之使用者不是合法使用者的風險會變高。 若要解決這個問題，您可以封鎖來自不受信任之網路的存取。 或者，您也可以要求進行多重要素驗證 (MFA)，保證確實是其他合法的帳戶擁有者想登入網路。 

使用 Azure AD 條件訪問，可以使用授予存取權限的單個策略來滿足此要求： 

- 將存取權授與所選的雲端應用程式
- 將存取權授與所選使用者和群組  
- 從以下位置發出存取嘗試時 
- 當存取的來源是： 
   - 不被信任的位置

## <a name="implementation"></a>實作

此方案的挑戰是將*來自不受信任的網路位置的訪問*轉換為條件訪問條件。 在條件訪問策略中，可以將[位置條件](location-condition.md)配置為解決與網路位置相關的方案。 位置條件可讓您選取具名位置，它是 IP 位址範圍、國家和地區的邏輯群組。  

通常，您的組織擁有一個或多個位址範圍，例如 199.30.16.0 - 199.30.16.15。
若要設定具名位置，請：

- 指定此範圍 （199.30.16.0/28） 
- 指派一個描述性名稱，例如**公司網路** 

若不想嘗試定義所有不被信任的位置，您可以：

- 包含任何位置 

   ![條件式存取](./media/untrusted-networks/02.png)

- 排除所有信任的位置 

   ![條件式存取](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>原則部署

使用本文中概述的方法，您現在可以為不受信任的位置配置條件訪問策略。 若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱[如何部署新原則](best-practices.md#how-should-you-deploy-a-new-policy)。 

## <a name="next-steps"></a>後續步驟

如果要瞭解有關條件訪問的更多內容，請參閱[Azure 活動目錄中的條件訪問是什麼？](../active-directory-conditional-access-azure-portal.md)
