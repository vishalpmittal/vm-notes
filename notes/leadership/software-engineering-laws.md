# Software Engineering Laws

## Key Takeaways

- **Start simple, stay simple:** Gall's Law and KISS remind us that working complex systems evolve from working simple systems — never design a big-bang architecture from scratch.
- **Org structure dictates system design:** Conway's Law means your team topology will be reflected in your software architecture, whether you intend it or not. Use this deliberately (Inverse Conway Maneuver).
- **People don't scale linearly:** Brooks's Law (adding people to a late project makes it later), the Ringelmann Effect (individual output drops with team size), and Price's Law (square root of contributors do half the work) all argue for small, empowered teams.
- **Metrics corrupt when they become targets:** Goodhart's Law warns that once a measure is used as a goal, people optimize for the metric rather than the outcome it was meant to reflect.
- **Estimation is fundamentally broken:** Hofstadter's Law ("it takes longer than you expect, even accounting for Hofstadter's Law") and Parkinson's Law ("work expands to fill available time") mean aggressive-but-honest timeboxing beats padding.

## Actionable Insights

- **Design for failure:** Apply Murphy's Law by assuming anything that can break will break. Build circuit breakers, retries, and graceful degradation from day one.
- **Guard your APIs ruthlessly:** Hyrum's Law means every observable behavior becomes a contract. Hide internal details, version explicitly, and treat undocumented behavior as a liability.
- **Profile before optimizing:** Knuth's principle — premature optimization wastes effort and adds complexity. Measure first, optimize the bottleneck identified by profiling (Amdahl's Law tells you where parallelism actually helps).
- **Be liberal in what you accept, conservative in what you send:** Postel's Law improves system interoperability and resilience, especially at API boundaries and integration points.
- **Set tight deadlines and small teams:** Combine Parkinson's Law with Brooks's Law — give focused teams constrained timelines to prevent work expansion and communication overhead.
- **Watch for Dunning-Kruger in code reviews and hiring:** The least skilled are often the most confident. Create structured evaluation rubrics rather than relying on self-assessment.

---

**Source:** https://newsletter.techworld-with-milan.com/p/the-20-software-engineering-laws
**Date:** 2026-05-28
**Tags:** software-engineering, engineering-laws, leadership, team-management, system-design, estimation
