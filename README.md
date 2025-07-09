# Action Repo

This repository simulates a GitHub repository that triggers webhooks on actions (Push, Pull Request, Merge).

## Setting up the Webhook

1. **Deploy your webhook-repo Flask app** (see its README for setup).
2. **Expose your local Flask server to the internet** (for GitHub to reach it) using [ngrok](https://ngrok.com/):
   ```bash
   ngrok http 5000
   ```
   Copy the HTTPS URL provided by ngrok (e.g., `https://abcd1234.ngrok.io`).
3. **Go to your GitHub repository settings > Webhooks > Add webhook**
   - **Payload URL:** `https://<ngrok-id>.ngrok.io/webhook`
   - **Content type:** `application/json`
   - **Events:** Select "Let me select individual events" and check:
     - Push
     - Pull request
   - **Active:** Yes
   - Save the webhook.

## Testing Locally

You can simulate webhook events using curl:

### Push Event
```bash
curl -X POST http://localhost:5000/webhook \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Event: push" \
  -d '{"after": "abc123", "pusher": {"name": "Travis"}, "ref": "refs/heads/staging"}'
```

### Pull Request Event
```bash
curl -X POST http://localhost:5000/webhook \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Event: pull_request" \
  -d '{"pull_request": {"id": 42, "user": {"login": "Travis"}, "head": {"ref": "staging"}, "base": {"ref": "master"}, "created_at": "2021-04-01T09:00:00Z"}}'
```

### Merge Event (when a PR is merged)
```bash
curl -X POST http://localhost:5000/webhook \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Event: merge" \
  -d '{"pull_request": {"id": 43, "user": {"login": "Travis"}, "head": {"ref": "dev"}, "base": {"ref": "master"}, "merged": true, "merged_at": "2021-04-02T12:00:00Z"}}'
```

## Notes
- The webhook-repo will store and display these events in the UI.
- For real GitHub events, push or open PRs in your repo after setting up the webhook.

# Demo