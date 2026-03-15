In 2015, Google had a problem. The mobile web was slow. Not slow because the technology to make it fast did not exist. Slow because publishers and developers kept optimizing for desktop, loading analytics scripts, advertising scripts, personalization scripts, and calling it a good mobile experience. On a 3G connection, a news page could take 8 or 10 seconds to load.

The solution Google built worked. And it created a different problem, larger and harder to solve than the original.

---

## What AMP promised

Accelerated Mobile Pages was technically elegant. The idea: define a restricted subset of HTML where custom JavaScript was prohibited, CSS had a 50KB limit, and heavy resources like iframes were replaced by controlled versions (`amp-iframe`, `amp-video`, `amp-ad`). Within those constraints, Google could pre-render pages in its CDN and deliver them instantly when a user clicked a search result.

The loading was genuinely instantaneous. The experience was noticeably better than the average news page at the time.

Google also created a powerful incentive for adoption: AMP pages appeared in a special carousel at the top of mobile search results (the "Top Stories" section) with visual priority and better positioning. For publishers whose business depended on organic Google traffic, not having AMP meant losing a privileged space in results.

---

## Why adoption was fast

Publishers adopted quickly because they had no real choice. The New York Times, The Guardian, Washington Post, BuzzFeed, and BBC all implemented AMP within the first year. Major news portals in Brazil followed the same path.

Implementation was technically simple in most cases: create an AMP version of each article, served at a different URL (`/amp/`). The CMS generated both versions. Google crawled the AMP version and served it from its cache.

For developers, AMP introduced a new set of skills. Not quite normal JavaScript, not quite normal HTML, but its own abstraction layer with its own documentation, components, and specific bugs.

---

## The URL that was not yours

The most visible problem with AMP was the URL. When a user arrived at an article through Google, the address bar showed:

```
https://google.com/amp/s/www.yoursite.com/article
```

Not `www.yoursite.com`. Google.com.

For the end user, this was confusing. The site did not look like the real site. For publishers, it was a threat to identity and branding. If someone shared the URL, they shared a Google URL, not the publisher's. Analytics data was affected. The direct relationship between publisher and reader was being mediated and controlled by Google.

Google proposed technical solutions for this problem, including Signed Exchanges (SXG), which allowed serving content from external cache while maintaining the original URL. Implementation was complex and browser support was inconsistent.

---

## The criticism that organized

In 2017 and 2018, resistance to AMP began to organize. The criticism was not about performance. Everyone agreed that faster pages were better. It was about power.

AMP was a Google project. Decisions about which elements were allowed, which components existed, which advertising APIs were supported. Those were Google's decisions. Publishers who adopted AMP were building on infrastructure Google controlled, according to rules Google defined.

Jeremy Keith, a developer and influential writer in the web community, published a series of articles arguing that AMP represented a privatization of parts of the open web. The Electronic Frontier Foundation expressed similar concerns. Mozilla had reservations about several aspects of the project.

The most substantive criticism was about incentives: AMP ensured that content stayed within Google's ecosystem, served from Google's CDN, accessed through Google links, which benefited Google in ways that were not necessarily aligned with the interests of publishers or users.

---

## The resistance

Some publishers decided not to implement AMP and invested in improving the performance of their normal pages. The argument was that if the page is fast enough, AMP is not needed to rank well.

That was true in theory. Google always said AMP was one path to performance, not the only path. In practice, the disadvantage of not appearing in the Top Stories carousel was real for publishers dependent on SEO.

The developer community built tools to measure Core Web Vitals, the performance metrics Google used to evaluate pages, and began optimizing for them directly. Lighthouse, web-vitals.js, and the integration of those metrics into Chrome DevTools democratized performance work.

---

## The decline

In 2021, Google made a fundamental change: starting that June, having an AMP page would no longer be a requirement to appear in the Top Stories carousel. Any page meeting the Core Web Vitals criteria would be eligible.

That announcement removed the main incentive that had made AMP attractive to publishers. Without the SEO advantage, maintaining a parallel version of every article in AMP format made no sense. Adoption began falling.

In March 2023, Google announced it would remove the AMP cache from search results for most use cases. The project continues as open source, but its practical relevance dropped substantially.

---

## What AMP left behind

AMP failed as an initiative to control content distribution. The open web was not captured by a single vendor's platform. That part of the failure is a positive outcome.

But AMP left real contributions. The discussions it provoked about mobile performance raised industry standards. The concept of Core Web Vitals (LCP, FID, CLS) emerged partly as a response to the need for platform-independent performance metrics. Google's investment in tools like Lighthouse was accelerated by the AMP context.

And AMP demonstrated something important about how the market responds to platform capture attempts: publishers and developers resist when the cost to their autonomy is clear. The resistance took a few years to organize, but it organized.

The AMP story is a case study in how a large company can identify a real problem, build a technically capable solution, and still fail because the solution serves the company's interests more than the interests of the users it was supposed to serve.

---

*Next: How SEO Broke the SPA. The rise of single-page applications created a different problem with Google: not performance, but indexation. The tension between SPAs and SEO is what brought server-side rendering back to the center of the debate.*
