---
title: 提升私用雲端許可權
titleSuffix: Azure VMware Solution by CloudSimple
description: 說明如何針對 vCenter 中的系統管理功能，在私人雲端上提升許可權
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 628824a3d303184e199452b0974c7f99d99a4933
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998947"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>從 CloudSimple 入口網站提升私用雲端 vCenter 許可權

如需您私人雲端 vCenter 的系統管理存取權，您可以暫時提升 CloudSimple 許可權。  使用較高的許可權，您可以安裝 VMware 解決方案、新增身分識別來源，以及管理使用者。

您可以在 vCenter SSO 網域上建立新的使用者，並取得 vCenter 的存取權。  當您建立新的使用者時，請將它們新增至 CloudSimple 的內建群組以存取 vCenter。  如需詳細資訊，請參閱[CloudSimple VMware vCenter 的私用雲端許可權模型](./learn-private-cloud-permissions.md)。

> [!CAUTION]
> 請勿對管理元件進行任何設定變更。 在提高許可權的狀態期間採取的動作可能會對您的系統造成負面影響，或可能導致系統無法使用。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="escalate-privileges"></a>提升權限

1. 存取 [CloudSimple 入口網站](access-cloudsimple-portal.md)。

2. 開啟 [**資源**] 頁面，選取您想要提升許可權的私人雲端。

3. 在 [**變更 vSphere 許可權**] 下的 [摘要] 頁面底部附近，按一下 [**呈報**]。

    ![變更 vSphere 許可權](media/escalate-private-cloud-privilege.png)

4. 選取 [vSphere] 使用者類型。  只有 `CloudOwner@cloudsimple.local` 本機使用者可以升級。

5. 從下拉式選單中選取 [呈報時間間隔]。 選擇可讓您完成工作的最短時間。

6. 選取核取方塊以確認您瞭解風險。

    ![呈報許可權對話方塊](media/escalate-private-cloud-privilege-dialog.png)

7. 按一下 [確定]  。

8. 擴大程式可能需要幾分鐘的時間。 完成時，按一下 **[確定]**。

許可權提升會開始，並持續到所選間隔結束為止。  您可以登入您的私用雲端 vCenter 來執行系統管理工作。

> [!IMPORTANT]
> 只有一位使用者可以有較高的權限。  您必須先取消呈報使用者的許可權，才能提升其他使用者的許可權。

> [!CAUTION]
> 新使用者必須僅新增至*雲端擁有者群組*、*雲端全域叢集-管理群組*、雲端-全域*存放裝置-* 系統管理群組、雲端-全域*網路-* 系統管理群組或*雲端全域 VM-管理群組*。  新增至系統*管理員*群組的使用者將會自動移除。  只有服務帳戶必須新增至*Administrators*群組，而服務帳戶不能用來登入 VSPHERE web UI。

## <a name="extend-privilege-escalation"></a>擴充許可權擴大

如果您需要額外的時間來完成您的工作，您可以擴充許可權擴大期間。  選擇可讓您完成系統管理工作的額外呈報時間間隔。

1. 在 CloudSimple 入口網站的 [**資源**] [  >  **私人**雲端] 中，選取您要擴充許可權提升的私人雲端。

2. 按一下 [摘要] 索引標籤底部附近的 [**擴充許可權擴大**]。

    ![擴充許可權擴大](media/de-escalate-private-cloud-privilege.png)

3. 從下拉式選單中選取呈報時間間隔。 檢查新的擴大結束時間。

4. 按一下 [**儲存**] 以擴充間隔。

## <a name="de-escalate-privileges"></a>取消提升許可權

系統管理工作完成後，您應該解除許可權的升級。  

1. 在 CloudSimple 入口網站的 [**資源**] [  >  **私人**雲端] 中，選取您想要對其取消升級許可權的私人雲端。

2. 按一下 [**取消呈報**]。

3. 按一下 [確定]  。

> [!IMPORTANT]
> 若要避免發生任何錯誤，請登出 vCenter，並在取消升級許可權後重新登入。

## <a name="next-steps"></a>接下來的步驟

* [設定要使用的 vCenter 身分識別來源 Active Directory](./set-vcenter-identity.md)
* 安裝備份解決方案以[備份工作負載虛擬機器](./backup-workloads-veeam.md)
