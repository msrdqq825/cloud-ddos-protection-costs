# cloud ddos protection: How It Works, What It Really Costs, and a Host That Bundles 60Gbps In for Free

DDoS-for-hire services cost a few dollars a month. Dealing with the aftermath of an attack doesn't. That gap is why "cloud ddos protection" gets searched so heavily, and why the market around it is a mess of enterprise quotes, per-gigabyte scrubbing fees, and free tiers that quietly stop helping the moment an attack actually lands.

This guide covers the practical side: how cloud-based mitigation works, which pricing model you should be wary of, how the major options stack up on entry price, and when a hosting provider with mitigation built into its own network beats bolting a separate service in front of your servers. Along the way we'll look at Sharktech, a hosting provider that has run its own DDoS mitigation network since 2003 and currently includes 60Gbps of protection with every hosted plan.

## What Cloud DDoS Protection Actually Does

The concept is simple: your traffic flows through the provider's network instead of straight to your server. Malicious packets get filtered out along the way, and only clean traffic reaches you. The execution is where providers differ.

A standard cloud mitigation pipeline runs four stages. First, **detection** — distinguishing an attack from a legitimate traffic spike, using IP reputation and known attack patterns, so a product launch doesn't get throttled like a botnet. Second, **response** — dropping malicious traffic at the network edge, whether that's volumetric junk like NTP or memcached amplification at Layer 3/4, or HTTP floods at Layer 7. Third, **routing** — breaking remaining traffic into manageable chunks so no single path saturates. Fourth, **adaptation** — learning from the attack so repeat attempts hit harder filters.

Two deployment models matter for buyers:

- **Always-on**: all traffic permanently routes through the scrubbing network. Zero activation delay, but you pay the latency and dependency cost on every request, attack or not.
- **On-demand**: traffic only diverts when an attack is detected. Cleaner day-to-day performance, but there's a detection-and-reroute gap at the start of every incident.

Neither is universally better. Latency-sensitive workloads (game servers, VoIP, trading) often can't tolerate always-on rerouting through distant scrubbing centers; a small business website usually can, and benefits from it.

The other distinction worth understanding: **CDN-edge protection versus network-level protection**. Services like Cloudflare sit in front of web traffic at hundreds of edge locations — excellent for HTTP/HTTPS, but non-HTTP services (game servers, VoIP endpoints, custom TCP APIs, DNS) don't pass through a web proxy at all. Network-level mitigation, the kind a hosting provider runs on its own backbone, filters every packet regardless of protocol. That difference decides which products can even help you.

## The Pricing Models That Decide Your Bill

Cloud DDoS protection pricing falls into three buckets, and picking the wrong one is the most expensive mistake in this category.

**Flat monthly pricing.** You pay a fixed fee, attacks or not. Predictable, easy to budget, and increasingly common at the entry level — Cloudflare includes unmetered Layer 3/4 protection even on its free plan, with paid tiers at roughly $20/month (Pro) and $200/month (Business).

**Usage-based pricing.** Billed per gigabyte scrubbed, per request, or per resource protected. Fine in calm months, dangerous during incidents — an attack is precisely when your traffic metrics explode, and some models pass that cost straight to you. Before signing anything, check whether the provider charges for attack traffic or only legitimate traffic.

**Enterprise quotes.** Akamai Prolexic, the SLA-backed scrubbing service with a 24/7 human response team, typically starts around $30,000/year in committed contracts. AWS Shield Advanced runs $3,000/month before data transfer fees. Azure DDoS Protection Standard lists at $2,944/month. These are legitimate products for financial services, healthcare, and infrastructure with regulatory requirements — and overkill for a game community or a mid-size web app.

For context, here's what entry pricing currently looks like across the well-known names (figures commonly cited as of mid-2026):

| Provider | Entry price | Model | Catch |
| --- | --- | --- | --- |
| Cloudflare Free | $0 | Flat, unmetered L3/L4 | Web traffic only; community-only support on free tier |
| Cloudflare Pro/Business | $20 / $200 per month | Flat | Non-HTTP needs Magic Transit (enterprise pricing) |
| AWS Shield Standard / Advanced | $0 / $3,000 per month | Flat + usage | AWS-native workloads only |
| Azure DDoS Basic / Standard | $0 / $2,944 per month | Flat + per-resource | Azure-native workloads only |
| Imperva Application Security | ~$368 per site/month | Flat | Bundles WAF and bot management you may not want |
| Sucuri | ~$200 per year per site | Flat | Websites only, not infrastructure |
| Akamai Prolexic | ~$30,000 per year | Enterprise contract | Serious money, serious SLA |

The pattern to notice: hyperscaler protection is either free-but-basic or enterprise-priced, web-proxy services don't cover non-HTTP traffic, and dedicated scrubbing starts at four figures. That leaves a gap in the middle — anyone running game servers, VoIP, or mixed workloads on a real budget — and that's the gap network-level providers aim at.

## Where Bundled Network-Level Protection Fits

If your workloads are already hosted somewhere, there's a third path that skips the separate-service math entirely: a hosting provider whose own network does the filtering.

Sharktech is one of the longer-running examples. The company has operated since 2003, runs its own ISP (AS46844, with peering visible on public exchange data), and handles mitigation in-house across five data centers: Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam. The relevant part for this discussion is what's included versus what's extra:

- **60Gbps of DDoS mitigation comes standard** with every hosted service — VPS, cloud, and bare-metal alike. Not a trial tier, not a first-month teaser. The $7.95 VPS and a $500 dedicated server get the same baseline.
- **1.1Tbps of total mitigation capacity** across the five facilities, following router upgrades the company completed across all sites. Capacity matters less for average attacks and more for absorbing simultaneous or unusually large ones.
- **A 100Gbps upgrade at $39/month per single IP**, available on dedicated and colocation servers. That price is a published, standing rate — not a limited promo — and it's aggressively cheap compared to dedicated scrubbing services.
- **Always-on filtering**, not null-routing. Cheap hosts advertising "DDoS protection" sometimes just black-hole your IP when traffic spikes. Sharktech's system routes attack traffic to its firewalls, filters it, and forwards clean traffic through, which is a categorically different product.

The filter list covers the attack vectors that actually show up in the wild: UDP floods, TCP SYN floods, HTTP and HTTP POST floods, ICMP floods, Slowloris, NTP and DNS amplification, ACK floods, SSDP, Memcached, SNMP, and Chargen reflection, NXDomain, Ping of Death, and Smurf variants. If you're comparing providers, that list is worth checking line by line — a lot of "protected" budget hosts handle a few of these and null-route the rest.

Is 60Gbps per service a lot? Context helps. Cloudflare's Q1 2025 threat report found 99% of Layer 3/4 attacks measured under 1 Gbps. Zayo reported attacks growing roughly 70% larger year over year into 2025, with government-sector targets averaging 5.5 Gbps. In other words: nearly every attack a normal server faces is comfortably inside 60Gbps; the big capacity number exists for the outliers, and the 100Gbps upgrade exists for people who attract outliers regularly — gaming hosts, controversial sites, extortion targets. One Sharktech customer, a WordPress developer, documented a year on the service after migrating a site under constant competitor attacks: the standard tier stopped the attacks until they grew large enough to overwhelm it, at which point upgrading to multi-data-center protection stopped them again.

For servers hosted elsewhere entirely, Sharktech also runs a **Remote Network DDoS Protection** service — an external BGP session plus GRE tunnel that routes your traffic through their scrubbing network without migrating anything. Requirements are real: you need at least a /24 IP block announced under your own name and a device (soft router is fine) that can hold the BGP and GRE sessions. Pricing is quote-based, scoped to your traffic profile. If you want to explore that route rather than migrating hosting, 👉 request a Remote Network DDoS Protection quote and the team will scope it to your actual workload.

## Plans and Pricing: The Full Lineup

Everything below is pulled from Sharktech's current order pages. Cloud and VPS plans include the 60Gbps standard mitigation; bare-metal plans include it too, with the 100Gbps option configurable at checkout for $39/month per IP.

### Cloud, VPS, and Managed Plans

| Plan | Core specs | DDoS protection | Billing cycle | Price (USD) | Order |
| --- | --- | --- | --- | --- | --- |
| Smart VPS | 2–128 vCPU, 4–256 GB RAM, 40 GB–2 TB NVMe, 4–304 TB transfer, Proxmox, 5 locations | 60Gbps included | Monthly; 25% off quarterly; 35% off semi-annual; **50% off annual** | From $7.95/mo ($3.98/mo on annual) | [Deploy a Smart VPS](https://bit.ly/SharKTech) |
| Public Cloud – Small | 4–16 vCPU, 8–32 GB RAM, 300–2400 GB SSD, 20 TB transfer, OpenStack | 60Gbps included | Monthly (usage-based scaling) | From $39/mo | [Configure Public Cloud Small](https://bit.ly/SharKTech) |
| Public Cloud – Medium | 8–32 vCPU, 16–64 GB RAM, 800–6400 GB SSD, 20 TB+ | 60Gbps included | Monthly | From $79/mo | [Configure Public Cloud Medium](https://bit.ly/SharKTech) |
| Public Cloud – Large | 32–128 vCPU, 64–256 GB RAM, 1500–12000 GB SSD, 20 TB+ | 60Gbps included | Monthly | From $249/mo | [Configure Public Cloud Large](https://bit.ly/SharKTech) |
| Public Cloud – Enterprise | 64+ vCPU, 128+ GB RAM, 5000+ GB SSD, 20 TB+ | 60Gbps included | Monthly | From $499/mo | [Configure Public Cloud Enterprise](https://bit.ly/SharKTech) |
| Dedicated Cloud | 8–512 vCPU, 16–1024 GB RAM, SSD/HDD/NVMe tiers, 20 TB included (overage $0.002/GB) | 60Gbps included | Monthly; 5% off quarterly; 10% off semi-annual; 15% off annual | From $86.23/mo | [Build a Dedicated Cloud](https://bit.ly/SharKTech) |
| Cloud Applications Platform | Managed platform, billed per cloudlet ($0.0035/hr) + storage + network | 60Gbps included | Hourly usage | From $5/mo | [Start on the Applications Platform](https://bit.ly/SharKTech) |
| Object Storage (S3) | 1 TB–1 PB, 5 locations, free inbound transfer | On protected network | Monthly | From $6/mo | [Get S3 Object Storage](https://bit.ly/SharKTech) |
| CDN – Basic | 5 TB bandwidth, 5 hosts, overage $8/TB | On protected network | Monthly | $29/mo | [Add Basic CDN](https://bit.ly/SharKTech) |
| CDN – Advanced | 50 TB bandwidth, 10 hosts, overage $0.0065/GB | On protected network | Monthly | $319/mo | [Add Advanced CDN](https://bit.ly/SharKTech) |
| CDN – Enterprise | 100 TB bandwidth, 20 hosts, overage $0.0045/GB | On protected network | Monthly | $419/mo | [Add Enterprise CDN](https://bit.ly/SharKTech) |

The Smart VPS annual discount deserves its own sentence: 50% off, applied automatically when you pick the yearly billing cycle, no promo code. That turns the entry tier into roughly $3.98/month with the same 60Gbps mitigation attached. Public Cloud tiers scale per-resource on top of the base (cores at $0.0025/hr, RAM at $0.0035/hr per GB), so treat those "from" prices as floors for a minimal configuration.

### Bare-Metal Servers (Los Angeles, Currently Orderable)

| Configuration | RAM | Network | DDoS protection | Price (USD) | Order |
| --- | --- | --- | --- | --- | --- |
| Dual Xeon E5-2695V4, 6× 2.5" bays | 64 GB | 10Gbps, 300 TB/mo (40/100G available) | 60Gbps free, 100Gbps +$39/mo per IP | From $259/mo | [Order E5-2695V4](https://portal.sharktech.net/aff.php?aff=1611&pid=741) |
| Dual Xeon Gold 6248, 3× 3.5" bays | 128 GB | 10Gbps, 300 TB/mo | Same | From $299/mo | [Order Gold 6248 (3-bay)](https://portal.sharktech.net/aff.php?aff=1611&pid=660) |
| Dual Xeon Gold 6248, 6× 2.5" bays | 128 GB | 10Gbps, 300 TB/mo | Same | From $309/mo | [Order Gold 6248 (6-bay)](https://portal.sharktech.net/aff.php?aff=1611&pid=636) |
| Dual Xeon Gold 6246, 3× 3.5" bays | 128 GB | 10Gbps, 300 TB/mo | Same | From $309/mo | [Order Gold 6246](https://portal.sharktech.net/aff.php?aff=1611&pid=814) |
| Dual Xeon Gold 6248, 6× U.2 bays | 128 GB | 10Gbps, 300 TB/mo | Same | From $329/mo | [Order Gold 6248 (U.2)](https://portal.sharktech.net/aff.php?aff=1611&pid=766) |
| Dual Xeon Gold 6248, 8× 3.5" + 4× U.2 | 128 GB | 10Gbps, 300 TB/mo | Same | From $389/mo | [Order Gold 6248 (8+4)](https://portal.sharktech.net/aff.php?aff=1611&pid=664) |
| AMD EPYC 7702, 10× U.2 bays | 128 GB | 10Gbps, 300 TB/mo | Same | From $499/mo | [Order EPYC 7702](https://portal.sharktech.net/aff.php?aff=1611&pid=729) |

A few models on the Los Angeles list (and some in other locations) show out-of-stock status at any given time, and bare-metal inventory rotates — if a specific chassis matters, check current availability. All bare-metal plans include free setup, an IPMI management platform, and 24/7 support.

## How the 100Gbps Upgrade Actually Works

The $39/month upgrade isn't just a bigger pipe on your existing IP. Sharktech assigns you separate IPs from prefixes announced via anycast across all five data centers. Incoming traffic to those IPs lands at the nearest site, passes through the mitigation stack, then reaches your server over a GRE tunnel — meaning inbound and outbound paths are asymmetric, which keeps the latency hit on return traffic to zero.

Two practical notes from Sharktech's own documentation: don't use 100G IPs as your server's primary management IP (keep public-facing services on them instead), and because detection happens per-site, attack notifications may report size imprecisely when an attack is spread across locations. Neither is a dealbreaker; both are the kind of detail you want to know before, not during, an incident.

If you're sizing up a bare-metal box with the upgrade attached, 👉 configure a dedicated server with 100Gbps protection and the order form will price the full stack before you commit.

## What Independent Reviews Say

Manufacturer specs only go so far, so here's what's verifiable from outside Sharktech's own materials:

- A LowEndTalk member documented a full year running an attack-prone WordPress site on Sharktech. Standard protection kept the site live under routine attacks; when attack volume eventually exceeded the standard tier, upgrading to multi-data-center protection resolved it. His closing line: "Overall, I recommend Sharktech, especially if you need DDoS protection."
- A HostAdvice technical review that benchmarked the Smart VPS platform reported 6,000+ random IOPS and sub-millisecond network latency, calling it one of the more technically impressive VPS offerings the reviewer had tested.
- Published customer testimonials include game-server operators reporting routine 3–8 Gbps attacks that "never skip a beat," and a China-based hosting company describing years of service without protection failures.

The recurring criticism in hosting communities concerns off-hours support response speed — a fair thing to weigh against the 24/7 on-site engineering claims, and worth asking about directly before committing to a large deployment.

## Choosing: A Short Decision Framework

**Stick with Cloudflare's free or Pro tier if** you run ordinary websites, your traffic is HTTP/HTTPS, and you're not latency-critical. It's genuinely good protection at a hard-to-beat price, and there's no reason to migrate hosting just for DDoS defense in that scenario.

**Consider hyperscaler native protection if** your workloads already live in AWS, Azure, or GCP — Shield Standard and DDoS Basic are free and automatic. Evaluate the paid tiers only if cost-protection guarantees and SLA credits matter at your scale.

**Look at network-level hosting if** you run game servers, VoIP, custom TCP services, or anything a web proxy can't sit in front of; you want flat, predictable pricing instead of usage-based billing that spikes during attacks; or you've been null-routed by a "DDoS-protected" budget host. This is where a provider like Sharktech earns its slot: the mitigation rides on infrastructure you'd be paying for anyway, the 60Gbps baseline covers nearly all realistic attack sizes, and the 100Gbps upgrade at $39/month handles the heavy cases without an enterprise contract.

**Get enterprise quotes if** you're regulated, critical-infrastructure, or need contractual SLAs with human response teams. Akamai Prolexic and similar services exist for exactly this, and their price is defensible there.

One closing thought on size: capacity numbers make headlines, but the median attack is tiny relative to any of these networks. What actually differentiates providers for normal workloads is whether filtering is always-on, whether it covers your protocol, whether your bill stays flat during an incident, and whether "protection" means filtering or just switching your IP off. Those four questions will sort any shortlist faster than a spec sheet.

If you want to test network-level mitigation on a real project before committing to anything serious, the Smart VPS entry tier is cheap enough to be a low-risk experiment — 👉 spin up a 60Gbps-protected VPS from $7.95/month ($3.98 on annual billing) and see how it holds up against whatever's been knocking on your door.
