# An Exposed API Key, a Suspended Google Project, and the Architecture Lessons Behind a Real-Time AI Voice System

Building AI products is often portrayed as choosing the right model or writing better prompts. In reality, production systems fail for very different reasons.

Recently, while building a production-grade real-time AI voice system, I learned that architecture, security, and platform decisions matter just as much as model quality.

This project ended up teaching me far more than I expected.

## It Started with a Suspended Google Project

The first major issue wasn't latency.

It was security.

During the early development phase, I was using the Gemini API directly, and part of the communication flow exposed an API key on the frontend. Although this is common during rapid prototyping, it is absolutely not something you want in production.

Eventually, Google suspended the Cloud project associated with the application.

I submitted an appeal, but the project remains suspended to this day.

There was only one practical option left: create an entirely new Google Cloud project, redeploy everything, and rethink the architecture from the ground up.

That incident completely changed how I approached authentication and security.

## Then Came the Latency Problem

After rebuilding the infrastructure, I upgraded to Google's latest Gemini models.

The model quality improved significantly, but another issue appeared.

Response latency increased dramatically.

In some conversations, users had to wait 10–15 seconds before hearing a response.

Technically, everything was working.

From a user's perspective, however, the conversation felt slow, robotic, and unnatural.

That was the moment I realized I wasn't fighting network latency anymore.

I was fighting **perceived latency**.

In voice AI, users don't measure milliseconds. They measure how natural the conversation feels.

## Digging into the Architecture

The system was originally built using a WebSocket-based client-to-server streaming architecture.

Instead of routing audio through the backend, the client communicated directly with the AI service in real time.

From a performance perspective, the architecture looked perfectly reasonable. From a production security perspective, it wasn't.

Sensitive communication flows were partially exposed on the frontend, authentication became increasingly difficult to manage, and the design was becoming harder to scale safely.

I moved part of the communication to the backend and introduced an access-token-based authentication flow.

Security improved, but the overall architecture became more complex. Managing API keys, SDK-specific authentication, access tokens, and different service account behaviors across environments started creating unnecessary operational overhead.

It became clear that patching the existing architecture wasn't the right answer.

The architecture itself needed to change.

## Migrating to Vertex AI

The turning point came when I migrated the entire real-time pipeline to Vertex AI (Google AI Agent Platform).

The migration wasn't just about changing APIs. It fundamentally changed how authentication worked.

Instead of exposing API keys, the backend now authenticates using the Google Auth Library with a bound service account. Ephemeral access tokens are generated securely on the server, while requests only require the project ID and location.

- No static API keys
- No sensitive credentials exposed to the client
- A much cleaner and significantly more secure production architecture

## The Performance Difference Was Immediate

Security wasn't the only improvement. Conversation quality improved dramatically.

Streaming became noticeably smoother. Voice turn completion felt much faster. Interruptions were handled more naturally.

Most importantly, conversations finally felt like real conversations.

Interestingly, raw latency didn't decrease by five times.

The **user experience** did.

> Reducing perceived latency turned out to be far more valuable than simply reducing response time.

That distinction changed how I think about building conversational AI.

## The Hardest Part Wasn't the Migration

Ironically, the biggest challenge wasn't implementing the solution. It was finding it.

Vertex AI's documentation around real-time voice interactions, WebSocket orchestration, and production authentication flows is still relatively fragmented. Many implementation details are spread across different examples, APIs, and documentation pages.

After spending hours experimenting, debugging, and reading scattered references, I eventually found the missing pieces — with an unexpected amount of help from Google's own AI Assistant.

Sometimes your best architectural documentation is still experimentation.

## Final Thoughts

This project reinforced something I believe many AI teams eventually discover.

Building production AI systems is no longer just about selecting the best model. It's about designing reliable systems around that model:

- Authentication
- Streaming architecture
- Security boundaries
- Infrastructure
- Platform selection
- Observability

These decisions ultimately determine whether an AI product feels like a polished production system or just another demo.

Sometimes, the biggest performance improvement doesn't come from optimizing code.

It comes from making the right architectural decision.
