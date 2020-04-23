---
title: 升級私有雲權限
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何升級私有雲上的許可權,以用於 vCenter 中的管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870463"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>從雲簡單門戶升級私有雲 vCenter 許可權

要對私有雲 vCenter 進行管理訪問,您可以暫時升級雲簡單許可權。  使用提升的許可權,您可以安裝 VMware 解決方案、添加標識源和管理使用者。

可以在 vCenter SSO 網域上創建新使用者,並授予對 vCenter 的訪問許可權。  創建新使用者時,請將它們添加到 CloudSimple 內建組中以訪問 vCenter。  有關詳細資訊,請參閱[VMware vCenter 的雲端簡單私有雲端許可權模型](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/)。

> [!CAUTION]
> 不要對管理元件進行任何配置更改。 在升級的特權狀態期間執行的操作可能會對您的系統產生負面影響,或可能導致您的系統不可用。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="escalate-privileges"></a>提升權限

1. 存[取 雲簡單門戶](access-cloudsimple-portal.md)。

2. 打開 **「資源」** 頁面,選擇要為其升級權限的私有雲。

3. 在 **「更改 vSphere」許可權**下的「摘要」頁底部附近,按下「**升級**」 。

    ![變更 vSphere 權限](media/escalate-private-cloud-privilege.png)

4. 選擇 vSphere 用戶類型。  只能`CloudOwner@cloudsimple.local`升級本地使用者。

5. 從下拉清單中選擇升級時間間隔。 選擇允許您完成任務的最短期限。

6. 選擇該複選框以確認您了解風險。

    ![升級權限對話框](media/escalate-private-cloud-privilege-dialog.png)

7. 按一下 [確定]  。

8. 升級過程可能需要幾分鐘時間。 完成後,按一下 **「確定**」。

許可權升級開始並持續到所選間隔的結束。  您可以登錄到私有雲 vCenter 以執行管理任務。

> [!IMPORTANT]
> 只有一個使用者可以具有升級的許可權。  必須先取消升級用戶的許可權,然後才能升級其他用戶的許可權。

> [!CAUTION]
> 新使用者只能添加到*雲擁有者組*、*雲-全球群集-管理員組*、*雲-全球-儲存-管理員組*、*雲-全球-網路-管理員群組*或*雲-全球-VM-管理員群組*。  新增到*管理員*群組的使用者將自動刪除。  只能將服務帳戶添加到*管理員*組,並且不得使用服務帳戶登錄到 vSphere Web UI。

## <a name="extend-privilege-escalation"></a>延伸權限升級

如果您需要額外的時間來完成任務,可以延長許可權升級期。  選擇允許您完成管理任務的額外升級時間間隔。

1. 在 CloudSimple 門戶中的 **「資源** > **私有雲**」上,選擇要為其擴展許可權升級的私有雲。

2. 在「摘要」選項卡底部附近,按下 **「擴展許可權升級**」 。

    ![延伸權限升級](media/de-escalate-private-cloud-privilege.png)

3. 從下拉清單中選擇升級時間間隔。 查看新的上報結束時間。

4. 按下 **「保存**」以延長間隔。

## <a name="de-escalate-privileges"></a>降級特權

完成管理任務后,應取消降級許可權。  

1. 在 CloudSimple 門戶中的 **「資源** > **私有雲**」上,選擇要取消許可權的私有雲。

2. 按下 **「取消升級**」。

3. 按一下 [確定]  。

> [!IMPORTANT]
> 為避免任何錯誤,請註銷 vCenter,並在取消升級許可權後再次登錄。

## <a name="next-steps"></a>後續步驟

* [設定 vCenter 識別來源以使用活動目錄](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* 將備份解決方案安裝到[備份工作負載虛擬機器](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/)