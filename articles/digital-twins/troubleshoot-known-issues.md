---
title: 已知問題-Azure 數位 Twins
description: 取得協助來辨識和緩和 Azure 數位 Twins 的已知問題。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044134"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure 數位 Twins 中的已知問題

本文提供與 Azure 數位 Twins 相關之已知問題的資訊。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>在 Cloud Shell 中的「400用戶端錯誤：不正確的要求」

Cloud Shell 中的命令可能會間歇性地失敗，並出現錯誤「400用戶端錯誤：不正確的 url 要求：」， http://localhost:50342/oauth2/token 後面接著完整堆疊追蹤。

### <a name="troubleshooting-steps"></a>疑難排解步驟

藉由重新執行 `az login` 命令並完成後續的登入步驟，即可解決此問題。

在此之後，您應該能夠重新執行命令。

### <a name="possible-causes"></a>可能的原因

這是 Cloud Shell 中已知問題的結果：[*從 Cloud Shell 取得權杖時，間歇性失敗，並出現400用戶端錯誤：不正確的要求*](https://github.com/Azure/azure-cli/issues/11749)。

## <a name="next-steps"></a>接下來的步驟

深入瞭解 Azure 數位 Twins 的安全性和許可權：
* [*概念： Azure 數位 Twins 解決方案的安全性*](concepts-security.md)