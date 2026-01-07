# NetWarden: Controlling the Chaos

I recently built "NetWarden – Local Gateway Edition", a system designed to bring sanity to local network traffic on Linux and macOS. It wasn't just about limiting speed; it was about intelligent traffic shaping.

## The Problem
Most bandwidth limiters are dumb pipes. They don't care if you're on a Zoom call or downloading a 50GB game. I needed something that understood *context*.

## System Architecture

The system was built with four distinct components:

1.  **Traffic Monitor**: A continuous observer of per-process and per-interface bandwidth. I couldn't manage what I couldn't measure.
2.  **Classifier**: The brain. It categorizes traffic into High (Zoom/SSH), Medium (Dev tools), and Low (Torrents/Downloads) based on rules.
3.  **Fairness Engine**: The conscience. It ensures high priority doesn't starve everything else, and that no single process hogs the quota.
4.  **Throttling Controller**: The enforcer. It talks directly to the OS primitives (`tc` on Linux, `pfctl`/`dnctl` on macOS).

## Technical Observations

### The OS is the Limit
Interfacing with `tc` and `pfctl` is efficient but brittle. Cross-platform compatibility for network shaping is a minefield of different syntax and capabilities. Python serving as the high-level logic controller over these low-level C/kernel tools proved to be a powerful pattern, abstracting the complexity of the syscalls away from the business logic.

### Fairness is Hard
Implementing a "fair-share" algorithm sounds simple until you strictly define "fair". I opted to prevent starvation of low-priority tasks while strictly capping them during congestion. This requires constant re-evaluation of the state, not just a static rule set.

### SNI Sniffing for Context
To make the throttling truly "smart", distinguishing traffic by port or process wasn't enough. I needed to know the *destination*. I experimented with SNI (Server Name Indication) sniffing using `scapy`. By intercepting the initial `Client Hello` packet of the TLS handshake on port 443, I could extract the domain name (e.g., `youtube.com`) before the traffic became encrypted. This allowed for domain-based rules (e.g., "Throttle Netflix", "Boost GitHub") without needing a full MITM proxy. I had to implement a manual byte-parser fallback because high-level library parsers often failed on partial packet captures.
