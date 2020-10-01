---
title: 在 Azure 入口網站 Azure AD 中查看受控識別的服務主體
description: 逐步指導您如何在 Azure 入口網站中檢視受控識別的服務主體。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611923"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>在 Azure 入口網站中檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 Azure 入口網站來檢視受控識別的服務主體。

 > [!NOTE] 
 > 服務主體是企業應用程式。 

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 啟用[虛擬機器](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[應用程式](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上的系統指派身分識別。

## <a name="view-the-service-principal"></a>檢視服務主體

此程序示範如何檢視已啟用系統指派身分識別的 VM 所具有的服務主體 (應用程式適用同樣的步驟)。

1. 按一下 [Azure Active Directory]****，然後按一下 [企業應用程式]****。
2. 選擇 [ **應用程式類型**] 下的 [ **所有應用程式** ]， **然後按一下 [** 套用]。
3. 在 [搜尋篩選] 方塊中，輸入已啟用受控識別的 Azure 資源名稱，或從顯示的清單中選擇它。

   ![在入口網站中檢視受控識別服務主體](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>後續步驟

[適用於 Azure 資源的受控識別](./overview.md)