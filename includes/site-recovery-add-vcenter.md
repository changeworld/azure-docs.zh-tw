---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174508"
---
在 [新增 vCenter]  中，指定 vSphere 主機或 vCenter 伺服器的易記名稱，然後指定伺服器的 IP 位址或 FQDN。 保留連接埠為 443，除非您的 VMware 伺服器設定為在不同連接埠上接聽要求。 選取將用於連接至 VMware vCenter 或 vSphere ESXi 伺服器的帳戶。 按一下 [確定]  。

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > 如果您要新增 VMware vCenter server 或 VMware vSphere 主機帳戶不在 vCenter 或主機伺服器上具有系統管理員權限，請確定帳戶已啟用這些權限：資料中心、 資料存放區、 資料夾、 主機、 網路、 資源、 虛擬機器和 vSphere 分散式交換器。 此外，VMware vCenter 伺服器需要啟用儲存體檢視權限。
