---
title: 部署 Azure Stack Edge GPU 裝置的預先部署檢查清單 |Microsoft Docs
description: 本文說明在您部署 Azure Stack Edge GPU 裝置之前，可以搜集的資訊。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 9d7b3388b7e3a31c23b34b21017f012d40e9f849
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268137"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 裝置的部署檢查清單  

本文說明可在 Azure Stack Edge 裝置的實際部署之前搜集的資訊。 

使用下列檢查清單，以確保您在 Azure Stack Edge 裝置的訂單之後，以及收到裝置之前，擁有這項資訊。 

## <a name="deployment-checklist"></a>部署檢查清單 

| 階段                             | 參數                                                                                                                                                                                                                           | 詳細資料                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 裝置管理               | <li>Microsoft Azure 訂用帳戶</li><li>Azure Active Directory 圖形 API</li><li>Microsoft Azure 儲存體帳戶</li>|<li>針對 Azure Stack Edge/資料箱閘道啟用，參與者許可權</li><li>確定系統管理員或使用者存取權</li><li>需要存取認證</li> |
| 裝置安裝               | 套件中的電源線。 <br>對我們而言，SVE 18/3 纜線針對 125 V 和15安培分級，具有 NEMA 5 15P 至 C13 (輸入輸出) 連接器已寄出。                                                                                                                                                                                                          | 隨附于裝置。<br>如需詳細資訊，請參閱 [支援的電源線清單（依國家/地區）](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>埠1至少有 1 X 1 GbE 的 RJ-45 網路纜線  </li><li> 埠3、埠4、埠5或埠6至少有 1 X 25 GbE SFP + 銅纜線</li>| 客戶需要購買這些纜線。<br>如需裝置網路卡支援的網路纜線、交換器及收發器的完整清單，請參閱 [>cavium FastlinQ 41000 系列互通性對照表](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) 和 [Mellanox 雙埠 25g ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。| 
| 第一次裝置連接      | 埠1具有固定的 IP (192.168.100.10/24) 用於初始連接。 <li>需要膝上型電腦以直接連線到埠1，並在 192.168.100.0/24 網路上使用 IP 位址。</li><li> 在下列位置使用裝置的本機 UI： `https://192.168.100.10` 進行進一步設定。</li><li> 初始設定完成後，裝置必須至少使用 1 GbE 交換器。 如果連接的交換器不是至少 1 Gbe，將無法存取本機 web UI。</li>|                                                                                                                   |
| 裝置登入                      | 裝置系統管理員密碼必須介於8到16個字元之間。 <br>此密碼必須包含下列其中 3 種字元：大寫、小寫、數字及特殊字元。                                            | 預設密碼是 *Password1* ，其會在第一次登入時過期。                                                     |
| 網路設定                  | 裝置隨附 2 x 1 GbE、4 x 25 GbE 網路埠。 <li>埠1僅用來設定管理設定。 您可以連接及設定一或多個資料埠。 </li><li> 從埠2到埠6的至少一個資料網路介面必須連線至網際網路 (，且可連線至 Azure) 。</li><li> IP 設定支援 DHCP/靜態 IPv4 設定。 | 靜態 IPv4 設定需要 IP、DNS 伺服器和預設閘道。                                                                                                                  |
| 計算網路設定     | <li>Kubernetes 節點需要2個靜態公用 ip，且至少有1個靜態 IP 供 Azure Stack Edge 中樞服務存取計算模組。</li><li>針對需要從 Kubernetes 叢集外部存取的每個額外服務或容器，各需要一個 IP。</li>                                                                                                                       | 僅支援靜態 IPv4 設定。                                                                      |
|  (選擇性) Web proxy 設定     | <li>Web proxy 伺服器 IP/FQDN，埠 </li><li>Web proxy 使用者名稱、密碼</li>                                                                                                                                                                                                    | 目前不支援計算設定。                                                                     |
| 防火牆和埠設定        | 針對裝置 Ip，請使用 [列出的 url 模式和埠](azure-stack-edge-system-requirements.md#networking-port-requirements) 。                                                                                                                                                  |                                                                                                                   |
|  (選用) MAC 位址            | 如果 MAC 位址需要列入允許清單，請從裝置的本機 UI 取得連線埠的位址。 |                                                                                                                   |
|  (選擇性) 網路交換器埠    | 裝置裝載 Hyper-v Vm 以進行計算。 某些網路交換器埠設定預設不會容納這些設定。                                                                                                        |                                                                                                                   |
|  (建議的) 時間設定       | 設定時區、主要 NTP 伺服器、次要 NTP 伺服器。                                                                                                                                                                    | 在區域網路上設定主要和次要 NTP 伺服器。<br>如果本機伺服器無法使用，可以設定公用 NTP 伺服器。                                                    |
|  (選擇性的) 補救伺服器設定 | <li>需要在區域網路上補救伺服器 IP 位址，也就是 WSUS 伺服器的路徑。 </li> | 預設會使用公用 windows update server。|
| 裝置設定                   | <li>在 DNS 組織內註冊的裝置名稱 </li><li>DNS 網域</li> |                                                                                                                   |
|  (選用) 憑證                      | 使用裝置產生的憑證 <br><br> 如果攜帶您自己的憑證，您需要： <li>以 DER 格式的受根信任簽署憑證與 *.cer* </li><li>*Pfx*格式的端點憑證</li>|端點憑證包含 Azure Resource Manager、Blob 儲存體、本機 web UI。                                                                                                                   |
| 啟用                        | 需要 Azure Stack Edge/Data Box Gateway 資源的啟用金鑰。                                                                                                                                                       | 一旦產生，金鑰會在3天內到期。                                                                        |


## <a name="next-steps"></a>後續步驟

準備部署 [Azure Stack Edge 裝置](azure-stack-edge-gpu-deploy-prep.md)。



