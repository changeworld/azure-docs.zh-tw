---
title: 已知問題-Azure 數位 Twins
description: 取得協助來辨識和緩和 Azure 數位 Twins 的已知問題。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531312"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 數位 Twins 中的已知問題

本文提供與 Azure 數位 Twins 相關之已知問題的資訊。

## <a name="managing-event-routes-in-the-azure-portal"></a>管理 Azure 入口網站中的事件路由

如果您使用個人[**Microsoft 帳戶（MSA）**](https://account.microsoft.com/account/Account)（例如帳戶）登入入口網站， *@outlook.com* 您會看到一個畫面，指出當您嘗試在入口網站中管理事件路由時，不論您的許可權等級為何，*您都需要有許可權才能查看事件路由*。

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="嘗試在 Azure 數位 Twins 實例上建立事件路由時，許可權錯誤 Azure 入口網站的螢幕擷取畫面":::

### <a name="troubleshooting-steps"></a>疑難排解步驟

目前無法在入口網站中管理事件路由的使用者，仍然可以使用 Azure 數位 Twins Api 或 CLI 來管理事件路由。 若要解決此問題，建議的策略是切換為事件路由管理的其中一個工具。

如需這項操作的指示，請參閱[*如何：管理端點和路由*](how-to-manage-routes.md)。

### <a name="possible-causes"></a>可能的原因

您已使用個人[Microsoft 帳戶（MSA）（](https://account.microsoft.com/account/Account)例如帳戶）登入入口網站 *@outlook.com* 。 管理 Azure 入口網站中的事件路由目前僅適用于公司網域帳戶上的 Azure 使用者。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>在 Cloud Shell 中的「400用戶端錯誤：不正確的要求」

Cloud Shell 中的命令可能會間歇性地失敗，並出現錯誤「400用戶端錯誤：不正確的 url 要求：」， http://localhost:50342/oauth2/token 後面接著完整堆疊追蹤。

### <a name="troubleshooting-steps"></a>疑難排解步驟

藉由重新執行 `az login` 命令並完成後續的登入步驟，即可解決此問題。

在此之後，您應該能夠重新執行命令。

### <a name="possible-causes"></a>可能的原因

這是 Cloud Shell 中已知問題的結果：[*從 Cloud Shell 取得權杖時，間歇性失敗，並出現400用戶端錯誤：不正確的要求*](https://github.com/Azure/azure-cli/issues/11749)。

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)