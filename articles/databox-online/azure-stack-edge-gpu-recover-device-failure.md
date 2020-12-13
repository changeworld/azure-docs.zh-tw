---
title: 從 Azure Stack Edge Pro 裝置失敗復原
description: 說明如何從 Azure Stack Edge Pro 失敗的裝置復原。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: e5734591bfc48469eacc1ad39cbb89f3850bfc8c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97367061"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>從失敗的 Azure Stack Edge Pro GPU 裝置復原 

本文說明如何從 Azure Stack Edge Pro GPU 裝置上的不可容忍失敗中復原。 Azure Stack Edge Pro GPU 裝置上的不可容忍失敗需要更換裝置。

## <a name="before-you-begin"></a>開始之前

請確定您已符合下列條件：

- 聯繫 Microsoft 支援服務有關裝置失敗的資訊，並建議裝置更換。 
- 備份您的裝置設定，如 [針對裝置故障進行準備](azure-stack-edge-gpu-prepare-device-failure.md)所述。


## <a name="configure-replacement-device"></a>設定更換裝置

當您的裝置遇到不可容忍的失敗時，您需要訂購更換裝置。 更換裝置的設定步驟會維持不變。 

從失敗的裝置中取出您備份的裝置設定資訊。 您將使用此資訊來設定替代裝置。  

請遵循下列步驟來設定更換裝置：

1. 收集 [部署檢查清單](azure-stack-edge-gpu-deploy-checklist.md)中所需的資訊。 您可以使用從先前裝置設定所儲存的資訊。 
1. 將相同設定的新裝置訂購至失敗的裝置。  若要下訂單，請在 Azure 入口網站中 [建立新的 Azure Stack Edge 資源](azure-stack-edge-gpu-deploy-prep.md#) 。
1. 解除[包裝](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)、[掛接](azure-stack-edge-gpu-deploy-install.md#rack-the-device)裝置的機架並[連接纜線](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。 
1. [連接到裝置的本機 UI](azure-stack-edge-gpu-deploy-connect.md)。
1. 使用您用於舊裝置的相同 IP 位址來設定網路。 使用相同的 IP 位址可將對您環境中所使用之任何用戶端電腦的影響降到最低。 請參閱如何設定 [網路設定](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
1. 指派相同的裝置名稱和 DNS 網域作為舊的裝置。 這樣一來，您的用戶端就可以使用相同的裝置名稱來與新的裝置通訊。 請參閱如何 [設定裝置設定](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。
1. 在新裝置上設定憑證的方式，與舊裝置相同。 請記住，新的裝置具有新的節點序號。 如果您在舊裝置上使用自己的憑證，您將需要取得新的節點憑證。 請參閱如何 [設定憑證](azure-stack-edge-gpu-deploy-configure-certificates.md)。
1. 從 Azure 入口網站取得啟用金鑰，並啟動新的裝置。 瞭解如何 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。

您現在已準備好部署在舊裝置上執行的工作負載。

## <a name="restore-edge-cloud-shares"></a>還原 Edge 雲端共用

請遵循下列步驟，在裝置上的 Edge cloud 共用上還原資料：

1. 使用先前在失敗裝置上建立的相同共用名稱來[新增共用](azure-stack-edge-j-series-manage-shares.md#add-a-share)。 請確定在建立共用時，請 **選取 [blob 容器** 已設定為 **使用現有** 選項]，然後選取與先前裝置搭配使用的容器。
1. [新增](azure-stack-edge-j-series-manage-users.md#add-a-user) 具有先前裝置存取權的使用者。
1. 新增與裝置上先前共用相關聯的[儲存體帳戶](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account)。 建立 Edge 儲存體帳戶時，請選取現有的容器，並指向對應至先前裝置上所對應之 Azure 儲存體帳戶的容器。 從裝置寫入至先前裝置上 Edge 儲存體帳戶的任何資料，已上傳至對應 Azure 儲存體帳戶中選取的儲存體容器。
1. 重新整理來自 Azure[的共用](azure-stack-edge-j-series-manage-shares.md#refresh-shares)資料。 這會將現有容器中的所有雲端資料提取到共用。

## <a name="restore-edge-local-shares"></a>還原 Edge 本機共用

若要為可能的裝置故障做準備，您可能已部署下列其中一種備份解決方案，以保護本機共用資料的 Kubernetes 或 IoT 工作負載：

| 協力廠商軟體           | 解決方案的參考                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 如需詳細資訊，請聯絡 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 如需詳細資訊，請聯絡 Commvault。 |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 如需詳細資訊，請洽詢 Veritas。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 如需詳細資訊，請聯絡 Veeam。 |

完整設定更換裝置之後，請啟用裝置以進行本機儲存體。 

請依照下列步驟來復原本機共用中的資料：

1. [設定裝置上的計算](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. [將本機共用新增](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) 回來。
1. 執行您選擇的資料保護解決方案所提供的復原程式。 請參閱上表中的參考。

## <a name="restore-vm-files-and-folders"></a>還原 VM 檔案和資料夾

若要為可能的裝置故障做準備，您可能已部署下列其中一個備份解決方案，以保護 Vm 上的資料：



| 備份解決方案        | 支援的 OS   | 參考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure 備份 Microsoft Azure 復原服務 (MARS) 代理程式 | Windows        | [關於 MARS 代理程式](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 整合，備份 & 復原解決方案簡介](https://www.cohesity.com/solution/cloud/azure) <br>如需詳細資訊，請聯絡 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 如需詳細資訊，請聯絡 Commvault。
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> 如需詳細資訊，請洽詢 Veritas。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 如需詳細資訊，請聯絡 Veeam。 |

完整設定更換裝置之後，您可以使用先前使用的 VM 映射重新部署 Vm。 

請遵循下列步驟來復原 Vm 中的資料：
 
1. [從裝置上的 vm 映射部署 vm](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 。 
1. 在 VM 上安裝您選擇的資料保護解決方案。
1. 執行您選擇的資料保護解決方案所提供的復原程式。 請參閱上表中的參考。

## <a name="restore-a-kubernetes-deployment"></a>還原 Kubernetes 部署

如果您透過 Azure Arc 執行 Kubernetes 部署，您可以在不可容忍的裝置故障之後還原部署。 您必須從儲存 `git` 應用程式定義的存放庫重新部署客戶應用程式/容器。 [使用 Azure Arc 部署 Kubernetes 的資訊](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>後續步驟

- 瞭解如何 [退回 Azure Stack Edge Pro 裝置](azure-stack-edge-return-device.md)。
