---
title: 如何疑難排解 Azure 資料目錄
description: 本文說明 Azure 資料目錄資源的常見疑難排解問題。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "68879545"
---
# <a name="troubleshooting-azure-data-catalog"></a>Azure 資料目錄的疑難排解

本文說明 Azure 資料目錄資源的常見疑難排解問題。 

## <a name="functionality-limitations"></a>功能限制

使用 Azure 資料目錄時，下列功能會受到限制：

- 不支援類型為 **Guest 角色** 的帳戶。 您無法新增來賓帳戶做為 Azure 資料目錄的使用者，且來賓使用者無法使用入口網站 [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) 。

- 不支援使用 Azure Resource Manager 範本或 Azure PowerShell 命令來建立 Azure 資料目錄資源。

- 無法在 Azure 租使用者之間移動 Azure 資料目錄資源。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 原則組態

您可能會遇到一種情況，您可以登入 Azure 資料目錄入口網站，但在您嘗試登入資料來源註冊工具時，您會遇到錯誤訊息，導致您無法登入。 當您在公司網路上或從公司網路外部連線時，可能會發生此錯誤。

註冊工具會使用「表單驗證」 ** ，根據 Azure Active Directory 驗證使用者登入。 Azure Active Directory 系統管理員必須在「全域驗證原則」 ** 中啟用表單驗證，才會成功登入。

利用全域驗證原則，您可以分別為內部網路和外部網路連線啟用驗證方法，如下圖所示。 如果您要連接的網路未啟用表單驗證，可能會發生登入錯誤。

 ![Azure Active Directory 全域驗證原則](./media/troubleshoot-policy-configuration/global-auth-policy.png)

如需詳細資訊，請參閱 [設定驗證原則](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 資料目錄](data-catalog-get-started.md)
