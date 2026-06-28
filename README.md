# Browser Rendering API for Web Scraping: Why Your Scraper Returns Empty HTML, How JS Rendering Actually Works, and Which Plan Is Worth Paying For (Full Pricing Table Inside)

You send a request to a page, you get back a 200 status code, and the response body is... basically nothing. A `<div id="root"></div>`, a pile of `<script>` tags, and none of the product names, prices, or reviews you actually wanted. If that sounds familiar, you've already met the problem that a **browser rendering API** exists to solve.

This isn't a rare edge case anymore. Most modern websites — React storefronts, Vue dashboards, infinite-scroll feeds — don't ship finished HTML from the server. They ship a skeleton and a JavaScript bundle, and the browser builds the actual page after the fact. A plain HTTP request never runs that JavaScript, so it never sees the content a human visitor sees. A browser rendering API closes that gap by spinning up a real (headless) browser on someone else's infrastructure, letting the page's scripts execute, and handing you back the fully built HTML.

This guide walks through what a browser rendering API actually does, how to turn it on with **ScraperAPI**, what it costs in real credits (not marketing language), the full current pricing table, and what other users say about the service — so you can decide whether it's the right fit before you commit a single API key.

## What Is a Browser Rendering API, Exactly?

A browser rendering API is a hosted service that loads a target URL inside a real or headless browser (usually Chromium), waits for the page's JavaScript to finish executing, and returns the resulting DOM — instead of the raw, often-empty HTML a basic `GET` request would return.

You'll run into the need for one whenever:

- The page is a single-page application (React, Vue, Angular, Svelte) that hydrates content client-side

- Data loads via background XHR/fetch calls after the initial page load

- Content appears only after a scroll, click, or form interaction

- You need a literal screenshot or PDF of how the page renders

- The site uses anti-bot systems that specifically fingerprint non-browser traffic

If none of that applies — a static blog, a server-rendered page, a simple product listing — you don't need rendering at all, and turning it on anyway just slows requests down and burns more credits than necessary. The trick with any browser rendering API is using it selectively: render when the content demands it, skip it when a plain HTTP fetch is enough.

## How ScraperAPI's Browser Rendering Feature Works

ScraperAPI bundles JavaScript rendering directly into its core scraping API rather than selling it as a separate product. You don't manage Chromium binaries, Docker containers, or browser crashes — you add one parameter to your existing request.

A basic rendered request looks like this:

bash

curl --request GET \

--url 'https://api.scraperapi.com?api_key=API_KEY&render=true&url=https://example.com/'



Setting `render=true` routes the request through a headless Chrome instance, lets the page's scripts run, and returns the fully rendered HTML. The feature is available on every plan — including the free trial — so you don't need to be on a specific tier to test it.

For pages where content appears with a delay (lazy-loaded sections, slow API calls), you can pair it with `wait_for_selector` to hold the response until a specific element shows up in the DOM, rather than guessing a fixed wait time:

bash

curl --request GET \

--url 'https://api.scraperapi.com?api_key=API_KEY&render=true&wait_for_selector=%23content&url=https://example.com/'



There's also a more advanced **rendering instruction set** for pages that need actual interaction before the data you want shows up — typing into a search box, clicking a button, then waiting for results to render. Instructions are passed as a JSON array (input, click, wait_for_selector, and similar actions) via request headers, capped at roughly 3–4 actions per request to keep latency and timeout risk manageable.

> **Practical tip:** keep the instruction chain short. Every extra click or page transition adds latency, and long chains are the most common reason rendered requests time out.

## What Browser Rendering Actually Costs in Credits

This is the part most comparison articles gloss over, so here's the actual credit math:

| Action | Credit cost |

|---|---|

| Standard (non-rendered) request | 1 credit |

| JS Rendering (`render=true`) | 10 credits |

| Premium proxies alone (`premium=true`) | 10 credits |

| Premium proxies + rendering combined | 25 credits |

| Ultra Premium + rendering combined | 75 credits |

| Amazon page | 5 credits |

| Google / Bing search results | 25 credits |

| LinkedIn page | 30 credits |

| Sites behind Cloudflare, Datadome, or PerimeterX (when bypassed) | +10 credits on top of the base cost |

A "Domain Cost Estimator" in the dashboard lets you check the exact cost for a given URL before you commit credits to it, and you can set a `max_cost` cap per request so a single page never silently eats more of your budget than expected. The practical lesson here: don't flip `render=true` on globally. Reserve it for the URLs that genuinely need it, and let static pages go through as 1-credit requests — that's the difference between a monthly plan lasting all month and running dry by week two.

## Full ScraperAPI Pricing Table (All Plans, Including Enterprise)

Every plan below includes the same core feature set — JS Rendering, premium proxies, JSON auto-parsing, rotating proxy pools, CAPTCHA and anti-bot handling, automatic retries, unlimited bandwidth, and a 99.9% uptime guarantee. What changes between tiers is volume, concurrency, and geotargeting precision.

| Plan | Monthly Price | Annual Price (10% off) | API Credits / mo | Concurrent Threads | Geotargeting | Get Started |

|---|---|---|---|---|---|---|

| **Hobby** | $49/mo | $44.10/mo | 100,000 | 20 | US & EU only | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Startup** | $149/mo | $134.10/mo | 1,000,000 | 50 | US & EU only | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Business** | $299/mo | $269.10/mo | 3,000,000 | 100 | Global | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Scaling** (most popular) | $475/mo | $427.50/mo | 5,000,000 | 200 | Global | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Professional** | $975/mo | $877.50/mo | 10,500,000 | 300 | Global | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Advanced** | $1,975/mo | $1,777.50/mo | 21,500,000 | 500 | Global | 👉 [Start free trial](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

| **Enterprise** | Custom | Custom | 22,000,000+ | 500+ | Global | 👉 [Talk to sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

A few things worth knowing before you pick a tier:

- **Hobby and Startup** are geo-restricted to US and EU proxy pools — fine if your targets are North American or European, limiting if you need broader country-level targeting.

- **Business and above** unlock global, country-level geotargeting and an unlimited analytics history window (lower tiers cap dashboard history at 30 days).

- **Scaling, Professional, Advanced, and Enterprise** plans support Pay-As-You-Go, so if you blow through your monthly credit allowance, you keep scraping at a fixed predictable rate instead of getting hard-cut off mid-project.

- Unused credits do **not** roll over — your balance resets each billing cycle, so it's worth sizing your plan to your actual average usage rather than your peak month.

- If you exceed your concurrent thread limit, requests are turned away with a 429 response rather than queued or charged extra — no surprise overage billing on concurrency.

## Free Trial and Current Offers

ScraperAPI runs a 7-day free trial that includes 5,000 API credits, no credit card required — enough to test rendering, proxy rotation, and the structured-data endpoints (Amazon, Google, Walmart) against your actual target sites before paying anything. There's also a standing 10% discount baked into every annual plan compared to paying monthly, which is reflected directly in the pricing table above.

A quick honesty note on coupon codes: a lot of third-party "ScraperAPI coupon" sites circulate codes that are unverified, expired, or simply don't apply at checkout. Rather than repeat unconfirmed codes here, the most reliable way to surface whatever promotion is currently active — trial extensions, seasonal discounts, or partner offers — is to 👉 [check the current offer through this link](https://www.scraperapi.com/?fp_ref=coupons) before you sign up, since active promotions are applied automatically at checkout when eligible.

## What Actual Users Say

On review platforms, ScraperAPI sits in solidly positive territory: roughly **4.5/5 on Trustpilot** (in the low-40s review count) and **around 4.4/5 on G2**. The recurring praise across both platforms centers on the same few points: setup is fast, documentation is clear, and proxy/CAPTCHA handling genuinely removes work that would otherwise be manual debugging.

> "It simplifies web scraping by handling proxy management, CAPTCHAs, and browser rendering, allowing me to focus on data extraction rather than technical issues."

The criticism is just as consistent. Credit costs can climb quickly once you're combining rendering with premium proxies on harder targets, and a few reviewers describe a learning curve around understanding exactly which parameter combinations cost what. Independent benchmark write-ups in 2026 generally place ScraperAPI in the mid-pack for raw success rate on the toughest anti-bot targets (search engines, some social platforms) while rating it competitively on price-per-successful-request for standard e-commerce and content sites — in other words, it's a strong general-purpose pick rather than the single best option for every possible target.

## ScraperAPI vs. Other Browser Rendering Options

It's worth knowing where ScraperAPI sits relative to the rest of the market, since "browser rendering API" isn't a single standardized product — it shows up in a few different shapes:

- **Dedicated JS-rendering APIs** (ScrapingBee, ScrapingAnt, Oxylabs, Scrapfly) work similarly: add a render parameter, get back rendered HTML. Pricing structures and credit multipliers vary, and which one performs best tends to depend heavily on the specific target site rather than any universal winner.

- **Enterprise unblocking platforms** (Bright Data, Zyte) generally lead on raw success rate against the hardest anti-bot targets, usually at a higher starting price point.

- **Cloud browser infrastructure** (Browserbase, Scrapfly's cloud browsers) is built more for teams running their own Playwright/Puppeteer code against remote browser sessions, rather than a single-call REST endpoint.

- **Edge-native rendering** (Cloudflare's Browser Rendering) is a different category entirely — it's aimed at developers building on Cloudflare Workers who need on-demand screenshots, PDFs, or markdown extraction from within their own infrastructure, not at general-purpose scraping with built-in proxy rotation.

ScraperAPI's niche is the middle ground: a single REST call that bundles rendering, proxy rotation, and anti-bot handling together, without you having to assemble those three pieces from separate vendors.

## Who Actually Needs This

A browser rendering API earns its cost when you're dealing with:

1. **E-commerce price monitoring** on JS-heavy storefronts where prices, stock status, or reviews load via client-side calls

2. **Market research** pulling data from dashboards and SPAs that never expose data in the initial HTML

3. **SEO and SERP tracking** where rendering is needed to capture how a page looks after JavaScript execution

4. **Lead generation and directory scraping** from sites with infinite scroll or "load more" interaction patterns

5. **AI/LLM data pipelines** that need clean, fully-rendered page content rather than a JavaScript shell

If your use case is mostly static content — blogs, documentation, server-rendered listings — you'll save money skipping `render=true` entirely and relying on the 1-credit base request.

## Frequently Asked Questions

**Does browser rendering work on every ScraperAPI plan, including the free trial?**

Yes. JS Rendering is included on all plans, from the free trial up through Enterprise — it's a feature of the core API rather than a paid add-on tier.

**How much slower is a rendered request compared to a normal one?**

Rendered requests take meaningfully longer than a standard HTTP fetch, since a real browser instance has to load and execute the page's scripts before returning a response. Using `wait_for_selector` instead of a long fixed delay is the most effective way to keep that overhead under control.

**Can I cancel or downgrade if I pick the wrong plan?**

Subscriptions can be canceled anytime from the dashboard with no cancellation fee, and there's a 7-day no-questions refund policy if the service doesn't fit your workflow.

**Do credits roll over if I don't use them all?**

No — the credit balance resets at each billing cycle. If your usage is inconsistent month to month, the Pay-As-You-Go option on Scaling-tier-and-above plans is generally a better fit than overbuying a fixed monthly allowance.

**What's the cheapest way to test before committing to a paid plan?**

👉 [Sign up for the free trial](https://www.scraperapi.com/?fp_ref=coupons) — 5,000 credits, 7 days, no credit card — and run your actual target URLs through it with `render=true` before deciding which paid tier matches your real usage pattern.

---

A browser rendering API isn't something every scraping project needs, but for the growing share of the web built on React, Vue, and Angular, it's the difference between an API call that returns real data and one that returns an empty shell. ScraperAPI's approach — bundling rendering, proxy rotation, and anti-bot handling into a single parameter on a single endpoint — keeps the integration simple, and the tiered pricing scales reasonably as request volume grows. 👉 [Compare the full plan lineup and start your free trial here](https://www.scraperapi.com/pricing/?fp_ref=coupons) before your next scraping project hits its first empty HTML response.
