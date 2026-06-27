#!/usr/bin/env python3
"""Post a build pass/fail alert to Teams via a Power Automate webhook (stdlib, py3.6+)."""
import json, os, urllib.request, urllib.error

env = os.environ
webhook = env.get("TEAMS_WEBHOOK")
if not webhook:
    print("TEAMS_WEBHOOK not set; skipping alert.")
    raise SystemExit(0)

passed = env.get("ALERT_STATUS", "failure") == "success"
head, color = ("Pipeline passed", "Good") if passed else ("Pipeline failed", "Attention")

payload = {"type": "message", "attachments": [{
    "contentType": "application/vnd.microsoft.card.adaptive",
    "content": {"type": "AdaptiveCard", "version": "1.4",
        "body": [
            {"type": "TextBlock", "size": "Large", "weight": "Bolder", "wrap": True,
             "color": color, "text": "{}: {}".format(head, env.get("ALERT_REPO", ""))},
            {"type": "FactSet", "facts": [
                {"title": "Workflow", "value": env.get("ALERT_WORKFLOW", "")},
                {"title": "Branch", "value": env.get("ALERT_BRANCH", "")},
                {"title": "Actor", "value": env.get("ALERT_ACTOR", "")}]}],
        "actions": [{"type": "Action.OpenUrl", "title": "View run",
                     "url": env.get("ALERT_URL", "")}]}}]}

req = urllib.request.Request(webhook, json.dumps(payload).encode(), method="POST",
                            headers={"Content-Type": "application/json"})
try:
    print("Teams alert -> HTTP", urllib.request.urlopen(req, timeout=10).getcode())
except (urllib.error.HTTPError, urllib.error.URLError) as e:
    print("Teams alert failed ->", getattr(e, "code", None) or e.reason)