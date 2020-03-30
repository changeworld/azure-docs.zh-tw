---
title: 帳戶管理 - 通過雲簡單門戶提供的 Azure VMware 解決方案
description: 介紹如何通過雲簡單門戶管理 Azure VMware 解決方案上的帳戶
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025362"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>按雲簡單門戶管理 Azure VMware 解決方案上的帳戶

當您創建雲簡單服務時，它會在 CloudSimple 上創建一個帳戶。 該帳戶與服務所在的 Azure 訂閱相關聯。 訂閱中具有擁有者和參與者角色的所有使用者都有權訪問 CloudSimple 門戶。 與 CloudSimple 服務關聯的 Azure 訂閱 ID 和租戶 ID 位於"帳戶"頁上。

要管理雲簡單門戶中的帳戶[，請訪問門戶](access-cloudsimple-portal.md)並選擇側功能表上的 **"帳戶**"。

選擇 **"摘要"** 以查看有關公司雲簡單配置的資訊。 將顯示雲配置的當前容量，包括私有雲數、總存儲數、vSphere 群集配置、節點數和計算核心數。 如果當前配置不能滿足所有需求，則包含一個連結以購買其他節點。

## <a name="email-alerts"></a>電子郵件警示

您可以添加要通知的任何人的電子郵件地址，瞭解私有雲配置的更改。

1. 在 **"其他電子郵件警報**"區域中，按一下"**添加新**"。
2. 輸入電子郵件地址。
3. 按返回。  

要項目剪除，請按一下**X**。

## <a name="cloudsimple-operator-access"></a>雲簡單操作員訪問

操作員訪問設置允許 CloudSimple 允許您支援工程師登錄到雲簡單門戶，從而説明您進行故障排除。  預設情況下啟用該設置。 支援工程師在登錄客戶帳戶時執行的所有操作都記錄在**活動** > **審核**頁面上，可供您查看。

按一下**啟用 CloudSimple 操作員訪問**切換以打開或關閉訪問。
