# 9proxy socks5: How to Get Working SOCKS5 Endpoints for Antidetect Browsers, Scrapers and Mobile Profiles

Most people searching this already have the account and are staring at a problem. A browser profile, a scraper, or a bot wants a SOCKS5 line in the format `host:port:user:pass`, and what they have in hand doesn't fit. The question hiding behind the search is rarely "does 9Proxy support SOCKS5" — the answer to that one is yes — but rather: which plan actually hands me a SOCKS5 endpoint, where do I copy it from, and why does the same line work in one tool and fail in the next.

So this is a setup and buying guide, not a feature list.

## Does 9Proxy actually hand out SOCKS5?

9Proxy is a residential proxy provider that advertises a pool of 20M+ residential IPs across 90+ countries, with targeting down to country, state, city, ZIP and ISP. HTTP, HTTPS and SOCKS5 are all supported. That part isn't in dispute; it's repeated across the provider's own documentation and in the third-party integration guides written by antidetect browser vendors.

What trips people up is that SOCKS5 arrives in two very different shapes depending on which billing model you bought.

| Model | How you get a SOCKS5 endpoint | Desktop app needed? |
| --- | --- | --- |
| Residential by IP | You forward a local port inside the 9Proxy app and point your tool at `127.0.0.1:<port>` | Yes (Windows / Mac) |
| Residential by GB | You generate endpoints in the dashboard and copy a real `IP:port` with username/password | No |

That single difference decides more setup outcomes than any other factor, so it's worth understanding before you pay.

## The GB-based route: a real SOCKS5 endpoint, no app

This is the cleaner path if your tool takes a plain proxy string. The whole flow lives in the browser dashboard.

1. Create a sub-user (the dashboard uses sub-accounts to attach credentials and traffic limits).
2. Open the Proxy Generator, choose your target: country, state, city, ZIP, or ISP.
3. Pick the protocol. HTTP, HTTPS, and SOCKS5 are selectable, along with sticky or rotating session behaviour.
4. Copy the endpoint. Host, port, username and password all come out ready to paste.
5. Drop it into your tool and run a proxy check before you run anything real.

The 9Proxy documentation uses `38.180.149.107` as a sample host and `17521` as a sample port, which tells you the shape of what you'll get: a standard residential endpoint, not a special proxy protocol of their own.

The username is where the interesting part lives. It's a structured string that carries your targeting and session settings:


<subaccount>-country-<country_code>-st-<state_code>-city-<city_code>-isp-<isp_code>-ssid-<session_id>-sst-<session_time>


A real example from the docs looks like `useruser123-country-US-ssid-rhdN1907ma`, paired with your sub-account password. Two practical consequences:

- If you want a stable IP for a logged-in session, keep the same `ssid` across requests; change it and you're effectively asking for a different identity.
- A typo in the country or city segment doesn't throw an error. It just quietly routes you somewhere else, which is the kind of thing you discover three hours into a scraping run when your US data has an Indonesian accent.

GB-based plans carry 180-day traffic validity, and Enterprise plans remove the expiry entirely. Authentication works with username/password or IP whitelisting, so tools that don't accept credentials can still be used by whitelisting your own machine's IP.

## The IP-based route: SOCKS5 via port forwarding

This model bills per IP with unlimited bandwidth, and unused IPs don't expire. The tradeoff is that the endpoints aren't handed to you as a clean string. You run the 9Proxy desktop app, search for IPs by country and state, right-click the one you want, and choose "Forward Port To Proxy". Pick a port (guides commonly use something like 60000), then open the Port Forwarding List to see the full line. A common pattern is forwarding several IPs to different local ports and loading that list into your antidetect browser or automation tool, each profile bound to `127.0.0.1:<port>` with SOCKS5 selected as the type.

Three things to know before you build a workflow around this:

- One forwarded IP equals one IP used from your balance. Forwarding is consumption.
- Residential IPs have natural lifespans measured in hours, up to roughly 24 hours. They drop, and the app shows you which ones are still reachable.
- The app is required. If you're running headless on a Linux box with no desktop environment, the IP-based model is the wrong purchase.

Third-party write-ups of the service also describe a short replacement window when a forwarded IP dies (around 60 seconds, handled through a "Today List" of still-live IPs you can reuse without paying again). Treat that as something to confirm with support for your own account rather than a guarantee.

## Where SOCKS5 setups actually break

The failures are almost never the proxy itself. They're these:

**iOS and iPadOS.** Apple's native proxy settings only take HTTP and HTTPS. On an iPhone or iPad, the documented workaround is Shadowrocket: add a server, set the type to SOCKS5, paste the address, port, username and password, then allow the VPN configuration when prompted. Without a client like that, SOCKS5 on iOS simply isn't reachable.

**Apps with no proxy support at all.** Some desktop software ignores system proxy settings entirely. That's what Proxifier is for: add the proxy server (type HTTPS or SOCKS5), enable authentication with your structured username and sub-account password, then create a proxification rule for the specific executable. This routes one app through the proxy without touching anything else on the machine.

**Browser extensions.** FoxyProxy and similar extensions accept your proxy IP, port, and type, but they live inside one browser. Fine for checking SERPs from a specific city, useless for anything that isn't a browser request.

**Antidetect browsers.** This is 9Proxy's home turf. Hidemyacc, ixBrowser, MuLogin, XLogin and similar tools all document the same flow: create a profile, open the proxy tab, select SOCKS5, paste host/port/username/password, hit "Check Proxy", then save. Each profile gets its own IP and its own fingerprint, which is the entire point of paying for residential addresses instead of datacenter ones.

| Tool | Protocol selection | The catch |
| --- | --- | --- |
| Hidemyacc | HTTP, HTTPS, SOCKS5 | Needs a sub-account and an active session first |
| ixBrowser | SOCKS5 | IP-based model means copying the forwarded port from the app |
| Proxifier | HTTPS or SOCKS5 | Enable authentication separately, or requests fail silently |
| FoxyProxy | HTTP, HTTPS, SOCKS5 | Browser-only scope |
| Shadowrocket (iOS) | HTTP or SOCKS5 | Must approve the VPN profile |
| MuLogin / XLogin | SOCKS5 | Paste the forwarded line from the 9Proxy app |

Whatever the tool, verify with an IP-check page before running real work. Two minutes of checking beats discovering a leak after a ban.

## Full plan line-up and what each one costs

9Proxy raised prices on its IP-based and bundle packages on June 1, 2026, the first adjustment the company says it has made. GB-based pricing was left untouched. The figures below reflect the post-adjustment structure. Prices are one-off balance top-ups rather than monthly subscriptions, so a package you buy stays in your account and unused IPs don't expire.

| Plan | What you get | Price | Effective rate | Buy |
| --- | --- | --- | --- | --- |
| 100 IPs package | 100 residential IPs, unlimited bandwidth per IP | $24 | $0.24/IP | [Get it](https://bit.ly/9-Proxy) |
| 500 IPs package | 500 residential IPs | $72 | $0.144/IP | [Get it](https://bit.ly/9-Proxy) |
| 1,000 IPs + 500 bonus | 1,500 IPs total | $126 | $0.084/IP | [Get it](https://bit.ly/9-Proxy) |
| 2,500 IPs package | 2,500 residential IPs | $210 | $0.084/IP | [Get it](https://bit.ly/9-Proxy) |
| 5,000 IPs package | 5,000 residential IPs | $360 | $0.072/IP | [Get it](https://bit.ly/9-Proxy) |
| 15,000 IPs package | 15,000 residential IPs | $720 | $0.048/IP | [Get it](https://bit.ly/9-Proxy) |
| 25,000 IPs package | 25,000 residential IPs | $863 | $0.035/IP | [Get it](https://bit.ly/9-Proxy) |
| 50,000 IPs package | 50,000 residential IPs | $1,438 | $0.029/IP | [Get it](https://bit.ly/9-Proxy) |
| 100,000 IPs (Business) | High-volume IP inventory | $2,300 | $0.023/IP | [Get it](https://bit.ly/9-Proxy) |
| 200,000 IPs (Business) | High-volume IP inventory | $4,140 | $0.021/IP | [Get it](https://bit.ly/9-Proxy) |
| 500,000 IPs (Business) | High-volume IP inventory | $8,625 | $0.018/IP | [Get it](https://bit.ly/9-Proxy) |
| 5 GB traffic plan | 5 GB, 180-day validity | $15 | $3.00/GB | [Get it](https://bit.ly/9-Proxy) |
| 50 GB + 5 GB bonus | 55 GB, 180-day validity | $105 | $2.10/GB | [Get it](https://bit.ly/9-Proxy) |
| 100 GB traffic plan | 100 GB, 180-day validity | $150 | $1.50/GB | [Get it](https://bit.ly/9-Proxy) |
| 200 GB traffic plan | 200 GB, 180-day validity | $200 | $1.00/GB | [Get it](https://bit.ly/9-Proxy) |
| 1,000 GB traffic plan | 1,000 GB, 180-day validity | $800 | $0.80/GB | [Get it](https://bit.ly/9-Proxy) |
| 2,000 GB traffic plan | 2,000 GB, 180-day validity | $1,500 | $0.75/GB | [Get it](https://bit.ly/9-Proxy) |
| 3,000 GB Enterprise | 3,000 GB, no expiry, team mode | $2,160 | $0.72/GB | [Get it](https://bit.ly/9-Proxy) |
| 6,000 GB Enterprise | 6,000 GB, no expiry, team mode | $4,200 | $0.70/GB | [Get it](https://bit.ly/9-Proxy) |
| 10,000 GB Enterprise | 10,000 GB, no expiry, team mode | $6,800 | $0.68/GB | [Get it](https://bit.ly/9-Proxy) |
| Starter bundle | 100 IPs + 5 GB | $30 | Mixed model | [Get it](https://bit.ly/9-Proxy) |
| Popular bundle | 1,500 IPs + 50 GB | $180 | Mixed model | [Get it](https://bit.ly/9-Proxy) |
| Pro bundle | 5,000 IPs + 500 GB | $720 | Mixed model | [Get it](https://bit.ly/9-Proxy) |

Enterprise packages include team mode (one owner plus up to five members), shared bandwidth without expiration inside the team, per-member traffic controls and activity logs.

## Which one to buy if you're here for SOCKS5

If your goal is antidetect browser profiles or social account sessions, buy IPs, not gigabytes. You want the same address to survive across a full session, and the per-IP model gives you a fixed endpoint with no bandwidth accounted against you. 100 IPs at $24 is a cheap way to test whether the pool works for your targets before scaling.

If your goal is scraping, price comparison, or ad verification, the rotation-heavy GB model is usually the better structure. Requests are small, you burn through a lot of different IPs, and manual forwarding becomes a bottleneck fast. The 5 GB entry tier at $15 exists so you can confirm that without committing.

The bundles are for the awkward middle case where one project needs sticky sessions and another needs volume. Whether the $30 Starter bundle is worth it over buying $24 of IPs and $15 of traffic separately depends on how much of each you'll actually consume, and it isn't automatically the cheaper option.

## The parts worth knowing before you pay

9Proxy doesn't publish a self-serve free tier, and third-party pricing trackers have noted the official pricing page historically routed prospects toward contact rather than open registration. Free trials have existed at various points, offered on request and subject to availability, and promotional batches of free IPs have been handed out through partner channels. Don't assume a specific trial exists on the day you sign up; ask support.

Review coverage in 2026 has been mixed on reliability. One third-party review site documented a roughly two-week outage in June 2026 followed by another disruption later in the year, while reseller catalogs continued selling the service and described it as operational again. The service itself issues working IPs when it's up; the complaint is about consistency, not legitimacy. That's a reasonable argument for starting with a small top-up rather than loading a large balance on day one, even though unused IPs don't expire.

Payment options include credit cards, bank cards, crypto (USDT, BTC, ETH, LTC, DOGE and others), Alipay, Apple Pay and Google Pay, according to the company's own channel announcements. If you're paying by crypto and your tooling sits behind a corporate network, sort out the whitelisting or credential setup first — retrying a payment is easier than untangling a half-configured session later.

## Signing up

Registration happens through the standard sign-up flow, and the referral invitation attached to it carries a 5% discount for the referred user, which is how 9Proxy runs its affiliate side. If you're going to create an account anyway, there's no reason to leave that on the table:

👉 [Create your 9Proxy account with the invite discount applied](https://bit.ly/9-Proxy)

Once you're in, decide the model first, then extract the endpoint. Doing it in the other order is how people end up with a GB plan and an antidetect browser waiting for a stable IP.

## Quick answers

**Can I use SOCKS5 without downloading anything?** On the GB-based model, yes. On the IP-based model, no, the desktop app does the port forwarding.

**Why does my SOCKS5 proxy connect but report the wrong country?** Check the structured username. Targeting lives in the username string, not in the host.

**Does SOCKS5 work on iPhone?** Not natively. Use Shadowrocket or an equivalent client that supports SOCKS5.

**Can SOCKS5 run a whole application, not just a browser?** Yes, via Proxifier, which tunnels a selected executable through the proxy.

**Do unused IPs disappear after 180 days?** The 180-day clock applies to GB traffic packages. IPs you've bought but not forwarded don't expire, and Enterprise traffic has no expiry at all.

**Is SOCKS5 faster than HTTP here?** Not inherently. Pick SOCKS5 when your tool needs it or when you want the proxy to handle the connection at a lower level; either protocol pulls from the same residential pool.
