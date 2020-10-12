---
title: 針對常見驗證錯誤進行疑難排解，Azure Marketplace
description: 提供在 Azure Marketplace 中使用 Cloud Partner 入口網站 Api 時常見驗證錯誤的協助。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89487312"
---
# <a name="troubleshooting-common-authentication-errors"></a>針對常見驗證錯誤進行疑難排解

> [!NOTE]
> Cloud Partner 入口網站 Api 已與整合，並且將繼續在合作夥伴中心中運作。 轉換導入了少量的變更。 請參閱 [CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md) 中所列的變更，以確保您的程式碼在轉換至合作夥伴中心之後仍繼續運作。 CPP Api 應該僅用於已整合的現有產品，然後轉換為合作夥伴中心;新產品應使用合作夥伴中心提交 Api。

此文章提供了使用 Cloud Partner 入口網站 API 時常見驗證錯誤的幫助。

## <a name="unauthorized-error"></a>未經授權錯誤

如果您不斷遇到 `401 unauthorized` 錯誤，請確認您是否擁有有效的存取權杖。  如果您尚未這麼做，請依照[使用入口網站來建立可存取資源的 Azure Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)中所述，建立基本的 Azure Active Directory (Azure AD) 應用程式和服務主體。 然後，使用應用程式或簡單的 HTTP POST 要求來驗證您的存取權。  您將包含租使用者識別碼、應用程式識別碼、物件識別碼和秘密金鑰，以取得存取權杖。

## <a name="forbidden-error"></a>「禁止」錯誤

如果您遇到 `403 forbidden` 錯誤，請確保已在 Cloud Partner 入口網站中將正確的服務主體加入到您的發行者帳戶中。 請依照[先決條件](./cloud-partner-portal-api-prerequisites.md)頁面中的步驟，將服務主體加入到入口網站。

如果已加入正確的服務主體，則確認所有其他資訊。 密切注意入口網站上輸入的物件識別碼。 在 [Azure Active Directory 應用程式註冊] 頁面中有兩個物件識別碼，而您必須使用本機物件識別碼。 您可以前往應用程式的 [應用程式註冊]**** 頁面，然後按一下 [本機目錄中受控的應用程式]**** 下的應用程式名稱，以尋找正確的值。 這會將您帶到應用程式的本機屬性，您可以在 [屬性]**** 頁面中找到正確的物件識別碼，如下圖所示。 此外，請確保在新增服務主體並進行 API 呼叫時使用正確的發行者識別碼。
