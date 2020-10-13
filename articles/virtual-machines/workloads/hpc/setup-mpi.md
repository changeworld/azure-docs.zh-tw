---
title: 設定 HPC 的訊息傳遞介面-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何在 Azure 上設定適用于 HPC 的 MPI。
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28853dd5066be2c40c72dca505c0a68d429f6ca0
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993529"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>設定 HPC 的訊息傳遞介面

[訊息傳遞介面 (MPI) ](https://en.wikipedia.org/wiki/Message_Passing_Interface)是開放程式庫，也是分散式記憶體平行處理的標準。 它通常會在許多 HPC 工作負載中使用。 [支援 RDMA](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上的 HPC 工作負載，可以使用 MPI 來透過低延遲和高頻寬的負載網路進行通訊。

在 Azure 上啟用 SR-IOV 的 VM 大小 (HBv2、HB、HC、NCv3、NDv2) 可讓您幾乎所有的 MPI 類別都能搭配 Mellanox OFED 使用。 在啟用非 SR-IOV 的 Vm 上，支援的 MPI 會使用 Microsoft Network Direct (ND) 介面，在 Vm 之間進行通訊。 因此，只支援 Microsoft MPI (MS-CHAP) 2012 R2 或更新版本和 Intel MPI 5.x 版。 較新版本 (2017、2018) Intel MPI 執行時間程式庫可能會與 Azure RDMA 驅動程式不相容。

針對 SR-IOV 啟用 RDMA 的 [虛擬機器](../../sizes-hpc.md#rdma-capable-instances)， [CentOS-HPC 7.6 版或](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) Marketplace 中的較新版本 VM 映射已針對 rdma 的 OFED 驅動程式優化並預先載入，以及各種常用的 MPI 程式庫和科學計算套件，是開始使用的最簡單方式。

雖然這裡的範例適用于 RHEL/CentOS，但這些步驟都是一般步驟，可用於任何相容的 Linux 作業系統，例如 Ubuntu (16.04、18.04 19.04、20.04) 和 SLES (12 SP4 和 15) 。 其他散發版本上其他 MPI 實作為設定的範例位於 [azhpc 映射](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)存放庫。

> [!NOTE]
> 在啟用 SR-IOV 的 Vm 上執行 MPI 作業需要設定分割區索引鍵， (p 金鑰) 在租使用者中進行隔離和安全性。 請依照 [探索資料 [分割索引鍵](#discover-partition-keys) ] 一節中的步驟，以瞭解如何判斷 p 索引鍵值，並正確設定 MPI 作業的值。

## <a name="ucx"></a>UCX

[整合通訊 X (UCX) ](https://github.com/openucx/ucx) 是適用于 HPC 的通訊 api 架構。 它最適合用於透過未經過的 MPI 通訊，且適用于許多 MPI 的 OpenMPI 和 MPICH。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC X 軟體工具](https://www.mellanox.com/products/hpc-x-toolkit)組包含 UCX 和 HCOLL。

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

執行 HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

如上面所述安裝 UCX。 HCOLL 是 [HPC X 軟體工具](https://www.mellanox.com/products/hpc-x-toolkit) 組的一部分，不需要特殊安裝。

從存放庫中的可用套件安裝 OpenMPI。

```bash
sudo yum install –y openmpi
```

組建 OpenMPI。

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

執行 OpenMPI。

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

檢查您的分割區索引鍵（如上所述）。

## <a name="intel-mpi"></a>Intel MPI

下載您選擇的 [INTEL MPI](https://software.intel.com/mpi-library/choose-download)版本。 根據版本變更 I_MPI_FABRICS 環境變數。 若為 Intel MPI 2018，請使用 `I_MPI_FABRICS=shm:ofa` 和2019，然後使用 `I_MPI_FABRICS=shm:ofi` 。

### <a name="non-sr-iov-vms"></a>非 SR-IOV Vm
針對非 SR-IOV Vm，下載5.x 運行時 [免費評估版](https://registrationcenter.intel.com/en/forms/?productid=1740) 的範例如下：
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
如需安裝步驟，請參閱 [Intel MPI Library 安裝指南](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) \(英文\)。
（選擇性）您可能會想要為最新版本的 Intel MPI)  (所需的非根非偵錯工具進程啟用 ptrace。
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
針對 SUSE Linux Enterprise Server VM 映射版本-SLES 12 SP3 for HPC、SLES 12 SP3 for hpc (Premium) 、SLES 12 SP1 for HPC、SLES 12 SP1 for hpc (Premium) 、SLES 12 SP4 和 SLES 15，會安裝 RDMA 驅動程式，並在 VM 上散發 Intel MPI 套件。 執行下列命令以安裝 Intel MPI：
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH \(英文\)

如上面所述安裝 UCX。 組建 MPICH。

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

正在執行 MPICH。

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

檢查您的分割區索引鍵（如上所述）。

## <a name="mvapich2"></a>MVAPICH2

組建 MVAPICH2。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

正在執行 MVAPICH2。

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI 社區版

安裝適用于 Platform MPI 的必要套件。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

依照安裝程式進行操作。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基準測試

下載 [OSU MPI 基準](http://mvapich.cse.ohio-state.edu/benchmarks/) 核對總和解壓縮。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

使用特定 MPI 程式庫建立基準測試：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基準測試位於 `mpi/` 資料夾底下。


## <a name="discover-partition-keys"></a>探索分割區索引鍵

探索 (p-金鑰) 的分割區索引鍵，以與相同租使用者 (可用性設定組或虛擬機器擴展集) 中的其他 Vm 進行通訊。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

兩者中較大的一個是應搭配 MPI 使用的租使用者金鑰。 範例：如果下列是 p 索引鍵，則0x800b 應該搭配 MPI 使用。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用 default 以外的資料分割 (0x7fff) 分割區索引鍵。 UCX 需要清除 p 索引鍵的 MSB。 例如，將 UCX_IB_PKEY 設定為0x800b 的0x000b。

另請注意，只要租使用者 (可用性設定組或虛擬機器擴展集) 存在，PKEYs 就會維持不變。 即使在新增/刪除節點時也是如此。 新的租使用者會取得不同的 PKEYs。


## <a name="set-up-user-limits-for-mpi"></a>設定 MPI 的使用者限制

設定 MPI 的使用者限制。

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>設定 MPI 的 SSH 金鑰

針對需要的 MPI 類型設定 SSH 金鑰。

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

上述語法假設共用的主目錄，否則 ssh 目錄必須複製到每個節點。

## <a name="next-steps"></a>接下來的步驟

- 深入瞭解 [已啟用的「已啟用](../../sizes-hpc.md#rdma-capable-instances)」 [H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) vm
- 請檢閱 [HB 系列概觀](hb-series-overview.md)和 [HC 系列概觀](hc-series-overview.md)，了解如何以最佳方式設定工作負載以獲得效能和可擴縮性。
- 請參閱 [Azure 運算技術社群部落格](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)的最新公告和一些 HPC 範例和結果。
- 如需執行中 HPC 工作負載較高階的架構檢視，請參閱 [Azure 上的高效能運算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
