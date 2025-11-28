We have packaged the Mock Graph MDM simulation into a Python file. This script simulates an Agentic workflow (Validator, Resolver, Cataloger) against a 10,000-row synthetic dataset to benchmark performance metrics like F1 Score, Reduction Ratio, and Autonomy.

Mock Graph Engine: Instead of spinning up a Docker container for Neo4j, you are using a Python adjacency list (collections.defaultdict). This allows for lightning-fast traversal (O(1) lookups) while mimicking the "Connected Components" logic used in real graph resolution.

Blocking Strategy (Reduction Ratio): The resolver_agent uses a "First Letter" blocking key. This is critical for scaling. Without it, you would do 10,000 * 10,000 comparisons (50M pairs). The Reduction Ratio metric in the output will likely be very high (~99%), proving the efficiency of blocking.

Human-in-the-Loop (Critic): The critic function mocks a human supervisor. By randomly rejecting 2% of merges, you calculate an Autonomy Rate. In a real system, this would be the percentage of decisions the AI makes without flagging for human review.

Cluster Purity: This measures the quality of your Golden Records. A score of 1.0 means every cluster contains only records belonging to the same truth_id.


Memory Hits (RAG):
This represents decisions the AI made instantly by recalling past experiences, rather than calculating from scratch. This mimics an experienced human steward.

Self-Correction (Reflector):
The autonomous_resolver might propose a match with low confidence (0.60). Instead of stopping, it passes this to reflector_agent. The Reflector applies "System 2" thinking (deeper analysis, mocked here by character-set Jaccard similarity). If it finds evidence, it overrules the hesitation and approves the match. This is Autonomy.

Quarantine (Async):
The code tracks stats["quarantined"]. In a Level 4 system, these items would pop up a dialog box [Yes/No] and block the thread. In Level 5, they are sidelined into a bucket for weekly human review, allowing the system to run at full speed 24/7.


Test Setup
Dataset: 100,000 synthetic customer records generated via Python Faker.
Entropy Injection:
30% Duplicates: 30,000 hidden pairs were inserted.
20% Typos: Text fields were corrupted with Levenshtein distance 1-2 errors.
10% Null Values: Critical fields (Email, Phone) were randomly deleted.
Environment: A Python simulation ((random, f-strings) to mimic the behavior, speed, and outputs of LLM agents so we can demo it instantly without API costs or latency) of the Pod Architecture, Real life scenario would be using faster models (GPT-4o-mini) for Worker nodes (Validator/Resolver) and the Critic/Reflector uses the reasoning model (GPT-4o).
  
You can run this file directly in any Python 3 environment. It uses only standard libraries, so no pip install is required.


  
