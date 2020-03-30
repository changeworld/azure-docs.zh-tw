---
title: 如何排除 Azure 資料目錄的故障
description: 本文介紹了 Azure 資料目錄資源的常見故障排除問題。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879545"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure 資料目錄的疑難排解

本文介紹了 Azure 資料目錄資源的常見故障排除問題。 

## <a name="functionality-limitations"></a>功能限制

使用 Azure 資料目錄時，以下功能受到限制：

- 不支援具有類型 **"來賓角色"的**帳戶。 不能將來賓帳戶添加為 Azure 資料目錄的使用者，並且來賓使用者不能在 中使用[https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)門戶。

- 不支援使用 Azure 資源管理器範本或 Azure PowerShell 命令創建 Azure 資料目錄資源。

- Azure 資料目錄資源無法在 Azure 租戶之間移動。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 原則組態

您可能會遇到一種情況，您可以登入 Azure 資料目錄入口網站，但在您嘗試登入資料來源註冊工具時，您會遇到錯誤訊息，導致您無法登入。 當您在公司網路上或從公司網路外部進行連接時，可能會發生此錯誤。

註冊工具會使用「表單驗證」 ** ，根據 Azure Active Directory 驗證使用者登入。 Azure Active Directory 系統管理員必須在「全域驗證原則」 ** 中啟用表單驗證，才會成功登入。

利用全域驗證原則，您可以分別為內部網路和外部網路連線啟用驗證方法，如下圖所示。 如果未為要連接的網路啟用表單身份驗證，則可能會出現登錄錯誤。

 ![Azure Active Directory 全域驗證原則](./media/troubleshoot-policy-configuration/global-auth-policy.png)

如需詳細資訊，請參閱 [設定驗證原則](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 資料目錄](data-catalog-get-started.md)
