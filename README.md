# Fact-Check-AI-Automation-
# 🧠 Fact Check AI

Fact Check AI is an automated workflow built using [n8n](https://n8n.io/) that verifies the authenticity of user-submitted claims using third-party fact-checking sources. It's designed for integration into chat-based platforms and uses a LangChain-compatible trigger to respond in real time.

Demo Video 
https://drive.google.com/file/d/1JIcQhRtgyAPkpi_n07-c6-NsvrR4bfbq/view?usp=sharing
https://drive.google.com/file/d/1bU6Pf8EA70jIcE-QXK3XlOPciepbAfTO/view?usp=sharing

---

## ⚙️ How It Works

The workflow consists of the following three nodes:

1. **Chat Trigger** (`When chat message received`)
   - Listens for user input (claim) in a chat interface.

2. **Fact Checking Node** (`Fact Check AI`)
   - Sends the input query to a custom `FactCheck` API that searches for existing fact-checks from reliable sources.

3. **Code Node** (`Code`)
   - Processes and formats the returned data using JavaScript to extract:
     - ✅ Verdict (True, False, Misleading, etc.)
     - 🧾 Original Claim
     - 🏢 Fact-Checking Organization
     - 🔗 Source URL
     - 📄 Title of the Fact Check

---

## 🛠 Example Code

Below is the core transformation logic inside the Code node:

```js
// Get the list of claims from the previous node's output
const claims = $input.item.json.claims;

// If there are no claims, return nothing
if (!claims || claims.length === 0) {
  return [];
}

// Map and format each claim
const formattedResults = claims.map(claim => {
  return {
    json: {
      Verdict: claim.claimReview?.[0]?.textualRating,
      Claim: claim.text,
      FactChecker: claim.claimReview?.[0]?.publisher?.name,
      SourceURL: claim.claimReview?.[0]?.url,
      FactCheckTitle: claim.claimReview?.[0]?.title,
    }
  };
});

return formattedResults;
