The web was designed to be read on monitors. Not small ones. The resolutions of 1024x768 and 1280x1024 were the reference points when the first CSS frameworks and layout conventions solidified. The average developer had a 17 or 19-inch monitor, a mouse, a keyboard, and a broadband connection.

In 2016, for the first time in history, mobile traffic surpassed desktop traffic globally. It was not a smooth transition. It was a rupture that invalidated design, performance, and business assumptions the industry had built over fifteen years.

---

## Before the iPhone: the failure of WAP

The attempt to bring the web to mobile phones started long before the iPhone. WAP (Wireless Application Protocol) was launched in 1999 as a standard for accessing online content on mobile devices.

WAP was a parallel web, not the real web. WAP pages were written in WML (Wireless Markup Language), not HTML. WAP browsers had minimal capabilities. The connection was slow, priced per kilobyte, and the experience bore no resemblance to what the desktop web offered.

Adoption was low and the experience was poor. WAP became a textbook example of technology that solved a problem users did not want solved that way. When the iPhone arrived in 2007 promising "the real internet" on a phone, the contrast with WAP was the entire point.

---

## January 2007: the iPhone changes everything

Steve Jobs took the stage at Macworld Conference in San Francisco with three products: "an iPod with a large touchscreen, a revolutionary phone, and an internet communications device." After repeating that three times, he revealed they were a single device.

What differentiated the iPhone was not just the hardware. It was the decision to include a complete WebKit browser, the same rendering engine as desktop Safari. For the first time, a mobile device loaded the real web, not an adapted version.

For developers, the implication was immediate and uncomfortable. Sites built for 1024px width, with 10px click targets, 12px fonts, and hover states on everything simply did not work well on a 320px iPhone with a finger as the input device.

---

## The duopoly that emerged

The iPhone had no real competition at launch. BlackBerry was a niche corporate product. Nokia and Motorola dominated the feature phone market but had no competitive smartphone.

Google saw the threat. In October 2007, less than four months after the iPhone launch, Google announced the Open Handset Alliance and Android. In September 2008, the first Android device (HTC Dream, also known as the T-Mobile G1) reached the market.

The war was declared. iOS and Android systematically eliminated the other competitors.

BlackBerry tried to respond with touchscreens but never fully abandoned its physical keyboard identity. In 2016, it announced it would stop developing its own hardware.

Windows Phone from Microsoft had genuinely innovative interface design, live tiles, bold typography, but arrived late (2010) and never convinced developers to build for a third platform when iOS and Android already dominated.

Symbian from Nokia was the world's most-used mobile OS before the iPhone. Incompatible with modern touchscreens, Nokia took too long to abandon it. In 2013, Microsoft bought Nokia's devices division for $7.2 billion and eventually cancelled everything.

By 2013, iOS and Android accounted for over 90% of the smartphone market. In 2024, that number is close to 99%.

---

## The turning point: 2016

For years, "mobile first" was more of a design slogan than a traffic reality. Many sites still saw most of their traffic from desktop, especially in markets like Europe and North America where personal computer penetration was high.

In October 2016, StatCounter published data showing that for the first time, mobile traffic had surpassed desktop traffic globally: 51.3% mobile versus 48.7% desktop. The number was not uniform, in countries like India, mobile had been dominant for years. But globally, the balance had tipped.

For product teams, this had a direct implication. The default user was no longer someone sitting in front of a computer. It was someone holding a phone, possibly in motion, possibly on a slow connection, definitely with a smaller screen.

---

## Emerging markets and the mobile-only user

An aspect frequently ignored in mobile-first discussions is that in several countries, Brazil, India, Indonesia, large parts of Africa, a significant portion of internet users never had a desktop computer. The smartphone was the first connected device they ever used.

In Brazil, over 80% of internet users accessed exclusively by phone as of 2023. Designing for mobile first in those markets is not a UX preference. It is the difference between existing and not existing for that audience.

This created specific tensions. Sites built by teams on MacBooks with gigabit connections, tested in device simulators, reached users on entry-level Androids with inconsistent 3G. The gap between development experience and actual use experience was enormous, and frequently invisible to the teams doing the building.

---

## 3G, 4G, 5G: how the network changed what was possible

The network is as determinative as hardware for what can be built.

3G (mainstream from 2007-2009) had bandwidth comparable to old dial-up connections: 384 kbps to a few mbps. Loading a 2MB page was painful. Asset optimization was critical.

4G/LTE (mainstream from 2012-2014) changed the picture substantially. Speeds of 10-100 mbps made loading reasonable pages nearly instant under normal conditions. SPAs started becoming viable on mobile.

5G (rolling out since 2019) promised sub-1ms latency and gigabit speeds. In practice, coverage is still inconsistent and the gains for the web are less dramatic than for low-latency use cases like IoT and high-quality streaming.

What did not change: mobile connections are inherently less stable than wired connections. You walk into an elevator, lose signal, the request fails. A web that works well on mobile is a web designed for network failures, with retry strategies, explicit loading states, and progressive enhancement.

---

## Mobile-first as a design discipline

Responsive design was the technical response to device fragmentation: fluid grids, flexible images, media queries. Ethan Marcotte named the concept in 2010, but designers and developers were already inventing the pattern in practice.

Luke Wroblewski went further in 2011 with "Mobile First", not as a CSS technique, but as a design process. The space and attention constraints of mobile force decisions about what actually matters. The desktop is a larger canvas where you can add what is left over. When you design from small to large, priorities become clearer.

This transformed how product teams work. The question "how does this look on mobile?" moved from afterthought to first criterion. Designers who worked at 1440px started working at 375px. Developers who had never tested on real devices started keeping collections of old Androids for testing.

Design that works well on mobile tends to work well on desktop too, because you were forced into clarity. The reverse is rarely true.

---

*Next: AMP and Google's Attempt to Control the Mobile Web. The rise of mobile created a new arena where Google tried to solve slow mobile pages by building a parallel web, and the industry pushed back.*
