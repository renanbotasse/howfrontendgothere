
In 2004, Internet Explorer had 95% market share. There was essentially no meaningful competition. Microsoft had won the first browser war so decisively that the Netscape team scattered, the browser landscape looked permanently settled, and the concept of web standards was close to meaningless because whatever IE did was the de facto standard.

What happened next is one of the stranger stories in technology history. That 95% became 5%, and it happened inside of fifteen years.

---

## How IE got there

The first browser war was between Netscape Navigator and Internet Explorer, roughly 1995 to 2001. Netscape created the web browser as a commercial product. Microsoft saw the internet's potential late and responded by acquiring Spyglass Mosaic, building IE on top of it, and making it free.

Free was the weapon that mattered. Netscape charged $49 per copy. IE was bundled with Windows. When most personal computers in the world ran Windows, bundling your browser with the operating system made your browser the default for most users who didn't know to look for an alternative.

Netscape tried to compete on features, introducing JavaScript and dynamic HTML. IE matched each move. Both sides added proprietary extensions to HTML and JavaScript, making sites that worked in one browser break in the other. "Best viewed in Netscape Navigator" and "Best viewed in Internet Explorer 4.0" were real banners on real websites.

Microsoft won. Netscape was acquired by AOL in 1998, and the Netscape browser team was largely released as the Mozilla project, a free, open-source browser. IE had the market.

---

## The dark ages of IE dominance

With no competitive pressure, Internet Explorer 6 shipped in 2001 and Microsoft basically stopped developing it. IE6 remained the dominant browser until 2010. IE7 didn't arrive until 2006, and IE8 until 2008.

For frontend developers, this was genuinely difficult. IE6 had broken box model behavior, inconsistent JavaScript support, no support for many CSS features that other browsers were implementing, and proprietary APIs like `innerHTML` that became standard later partly because everyone was using them.

Web development meant writing code that worked in IE6 and then making it also work in Firefox. Conditional comments let you serve IE-specific CSS. JavaScript compatibility libraries patched missing functionality. A significant portion of every project's time went to IE-specific bugs.

The fact that IE6's box model was wrong, it calculated element widths to include padding and border by default, which is actually more intuitive than the CSS spec, eventually became the rationale for `box-sizing: border-box`, which is now standard practice. The wrong behavior was so common that CSS added a way to get it back.

---

## Firefox changes the game

The Mozilla project rebuilt Netscape's browser from scratch with a focus on standards compliance and extensibility. Firefox 1.0 launched in November 2004 with a genuine marketing push, a two-page ad in the New York Times paid for by donations. A hundred thousand people donated. The ad ran.

Firefox offered tabbed browsing, pop-up blocking, and extensions, including Firebug, which became the first real browser developer tool. Developers especially adopted it, which influenced recommendation patterns. Firefox grew to about 30% market share by 2009.

But Firefox didn't kill IE. It established that there was a market for alternatives, that users would switch if given a reason, and that the browser market was not as settled as it appeared. The actual killing came later.

---

## Chrome enters

Google launched Chrome in September 2008. The design decisions were deliberate: a minimal interface, each tab as its own process (so one crashed tab didn't crash the browser), and a V8 JavaScript engine that was dramatically faster than anything else available.

![Browser market share over time: IE decline, Firefox rise, Chrome dominance](/images/browser-market-share.svg)

JavaScript performance mattered because web applications were getting more complex. Gmail, Google Maps, Google Docs, all Google products that needed JavaScript to run fast. Chrome's V8 engine used just-in-time compilation, turning JavaScript into machine code at runtime instead of interpreting it. The speed difference was noticeable.

Chrome also benefited from Google's distribution reach, Chrome was promoted on Google's homepage, which at the time was one of the highest-traffic pages on the internet. Updates happened silently and automatically. Developers didn't have to worry about which version of Chrome a user had in the same way they worried about IE versions.

Chrome passed IE in global market share around 2012. It has never given up the lead since.

---

## WebKit and Safari

Apple had its own reasons to care about browsers. In 2003, Safari launched on macOS, using a rendering engine forked from KHTML, a project of the KDE open-source community. Apple called their engine WebKit.

When the iPhone shipped in 2007, Mobile Safari came with it. This was the browser that decided what the mobile web was. Apple made two choices that had lasting consequences. First, they didn't ship a Flash player for iPhone, which accelerated Flash's decline. Second, they controlled which browser engine ran on iOS, WebKit only, a policy that held until regulatory pressure forced a change in 2024.

Safari's market share is tied to Apple hardware market share. On desktop it's roughly 10-15%. On mobile, because of iOS's share in wealthy markets, it can be 25% or more depending on region. That makes it too important to ignore and positions Apple as a major voice in web standards discussions.

---

## What the browser wars established

Browser diversity is not just aesthetically nice. Competitive browsers drove standards compliance, IE's dominance had let Microsoft ignore or define standards unilaterally. When Firefox and Chrome competed on standards conformance, vendors had an incentive to implement specs correctly.

The engines that emerged from the wars, V8 (Chrome/Edge), SpiderMonkey (Firefox), JavaScriptCore (Safari), are genuinely different implementations of the ECMAScript spec. When all three implementations agree on behavior, it's a reasonable signal that a feature is working as intended. When they disagree, it surfaces ambiguities in the spec.

And the browser became the deployment target for desktop applications, not just web pages. The fact that Chrome runs fast enough to support Google Docs and Figma and VS Code in the browser means that "cross-platform" increasingly means "runs in a browser," which means web standards are effectively the universal software distribution platform for any device with a screen.

That's a long way from 95% IE market share and "best viewed in Internet Explorer 4.0."

---

*Next: How JavaScript Left the Browser. Node.js put the JavaScript runtime on servers in 2009. That one decision started a chain of events that changed the frontend ecosystem more than any framework did.*
