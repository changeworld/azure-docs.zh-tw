---
title: 針對常見驗證錯誤進行疑難排解 |Azure Marketplace
description: 使用 Cloud Partner 入口網站 API 時，提供常見驗證錯誤的幫助。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 7684e097118fce4dcd471257ec0d9d476ce342be
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964918"
---
# <a name="troubleshooting-common-authentication-errors"></a>針對常見驗證錯誤進行疑難排解

> [!NOTE]
> Cloud Partner 入口網站 API 已和合作夥伴中心整合，並會在將供應項目移轉到合作夥伴中心後繼續運作。 該整合會引進些微的變更。 請參閱[CLOUD PARTNER 入口網站 API 參考](./cloud-partner-portal-api-overview.md)中所列的變更，以確保您的程式碼會在遷移至合作夥伴中心後繼續運作。

此文章提供了使用 Cloud Partner 入口網站 API 時常見驗證錯誤的幫助。

## <a name="unauthorized-error"></a>未經授權錯誤

如果您不斷遇到 `401 unauthorized` 錯誤，請確認您是否擁有有效的存取權杖。  如果您尚未這麼做，請依照[使用入口網站來建立可存取資源的 Azure Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)中所述，建立基本的 Azure Active Directory (Azure AD) 應用程式和服務主體。 然後，使用應用程式或簡單的 HTTP POST 要求來驗證您的存取權。  您將包含租用戶識別碼、應用程式識別碼、物件識別碼和祕密金鑰以取得存取權杖，如下圖所示：

![疑難排解 401 錯誤](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>「禁止」錯誤

如果您遇到 `403 forbidden` 錯誤，請確保已在 Cloud Partner 入口網站中將正確的服務主體加入到您的發行者帳戶中。
請依照[先決條件](./cloud-partner-portal-api-prerequisites.md)頁面中的步驟，將服務主體加入到入口網站。

如果已加入正確的服務主體，則確認所有其他資訊。 密切注意入口網站上輸入的物件識別碼。 在 [Azure Active Directory 應用程式註冊] 頁面中有兩個物件識別碼，而您必須使用本機物件識別碼。 您可以前往應用程式的 [應用程式註冊]**** 頁面，然後按一下 [本機目錄中受控的應用程式]**** 下的應用程式名稱，以尋找正確的值。 這會將您帶到應用程式的本機屬性，您可以在 [屬性]**** 頁面中找到正確的物件識別碼，如下圖所示。 此外，請確保在新增服務主體並進行 API 呼叫時使用正確的發行者識別碼。

![疑難排解 403 錯誤](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
