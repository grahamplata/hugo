---
target: homepage (site-wide review)
total_score: 17
max_score: 28
na_heuristics: 5,7,10
p0_count: 0
p1_count: 2
target_identity: "file:/Users/grahamplata/dev/grahamplata/hugo/content/_index.md"
target_fingerprint: "sha256:472226e770aa3b900920a28a51636fcdeaf5318d9db6e67f22dd6aa5433101dd"
target_path: /Users/grahamplata/dev/grahamplata/hugo/content/_index.md
timestamp: 2026-09-29T21-05-14Z
slug: content-index-md
---
Method: dual-agent (A: design-review agent · B: detector/evidence agent)

# Design Critique — Graham Plata's site, round 2 (post-fix re-check)

## Design Health Score

| # | Heuristic | Score | Key Issue |
|---|-----------|-------|-----------|
| 1 | Visibility of System Status | 1 | Nav still never marks the current section |
| 2 | Match System / Real World | 4 | Builder vocabulary throughout reads authentic |
| 3 | User Control and Freedom | 3 | 404 dead-end genuinely fixed |
| 4 | Consistency and Standards | 1 | Four divergent list-item templates now coexist and visibly collide on /tags/volkswagen/ |
| 5 | Error Prevention | n/a | Static site, no forms |
| 6 | Recognition Rather Than Recall | 3 | Good pairing, docked for Recent section's disconnected markup |
| 7 | Flexibility and Efficiency | n/a | No power-user paths needed at this scale |
| 8 | Aesthetic and Minimalist Design | 2 | "Jan 1, 0001" repeated 25 times undercuts minimalist intentionality |
| 9 | Error Recovery | 3 | 404 now offers real recovery (up from 0) |
| 10 | Help and Documentation | n/a | Not applicable |
| **Total** | | **17/28 (61%)** | **Acceptable, up from 14/28 (50%)** |

## Design Specificity Verdict
Unchanged: content is specific and earned, chrome remains generic/interchangeable. The new Recent homepage feed was an opportunity to visually distinguish car builds from code projects and didn't take it. Detector scans (source + live, both viewports) returned zero findings on all 11 URLs including /tags/ — confirms the detector cannot see date-formatting or HTML-nesting defects; a clean scan is not proof of quality.

## Priority Issues

[P1] /tags/ shows "Jan 1, 0001" as the date for every tag - independently verified, 25 occurrences via curl. Root cause confirmed in source: themes/etch/layouts/_default/taxonomy.html renders term pages via the shared li.html, which unconditionally formats .Date with no zero-check. Pre-existing latent bug, made newly reachable by this round's tag-footer links. Fix: guard with {{ if not .Date.IsZero }} in _default/li.html. -> /impeccable harden

[P1] Posts and projects render inconsistently on shared taxonomy pages. layouts/posts/li.html shows title+description (no date); themes/etch/layouts/_default/li.html (what projects fall back to) shows title+date (no description). Collides visibly on /tags/volkswagen/ where both types share one list. Fix: normalize the two li templates, or give taxonomy pages their own row template. -> /impeccable clarify

[P2] New homepage Recent list has invalid HTML - confirmed by both agents independently. layouts/index.html's {{- if .Description }} block sits after the closing </li>, so <small class="post-description"> renders as a ul-direct-child sibling of li, not nested inside it. Breaks list semantics for screen readers; works visually only by CSS accident. Fix: move description inside the li; rename class to the site's .description convention. -> /impeccable harden

[P2 - deferred, confirmed still present] Light-mode link contrast still fails WCAG AA (#007dfa on white, 3.95:1). Unchanged from last round's scope decision.

[P2 - deferred, confirmed still present] Inconsistent/generic alt text on Wookies in the Woods (6 images share one alt string) and About page hero (alt="Toonami", describes nothing in the photo). Unchanged.

## Persona Red Flags
Alex (power user): clicks /tags/ expecting fast topic browsing, gets 24+ rows of "Jan 1, 0001" - reads as broken software.
Sam (accessibility-dependent): homepage screen reader announces "list, 6 items" then hits a description text node disconnected from any list item. Same alt-text gaps persist on Wookies/About.
Casey (distracted mobile): on /projects/, "project" label appears on 3 of 4 items (no-op, since already on /projects/) while only Bali-ish Green Rabbit's "car" label is actually distinguishing.

## Minor Observations
- List-item markup now fragmented across four independent templates (_default/li.html, posts/li.html, projects/list.html inline, index.html inline).
- 16v and matchmaking are draft:true and render live under hugo server -D - expected local-dev behavior, verify production build doesn't also set buildDrafts.
- Project type: values inconsistent ("project" x3, "car" x1) - field renders now, but content itself never normalized.
- Copyright footer still hardcoded to "© 2026".
