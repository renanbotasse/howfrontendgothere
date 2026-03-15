This encyclopedia is about frontend. More specifically, about JavaScript frontend. The tools, frameworks, patterns, and decisions that shaped how web interfaces are built. Before going any further, it is worth saying explicitly what this is not covering, because the omission has consequences.

The software market is much larger than React, TypeScript, and Next.js. Developers who spend most of their time inside the JavaScript ecosystem tend to underestimate how much of the world exists outside it.

---

## What runs corporate software

Java has been around since 1995 and shows no signs of slowing down. Banks, insurance companies, healthcare systems, enterprise ERPs. A large portion of the software infrastructure that moves money and data at scale runs on Java or C#. The Spring ecosystem is enormous. The Java/JVM market (including Kotlin and Scala) represents a significant share of all backend job openings worldwide.

C# and the Microsoft .NET ecosystem dominate enterprise on Windows. Government systems, hospital software, financial platforms, especially in markets where Microsoft has strong historical presence, like parts of Europe and North America.

These are not legacy systems about to be replaced. They are active platforms with ongoing development, large communities, and solid job markets. A developer who only knows JavaScript cannot have a real conversation with half of the enterprise market.

---

## What powers the web beyond JavaScript

PHP is the most underrated language in any serious technical discussion. Over 43% of websites on the internet run WordPress, which is PHP. Laravel is one of the most elegant web frameworks in existence, comparable to Rails in productivity. Magento, Drupal, and dozens of e-commerce platforms that move billions in transactions are PHP.

The narrative that PHP died is a Twitter narrative, not a market reality. If you were hiring someone to maintain and evolve the majority of websites in the world, you would need a competent PHP developer.

Python dominates data science, machine learning, and automation. The Django and FastAPI ecosystem is robust for web backends. Infrastructure scripts, data pipelines, the ML models feeding product features: most of that is Python. The explosion of generative AI over the last few years has reinforced Python's position as the lingua franca of data science.

Ruby had its moment with Rails (2004-2015) and still has a significant base. GitHub was built on Rails. Shopify runs on Rails. The framework influenced how an entire generation thought about convention over configuration.

---

## What runs mobile natively

Swift and Kotlin are the native languages of iOS and Android respectively. For applications that need maximum performance, access to OS-level APIs, or deep hardware integration: camera, sensors, low-level notifications. Native development is still the choice.

React Native and Flutter tried to unify this, with varying degrees of success depending on the use case. "Write once, run everywhere" never fully eliminated the need for native code for specific requirements. Many large mobile apps have parts in React Native and parts in Swift or Kotlin.

---

## What runs infrastructure

Go became the language of choice for infrastructure tools and high-throughput systems. Docker, Kubernetes, Terraform, and a large number of DevOps tools are written in Go. The language was designed for fast compilation, efficient concurrency, and simple deployment, everything that matters for server-side tooling.

Rust is growing in segments where performance and memory safety are critical: compilers, browsers (parts of Firefox and Chrome are Rust), embedded systems, build tools like SWC and Turbopack. The learning curve is steep, but adoption in critical infrastructure projects is accelerating.

C and C++ still run most of the software you use without knowing it. Operating systems, drivers, game engines, embedded software, parts of browsers, databases. These are not languages most web developers touch, but they are the foundation on which everything else is built.

---

## Why this matters for frontend developers

The point is not to learn all these languages. The point is to understand where your work fits.

A React frontend needs a backend to provide data. That backend might be Node.js. It might also be Python, Java, Go, or anything else. Knowing how to talk to the backend team, understanding why an API was designed a certain way, understanding the server-side constraints. That requires at least an awareness that other worlds beyond JavaScript exist.

When evaluating a job opportunity, understanding the broader ecosystem helps calibrate what is expected. A company with a legacy Java system and a React frontend has different needs than a full-stack Node.js startup. The frontend might look the same. The context is completely different.

This encyclopedia covers JavaScript and the web frontend because that is where most of its readers spend most of their time. But the map is not the territory.

---

*Next: Why React, Angular, and Vue Exist at the Same Time. Even inside JavaScript, the ecosystem fragmented into distinct schools of thought. Understanding why each one exists is understanding how technical ecosystems actually work.*
