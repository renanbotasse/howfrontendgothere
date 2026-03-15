
Embedding AI into a product used to require a machine learning team, model training infrastructure, and months of work. The OpenAI API changed this. Starting in 2020, and especially after ChatGPT's launch in late 2022, calling an AI model became an HTTP request. Adding AI capabilities to a frontend application became something a developer with no ML background could do in an afternoon.

The result was a rapid shift: AI stopped being a feature that large tech companies had and started being a feature that any team with a credit card and an API key could ship.

---

## The streaming response pattern

The first thing most developers noticed when integrating an LLM API was that responses take time — a few seconds for short answers, longer for detailed ones. Showing a loading spinner for five seconds while a response generates is a bad user experience. The solution is streaming.

LLM APIs support streaming responses via Server-Sent Events. The model generates tokens (roughly, words or word fragments) one at a time, and the API streams each token as it's generated. The UI appends each token to the displayed text, creating the "typing" effect that has become the signature visual of AI interfaces.

```javascript
const response = await fetch('/api/chat', {
  method: 'POST',
  body: JSON.stringify({ message: userInput }),
});

const reader = response.body.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;
  const chunk = decoder.decode(value);
  setDisplayedText(prev => prev + chunk);
}
```

This pattern is now so common that the "streaming text" animation is immediately recognizable as "this is an AI response." Vercel's AI SDK abstracts the streaming logic and provides React hooks that handle the state management.

---

## Semantic search

Traditional keyword search looks for exact or near-exact matches. A search for "receipt" won't find documents tagged with "invoice" even though they're the same thing. Semantic search uses embedding models to convert text into vectors — numerical representations where similar meanings produce similar vectors.

You store document embeddings in a vector database (Pinecone, Weaviate, pgvector in PostgreSQL). When a user searches, you embed their query and find documents with similar vectors. A search for "how do I cancel my subscription" finds articles about "account termination" and "ending your membership" even without the keyword match.

The frontend change is subtle but significant: the search box can be treated as a question box. Interfaces that previously required users to guess the right keyword can accept natural language. The backend does the semantic matching. The frontend's job is to collect the question and display the results.

---

## RAG — grounding AI responses in your data

Language models are trained on data up to their training cutoff. They don't know about your company's specific products, policies, or documentation. Retrieval-Augmented Generation (RAG) is the pattern that grounds AI responses in your data.

The flow: take the user's question, run semantic search against your document store, retrieve the relevant documents, include those documents as context in the prompt, ask the model to answer the question based on that context.

```
User: "What's the return policy for digital downloads?"

Retrieved context: [relevant excerpt from your returns policy page]

Prompt to model: "Given this context: {retrieved_docs}, answer: {user_question}"

Model response: "Based on our policy, digital downloads are non-refundable except..."
```

The model doesn't need to know your policy from training — it reads it from the retrieved context and synthesizes an answer. This pattern enables AI chat support, documentation search, internal knowledge bases, and any use case where the model needs access to specific, current information.

---

## Natural language interfaces

The more ambitious application of LLMs in frontend is replacing structured UI with natural language. Instead of a filter panel with dropdowns for category, price range, and availability — a search input where users type "blue running shoes under $100 in stock." The model parses the intent and translates it to a structured query.

This works well for complex filtering and discovery interfaces where the combinatorial space of options is too large for a traditional UI to present cleanly. Legal document search, product catalog exploration, data analysis interfaces — contexts where the user knows what they want but the structured UI forces them to learn the tool's language.

It works less well when the user doesn't know what they want and benefits from seeing options. A checkout form shouldn't be replaced with natural language — the structured form provides the right affordances. The judgment is about where free-form input reduces friction versus where it creates ambiguity.

---

## The latency challenge

API calls to language models take hundreds of milliseconds to several seconds. This is fast for AI inference; it's slow for UI interactions. The gap between "a button that responds instantly" and "an AI feature that responds in 2 seconds" is the gap between feeling like software and feeling like waiting.

Techniques that reduce perceived latency: streaming responses (show something immediately), optimistic UI (show a plausible placeholder while waiting), eager fetching (start the API call before the user has finished their input), and caching (identical queries return cached results without hitting the API).

The infrastructure choice matters too. Running inference at the edge — on Cloudflare Workers AI, Vercel AI Gateway, or regional endpoints — reduces network latency compared to requests to a single origin. For real-time applications like code generation or inline suggestions, reducing latency from 500ms to 200ms is perceptible.

---

## The abstraction layer

Vercel AI SDK, LangChain, and similar libraries provide abstractions over LLM providers that standardize the API surface and handle streaming, tool use, and message history management. Switching from OpenAI to Anthropic to a local model becomes a configuration change rather than a refactor.

The abstractions also handle patterns that are common enough to be standardized: conversation history management, tool/function calling, structured output extraction. A model that can call functions — search for information, update a database, trigger a workflow — and have the results fed back into the conversation creates a loop that enables more autonomous behavior.

This is the architecture behind AI agents: a model that decides what tools to call, calls them, processes the results, and continues generating. The frontend's role in agentic interfaces is showing the user what the model is doing — which tools it's calling, what decisions it's making — and giving them the right affordances to intervene.

---

*Next: How AI Is Changing Component Generation. V0, Cursor, Claude — generating UI components from descriptions is now a real workflow. What it changes about how frontend code gets written, and what it doesn't change, is worth examining honestly.*
