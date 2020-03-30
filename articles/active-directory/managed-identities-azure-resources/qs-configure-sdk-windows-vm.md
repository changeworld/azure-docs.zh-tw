---
title: 使用 SDK 在 VM - Azure AD 上配置託管標識
description: 使用 Azure SDK 在 Azure VM 上設定和使用 Azure 資源的受控識別適用的逐步指示。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183467"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>使用 Azure SDK 以 Azure 資源管理的受控識別設定 VM

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

適用於 Azure 資源的受控識別會在 Azure Active Directory (AD) 中為 Azure 服務提供自動的受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure SDK 來啟用和移除 Azure VM 的 Azure 資源受控識別。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure 資源受控識別支援的 Azure SDK 

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援 Azure 資源的受控識別，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| .NET   | [從 Azure 資源的受控識別啟用的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [從 Azure 資源的受控識別啟用的虛擬機器管理儲存體](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [建立已啟用系統指派受控識別的虛擬機器](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [建立已啟用系統指派受控識別的虛擬機器](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [建立已啟用系統指派身分識別的 Azure VM](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>後續步驟

- 若要了解您還可以如何使用 Azure 入口網站、PowerShell、CLI 和資源範本，請參閱**設定 Azure VM 身分識別**底下的相關文章。
