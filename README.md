1.How to run the project locally?
git clone https://github.com/wnubhav/RAG.
cd RAG

setup python environment and run with your personal api-key.

2.Architecture Explanation
I went with LangGraph instead of AutoGen mainly because the assignment specifically recommends LangGraph as the preferred framework. Beyond that, it just fits this project really well. LangGraph lets you build agent workflows as an actual graph, with clear nodes and edges, which makes it easy to handle things like intent-based routing, step-by-step lead qualification, and making sure the lead-capture tool only fires at the right moment. AutoGen is great when you need several agents talking to each other, but here we only need one focused conversational agent, so LangGraph felt simpler and more appropriate.
For state management, I created a custom AgentState using a TypedDict. It holds four main pieces:
messages – the full conversation history (user messages, agent replies, tool messages), which gives the agent memory across 5–6+ turns
intent – the currently detected user intent
lead_details – a dictionary that gradually collects name, email, and platform
lead_collected – a boolean that stops the tool from being called more than once

3.WhatsApp Deployment
To integrate this agent with WhatsApp using Webhooks:
i.Use the official WhatsApp Business Platform . Obtain a phone number ID, access token, and verify webhook URL.
ii.Build a webhook server
iii.Create a simple HTTP server (FastAPI / Flask / Express) with two endpoints: GET(handle Meta’s verification challenge ) and POST (receive incoming messages)

Message handling logic:
i.Extract sender phone number (session key)
ii.Load or initialize agent state from persistent storage (Redis / PostgreSQL) using sender ID as key
iii.Pass the user message to the LangGraph app.invoke() with the persisted state
iv.Get the agent’s response message(s)
v.Send reply via WhatsApp Cloud API using the access token

State persistence:
i.Serialize/deserialize AgentState (messages, lead_details, lead_collected) per user session.
ii.Expire old sessions after inactivity.
