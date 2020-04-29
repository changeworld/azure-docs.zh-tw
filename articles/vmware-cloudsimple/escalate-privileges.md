---
title: Azure VMware Solution by CloudSimple-提升 CloudSimple 許可權
description: 說明如何提升 CloudSimple 許可權，以在私用雲端 vCenter 中執行系統管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025328"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>提升 CloudSimple 許可權以在私用雲端 vCenter 中執行系統管理功能

CloudSimple 許可權方法的設計，是為了讓 vCenter 使用者能夠執行正常作業所需的許可權。 在某些情況下，使用者可能需要額外的許可權來執行特定工作。  您可以在有限的期間內提升 vCenter SSO 使用者的許可權。

提高許可權的原因可能包括下列各項：

* 身分識別來源的設定
* 使用者管理
* 刪除分散式通訊埠群組
* 安裝 vCenter 解決方案（例如備份應用程式）
* 建立服務帳戶

> [!WARNING]
> 在提高許可權狀態中採取的動作可能會對您的系統造成不良影響，而且可能會導致系統無法使用。 在擴大期間只執行必要的動作。

從 CloudSimple 入口網站，針對 vCenter SSO 上的 CloudOwner 本機使用者[提升許可權](escalate-private-cloud-privileges.md)。  只有在 vCenter 上設定了其他身分識別提供者時，您才可以呈報遠端使用者的許可權。  提升許可權牽涉到將選取的使用者新增至 vSphere 內建的系統管理員群組。  只有一位使用者可以有較高的權限。  如果您需要呈報其他使用者的許可權，請先取消升級目前使用者的許可權。

來自其他身分識別來源的使用者必須新增為 CloudOwner 群組的成員。

> [!CAUTION]
> 新使用者必須僅新增至*雲端擁有者群組*、*雲端全域叢集-管理群組*、雲端-全域*存放裝置-* 系統管理群組、雲端-全域*網路-* 系統管理群組或*雲端全域 VM-管理群組*。  新增至系統*管理員*群組的使用者將會自動移除。  只有服務帳戶必須新增至*Administrators*群組，而服務帳戶不能用來登入 VSPHERE web UI。

在擴大期間，CloudSimple 會使用具有相關聯警示通知的自動化監視，來識別對環境的任何意外變更。
