---
target: homepage (site-wide review)
total_score: 14
max_score: 28
na_heuristics: 5,7,10
p0_count: 1
p1_count: 2
target_identity: "file:/Users/grahamplata/dev/grahamplata/hugo/content/_index.md"
target_fingerprint: "sha256:472226e770aa3b900920a28a51636fcdeaf5318d9db6e67f22dd6aa5433101dd"
target_path: /Users/grahamplata/dev/grahamplata/hugo/content/_index.md
timestamp: 2026-09-28T01-52-52Z
slug: content-index-md
---
Method: dual-agent (A: design-review agent · B: detector/evidence agent)

# Design Critique — Graham Plata's site (homepage as anchor, reviewed holistically)

## Design Health Score

| # | Heuristic | Score | Key Issue |
|---|-----------|-------|-----------|
| 1 | Visibility of System Status | 2 | Nav never marks the current section |
| 2 | Match System / Real World | 3 | Real domain terms match how an actual builder talks |
| 3 | User Control and Freedom | 1 | 404 page is a true dead end |
| 4 | Consistency and Standards | 2 | Posts vs Projects list items use different visual weights for the same pattern |
| 5 | Error Prevention | n/a | Static site, no forms/input |
| 6 | Recognition Rather Than Recall | 3 | Lists consistently show description text alongside titles |
| 7 | Flexibility and Efficiency | n/a | No power-user paths exist at this content scale |
| 8 | Aesthetic and Minimalist Design | 3 | Genuinely uncluttered, at the cost of feeling generic |
| 9 | Error Recovery | 0 | 404 page gives zero help |
| 10 | Help and Documentation | n/a | Not applicable to a personal blog |
| **Total** | | **14/28** | **Acceptable (50%)** |

## Design Specificity Verdict
Mixed — the chrome is generic/interchangeable, the content (Wookies packing list, 16v BOM, About page photo) is specific and earned. Detector runs (source + live, both viewports) came back completely clean (exit 0, zero findings) on both agents' scans, yet the LLM review found a P0 dead-end 404, a verified WCAG AA link-contrast failure (3.95:1, independently confirmed), a broken template field, and structural duplication — a clear detector blind spot.

## Priority Issues

[P0] 404 page is a complete, unstyled dead end. `themes/etch/layouts/404.html` is a 0-byte file (verified via wc -c); live requests return bare `<h1>Page Not Found</h1>` with no header/nav/footer/CSS. Fix: populate 404.html via baseof.html's block structure with a link home. -> /impeccable harden

[P1] Project category data exists but the template checks the wrong field name. `layouts/projects/list.html` checks `.Params.project_type`; all project front matter uses `type:` instead (verified across all 4 project files) — the category span has never rendered. Tags are also populated but never surfaced anywhere. Fix: correct the field-name mismatch and surface tags via Hugo taxonomy pages. -> /impeccable clarify

[P1] Home page and /posts/ are functionally the same page — both render the identical year-grouped list with no distinct purpose. Fix: give home a distinct job (e.g. recent items across posts+projects) instead of duplicating the archive. -> /impeccable layout

[P2] Light-mode link color fails WCAG AA contrast — verified 3.95:1 (`#007dfa` on white), independently recomputed via the WCAG relative-luminance formula. Dark mode passes at ~7.6:1. Fix: darken `--color-link` in main.css's :root block toward ~#0066d6. -> /impeccable typeset

[P2] Inconsistent alt text — verified: all 6 images in content/posts/wookies/index.md share literal alt="Wookies in the Woods"; content/about/index.md's hero image has alt="Toonami" (describes nothing in the photo). The L'oe Show post (fixed earlier this session) proves the site can do this well. Fix: rewrite Wookies/About alt text following the L'oe Show pattern. -> /impeccable harden

## Persona Red Flags
Jordan (First-Timer): lands on /posts/nullstring/ (title+date, nothing else) or a mistyped URL (brandless 404) with no signal either is intentional/still-on-site.
Sam (Accessibility-Dependent): screen reader announces the same alt text for 6 different Wookies photos; "Toonami" for an unrelated About page photo; no landmark nav on the 404.
Casey (Distracted Mobile User): /projects/ list items (car build vs. software project) share identical typography with no thumbnail, so fast thumb-scanning can't visually pattern-match.

## Minor Observations
- Copyright footer hardcoded to "© 2026" — will go stale in 2027.
- 16v Bill-of-Materials renders "TBD"/blank cells with the same visual weight as real data.
- Gists list is a third, slightly different list template pattern vs Posts/Projects; its language tag renders as unstyled plain text.
- `--color-border-strong` (#b1b1b1) computes to ~2.14:1 against white, below the 3:1 UI-boundary guidance (low severity, decorative).
- The theme ships an unused {{< toc >}} shortcode; the growing 16v "living document" page would benefit from it.

## Questions to Consider
1. If the header/footer were removed, could you tell this was a car-builder's site from the homepage alone?
2. Why do Posts and Projects have no relationship to each other when the content (and its tags) clearly do?
