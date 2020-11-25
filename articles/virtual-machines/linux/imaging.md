---
title: 建立適用於 Azure 的 Linux 映像概觀
description: 如何帶入您的 Linux VM 映像或建立新映像以在 Azure 中使用。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: e364578cdec8696688cf19e14fd0529f1ca3fbb3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842612"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>在 Azure 中帶入並建立 Linux 映像

本概觀涵蓋有關映像處理的基本概念，以及如何在 Azure 中成功建置和使用 Linux 映像。 將自訂映像帶入 Azure 之前，您必須先了解您可以使用的類型和選項。

本文將討論映像決策點和需求、說明重要概念，讓您可以遵循，並且能夠建立您自己的自訂映像以符合您的規格。

## <a name="difference-between-managed-disks-and-images"></a>受控磁碟與映像之間的差異


Azure 可讓您將 VHD 帶入平台，作為[受控磁碟](../faq-for-disks.md#managed-disks)或作為映像的來源使用。 

Azure 受控磁碟是單一 VHD。 您可以使用現有的 VHD 並從中建立受控磁碟，或從頭開始建立空的受控磁碟。 您可以藉由將磁碟連結至 VM，從受控磁碟建立 VM，但是一個 VHD 只能與一個 VM 搭配使用。 您無法修改任何 OS 屬性，Azure 只會嘗試開啟 VM，並使用該磁碟啟動。 

Azure 映像可由多個 OS 磁碟和資料磁碟組成。 當您使用受控映像來建立 VM 時，平台會建立映像的複本，並將其用來建立 VM，因此受控映像支援對多個 VM 重複使用相同的映像。 Azure 也提供映像的進階管理功能，例如全域複寫，以及透過[共用映像庫](shared-image-galleries.md)進行版本設定。 



## <a name="generalized-and-specialized"></a>一般化和特殊化

Azure 提供兩種主要映像類型：一般化和特殊化。 一般化和特殊化詞彙原本就是 Windows 的術語，遷移至 Azure。 這些類型會定義平台開啟 VM 時，如何進行處理。 這兩種類型都有優點和缺點，以及必要條件。 開始之前，您必須知道您需要的映像類型。 以下摘要說明您需要選擇的案例和類型：

| 狀況      | 映像類型  | 儲存體選項 |
| ------------- |:-------------:| :-------------:| 
| 建立可設定供多個 VM 使用的映像，我可以設定主機名稱、新增管理使用者，並且在第一次開機時執行其他工作。 | 一般化 | 共用映像庫或獨立受控映像 |
| 從 VM 快照集或備份建立映像 | 特製化 |共用映像庫或受控磁碟 |
| 快速建立不需要任何設定以建立多個 VM 的映像 |特製化 |共用映像庫 |


### <a name="generalized-images"></a>一般化映像

一般化映像是一種映像，需要在第一次開機時完成設定。 例如，在第一次開機時，您會設定主機名稱、管理使用者和其他 VM 特定設定。 當您想要多次重複使用映像，以及當您想要在建立期間傳入參數時，這會很有用。 如果一般化映像包含 Azure 代理程式，代理程式將會處理參數，並且將初始設定已完成的訊號傳回平台。 這個程序稱為[佈建](./provisioning.md)。 

佈建需要映像中包含佈建程式。 有兩個佈建程式：
- [Azure Linux 代理程式](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

這些是建立映像的[必要條件](./create-upload-generic.md)。


### <a name="specialized-images"></a>特殊化映像
這些是已完全設定且不需要 VM 和特殊參數的映像，平台只會開啟 VM，您需要在 VM 中處理唯一性，例如設定主機名稱，以避免在相同的 VNET 上發生 DNS 衝突。 

這些映像不需要佈建代理程式，不過，您可能會想要有擴充功能處理功能。 您可以安裝 Linux 代理程式，但是停用佈建選項。 雖然您不需要佈建代理程式，但是映像必須滿足 Azure 映像的[必要條件](./create-upload-generic.md)。


## <a name="image-storage-options"></a>映像儲存體選項
帶入您的 Linux 映像時，您有兩個選項：

- 適用於在開發和測試環境中建立簡單 VM 的受控映像。
- [共用映像庫](shared-image-galleries.md)，以大規模建立和共用映像。


### <a name="managed-images"></a>受控映像

受控映像可用於建立多個 VM，但是有很多限制。 受控映像只能從一般化來源 (VM 或 VHD) 建立。 受控映像只能用來在相同區域中建立 VM，而且無法在訂用帳戶和租用戶之間共用。

受控映像可用於開發和測試環境，您需要幾個簡單的一般化映像，在單一區域和訂用帳戶內使用。 

### <a name="azure-shared-image-gallery-sig"></a>Azure 共用映像庫 (SIG)

建議針對大規模建立、管理和共用映像使用[共用映像庫](shared-image-galleries.md)。 共用映像庫可協助您圍繞映像來建置結構和組織。  

- 同時支援一般化和特殊化映像。
- 支援第 1 代和第 2 代映像。
- 映像的全域複寫。
- 對映像進行版本控制和分組，以便管理。
- 在支援可用性區域的區域中，具有區域備援儲存體 (ZRS) 的高可用性映像。 ZRS 針對區域性失敗提供更佳的復原能力。
- 使用 Azure RBAC 在訂用帳戶之間共用，甚或在 Active Directory (AD) 租用戶之間共用。
- 使用每個區域中的映像複本來調整您的部署。

概括而言，您會建立一個 SIG，是由下列各項所組成：
- 映像定義 - 這些是保留映像群組的容器。
- 映像版本 - 這些是實際的映像



## <a name="hyper-v-generation"></a>Hyper-V 世代

Azure 支援 Hyper-V 第 1 代 (Gen1) 和第 2 代 (Gen2)，Gen2 是最新的世代，提供超越 Gen1 的額外功能。 例如：記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。 在內部部署環境執行的第 2 代 VM 擁有一些 Azure 尚未支援的功能。 如需詳細資訊，請參閱「功能」一節。 如需詳細資訊，請參閱[本篇文章](../generation-2.md)。 如果您需要額外的功能，請建立 Gen2 映像。

如果您仍然需要建立自己的映像，請確定其符合[映像必要條件](./create-upload-generic.md)，並上傳至 Azure。 發行版本特定需求：


- [CentOS 型發行版本](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>後續步驟

了解如何建立[共用映像庫](tutorial-custom-images.md)。