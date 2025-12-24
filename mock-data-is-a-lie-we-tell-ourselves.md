# Mock Data is a Lie We Tell Ourselves

We love mocks. They are fast. They are reliable. They are safe.

They are also a complete fabrication of reality.

## The Honeymoon Phase

You start building your UI. You create a `data.json` file.
The user object has every field populated. The list of items has exactly 10 elements. The images are all perfectly sized 400x400 placeholders.

You build components that rely on this perfection. "If `user.name` exists, render the avatar." It feels productive. It looks great.

## The Crash

Then comes "Integration Day." You switch the URL from `localhost:3000/api` to the real staging server.

**1. The 500ms Reality Gap**
Your mocks responded in 0ms. The real API takes 500ms. Suddenly, your sleek UI flashes white, then content, then white again. You never built loading states because you never *felt* the wait.

**2. The Partial Failure**
Your mock user always had a `subscription`. The real API returns `null` for new users. Your specialized "Pro Dashboard" component crashes the entire app because you didn't check for existence.

**3. The Rate Limit**
You put a `fetch` call in a `useEffect` without a dependency array. With mocks, it was fine—just a silent infinite loop. With the real API, you just get banned for 429 Too Many Requests in 3 seconds.

## The Lesson

We treat integration as a "step" at the end of the process. "I'll build the frontend, then hook it up."

This is backwards.

**Integration is the job.**

The UI is just a function of the data's chaos. If you aren't developing against that chaos—against the latency, the missing fields, the errors—you aren't building software. You're just painting.

Stop trusting your mocks. Start breaking your UI early.
