---
title: 設定和部署 Azure Stack Edge GPU 的快速入門 | Microsoft Docs
description: 在收到裝置後開始部署 Azure Stack Edge GPU。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to quickly deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 2d1bd7d2a4f066311ea01046a8d71e6ecb52c3f5
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919796"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>快速入門：開始使用搭配 GPU 的 Azure Stack Edge Pro 

本快速入門詳細說明部署 Azure Stack Edge Pro GPU 裝置所需的必要條件和步驟。 快速入門的步驟會在 Azure 入口網站和裝置的本機 Web UI 上執行。 

完成整個程序大約需要 1.5 小時。 如需詳細的逐步指示，請移至[教學課程：準備部署 Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist)。 


## <a name="prerequisites"></a>必要條件

開始部署之前，請確定已符合下列必要條件：

1. Azure Stack Edge Pro GPU 裝置已傳遞至您的站台，並且[拆封](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)並[掛接在機架上](azure-stack-edge-gpu-deploy-install.md#rack-the-device)。 
1. 設定網路，讓您的裝置可連線到[列出的 URL 模式和連接埠](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements)。 
1. 您具有 Azure 訂用帳戶的擁有者或參與者存取權。
1. 在 Azure 入口網站中移至 [首頁] > [訂用帳戶] > [您的訂用帳戶] > [資源提供者]。 搜尋 `Microsoft.DataBoxEdge` 並註冊資源提供者。 如果您要建立 IoT 中樞資源以部署計算工作負載，請重複相關步驟以註冊 `Microsoft.Devices`。
1. 確定您至少有 2 個免費的靜態連續 IP 可供 Kubernetes 節點使用，且至少有 1 個靜態 IP 可用於 IoT Edge 服務。 針對您部署的每個模組或外部服務，您將需要 1 個額外的 IP。
1. 請參閱[部署檢查清單](azure-stack-edge-gpu-deploy-checklist.md)，以了解裝置設定所需的一切。 


## <a name="deployment-steps"></a>部署步驟

1. **安裝**：透過跳接纜線或 USB 乙太網路介面卡，將連接埠 1 連線至用戶端電腦。 透過至少 1 GbE 的交換器和 SFP+ 銅纜線，另外將至少一個裝置連接埠 (連接埠 3 到連接埠 6，最好是 25 GbE) 連線至網際網路。 將隨附的電源線連線至電源供應器，並連線至不同的配電插座。 按下前端面板上的電源按鈕以開啟裝置。  

    請參閱 [Cavium FastlinQ 41000 系列互通性對照表](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)和 [Mellanox 雙連接埠 25G ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)，以了解相容的網路線和交換器。

    以下是部署裝置所需的最低佈線設定：![已連接纜線的裝置背面](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **連線**：使用靜態 IP 位址 **192.168.100.5** 和子網路 **255.255.255.0**，在電腦的乙太網路介面卡上設定 IPv4 設定。 開啟瀏覽器，並經由 https://192.168.100.10 連線至裝置的本機 Web UI。 這可能需要幾分鐘的時間。 當您看到安全性憑證警告時，請繼續進入網頁。

3. **登入**：使用預設密碼 *Password1* 登入裝置。 變更裝置管理員密碼。 密碼必須包含 8 到 16 個字元，並且包含下列其中 3 種字元：大寫、小寫、數值和特殊字元。

4. **設定網路**：如果您的網路中有 DHCP 伺服器，請接受連線資料連接埠的預設 DHCP 設定。 否則，請提供 IP、DNS 伺服器和預設閘道。 請參閱[網路設定](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network)的詳細資訊。

5. **設定計算網路**：啟用裝置上的連接埠以建立虛擬交換器。 在您建立交換器的相同網路中，為 Kubernetes 節點輸入 2 個免費的連續靜態 IP。 提供至少 1 個靜態 IP 讓 IoT Edge Hub 服務存取計算模組，並且為您想要從 Kubernetes 叢集外部存取的每個額外服務或容器提供 1 個靜態 IP。 

    必須要有 Kubernetes 才能部署所有容器化的工作負載。 如需[計算網路設定](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)的詳細資訊，請參閱。

6. **設定 Web Proxy**：如果您在環境中使用 Web Proxy，請在 `http://<web-proxy-server-FQDN>:<port-id>` 中輸入 Web Proxy 伺服器 IP。 將驗證設定為 [無]。 請參閱 [Web Proxy 設定](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy)的詳細資訊。

7. **設定裝置**：輸入裝置名稱和 DNS 網域，或接受預設值。 

8. **設定更新伺服器**：接受預設的 Microsoft Update 伺服器，或指定 Windows Server Update Services (WSUS) 伺服器和伺服器的路徑。 

9. **設定時間設定**：接受預設的時間設定，或設定區域網路上的時區、主要 NTP 伺服器和次要 NTP 伺服器，或設為與公用伺服器相同。

10. **設定憑證**：如果您變更了裝置名稱和 (或) DNS 網域，則必須產生憑證或新增憑證，以啟用裝置。 

    - 若要測試非生產工作負載，請使用[產生憑證選項](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。 
    - 如果您自備憑證 (包括簽署鏈結)，請以適當的格式[新增憑證](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)。 請務必先上傳簽署鏈結。 請參閱[建立憑證](azure-stack-edge-j-series-create-certificates-tool.md)和[透過本機 UI 上傳憑證](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates)。

11. **啟動**：取得啟用金鑰 

    1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 [概觀] > [裝置設定] > [啟用] > [產生金鑰]。 複製金鑰。 
    1. 在本機 Web UI 中移至 [開始使用] > [啟用]，並提供啟用金鑰。 套用金鑰時，啟用裝置需要幾分鐘的時間。 當系統提示您安全地儲存裝置金鑰以供日後復原使用時，請下載 `<device-serial-number>`.json 檔案。 

12. **設定計算**：在 Azure 入口網站中，移至 [概觀] > [裝置]。 確認裝置處於 [線上] 狀態。 在左窗格中，移至 [Edge 計算] > [開始使用] > [設定 Edge 計算] > [計算]。 請提供現有或新的 IoT 中樞服務，並等候約 20 分鐘，讓計算進行設定。 請參閱詳細資訊：[教學課程：在 Azure Stack Edge Pro GPU 裝置上設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)

您已準備就緒，可[透過 IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md)、[透過 `kubectl`](azure-stack-edge-gpu-create-kubernetes-cluster.md) 或[透過已啟用 Azure Arc 的 Kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md) 在裝置上部署計算工作負載！ 若在安裝期間遇到任何問題，請參閱[對裝置問題進行疑難排解]()、[訂單問題](azure-stack-edge-gpu-troubleshoot.md)、[憑證問題](azure-stack-edge-j-series-certificate-troubleshooting.md)或 Kubernetes 問題。 

## <a name="next-steps"></a>後續步驟

[安裝 Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-install.md)



