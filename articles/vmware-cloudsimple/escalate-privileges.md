---
title: Azure VMware Solution by CloudSimple-呈報 CloudSimple 許可權
description: 說明如何提升 CloudSimple 許可權，以在私用雲端 vCenter 中執行系統管理功能
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895722"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>提升 CloudSimple 許可權以在私用雲端 vCenter 中執行系統管理功能

CloudSimple 許可權方法的設計目的是為 vCenter 使用者提供執行正常作業所需的許可權。 在某些情況下，使用者可能需要額外的許可權，才能執行特定工作。  您可以在一段有限期間內提升 vCenter SSO 使用者的許可權。

提高許可權的原因可能包括下列各項：

* 設定身分識別來源
* 使用者管理
* 刪除分散式通訊埠群組
*  (安裝 vCenter 解決方案，例如備份應用程式) 
* 建立服務帳戶

> [!WARNING]
> 在提高許可權狀態中採取的動作可能會對系統造成負面影響，而且可能會導致您的系統無法使用。 在擴大期間執行必要的動作。

在 CloudSimple 入口網站中，提升 vCenter SSO 上 CloudOwner 本機使用者的 [許可權](escalate-private-cloud-privileges.md) 。  只有在 vCenter 上已設定其他識別提供者時，您才可以提升遠端使用者的許可權。  提升許可權牽涉到將選取的使用者新增至 vSphere 內建的 Administrators 群組。  只有一位使用者可以提升許可權。  如果您需要提升另一位使用者的許可權，請先將目前使用者的許可權解除提升。

其他身分識別來源的使用者必須新增為 CloudOwner 群組的成員。

> [!CAUTION]
> 新使用者只能新增至 *雲端擁有者群組*、 *雲端全域* 叢集-系統管理群組、雲端-全域 *存放裝置-* 管理群組、 *雲端全域-網路-系統管理* 群組或 *雲端全域 VM-管理群組*。  新增至系統 *管理員* 群組的使用者將會自動移除。  只有服務帳戶必須加入至系統 *管理員* 群組，而且服務帳戶不能用來登入 VSPHERE web UI。

在擴大期間，CloudSimple 會使用自動化監視與相關聯的警示通知，以識別任何意外的環境變更。
