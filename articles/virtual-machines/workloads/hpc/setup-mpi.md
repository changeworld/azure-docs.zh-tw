---
title: 設定 HPC 的訊息傳遞介面-Azure 虛擬機器 |Microsoft Docs
description: 瞭解如何為 Azure 上的 HPC 設定 MPI。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/04/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1b2d707569221a79ad53f04bcc379f5067ed9b04
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905528"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>設定 HPC 的訊息傳遞介面

[ (MPI) 的訊息傳遞介面](https://en.wikipedia.org/wiki/Message_Passing_Interface)，是分散式記憶體平行處理的開放程式庫和刪除標準。 它通常會在許多 HPC 工作負載中使用。 [支援 RDMA](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm 上的 HPC 工作負載可以使用 MPI，透過低延遲和高頻寬的網路來進行通訊。

Azure (HBv2、HB、HC、NCv3、NDv2) 上的 SR-IOV 已啟用 VM 大小，幾乎可讓 MPI 的任何一種類別與 Mellanox OFED 搭配使用。 在非 SR-IOV 啟用的 Vm 上，支援的 MPI 執行會使用 Microsoft Network Direct (ND) 介面，在 Vm 之間進行通訊。 因此，只支援 Microsoft MPI (MS-MPI) 2012 R2 或更新版本，以及 Intel MPI 5.x 版本。 較新版本 (2017、2018) Intel MPI 執行時間程式庫，可能會與 Azure RDMA 驅動程式不相容。

對於支援 SR-IOV 的[Rdma vm](../../sizes-hpc.md#rdma-capable-instances)，在 Marketplace 中[CentOS-HPC 7.6 版或更新](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)版本的 VM 映射會針對 RDMA 的 OFED 驅動程式和各種常用的 MPI 程式庫和科學運算套件進行優化和預先載入，而且是開始使用的最簡單方式。

雖然這裡的範例適用于 RHEL/CentOS，但這些步驟是一般的，而且可以用於任何相容的 Linux 作業系統，例如 Ubuntu (16.04、18.04 19.04、20.04) 和 SLES (12 SP4 和 15) 。 在其他散發版本上設定其他 MPI 部署的更多範例位於[azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)存放庫。

> [!NOTE]
> 在啟用 SR-IOV 的 Vm 上執行 MPI 作業需要設定分割區索引鍵 (p-金鑰) 跨租使用者進行隔離和安全性。 請遵循[探索資料分割索引鍵](#discover-partition-keys)一節中的步驟，以取得判斷 p 索引鍵值，並針對 MPI 作業正確設定這些值的詳細資訊。

## <a name="ucx"></a>UCX

[整合通訊 X (UCX) ](https://github.com/openucx/ucx)是 HPC 的通訊 api 架構。 它已針對透過「未通過」的 MPI 通訊進行優化，並可搭配許多 MPI 執行，例如 OpenMPI 和 MPICH。

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

[HPC-X 軟體工具](https://www.mellanox.com/products/hpc-x-toolkit)組包含 UCX 和 HCOLL。

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

如上面所述安裝 UCX。 HCOLL 是[HPC X 軟體工具](https://www.mellanox.com/products/hpc-x-toolkit)組的一部分，而且不需要特殊安裝。

從存放庫中提供的封裝安裝 OpenMPI。

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

如先前所述，檢查您的分割區索引鍵。

## <a name="intel-mpi"></a>Intel MPI

[下載 INTEL MPI](https://software.intel.com/mpi-library/choose-download)。

視版本而定，變更 I_MPI_FABRICS 環境變數。 若是 Intel MPI 2018，請使用 `I_MPI_FABRICS=shm:ofa` 2019 的，使用 `I_MPI_FABRICS=shm:ofi` 。

根據預設，進程固定可正常運作15、30和 60 PPN。

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

如先前所述，檢查您的分割區索引鍵。

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

## <a name="platform-mpi-community-edition"></a>平臺 MPI 社區版本

安裝平臺 MPI 所需的套件。

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

遵循安裝程式。

## <a name="osu-mpi-benchmarks"></a>OSU MPI 基準測試

下載[OSU MPI 基準](http://mvapich.cse.ohio-state.edu/benchmarks/)核對總和解壓縮。

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

使用特定 MPI 程式庫建立效能評定：

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 基準測試位於 `mpi/` 資料夾底下。


## <a name="discover-partition-keys"></a>探索分割區索引鍵

探索資料分割索引鍵 (p-key) ，以便與相同租使用者中的其他 Vm （ (可用性設定組或虛擬機器擴展集) ）進行通訊。

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

兩者中的較大者就是應該搭配 MPI 使用的租使用者金鑰。 範例：如果下列是 p 按鍵，0x800b 應該與 MPI 搭配使用。

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

使用預設 (0x7fff) 分割區索引鍵以外的資料分割。 UCX 需要清除 p 金鑰的 MSB。 例如，將 UCX_IB_PKEY 設定為0x800b 的0x000b。

另請注意，只要租使用者 (可用性設定組或虛擬機器擴展集) 存在，PKEYs 就會維持不變。 即使新增/刪除節點也是如此。 新的租使用者會取得不同的 PKEYs。


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

為需要的 MPI 類型設定 SSH 金鑰。

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

上述語法假設有共用的主目錄，否則就必須將 ssh 目錄複寫到每個節點。

## <a name="next-steps"></a>後續步驟

- 瞭解[已啟用無功能](../../sizes-hpc.md#rdma-capable-instances)的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm
- 請參閱[HB 系列總覽](hb-series-overview.md)和[HC 系列總覽](hc-series-overview.md)，以瞭解如何針對效能和擴充性，以最佳方式設定工作負載。
- 閱讀[Azure 計算技術小組的 blog](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)，瞭解最新的公告和一些 HPC 範例和結果。
- 如需執行 HPC 工作負載的更高層級架構視圖，請參閱[Azure 上的高效能運算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
