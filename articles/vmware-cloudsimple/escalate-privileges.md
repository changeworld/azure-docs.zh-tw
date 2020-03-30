---
title: Azure VMware 解決方案（按雲簡單 - 升級雲簡單許可權）
description: 描述如何升級雲簡單許可權以在私有雲 vCenter 中執行管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025328"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>升級雲簡單許可權，以在私有雲 vCenter 中執行管理功能

CloudSimple 許可權方法旨在為 vCenter 使用者提供執行正常操作所需的許可權。 在某些情況下，使用者可能需要其他許可權才能執行特定任務。  您可以在有限時間內升級 vCenter SSO 使用者的許可權。

升級特權的原因可能包括：

* 標識源的配置
* 使用者管理
* 刪除分散式埠組
* 安裝 vCenter 解決方案（如備份應用）
* 建立服務帳戶

> [!WARNING]
> 在升級的特權狀態下執行的操作可能會對系統產生負面影響，並可能導致系統不可用。 在上報期間僅執行必要的操作。

從雲簡單門戶中，[在](escalate-private-cloud-privileges.md)vCenter SSO 上升級雲擁有者本地使用者的許可權。  僅當在 vCenter 上配置了其他標識提供程式時，才能提升遠端使用者的許可權。  許可權升級涉及將所選使用者添加到 vSphere 內置管理員組。  只有一個使用者可以具有升級的許可權。  如果需要升級其他使用者的許可權，請首先取消升級當前使用者的許可權。

必須添加來自其他標識源的使用者作為 CloudOwner 組的成員。

> [!CAUTION]
> 新使用者只能添加到*雲擁有者組*、*雲-全球群集-管理員組*、*雲-全球-存儲-管理員組*、*雲-全球-網路-管理員組*或*雲-全球-VM-管理員組*。  添加到*管理員*組的使用者將自動刪除。  只能將服務帳戶添加到*管理員*組，並且不得使用服務帳戶登錄到 vSphere Web UI。

在升級期間，CloudSimple 使用自動監視與關聯的警報通知來識別對環境的任何意外更改。
