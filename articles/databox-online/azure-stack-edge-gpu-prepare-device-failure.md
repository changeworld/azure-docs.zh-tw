---
title: 準備 Azure Stack Edge Pro 裝置失敗
description: 說明如何取代 Azure Stack Edge Pro 失敗的裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: b437ce7b6894ebefe38b32f27d370d9f8c4bfe80
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369016"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>準備 Azure Stack Edge Pro GPU 裝置失敗

本文會詳細說明如何在 Azure Stack Edge Pro GPU 裝置上儲存和備份裝置設定和資料，以協助您準備裝置故障。 

本文不包含備份 Azure Stack Edge Pro GPU 裝置上部署之 Kubernetes 和 IoT 容器的步驟。 

## <a name="understand-device-failures"></a>瞭解裝置失敗

您 Azure Stack Edge Pro GPU 裝置可能會遇到兩種類型的硬體故障。

- 需要更換硬體元件的可容忍失敗。 這些失敗會讓您以降級狀態操作裝置。 這些失敗的範例包括單一故障的電源供應器 (PSU) 或裝置上的單一故障磁片。 在上述每一種情況下，裝置都可以繼續運作。 請儘快聯絡 Microsoft 支援服務，以取代失敗的元件。

- 當您的裝置上有兩個磁片故障時，您需要更換整個裝置的不可容忍失敗。 在這些情況下，請立即聯絡 Microsoft 支援服務。 在判斷出需要更換裝置之後，支援人員將有助於更換您的 Azure Stack Edge 裝置。

若要準備不可容忍的失敗，您需要在裝置上備份下列專案：

- 裝置設定的資訊
- Edge 本機共用和 Edge 雲端共用中的資料
- 與裝置上執行的 Vm 相關聯的檔案和資料夾


## <a name="back-up-device-configuration"></a>備份裝置設定

在裝置的初始設定期間，請務必保留裝置設定資訊的複本，如 [部署檢查清單](azure-stack-edge-gpu-deploy-checklist.md)中所述。 在復原期間，此設定資訊將用來套用至新的更換裝置。 

## <a name="protect-device-data"></a>保護裝置資料

裝置資料可以是下列其中一種類型：

- Edge 雲端共用中的資料
- 本機共用中的資料
- Vm 上的檔案和資料夾

下列各節將討論在裝置上保護這些資料類型的步驟和建議。

## <a name="protect-data-in-edge-cloud-shares"></a>保護 Edge 雲端共用中的資料

您可以建立 Edge 雲端共用，以將裝置的資料分層至 Azure。 根據可用的網路頻寬，在您的裝置上設定頻寬範本，以在發生不可容忍的失敗時，將任何資料遺失降至最低。

> [!IMPORTANT]
> 如果裝置具有不可容忍的失敗，則不會遺失從裝置到 Azure 的本機資料。 

## <a name="protect-data-in-edge-local-shares"></a>保護 Edge 本機共用中的資料

如果您要部署 Kubernetes 或 IoT Edge，請將設定為將應用程式資料儲存在本機裝置上，並不要與 Azure 儲存體同步處理。 資料會儲存在裝置上的共用。 您可能會發現備份這些共用中的資料很重要。

下列協力廠商資料保護解決方案可以針對本機 SMB 或 NFS 共用中的資料提供備份解決方案。 

| 協力廠商軟體           | 解決方案的參考                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 如需詳細資訊，請聯絡 Cohesity。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 如需詳細資訊，請聯絡 Commvault。          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 如需詳細資訊，請洽詢 Veritas。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 如需詳細資訊，請聯絡 Veeam。 |


## <a name="protect-files-and-folders-on-vms"></a>保護 Vm 上的檔案和資料夾

Azure Stack Edge 可與 Azure 備份和其他協力廠商資料保護解決方案搭配運作，以提供備份解決方案來保護裝置上所部署 Vm 中包含的資料。 下表列出可供您選擇的可用解決方案參考。


| 備份解決方案        | 支援的 OS   | 參考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure 備份 Microsoft Azure 復原服務 (MARS) 代理程式 | Windows        | [關於 MARS 代理程式](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 整合，備份 & 復原解決方案簡介](https://www.cohesity.com/solution/cloud/azure) <br>如需詳細資訊，請聯絡 Cohesity。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>如需詳細資訊，請聯絡 Commvault。                          |
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> 如需詳細資訊，請洽詢 Veritas。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 如需詳細資訊，請聯絡 Veeam。 |


## <a name="next-steps"></a>後續步驟

- 瞭解如何 [從失敗的 Azure Stack Edge PRO GPU 裝置復原](azure-stack-edge-gpu-recover-device-failure.md)。