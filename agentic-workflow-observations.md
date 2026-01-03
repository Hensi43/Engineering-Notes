# Junior Dev Log: The DOM is Finally Clicking

**The Struggle is Real**
As a beginner, "integrating with Chrome" usually means me opening the console, typing `document.querySelector(...)`, effectively getting `null`, and giving up. The invisible wall between my code and what actually happens in the browser window has always felt impossible to cross. But I stuck to it.

**Seeing the Wiring**
Using Antigravity changed how I look at the DOM. It’s not just about getting the code written; it’s about watching *how* it interacts with Chrome.
*   When I ask it to "find the login button," I see it navigating the DOM tree in ways I didn't know were possible.
*   It handles the messy parts of Chrome integration—waiting for elements to load, handling shadow DOMs, dealing with event listeners—that usually trip me up.

**Learning by Watching**
I'm not just letting the agent do the work; I'm reverse-engineering its logic.
*   "Oh, *that's* how you target a dynamic class."
*   "So *that's* the lifecycle of a Chrome extension content script."

**Verdict**
I'm still a starter. I still get stuck. But now, instead of banging my head against the Chrome DevTools, I have a guide that shows me the wiring under the hood. The DOM isn't a black box anymore; it's just another API, and I'm finally learning how to speak its language.

# 2025-12-23: Refine Exam Evaluator UI

**From Functional to Polished**
Today was about taking the Exam Evaluator web app from "it works" to "it feels good to use." The logic was there, but the interface needed love.

**Key Changes**
*   **Visual Stepper:** Added a clear progress indicator. Users now know exactly where they are—uploading, processing, or viewing results.
*   **Structured Output:** Moved away from raw text dumps. The evaluation results are now presented in a structured, readable format.
*   **Modern Styling:** Refined the input fields and cards. Better spacing, cleaner typography, and a more coherent color palette.

**Reflection**
It's amazing how much a few UI tweaks can change the perception of an app. The underlying AI didn't change, but the value of the tool feels doubled just because the data is presented better.

# 2025-12-24: The Monorepo Reality Check

**Context**
Scaffolding the Exam Evaluator with Expo (mobile) and FastAPI (backend) in a single monorepo.

**The Friction**
Ideally, a monorepo is "one repo to rule them all." In reality, it's "one repo to configure them all."
*   **Context Switching:** Jumping between `pip install` for the backend and `npm install` for the frontend in the same terminal session is a mental hiccup every time.
*   **Tooling Overlap:** VS Code gets confused. "Is this a Python project? A React project?" It tries to lint my Python files with ESLint rules until I tweak the workspace settings.
*   **The "Glue" Code:** I spent more time writing scripts to run both servers simultaneously than I did writing the actual servers.

**Reflection**
Monorepos solve the "where is everything?" problem but introduce the "how do I run everything?" problem. It was a stark contrast to the `engineering-notes` repo I'm in right now, which is simple. The difference between a controlled environment and reality—which I wrote about in "Mock Data is a Lie"—isn't just philosophy; it was my exact morning.

# 2025-12-27: The "Just Styling" Trap

**The Goal**
Fix the visual glitches on the mobile app. Make it look like the design. Simple CSS work, right?

**The Trap**
I underestimated the "Native" in React Native.
*   **Babel Hell:** `NativeWind` isn't just a library; it's a compiler hack. If your Babel config is slightly off, styles fail silently.
*   **The Mobile Feedback Loop:** Change code -> Save -> Bundler rebuilds -> Simulator reloads. It’s 10x slower than the DOM.
*   **Invisible Errors:** The app crashes with cryptic messages because I imported a web-only component.

**Takeaway**
On the web, we are spoiled. The browser swallows our mistakes. In mobile land, the environment is hostile. You can't just "hack it" until it works; you have to understand the build pipeline.

# 2026-01-02: The Semester Hiatus

**The Silence**
The repo has been quiet. Not abandoned, just paused. The semester demanded full attention—studying for papers in Cryptography, Project Management, and Biomedical Instrumentations took priority over the "green squares" on GitHub.

**The Restart** 
There's always a bit of friction when restarting after a few days off. The context switching cost is real. But the best way to resume is simply to write something. This entry is the commit that breaks the silence. I was studying, I couldn't commit, but I'm back now.

# 2026-01-02: The Circular Dependency Trap

**The Context**
While refining the `exam-evaluator`, I hit a classic wall: Circular Imports in Python.
*   `User` model depends on `Exam`.
*   `Exam` model depends on `User`.
*   Result: `ImportError` at startup.

**The Fix**
It wasn't just moving files. I had to strictly separate Pydantic schemas (data shapes) from ORM models (database tables).
*   **Schemas:** Pure data validation. No logic.
*   **Models:** Database relationships.
*   **TYPE_CHECKING:** Using Python's `if TYPE_CHECKING:` block to allow the IDE to see the types without confusing the runtime interpreter.

**UI Refactor**
I also cleaned up the Auth flow. Instead of a janky "check token then render," I implemented a proper loading state that blocks the UI until the user session is confirmed. Authenticating is a state, not just a side effect.

# 2026-01-03: The Database Handshake

**The Context**
I spent today transforming FormSync into a real SaaS. The headline feature? "Google Integration." The reality? A deep dive into database linking.

**The "Just Add Auth" Myth**
It’s easy to think of authentication as a UI widget. You drop in a "Sign in with Google" button, and you're done. But in the `@/update` workflow, I had to confront the backend reality.
*   **Identity vs. Record:** Google verifies the *identity*, but my Supabase database needs a *record*. Bridging that gap—ensuring the Google token actually resolves to a row in the `users` table—is where the real engineering happens.
*   **The Clean Up:** I also had to scrub the codebase of hardcoded secrets and unused artifacts. It wasn't glamorous.

**The Verdict**
The UI is just the tip of the iceberg. The "Google Integration" didn't truly exist until the database handshake was confirmed. Until the user is a row in the database, the feature is just a mirage.
