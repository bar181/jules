**Jules (beta) vs Your MVP Plan — feature-by-feature checklist**

| #                          | Capability                                         | Jules by Google                                            | In Your Plan                                                  | Phase / note        |
| -------------------------- | -------------------------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------- | ------------------- |
| **Core workflow**          |                                                    |                                                            |                                                               |                     |
| 1                          | GitHub OAuth, repo + branch picker                 | ✔ Built-in ([blog.google][1])                              | ✔                                                             | Phase 1             |
| 2                          | Asynchronous jobs (dev can work while agent runs)  | ✔ Cloud VM per task ([blog.google][1])                     | ✔ Queue + worker                                              | Phase 2             |
| 3                          | Full-project clone into **isolated VM**            | ✔ Google Cloud VM ([blog.google][1])                       | 🟡 Ephemeral **Docker container** on your host; not remote VM | Phase 2             |
| 4                          | Multi-step *Plan → diff* preview                   | ✔ Shows plan + reasoning ([blog.google][1])                | ✔                                                             | Phase 3             |
| 5                          | User approval / steerability of plan & diff        | ✔ Edit during run ([blog.google][1])                       | ✔ Approve plan & diff; deeper step-editing post-MVP           | Phase 3 / Tier 1    |
| 6                          | Code generation (tests, features, bug-fixes, deps) | ✔ Test writing, bug fix, dep bump ([blog.google][1])       | ✔                                                             | Phase 3             |
| 7                          | Automated test run before PR                       | ✔ Verifies changes work ([blog.google][1])                 | ✔                                                             | Phase 4             |
| 8                          | Commit + branch + Pull Request creation            | ✔ Auto-PR ([blog.google][1])                               | ✔                                                             | Phase 4             |
| 9                          | Real-time progress stream                          | ✔ Visible workflow ([blog.google][1])                      | ✔ WebSocket log panel                                         | Phase 3             |
| 10                         | Audio changelog / summary                          | ✔ Audio summary of PR ([blog.google][1], [THE DECODER][2]) | ✔ TTS summary                                                 | Phase 4             |
| **Task triggers & limits** |                                                    |                                                            |                                                               |                     |
| 11                         | GitHub label **`assign-to-jules`** starts task     | ✔ Webhook trigger ([AIbase][3])                            | ✔ GitHub App trigger                                          | Phase 5             |
| 12                         | Task quota (5/day, 2 concurrent)                   | ✔ Free-tier limits ([AIbase][3])                           | ✔ Custom quota table                                          | Phase 5             |
| **Scale & concurrency**    |                                                    |                                                            |                                                               |                     |
| 13                         | Parallel task execution                            | ✔ Parallel VMs ([blog.google][1])                          | ✔ Queue + worker pool                                         | Phase 2             |
| **Model & config**         |                                                    |                                                            |                                                               |                     |
| 14                         | Fixed model (Gemini 2.5 Pro)                       | ✔                                                          | 🟢 User-selectable LLM (OpenAI, Gemini, Claude)               | Phase 6             |
| 15                         | BYO LLM keys                                       | ❌                                                          | ✔ Encrypted in Supabase                                       | Phase 6             |
| **UI & history**           |                                                    |                                                            |                                                               |                     |
| 16                         | Task panel / history                               | ✔ “Manage all your Jules tasks” ([blog.google][1])         | 🟡 Basic list in MVP; full history post-MVP                   | Tier 1              |
| **Language scope**         |                                                    |                                                            |                                                               |                     |
| 17                         | Python & JavaScript focus                          | ✔ ([AIbase][3])                                            | ✔ Same initial focus                                          | MVP                 |
| **Privacy & security**     |                                                    |                                                            |                                                               |                     |
| 18                         | Code kept private; not used for model training     | ✔ ([blog.google][1])                                       | ✔ Container isolation; no training use                        | Phase 2             |
| **Extra tooling**          |                                                    |                                                            |                                                               |                     |
| 19                         | Usage panel inside GitHub (no separate UI)         | ✔                                                          | 🟢 Web UI + optional IDE plugin post-MVP                      | Future              |
| 20                         | Auto-dependency bump                               | ✔ ([blog.google][1])                                       | 🟡 Supported via prompt but not explicit task type            | Add to Phase 3 list |

**Legend:** ✔ = fully covered 🟡 = partially / later ❌ = not planned 🟢 = added advantage

---

### Highlights

* **Feature parity is strong:** Every marquee Jules ability has an MVP home except for VM isolation (your design uses Docker) and the auto dep-bump shortcut.
* **Your advantages:** user-pick LLMs, BYO keys, clearer quota config, future IDE plugin.


[1]: https://blog.google/technology/google-labs/jules/ "Jules: Google’s autonomous AI coding agent"
[2]: https://the-decoder.com/google-launches-coding-agent-jules/?utm_source=chatgpt.com "Google launches coding agent \"Jules\" - The Decoder"
[3]: https://www.aibase.com/news/18184 "Google Jules Beta version launched globally! Challenging Codex AI to autonomously generate PR with 5 free tasks per day"
