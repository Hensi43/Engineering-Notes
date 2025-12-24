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
