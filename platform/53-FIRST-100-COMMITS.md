# The First 100 Commits

To maintain git history health, avoid giant pull requests, and enable fast rollbacks, developers must commit in small, incremental steps. Below is the mandatory commit roadmap for the first 100 commits of the Chronicle project.

---

## Commits 1–10: Project Bootstrap & Ingestion Config
1. `init: monorepo root structure and package.json configuration`
2. `config: setup eslint, typescript config, and prettier variables`
3. `feat(infra): initialize local Docker compose for postgres and redis`
4. `feat(infra): create base supabase config files and schema folders`
5. `feat(infra): configure nextjs apps/web tailwind design tokens`
6. `feat(infra): configure fastify services/api routing gateway`
7. `feat(infra): configure packages/ui shared design button primitives`
8. `feat(infra): configure packages/database client connection helper`
9. `config: setup github actions CI build and lint testing workflow`
10. `test: add unit testing framework setup (vitest)`

## Commits 11–20: Database Initialization
11. `feat(db): migration 001_users table schema definition`
12. `feat(db): migration 002_organizations table schema definition`
13. `feat(db): migration 003_repositories table schema definition`
14. `feat(db): migration 004_events table partitioned by timestamp`
15. `feat(db): migration 005_evidence table with confidence scores`
16. `feat(db): migration 006_timelines table for temporal views`
17. `feat(db): migration 007_memories table with decay rate parameters`
18. `feat(db): migration 008_questions table query logger`
19. `feat(db): migration 009_answers table with evidence arrays`
20. `feat(db): migration 010_integrations and embeddings tables`

## Commits 21–35: Authentication & Workspaces
21. `feat(auth): nextauth integration in apps/web`
22. `feat(auth): github oauth callback handler and profile extractor`
23. `feat(auth): google oauth callback handler`
24. `feat(auth): session JWT token storage and decryption helpers`
25. `feat(auth): middleware for JWT token validation`
26. `feat(db): create user_organizations association table`
27. `feat(auth): RBAC role verification decorator for Fastify`
28. `feat(auth): owner permission constraints implementation`
29. `feat(auth): admin permission constraints implementation`
30. `feat(auth): member/viewer permission constraints implementation`
31. `test(auth): unit tests for RBAC role authorization matrix`
32. `test(auth): integration tests for OAuth token rotation`
33. `feat(ui): login screen layout and oauth buttons`
34. `feat(ui): organization creation modal`
35. `feat(ui): workspace switcher dropdown menu`

## Commits 36–50: GitHub Integration
36. `feat(github): github oauth authorization endpoint`
37. `feat(github): secure token encryption using AES-256 GCM`
38. `feat(github): repository discovery client API call`
39. `feat(github): webhook endpoint validation and signature check`
40. `feat(github): push webhook event parser`
41. `feat(github): pull_request webhook event parser`
42. `feat(github): issues and issue_comment webhook parsers`
43. `feat(github): release webhook parser`
44. `feat(queue): initialize Upstash Redis connection with BullMQ`
45. `feat(queue): github ingestion job processor`
46. `feat(github): historical commit backfill worker`
47. `feat(github): historical pull request backfill worker`
48. `feat(github): retry queue backoff helper`
49. `test(github): mock GitHub webhooks payload validation test`
50. `test(github): integration test for BullMQ ingestion reliability`

## Commits 51–65: Slack Integration
51. `feat(slack): slack bot installation oauth endpoint`
52. `feat(slack): save workspace bot tokens in integrations table`
53. `feat(slack): channel discovery API client`
54. `feat(slack): webclient client helper wrapper`
55. `feat(slack): slack event webhook receiver`
56. `feat(slack): message and thread event parser`
57. `feat(slack): message reaction listener (indexing emoji feedback)`
58. `feat(slack): user profiles synchronization service`
59. `feat(queue): slack ingestion job processor`
60. `feat(slack): historical channel message backfill service`
61. `test(slack): mock slack message event webhook test`
62. `test(slack): verify message thread structure indexing logic`
63. `feat(ui): integrations management grid cards`
64. `feat(ui): github repository selector interface`
65. `feat(ui): slack channel sync configuration toggles`

## Commits 66–80: Timeline & Causal Graph Engines
66. `feat(timeline): normalize github commit event primitive`
67. `feat(timeline): normalize slack message event primitive`
68. `feat(timeline): normalize deploy and alert event primitives`
69. `feat(timeline): time-bucket query builder in PostgreSQL`
70. `feat(timeline): zoom logic for semantic cluster scaling`
71. `feat(graph): nodes database schema and CRUD operations`
72. `feat(graph): edges database schema and CRUD operations`
73. `feat(graph): edge creation on GitHub commit actions`
74. `feat(graph): edge creation on Slack thread connections`
75. `feat(graph): graph traversal recursive SQL query`
76. `test(timeline): unit tests for event normalization`
77. `test(graph): verify traversal returns correct path for Commit ↔ Chat`
78. `feat(ui): horizontal/vertical timeline viewer layout`
79. `feat(ui): timeline zoom hotkey controls`
80. `feat(ui): timeline filtering by event type`

## Commits 81–90: Memory & Planning Engines
81. `feat(ai): gemini client setup and api key injection`
82. `feat(memory): gemini flash entity extraction service`
83. `feat(memory): gemini embedding 3072d generator`
84. `feat(memory): save vector embeddings to Supabase pgvector`
85. `feat(memory): alias resolution synonym dictionary`
86. `feat(memory): 24h cron worker for memory consolidation`
87. `feat(memory): short-term vs long-term memory query routing`
88. `feat(planner): question planner query deconstruction`
89. `feat(planner): execution DAG planner compiler`
90. `test(planner): verify planner creates correct retrieval DAG`

## Commits 91–100: Answer Synthesis & UI Polish
91. `feat(ranking): evidence ranking scoring mathematics`
92. `feat(compression): AST-based code signature truncation`
93. `feat(compression): slack thread de-noising parser`
94. `feat(synthesis): gemini 2.5 pro response generator`
95. `feat(synthesis): post-generation grounding citation parser`
96. `feat(synthesis): SSE streaming response route`
97. `feat(ui): query search box and autocomplete`
98. `feat(ui): Answer Card markdown renderer`
99. `feat(ui): split-screen citation Evidence Panel`
100. `feat(obs): opentelemetry spans and standard error log formats`
