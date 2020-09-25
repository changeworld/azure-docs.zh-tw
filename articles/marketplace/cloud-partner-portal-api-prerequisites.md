---
title: API 必要條件-Azure Marketplace
description: 使用 Cloud Partner 入口網站 Api 的必要條件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261153"
---
# <a name="api-prerequisites"></a>API 先決條件

> [!NOTE]
> Cloud Partner 入口網站 Api 已與整合，並且將繼續在合作夥伴中心中運作。 轉換導入了少量的變更。 請參閱 [CLOUD PARTNER 入口網站 API 參考](cloud-partner-portal-api-overview.md) 中所列的變更，以確保您的程式碼在轉換至合作夥伴中心之後仍繼續運作。 請僅針對已整合的現有產品使用 CPP Api，然後轉換為合作夥伴中心;新產品應使用合作夥伴中心提交 Api。

您需要兩個必要的程式設計資產，才能使用 Cloud Partner 入口網站 Api：服務主體和 Azure Active Directory (Azure AD) 存取權杖。

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>在 Azure Active Directory 租使用者中建立服務主體

首先，您必須在 Azure AD 租用戶中建立一個服務主體。 此租用戶將會在 Cloud Partner 入口網站中獲指派一組自己的權限。 您的程式碼會使用此租使用者來呼叫 Api，而不是使用您的個人認證。 如需建立服務主體的完整說明，請參閱 [如何：使用入口網站來建立可存取資源 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="add-service-principal-to-your-account"></a>將服務主體新增至您的帳戶

既然您已在租使用者中建立服務主體，您可以將其新增為您合作夥伴中心入口網站帳戶的使用者。 服務主體就像使用者一樣，可以是入口網站的擁有者或參與者。 如需詳細資訊，請參閱下面的 **後續步驟** 。

## <a name="next-steps"></a>後續步驟

請參閱 [管理 Azure AD 的應用程式](partner-center-portal/manage-account.md#manage-azure-ad-applications)。
