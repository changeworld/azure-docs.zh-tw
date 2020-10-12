---
title: 需要來自不受信任網路的 MFA-Azure Active Directory
description: 瞭解如何在 Azure Active Directory (Azure AD) 中設定條件式存取原則，以針對來自不受信任網路的存取嘗試進行存取。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b2f9a6e13fdc39ab18a2056fab15982aa9fb0e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948159"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to：使用條件式存取要求從不受信任的網路存取 MFA   

Azure Active Directory (Azure AD) 可讓使用者從任何位置單一登入到裝置、應用程式和服務。 您的使用者不僅可以從組織的網路存取雲端應用程式，還可以從任何未受信任的網際網路位置存取。 從未受信任的網路存取的常見最佳做法是要求多重要素驗證 (MFA)。

本文提供您設定條件式存取原則所需的資訊，而此原則需要 MFA 才能從不受信任的網路進行存取。 

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉以下各項： 

- Azure AD 條件式存取的[基本概念](overview.md) 
- 在 Azure 入口網站中設定條件式存取原則的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>案例描述

在從您組織的網路登入的情況下，若要達到安全性和生產力之間的最佳平衡，只要求密碼應該就足夠了。 不過，對於來自不受信任之網路位置的存取，登入之使用者不是合法使用者的風險會變高。 若要解決這個問題，您可以封鎖來自不受信任之網路的存取。 或者，您也可以要求進行多重要素驗證 (MFA)，保證確實是其他合法的帳戶擁有者想登入網路。 

使用 Azure AD 條件式存取時，您可以透過授與存取權的單一原則來解決這項需求： 

- 將存取權授與所選的雲端應用程式
- 將存取權授與所選使用者和群組  
- 從以下位置發出存取嘗試時 
- 當存取的來源是： 
   - 不被信任的位置

## <a name="implementation"></a>實作

此案例的挑戰是要將 *不受信任網路位置的存取權* 轉譯成條件式存取條件。 在條件式存取原則中，您可以設定 [位置條件](location-condition.md) 來解決與網路位置相關的案例。 位置條件可讓您選取具名位置，它是 IP 位址範圍、國家和地區的邏輯群組。  

一般而言，您的組織會擁有一或多個位址範圍，例如 199.30.16.0-199.30.16.15。
若要設定具名位置，請：

- 指定此範圍 (199.30.16.0/28)  
- 指派一個描述性名稱，例如**公司網路** 

若不想嘗試定義所有不被信任的位置，您可以：

- 包含任何位置 

   ![條件式存取](./media/untrusted-networks/02.png)

- 排除所有信任的位置 

   ![條件式存取](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>原則部署

使用本文所述的方法，您現在可以設定不受信任位置的條件式存取原則。 若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱[如何部署新原則](best-practices.md#how-should-you-deploy-a-new-policy)。 

## <a name="next-steps"></a>接下來的步驟

如果您想要深入瞭解條件式存取，請參閱 [什麼是 Azure Active Directory 中的條件式存取？](./overview.md)