Phase 3 — Post pending reviews
Only after the user picks findings. Posting is mechanical — no re-analysis.
Mechanics (load-bearing details):
Authenticated gh CLI. One pending review per PR per reviewer — bundle
all of that PR's findings as threads in a single call.
Create drafts via GraphQL, NOT the REST bulk comments array (REST
silently turns draft comments into file-level comments with no line):
mutation($pr: ID!, $summary: String!, $threads: [DraftPullRequestReviewThread!]!) {
  addPullRequestReview(input: { pullRequestId: $pr, body: $summary, threads: $threads }) {
    pullRequestReview { id state }
  }
}
​
Each thread: { path, line, body } with the finding text. No event
field — that is what keeps it PENDING.
Anchor line to a line that exists in the file at the head commit
(changed lines are safest; every line of a new file is fair game).
Never guess line numbers — grep the head file for the anchor string.
Review body states it is a pending review and summarizes severity counts.
After posting, verify: review state === "PENDING" and
repos/.../pulls/{n}/comments shows 0 by the posting account.
Return one deep link per PR
(pull/NNN#pullrequestreview-<id>), and remind the user that only they
can submit.
Cross-repo feature batches: check that submodule bumps point at the
sibling PRs' head commits, and that shared-component changes are consumed
consistently by every app (same prop names, same conventions).
