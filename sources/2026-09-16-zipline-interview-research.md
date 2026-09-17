# Raw research notes — Zipline interview process (web, 2026-09-16)

Collected via web search on 2026-09-16. Immutable. Secondhand aggregator and
candidate-report content — not from Zipline. Treat as rumour-grade except where
it comes from a Zipline job posting.

## Company facts
- Drone delivery / instant logistics. Founded by Keller Rinaudo Cliffton (CEO).
- Jan 2026: $600M round led by Valor Equity Partners, valuing the company at
  $7.6B. Oversubscribed; a later tranche took Series H to ~$800M total.
  Investors incl. Fidelity, Baillie Gifford, Tiger Global, Paradigm.
  Source: dronexl.co/2026/01/20/zipline-reaches-7-6-billion-valuation-drone/
- ~1,545 employees as of 2026-01-31. Source: Tracxn company profile.
- 2 million cumulative deliveries; second million ~4x faster than the first.
- Seven countries of operation; African markets incl. Rwanda, Ghana, Nigeria,
  Kenya, Côte d'Ivoire.
- Rwanda: powers the national blood delivery network. Feb 2026 expansion
  agreement — first country with nationwide autonomous delivery. Third hub in
  Karongi District, Africa's first urban drone delivery network, and Zipline's
  first overseas AI/robotics R&D facility.
  Source: dronexl.co/2026/02/05/zipline-rwanda-nationwide-autonomous-delivery/
- Funded in part by a $150M US State Department contract (Nov 2025) aiming to
  triple the African network from 5,000 to 15,000 health facilities.
- Ghana: COVID-19 vaccine distribution network.
- US expansion 2026: Houston, Phoenix, 4+ states. CEO called 2026 the
  "breakout year".

## Africa-facing engineering roles (from Zipline job postings)
Senior Full Stack Software Engineer — Health Care. Kigali, Rwanda (remote option).
Posted 2026-03-19, now closed. Source: jobs.techstars.com/companies/zipline
- Team: **Nest Applications Software** — fulfilment, maintenance and flight
  systems powering operations in Africa.
- Stack: Python, Go, React, Kafka, PostgreSQL, AWS, Kubernetes.
- Wants: 8+ yrs distributed systems in production; full stack Python/Go; React;
  Kafka or similar event-driven; PostgreSQL modelling and perf tuning;
  automated testing at multiple layers; mentoring juniors.
- Responsibilities lean heavily on user research into operational workflows,
  modular architecture, observability and reliability, reducing team friction.
- Nice to have: fulfilment / logistics / warehouse systems; willingness to
  travel or relocate to Kigali; production K8s and AWS.

## Wider Zipline stack (across teams, from postings)
- Backend/platform: Go, Python, PostgreSQL + PostGIS, Kafka, AWS.
- ML infra: Python, PyTorch, Kubernetes, AWS, infrastructure-as-code.
- Onboard / edge: Python and C++ on constrained real-time Linux.
- Maps platform: geospatial data pipelines, routing, flight intelligence —
  Go, Python, AWS.
- Data platform: React, Python, SQL, Rust. Spark, Airflow, EKS elsewhere.

## Reported interview process
Senior SWE loop, six stages (Dataford guide):
1. Recruiter call — background, interest in Zipline, basic role requirements.
2. Hiring manager screen.
3. Take-home assessment.
4. Technical interview on the take-home.
5. System design.
6. Product prioritisation.

Glassdoor (22 SWE interview reviews):
- 33.3% positive experience; difficulty 2.9/5 for SWE, 3.3/5 for Senior SWE.
- Average process length ~27 days.
- Rounds reported: coding for a real-life use case where domain integration
  matters (e.g. serial ports for hardware); **code comprehension** — read and
  upgrade an existing function; behavioural HR round; cross-functional
  interview with an electrical engineer.

Take-home:
- Guideline ~2 hours, but Blind reports of candidates spending ~8.
- Expected to be production-grade: clean architecture, tests, modular design.

## Reported question themes
- System design: a communication bus for sensor information; backend services
  for drone flight coordination and airspace routing; critical supply chain
  management design questions.
- Behavioural: why Zipline / why the mission; "customer obsession" in
  engineering work; talk about your *worsts* and what you learned; conflict
  between team members; a difficult decision; an innovative project;
  unsatisfied with a design outcome; able to work independently; comfortable
  travelling.
- Technical chat: debugging tools, process for approaching a new problem,
  Python experience, Django, designing and testing software.
- Repeated framing across guides: **not standard big-tech algorithmic puzzles**
  — practical, high-integrity engineering, case studies, deep technical
  discussion of real systems.

## Source URLs
- https://www.zipline.com/
- https://dataford.io/interview-guides/zipline/software-engineer
- https://dataford.io/interview-guides/zipline/forward-deployed-engineer
- https://www.glassdoor.com/Interview/Zipline-Software-Engineer-Interview-Questions-EI_IE1394276.0,7_KO8,25.htm
- https://www.glassdoor.com/Interview/Zipline-Senior-Software-Engineer-Interview-Questions-EI_IE1394276.0,7_KO8,32.htm

## CORRECTION added 2026-09-16 (same day): two companies named Zipline
- Glassdoor **E1394276** = Transportation & Logistics, 257 reviews, 3.7 stars =
  **the drone company**. This is the ID behind every interview figure cited above.
- Glassdoor **E1732473** = Information Technology, 27 reviews, 4.7 stars =
  **Retail Zipline**, a retail-communications SaaS. Different company.
- `github.com/retailzipline/zipline_take_home` is **Retail Zipline's** public
  take-home: extend a bare-bones email service, Rails 7 + Hotwire/Turbo/Stimulus.
  It is NOT the drone company's take-home. It is the top search hit for
  "zipline take home" and is a decoy.

## Take-home, second pass (2026-09-16)
- Stated guideline 2-4 hours. Dataford says 3-4 to do a half-decent job.
  Blind/Glassdoor candidates report 8-12 hours to be competitive.
- Reported example tasks (aggregator-sourced, not confirmed):
  - an **inventory management and packing API** optimising package distribution
    under strict payload weight thresholds
  - a **path-planning simulation** computing risk-aware flight trajectories
    around localised weather anomalies
- Stated evaluation criteria: code quality and modularity (extensible,
  self-documenting, organised); optimisation and trade-offs (complexity
  understood and choices justified); error handling and edge cases.
- Followed by a **~45-minute presentation of the solution to a panel** that asks
  why and how.
- Negative signal: multiple candidates report completing the take-home and never
  hearing back, describing it as disrespectful of their time.
- Sources: dataford.io/interview-guides/zipline/software-engineer;
  glassdoor E1394276 SWE interview reviews; teamblind.com/company/Zipline
- https://climbtheladder.com/zipline-interview-questions/
- https://www.teamblind.com/company/Zipline/posts/zipline-interview
- https://jobs.techstars.com/companies/zipline/jobs/71249905-senior-full-stack-software-engineer-health-care
- https://dronexl.co/2026/01/20/zipline-reaches-7-6-billion-valuation-drone/
- https://dronexl.co/2026/02/05/zipline-rwanda-nationwide-autonomous-delivery/
- https://tracxn.com/d/companies/zipline
