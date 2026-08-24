A browser-based research prototype studying whether people can detect deception from a human or an AI in a safety-critical decision task.

How the human/AI advisory split works
Human advisories: pulled from a pool of real lines submitted via contributor-console.html, merged in via merge-tool.html. Contributors see only the situation description, not which action is actually safe or risky — so they have to judge it themselves, same as the AI.
AI advisories, generated live via the Claude API at the moment each round starts, using a hidden instruction telling it to either genuinely help or persuasively push the risky option. The AI is also given the situation only — not the safe/risk labels — so both sides work from the same information.
Running it locally (no live AI)

Just open index.html in any browser. The game works fully offline in this mode human advisories will show correctly, but "machine" rounds will silently fall back to human-submitted text as well, since browsers can't call the Anthropic API directly without a key.

Running it with live AI-generated advisories

Browsers can't call the Anthropic API directly (no CORS support, and an API key should never be exposed in client-side code). This requires a small proxy:

Create an API key at console.anthropic.com.
Deploy a Cloudflare Worker that forwards requests to Anthropic's API, holding the key as a server-side secret (see /worker/proxy.js for the script, if included — otherwise ask for it).
In index.html, find generateMachineAdvisory() and set the fetch() URL to your deployed Worker's URL.
Host index.html somewhere with a real HTTPS origin (GitHub Pages, Netlify, etc.) - it will not work opened as a local file:// page, since local files can't make outbound network requests.
Add a spending cap on your Anthropic account before going live, and lock the Worker to only accept requests from your specific site's origin.

Data collected

Each played session logs, per round: the scenario, the true source (human/machine), the true intent (help/sabotage), the advisory text shown, the player's action choice, the outcome, and both of the player's verdict guesses with correctness. This exports as a timestamped JSON file via the in-game "Export session data" button - nothing is transmitted or stored anywhere automatically.

Status

Working prototype. Mechanic validated solo. Human data collection pipeline built and tested with one contributor. Live AI generation confirmed working end-to-end via Cloudflare Worker proxy. Not yet run as a real multi-participant study.
