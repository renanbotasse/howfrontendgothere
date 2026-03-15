
The iPhone launched in January 2007. Steve Jobs stood on stage and described the browser as "a real browser, not a watered-down mobile browser." He meant that it ran a full WebKit, the same rendering engine as Safari on the Mac.

For web developers, this was immediately significant. Not because iPhone adoption was immediate, it wasn't, but because it established a requirement: web pages needed to work on a device with a small screen, no mouse, and a touch interface. Within three years, that requirement wasn't aspirational. It was the reality of where web traffic was going.

---

## Responsive design

Before mobile, web design meant designing for desktop browsers at fixed widths. 960 pixels was a common target. Layouts were fixed or used percentage widths within a fixed outer container.

Ethan Marcotte's 2010 article "Responsive Web Design" described a new approach: fluid grids, flexible images, and media queries. The same HTML and CSS would render differently depending on the viewport width. On desktop, a three-column layout. On tablet, two columns. On mobile, a single column.

Media queries were the technical mechanism:

```css
.layout {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}

@media (max-width: 768px) {
  .layout {
    grid-template-columns: 1fr;
  }
}
```

The article named a practice that developers were already starting to do and gave it a framework. Responsive design became the new baseline. "Does this work on mobile?" became a question in every project.

---

## Touch events and interaction design

Touch events are fundamentally different from mouse events. There's no hover state on a touchscreen, you can't hover, you can only tap. The 44px minimum touch target size became a usability guideline, small tap targets cause accidental taps and frustrate users. Swipe gestures replaced some keyboard shortcuts.

The 300ms click delay was an infamous early mobile web problem. Mobile browsers added a 300ms delay before firing a click event because they were waiting to see if the user was double-tapping to zoom. This made web UIs feel sluggish compared to native apps.

The fix was the `touch-action: manipulation` CSS property, or using pointer events, which eliminated the delay for elements where double-tap zoom wasn't needed. Later browsers made the delay conditional on whether the page had a proper viewport meta tag:

```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
```

Without this tag, mobile browsers zoomed out to fit a desktop-designed page. With it, the page rendered at the device's width and the browser knew zoom behavior was being handled by the design.

---

## Performance on constrained hardware

Mobile performance in the early smartphone era was humbling. The same JavaScript that ran smoothly on a desktop processor choked on a 2010 phone's ARM chip. The same images that loaded acceptably on WiFi were painful over 3G.

Mobile changed the performance conversation from "this is slow sometimes" to "this is consistently slow for a third of your users." And not just "slow", on entry-level Android devices, common in emerging markets and among users who can't afford recent iPhones, JavaScript execution time can be 5-10x slower than on a MacBook Pro. Bundle size isn't just a network cost; it's a parse and execution cost.

The insight that took the industry a while to internalize: testing performance on a developer machine is not testing the performance your users experience. Chrome DevTools' device emulation and CPU throttling became standard practice precisely because of this gap. The "6x CPU slowdown" preset in DevTools is meant to simulate a mid-range Android device.

---

## Native apps vs. web: the long competition

iOS and Android native apps had capabilities the web didn't: reliable push notifications, access to hardware APIs, offline functionality, App Store distribution. For applications that needed any of these, native was the default.

The gap has narrowed. Service workers enabled offline. Push notifications are supported on most platforms. Hardware APIs, camera, location, accelerometer, are available to web apps with user permission. WebGL and WebAssembly made compute-intensive applications viable.

React Native, released in 2015, offered a middle path: write components in React, render to native views. The web's programming model, native performance and capabilities. It became the dominant cross-platform mobile development approach for teams with web frontend skills.

Flutter (2018) took a different approach: its own rendering engine, its own components, Dart instead of JavaScript. It targets mobile, web, and desktop from one codebase.

The market for "build a mobile app" split: React Native for teams coming from web, Flutter for teams who wanted more control over rendering, native Swift or Kotlin for teams that needed maximum performance or deepest OS integration.

---

## Mobile-first design

"Mobile first" reversed the design process. Instead of designing for desktop and scaling down, you designed for the most constrained context and scaled up. This produced better designs: forced to prioritize content for small screens, designers made choices about what was actually important. The desktop version then had room to do more.

Mobile first also influenced the technical approach. If you write CSS for mobile and use media queries to add complexity for larger screens, your CSS baseline is simpler and the progressive enhancement is additive:

```css
/* Mobile, the base */
.nav { display: flex; flex-direction: column; }

/* Desktop, enhanced */
@media (min-width: 1024px) {
  .nav { flex-direction: row; }
}
```

Versus the desktop-first approach where you're undoing desktop styles for mobile, which produces more CSS and more potential for override conflicts.

The mobile web changed what frontend developers needed to know. Touch events, viewport management, performance on constrained hardware, responsive design, none of these were part of the job in 2005 and all of them are table stakes now.

---

*Next: How Design and Engineering Converged. Figma, design tokens, component-based design systems, the distance between design files and production code has shrunk. The shift changes what collaboration between designers and developers looks like.*
