---
title: オンプレミスから Azure への復旧
titleSuffix: Azure Resiliency Technical Guidance
description: オンプレミス インフラストラクチャから Azure への復旧システムとその設計について説明します。
author: adamglick
ms.date: 08/18/2016
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: seojan19, resiliency
ms.openlocfilehash: 768e53e1024533b384c610378385c96d88d8571f
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248547"
---
[!INCLUDE [header](../_includes/header.md)]

# <a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a><span data-ttu-id="71fbf-103">Azure の回復性技術ガイダンス:オンプレミスから Azure への復旧</span><span class="sxs-lookup"><span data-stu-id="71fbf-103">Azure resiliency technical guidance: Recovery from on-premises to Azure</span></span>

<span data-ttu-id="71fbf-104">Azure は、高可用性とディザスター リカバリーの目的で、オンプレミスのデータセンターから Azure への拡張を可能にする包括的なサービスのセットを提供します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-104">Azure provides a comprehensive set of services for enabling the extension of an on-premises datacenter to Azure for high availability and disaster recovery purposes:</span></span>

- <span data-ttu-id="71fbf-105">**ネットワーク**:仮想プライベート ネットワークでは、オンプレミスのネットワークをクラウドに安全に拡張できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-105">**Networking**: With a virtual private network, you securely extend your on-premises network to the cloud.</span></span>
- <span data-ttu-id="71fbf-106">**コンピューティング**:Hyper-V をオンプレミスで使用しているお客様は、既存の仮想マシン (VM) を Azure にリフト アンド シフトすることができます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-106">**Compute**: Customers using Hyper-V on-premises can “lift and shift” existing virtual machines (VMs) to Azure.</span></span>
- <span data-ttu-id="71fbf-107">**ストレージ**:StorSimple でファイル システムを Azure Storage に拡張します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-107">**Storage**: StorSimple extends your file system to Azure Storage.</span></span> <span data-ttu-id="71fbf-108">Azure Backup サービスでは、ファイルや SQL データベースを Azure Storage にバックアップできます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-108">The Azure Backup service provides backup for files and SQL databases to Azure Storage.</span></span>
- <span data-ttu-id="71fbf-109">**データベースのレプリケーション**:SQL Server 2014 (またはそれ以降) の可用性グループでは、オンプレミスのデータに対して高可用性とディザスター リカバリーを実装できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-109">**Database replication**: With SQL Server 2014 (or later) Availability Groups, you can implement high availability and disaster recovery for your on-premises data.</span></span>

## <a name="networking"></a><span data-ttu-id="71fbf-110">ネットワーク</span><span class="sxs-lookup"><span data-stu-id="71fbf-110">Networking</span></span>

<span data-ttu-id="71fbf-111">Azure Virtual Network を使用すると、論理的に切り離されたセクションを Azure 内に作成し、IPsec 接続を使用してオンプレミス データセンターまたは単一のクライアント コンピューターに安全に接続できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-111">You can use Azure Virtual Network to create a logically isolated section in Azure and securely connect it to your on-premises datacenter or a single client machine by using an IPsec connection.</span></span> <span data-ttu-id="71fbf-112">Virtual Network を使用すると、Windows Server、メインフレーム、UNIX で実行中のシステムを含むオンプレミス システムにあるデータおよびアプリケーションへの接続性を保ちながら、Azure のスケーラブルなオンデマンドのインフラストラクチャを活用できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-112">With Virtual Network, you can take advantage of the scalable, on-demand infrastructure in Azure while providing connectivity to data and applications on-premises, including systems running on Windows Server, mainframes, and UNIX.</span></span> <span data-ttu-id="71fbf-113">詳細については、 [Azure Network のドキュメント](/azure/virtual-network/virtual-networks-overview/) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-113">See [Azure networking documentation](/azure/virtual-network/virtual-networks-overview/) for more information.</span></span>

## <a name="compute"></a><span data-ttu-id="71fbf-114">Compute</span><span class="sxs-lookup"><span data-stu-id="71fbf-114">Compute</span></span>

<span data-ttu-id="71fbf-115">Hyper-V をオンプレミスで使用している場合は、"リフトとシフト" によって既存の仮想マシンを Azure、および Windows Server 2012 (またはそれ以降) を実行するサービス プロバイダーに移行できます。VM に変更を加えたり、VM 形式を変換したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="71fbf-115">If you're using Hyper-V on-premises, you can “lift and shift” existing virtual machines to Azure and service providers running Windows Server 2012 (or later), without making changes to the VM or converting VM formats.</span></span> <span data-ttu-id="71fbf-116">詳細については、「[Azure 仮想マシン用のディスクと VHD について](/azure/virtual-machines/virtual-machines-linux-about-disks-vhds/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-116">For more information, see [About disks and VHDs for Azure virtual machines](/azure/virtual-machines/virtual-machines-linux-about-disks-vhds/?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).</span></span>

## <a name="azure-site-recovery"></a><span data-ttu-id="71fbf-117">Azure Site Recovery</span><span class="sxs-lookup"><span data-stu-id="71fbf-117">Azure Site Recovery</span></span>

<span data-ttu-id="71fbf-118">サービスとしてのディザスター リカバリー (DRaaS) を希望する場合は、[Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)を利用できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-118">If you want disaster recovery as a service (DRaaS), Azure provides [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).</span></span> <span data-ttu-id="71fbf-119">Azure Site Recovery は、VMware、Hyper-V、および物理サーバーの包括的な保護を提供します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-119">Azure Site Recovery offers comprehensive protection for VMware, Hyper-V, and physical servers.</span></span> <span data-ttu-id="71fbf-120">Azure Site Recovery では、別のオンプレミス サーバーまたは Azure を復旧サイトとして使用することができます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-120">With Azure Site Recovery, you can use another on-premises server or Azure as your recovery site.</span></span> <span data-ttu-id="71fbf-121">Azure Site Recovery の詳細については、 [Azure Site Recovery のドキュメント](https://azure.microsoft.com/documentation/services/site-recovery/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-121">For more information on Azure Site Recovery, see the [Azure Site Recovery documentation](https://azure.microsoft.com/documentation/services/site-recovery/).</span></span>

## <a name="storage"></a><span data-ttu-id="71fbf-122">Storage</span><span class="sxs-lookup"><span data-stu-id="71fbf-122">Storage</span></span>

<span data-ttu-id="71fbf-123">オンプレミスのデータのバックアップ サイトとして Azure を使用する場合、いくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="71fbf-123">There are several options for using Azure as a backup site for on-premises data.</span></span>

### <a name="storsimple"></a><span data-ttu-id="71fbf-124">StorSimple</span><span class="sxs-lookup"><span data-stu-id="71fbf-124">StorSimple</span></span>

<span data-ttu-id="71fbf-125">StorSimple は、オンプレミスのアプリケーション用のクラウド ストレージを安全かつ透過的に統合します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-125">StorSimple securely and transparently integrates cloud storage for on-premises applications.</span></span> <span data-ttu-id="71fbf-126">また、パフォーマンスの高い階層型のローカルとクラウドのストレージ、ライブ アーカイブ、クラウドベースのデータ保護、障害復旧を実現する単一のアプライアンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-126">It also offers a single appliance that delivers high-performance tiered local and cloud storage, live archiving, cloud-based data protection, and disaster recovery.</span></span> <span data-ttu-id="71fbf-127">詳細については、 [StorSimple 製品のページ](https://azure.microsoft.com/services/storsimple/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-127">For more information, see the [StorSimple product page](https://azure.microsoft.com/services/storsimple/).</span></span>

### <a name="azure-backup"></a><span data-ttu-id="71fbf-128">Azure Backup</span><span class="sxs-lookup"><span data-stu-id="71fbf-128">Azure Backup</span></span>

<span data-ttu-id="71fbf-129">Azure Backup を使用すると、Windows Server 2012 (またはそれ以降)、Windows Server 2012 Essentials (またはそれ以降)、および System Center 2012 Data Protection Manager (またはそれ以降) で、使い慣れたバックアップ ツールによるクラウドでのバックアップを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-129">Azure Backup enables cloud backups by using the familiar backup tools in Windows Server 2012 (or later), Windows Server 2012 Essentials (or later), and System Center 2012 Data Protection Manager (or later).</span></span> <span data-ttu-id="71fbf-130">これらのツールにより、バックアップのストレージの場所がローカル ディスクか Azure Storage かに関係なく、バックアップ管理のためのワークフローを確立できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-130">These tools provide a workflow for backup management that is independent of the storage location of the backups, whether a local disk or Azure Storage.</span></span> <span data-ttu-id="71fbf-131">データがクラウドにバックアップされたのち、許可されているユーザーは簡単にバックアップをサーバーに回復できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-131">After data is backed up to the cloud, authorized users can easily recover backups to any server.</span></span>

<span data-ttu-id="71fbf-132">増分バックアップにより、ファイルの変更部分だけがクラウドに転送されます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-132">With incremental backups, only changes to files are transferred to the cloud.</span></span> <span data-ttu-id="71fbf-133">そうすることで、ストレージの効率的な使用、帯域幅の消費の削減、および複数のバージョンのデータの特定の時点への復旧が可能になります。</span><span class="sxs-lookup"><span data-stu-id="71fbf-133">This helps to efficiently use storage space, reduce bandwidth consumption, and support point-in-time recovery of multiple versions of the data.</span></span> <span data-ttu-id="71fbf-134">データ保持ポリシー、データ圧縮、データ転送スロットルなどの追加機能を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-134">You can also choose to use additional features, such as data retention policies, data compression, and data transfer throttling.</span></span> <span data-ttu-id="71fbf-135">Azure をバックアップの場所として使用すると、バックアップが自動的に "オフサイト" になるという明らかな利点があります。</span><span class="sxs-lookup"><span data-stu-id="71fbf-135">Using Azure as the backup location has the obvious advantage that the backups are automatically “offsite”.</span></span> <span data-ttu-id="71fbf-136">これにより、オンサイトのバックアップ メディアを保護するための追加の要件が不要になります。</span><span class="sxs-lookup"><span data-stu-id="71fbf-136">This eliminates the extra requirements to secure and protect on-site backup media.</span></span>

<span data-ttu-id="71fbf-137">詳細については、「[Azure Backup とは](/azure/backup/backup-introduction-to-azure-backup/)」と「[Azure Backup への DPM データの構成](https://technet.microsoft.com/library/jj728752.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-137">For more information, see [What is Azure Backup?](/azure/backup/backup-introduction-to-azure-backup/) and [Configure Azure Backup for DPM data](https://technet.microsoft.com/library/jj728752.aspx).</span></span>

## <a name="database"></a><span data-ttu-id="71fbf-138">Database</span><span class="sxs-lookup"><span data-stu-id="71fbf-138">Database</span></span>

<span data-ttu-id="71fbf-139">ハイブリッド IT 環境内の SQL Server データベースのディザスター リカバリー ソリューションを実現するには、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布、および Azure Blob Storage によるバックアップと復元を使用します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-139">You can have a disaster recovery solution for your SQL Server databases in a hybrid-IT environment by using AlwaysOn Availability Groups, database mirroring, log shipping, and backup and restore with Azure Blob storage.</span></span> <span data-ttu-id="71fbf-140">これらすべてのソリューションは、Azure Virtual Machines で実行される SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-140">All of these solutions use SQL Server running on Azure Virtual Machines.</span></span>

<span data-ttu-id="71fbf-141">AlwaysOn 可用性グループは、オンプレミスとクラウドの両方にデータベースのレプリカが存在するハイブリッド IT 環境で使用できます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-141">AlwaysOn Availability Groups can be used in a hybrid-IT environment where database replicas exist both on-premises and in the cloud.</span></span> <span data-ttu-id="71fbf-142">これを次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-142">This is shown in the following diagram.</span></span>

![SQL Server AlwaysOn Availability Groups in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

<span data-ttu-id="71fbf-144">データベース ミラーリングは、証明書ベースのセットアップにおいて、オンプレミスのサーバーとクラウドにまたがって実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-144">Database mirroring can also span on-premises servers and the cloud in a certificate-based setup.</span></span> <span data-ttu-id="71fbf-145">次の図にこの概念を示します。</span><span class="sxs-lookup"><span data-stu-id="71fbf-145">The following diagram illustrates this concept.</span></span>

![SQL Server database mirroring in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

<span data-ttu-id="71fbf-147">ログ配布を使用して、オンプレミスのデータベースを Azure 仮想マシン内の SQL Server データベースと同期することができます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-147">Log shipping can be used to synchronize an on-premises database with a SQL Server database in an Azure virtual machine.</span></span>

![SQL Server log shipping in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

<span data-ttu-id="71fbf-149">最後に、オンプレミスのデータベースを Azure Blob Storage に直接バックアップできます。</span><span class="sxs-lookup"><span data-stu-id="71fbf-149">Finally, you can back up an on-premises database directly to Azure Blob storage.</span></span>

![Back up SQL Server to Azure Blob storage in a hybrid cloud architecture](./images/technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

<span data-ttu-id="71fbf-151">詳細については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)」と「[Azure Virtual Machines における SQL Server のバックアップと復元](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71fbf-151">For more information, see [High availability and disaster recovery for SQL Server in Azure virtual machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/) and [Backup and restore for SQL Server in Azure virtual machines](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery/).</span></span>

## <a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a><span data-ttu-id="71fbf-152">Microsoft Azure でのオンプレミスの回復のためのチェックリスト</span><span class="sxs-lookup"><span data-stu-id="71fbf-152">Checklists for on-premises recovery in Microsoft Azure</span></span>

<!-- markdownlint-disable MD024 -->

### <a name="networking"></a><span data-ttu-id="71fbf-153">ネットワーク</span><span class="sxs-lookup"><span data-stu-id="71fbf-153">Networking</span></span>

1. <span data-ttu-id="71fbf-154">このドキュメントの「ネットワーク」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-154">Review the Networking section of this document.</span></span>
2. <span data-ttu-id="71fbf-155">Virtual Network を使用してオンプレミスをクラウドに安全に接続する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-155">Use Virtual Network to securely connect on-premises to the cloud.</span></span>

### <a name="compute"></a><span data-ttu-id="71fbf-156">Compute</span><span class="sxs-lookup"><span data-stu-id="71fbf-156">Compute</span></span>

1. <span data-ttu-id="71fbf-157">このドキュメントの「計算」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-157">Review the Compute section of this document.</span></span>
2. <span data-ttu-id="71fbf-158">VM を Hyper-V と Azure 間に再配置する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-158">Relocate VMs between Hyper-V and Azure.</span></span>

### <a name="storage"></a><span data-ttu-id="71fbf-159">Storage</span><span class="sxs-lookup"><span data-stu-id="71fbf-159">Storage</span></span>

1. <span data-ttu-id="71fbf-160">このドキュメントの「Storage」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-160">Review the Storage section of this document.</span></span>
2. <span data-ttu-id="71fbf-161">クラウド ストレージを使用するために StorSimple サービスを利用する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-161">Take advantage of StorSimple services for using cloud storage.</span></span>
3. <span data-ttu-id="71fbf-162">Azure Backup サービスを使用する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-162">Use the Azure Backup service.</span></span>

### <a name="database"></a><span data-ttu-id="71fbf-163">Database</span><span class="sxs-lookup"><span data-stu-id="71fbf-163">Database</span></span>

1. <span data-ttu-id="71fbf-164">このドキュメントの「データベース」セクションを確認する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-164">Review the Database section of this document.</span></span>
2. <span data-ttu-id="71fbf-165">Azure VM でバックアップとしての SQL Server の使用を検討する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-165">Consider using SQL Server on Azure VMs as the backup.</span></span>
3. <span data-ttu-id="71fbf-166">AlwaysOn 可用性グループを設定する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-166">Set up AlwaysOn Availability Groups.</span></span>
4. <span data-ttu-id="71fbf-167">証明書ベースのデータベース ミラーリングを構成する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-167">Configure certificate-based database mirroring.</span></span>
5. <span data-ttu-id="71fbf-168">ログ配布を使用する。</span><span class="sxs-lookup"><span data-stu-id="71fbf-168">Use log shipping.</span></span>
6. <span data-ttu-id="71fbf-169">オンプレミスのデータベースを Azure Blob Storage にバックアップする。</span><span class="sxs-lookup"><span data-stu-id="71fbf-169">Back up on-premises databases to Azure Blob storage.</span></span>

<!-- markdownlint-enable MD024 -->
