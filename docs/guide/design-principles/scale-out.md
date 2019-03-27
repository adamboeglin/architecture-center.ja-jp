---
title: スケールアウトのための設計
titleSuffix: Azure Application Architecture Guide
description: クラウド アプリケーションは、水平方向のスケーリングで設計する必要があります。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 32ea1e7dc732c819783ad2fc06dbcffd75685d23
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243083"
---
# <a name="design-to-scale-out"></a><span data-ttu-id="85efb-103">スケールアウトのための設計</span><span class="sxs-lookup"><span data-stu-id="85efb-103">Design to scale out</span></span>

## <a name="design-your-application-so-that-it-can-scale-horizontally"></a><span data-ttu-id="85efb-104">水平方向に拡張できるようにアプリケーションを設計する</span><span class="sxs-lookup"><span data-stu-id="85efb-104">Design your application so that it can scale horizontally</span></span>

<span data-ttu-id="85efb-105">クラウドの主な利点は、柔軟なスケーリングです。必要なだけの容量を使用でき、負荷が増加すればスケールアウトし、余分な容量が必要ない場合にはスケールインします。</span><span class="sxs-lookup"><span data-stu-id="85efb-105">A primary advantage of the cloud is elastic scaling &mdash; the ability to use as much capacity as you need, scaling out as load increases, and scaling in when the extra capacity is not needed.</span></span> <span data-ttu-id="85efb-106">需要に応じて新規インスタンスを追加または削除し、水平方向に拡張できるようにアプリケーションを設計します。</span><span class="sxs-lookup"><span data-stu-id="85efb-106">Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

## <a name="recommendations"></a><span data-ttu-id="85efb-107">Recommendations</span><span class="sxs-lookup"><span data-stu-id="85efb-107">Recommendations</span></span>

<span data-ttu-id="85efb-108">**インスタンスの粘性の阻止**。</span><span class="sxs-lookup"><span data-stu-id="85efb-108">**Avoid instance stickiness**.</span></span> <span data-ttu-id="85efb-109">粘性または*セッション親和性*とは、同じクライアントからの要求は常に同じサーバーにルーティングされることを指します。</span><span class="sxs-lookup"><span data-stu-id="85efb-109">Stickiness, or *session affinity*, is when requests from the same client are always routed to the same server.</span></span> <span data-ttu-id="85efb-110">粘性により、アプリケーションのスケールアウトの機能が制限されます。たとえば、大量のユーザーからのトラフィックがインスタンス間で分散されません。</span><span class="sxs-lookup"><span data-stu-id="85efb-110">Stickiness limits the application's ability to scale out. For example, traffic from a high-volume user will not be distributed across instances.</span></span> <span data-ttu-id="85efb-111">粘性の原因となるものには、メモリでのセッション状態の保存、および暗号化用のマシン固有のキーの使用などがあります。</span><span class="sxs-lookup"><span data-stu-id="85efb-111">Causes of stickiness include storing session state in memory, and using machine-specific keys for encryption.</span></span> <span data-ttu-id="85efb-112">あらゆるインスタンスであらゆる要求を処理できることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="85efb-112">Make sure that any instance can handle any request.</span></span>

<span data-ttu-id="85efb-113">**ボトルネックの特定**</span><span class="sxs-lookup"><span data-stu-id="85efb-113">**Identify bottlenecks**.</span></span> <span data-ttu-id="85efb-114">スケールアウトは、すべてのパフォーマンスの問題が修正されるマジックではありません。</span><span class="sxs-lookup"><span data-stu-id="85efb-114">Scaling out isn't a magic fix for every performance issue.</span></span> <span data-ttu-id="85efb-115">たとえば、バックエンドのデータベースがボトルネックである場合、Web サーバーを追加しても改善されません。</span><span class="sxs-lookup"><span data-stu-id="85efb-115">For example, if your backend database is the bottleneck, it won't help to add more web servers.</span></span> <span data-ttu-id="85efb-116">問題に対してより多くのインスタンスを投入する前に、まずシステムでボトルネックを特定して解決します。</span><span class="sxs-lookup"><span data-stu-id="85efb-116">Identify and resolve the bottlenecks in the system first, before throwing more instances at the problem.</span></span> <span data-ttu-id="85efb-117">システムのステートフルな部分は、最もボトルネックの原因となりやすいものです。</span><span class="sxs-lookup"><span data-stu-id="85efb-117">Stateful parts of the system are the most likely cause of bottlenecks.</span></span>

<span data-ttu-id="85efb-118">**スケーラビリティの要件によるワークロードの分解**。</span><span class="sxs-lookup"><span data-stu-id="85efb-118">**Decompose workloads by scalability requirements.**</span></span>  <span data-ttu-id="85efb-119">アプリケーションは多くの場合、異なるスケーリング要件がある、複数のワークロードで構成されます。</span><span class="sxs-lookup"><span data-stu-id="85efb-119">Applications often consist of multiple workloads, with different requirements for scaling.</span></span> <span data-ttu-id="85efb-120">たとえば、公開サイトと、それとは別に管理サイトがあるアプリケーションがあります。</span><span class="sxs-lookup"><span data-stu-id="85efb-120">For example, an application might have a public-facing site and a separate administration site.</span></span> <span data-ttu-id="85efb-121">公開サイトでは突然のトラフィック増加が発生する可能性がある一方、管理サイトの負荷はより小さな、より予測がしやすいものです。</span><span class="sxs-lookup"><span data-stu-id="85efb-121">The public site may experience sudden surges in traffic, while the administration site has a smaller, more predictable load.</span></span>

<span data-ttu-id="85efb-122">**多くのリソースを消費するタスクのオフロード**。</span><span class="sxs-lookup"><span data-stu-id="85efb-122">**Offload resource-intensive tasks.**</span></span> <span data-ttu-id="85efb-123">多くの CPU または I/O リソースを必要とするタスクは、可能であれば[バックグラウンド ジョブ][background-jobs]に移動し、ユーザーの要求を処理しているフロント エンド上の負荷を最小限にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85efb-123">Tasks that require a lot of CPU or I/O resources should be moved to [background jobs][background-jobs] when possible, to minimize the load on the front end that is handling user requests.</span></span>

<span data-ttu-id="85efb-124">**組み込みの自動スケール機能の使用**。</span><span class="sxs-lookup"><span data-stu-id="85efb-124">**Use built-in autoscaling features**.</span></span> <span data-ttu-id="85efb-125">多くの Azure コンピューティング サービスでは、自動スケーリングの組み込みサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="85efb-125">Many Azure compute services have built-in support for autoscaling.</span></span> <span data-ttu-id="85efb-126">アプリケーションに予測可能な通常のワークロードがある場合は、スケジュールに基づいてスケールアウトします。</span><span class="sxs-lookup"><span data-stu-id="85efb-126">If the application has a predictable, regular workload, scale out on a schedule.</span></span> <span data-ttu-id="85efb-127">たとえば、営業時間内にスケールアウトします。</span><span class="sxs-lookup"><span data-stu-id="85efb-127">For example, scale out during business hours.</span></span> <span data-ttu-id="85efb-128">ワークロードが予測可能でない場合は、CPU や要求キューの長さなどのパフォーマンス メトリックを使用して、自動スケーリングをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="85efb-128">Otherwise, if the workload is not predictable, use performance metrics such as CPU or request queue length to trigger autoscaling.</span></span> <span data-ttu-id="85efb-129">自動スケールのベスト プラクティスについては、「[自動スケール][autoscaling]」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85efb-129">For autoscaling best practices, see [Autoscaling][autoscaling].</span></span>

<span data-ttu-id="85efb-130">**クリティカルなワークロードの積極的な自動スケーリングの検討**。</span><span class="sxs-lookup"><span data-stu-id="85efb-130">**Consider aggressive autoscaling for critical workloads**.</span></span> <span data-ttu-id="85efb-131">クリティカルなワークロードの場合、ニーズには常に事前に対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85efb-131">For critical workloads, you want to keep ahead of demand.</span></span> <span data-ttu-id="85efb-132">負荷の高い場所に新しいインスタンスを追加して過度のトラフィックを処理し、段階的にスケールバックすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85efb-132">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

<span data-ttu-id="85efb-133">**スケールインの設計**。</span><span class="sxs-lookup"><span data-stu-id="85efb-133">**Design for scale in**.</span></span>  <span data-ttu-id="85efb-134">柔軟なスケールでは、インスタンスが削除されると、アプリケーションにスケールインの期間が生じることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85efb-134">Remember that with elastic scale, the application will have periods of scale in, when instances get removed.</span></span> <span data-ttu-id="85efb-135">アプリケーションでは、削除中のインスタンスを適切に処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85efb-135">The application must gracefully handle instances being removed.</span></span> <span data-ttu-id="85efb-136">スケールインを処理する方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="85efb-136">Here are some ways to handle scalein:</span></span>

- <span data-ttu-id="85efb-137">シャットダウン イベント (使用可能な場合) をリッスンし、正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="85efb-137">Listen for shutdown events (when available) and shut down cleanly.</span></span>
- <span data-ttu-id="85efb-138">サービスのクライアントまたはコンシューマーは一時的な障害の処理に対応し、再試行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85efb-138">Clients/consumers of a service should support transient fault handling and retry.</span></span>
- <span data-ttu-id="85efb-139">実行時間の長いタスクの場合は、チェックポイントまたは[パイプとフィルター処理][pipes-filters-pattern]パターンを使用して、作業を分割することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="85efb-139">For long-running tasks, consider breaking up the work, using checkpoints or the [Pipes and Filters][pipes-filters-pattern] pattern.</span></span>
- <span data-ttu-id="85efb-140">作業項目をキューに配置して、インスタンスが処理の最中に削除された場合は別のインスタンスが作業を取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="85efb-140">Put work items on a queue so that another instance can pick up the work, if an instance is removed in the middle of processing.</span></span>

<!-- links -->

[autoscaling]: ../../best-practices/auto-scaling.md
[background-jobs]: ../../best-practices/background-jobs.md
[pipes-filters-pattern]: ../../patterns/pipes-and-filters.md
