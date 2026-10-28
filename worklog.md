# Project Worklog — Cyber-Forensics 3D Portfolio (Zain Abbas)

Project: 3D interactive personal portfolio for a Criminology student (Zain Abbas, Gilgit).
Stack: Next.js 16 App Router + TS + Tailwind 4 + shadcn/ui + React Three Fiber + Framer Motion + Prisma/SQLite.

Design contract (all agents MUST follow):
- Palette: bg deep navy `#0f172a` / `#020617`, primary accent cyan `#06b6d4` (cyan-400/500), secondary accent amber-400, text slate-200/300/400.
- Font: Geist Sans for headings/body, Geist Mono (`font-mono`) for terminal/labels/badges.
- Look: dark sleek cyber-forensics. Glass cards `bg-slate-900/70 backdrop-blur border border-cyan-500/15`, rounded-xl, mono uppercase section kickers like `// 01 — ABOUT`, subtle grid backgrounds, scanline vibes, amber used sparingly (warnings, highlights, "CLASSIFIED" tags).
- All section components are client components ('use client'), export named function, live in `src/components/portfolio/`.
- Entrance animations use the shared `<Reveal>` wrapper; card hover tilt uses shared `<TiltCard>`; headings use shared `<SectionHeading>`.
- Root page assembles sections in page.tsx with ids: #home, #about, #skills, #cases, #timeline, #contact.

---
Task ID: 2-a
Agent: Z.ai Code (agent 2-a)
Task: Hero section, evidence globe (R3F), navbar, footer

Work Log:
- Read worklog.md design contract + inspected shared primitives (reveal/tilt-card/section-heading untouched) and shadcn button.
- Created src/components/portfolio/evidence-globe.tsx: 'use client' R3F scene (Canvas dpr [1,1.75], camera [0,0.3,7.2] fov 42, alpha+antialias, absolute-inset style) with: 1400-point Fibonacci-sphere globe (cyan #22d3ee points, additive, depthWrite false), icosahedron wireframe shell (2.64, detail 2, opacity 0.07), 12 QuadraticBezierCurve3 arcs (midpoint pushed to r*1.35, 50 samples) as THREE.Line primitives alternating cyan/amber opacity 0.35, 12 travelling data packets (r 0.045 spheres, per-arc speed/offset, curve.getPoint(t%1) in useFrame), two tilted torus orbit rings (3.5/0.008) precessing in opposite directions, 4 wireframe octa/tetra evidence markers orbiting at r 3.2–4.1 wrapped in drei <Float>, 500-point white/cyan starfield shell r 6–13 with vertexColors; whole scene in one group with constant spin (delta*0.07) + pointer parallax (rotation.x→py*0.18, rotation.y offset→px*0.28, position lerp, factor 0.04) via useThree(s=>s.pointer); all geometry/materials memoized and disposed on unmount.
- Created src/components/portfolio/hero.tsx: next/dynamic ssr:false globe in `absolute inset-0 lg:left-[35%] opacity-40 sm:opacity-60 lg:opacity-100` wrapper, 3 pointer-events-none overlays (56px cyan grid w/ radial mask, left legibility gradient md+, bottom fade to #0f172a), staggered framer-motion entrance (delays 0.05–0.5): pulsing-dot "SYSTEM ONLINE // PORTFOLIO v2.0" badge, gradient H1 "Decoding Crime through Science & Data", terminal lines (zain@forensics:~$ ./profile --mode=investigator / specialties + blinking amber caret), intro paragraph, shadcn Button asChild CTAs (#cases "Explore Case Studies" cyan glow, #contact "Contact Me" outline), mono metadata row (MapPin/GraduationCap/ShieldCheck, amber icons), SCROLL cue with bouncing ChevronDown.
- Created src/components/portfolio/navbar.tsx: fixed header, framer useScroll+useSpring gradient progress bar (cyan→amber, h-[2px], z-[60]), scrollY>24 glass state (bg-[#0f172a]/85 backdrop-blur border-cyan-500/10), Fingerprint logo + Z.ABBAS mono stack, 5 indexed uppercase mono links (amber 01.–05.), CONNECT sm button, AnimatePresence mobile panel (Menu/X, aria-label/aria-expanded/aria-controls, closes on link click).
- Created src/components/portfolio/footer.tsx: mt-auto footer on #020617, 3-col grid (identity+tagline+BASE line, SITEMAP anchors, DIRECT CHANNELS mailto/tel/address with cyan icons), bottom bar with © year line + "CASE FILE: ZA-2025 // STATUS: OPEN" + "BUILT WITH NEXT.JS + THREE.JS".
- Ran `bun run lint` → clean (no errors/warnings anywhere). Ran `bunx tsc --noEmit` → no errors in src/ (only pre-existing errors in examples/ and skills/).

Stage Summary:
- 4 files delivered exactly: evidence-globe.tsx, hero.tsx, navbar.tsx, footer.tsx (all 'use client', named exports EvidenceGlobe/Hero/Navbar/Footer).
- Hero renders the globe right-aligned on desktop via ssr:false dynamic import with pulsing-cyan loading fallback; page.tsx should render <Hero /> inside section #home, then <Navbar /> (fixed) and <Footer /> (mt-auto).
- Anchor ids consumed: #home (logo), #about/#skills/#cases/#timeline/#contact (nav+footer) — matches worklog contract; #cases and #contact also targeted by hero CTAs.
- Globe is dispose-safe (arcs' geometry/materials freed), typed strictly (no any), pointer parallax only (no OrbitControls) so it works as a non-blocking hero background.
- Lint passes project-wide; nothing outside the 4 files + worklog.md was modified.

---

Task ID: 2-b
Agent: GLM (agent 2-b — content sections)
Task: Create About, Skills, CaseStudies, Timeline and Contact section components

Work Log:
- Read worklog.md design contract + inspected shared primitives (reveal.tsx, tilt-card.tsx, section-heading.tsx), shadcn badge/dialog/button APIs and use-toast hook.
- Created `src/components/portfolio/about.tsx`: dossier TiltCard (ZA avatar + Fingerprint watermark, ACTIVE amber badge, icon detail rows), 3-paragraph narrative, amber case-note quote, and local `StatCounter` (framer-motion useInView + animate count-up with 2s fallback timeout guaranteeing final value renders).
- Created `src/components/portfolio/skills.tsx`: masked cyan grid background (inline style, 56px lines), 6 TiltCard skill cells (Brain/Fingerprint/ShieldCheck/Microscope/Radar/FileText) with mono amber indexes, translateZ(30px) content depth, and mono tag chips.
- Created `src/components/portfolio/case-studies.tsx`: 4 typed mock CaseFile entries, md:grid-cols-2 TiltCard grid with status badges (ONGOING=amber, CLOSED=cyan), and a single state-driven shadcn Dialog modal (OVERVIEW / METHODOLOGY / KEY FINDINGS / TOOLS USED sections, cyan/amber ▸ markers, "MOCK / ACADEMIC EXERCISE" footer, custom cyan scrollbar).
- Created `src/components/portfolio/timeline.tsx`: vertical line (left-4 mobile, centered lg), alternating lg:w-1/2 cards, per-item node dots (pulsing ring on first/current entry), 6 entries with EDUCATION/CERTIFICATION/EXPERIENCE type badges + BS Criminology chips.
- Created `src/components/portfolio/contact.tsx`: terminal-style form card (titlebar `zain@forensics:~$ ./initiate_contact --secure`, mono `> FIELD` labels, client validation with amber `! ... required` errors, TRANSMITTING… loading state), POST to relative `/api/contact` with try/catch + toast success/destructive, success terminal output block with returned caseId, plus DIRECT_CHANNELS panel (mailto/tel/plain/LinkedIn/GitHub/ResearchGate rows with ExternalLink icons and PGP footnote).
- Fixed lint issues in my files: moved synchronous setState out of effect body in StatCounter (timer-callback based) and wrapped `date // category` in a template-string expression in the case dialog.
- Verified with `bun run lint` (0 errors) and `bunx tsc --noEmit` (no errors in my files; only pre-existing examples/ + skills/ issues remain). dev.log shows clean compiles.

Stage Summary:
- 5 files created, exactly as scoped: about.tsx, skills.tsx, case-studies.tsx, timeline.tsx, contact.tsx in `src/components/portfolio/` — all 'use client', strict TS, no `any`, palette restricted to slate/cyan/amber.
- All sections consume shared Reveal/TiltCard/SectionHeading primitives via relative imports; no edits to shared primitives, page.tsx, layout.tsx, or globals.css.
- Section ids ready for page assembly/nav: #about, #skills, #cases, #timeline, #contact (inner wrappers `mx-auto max-w-6xl px-6`).
- StatCounter count-up ships with a 2s inView fallback so final values always render; case modal is fully accessible (aria-haspopup, aria-label, DialogTitle/Description).
- Contact posts JSON {name,email,subject,message} to `/api/contact` (relative URL), expects `{ok:true, caseId}`, handles failure with destructive toast + amber terminal error line — API agent (2-c) can rely on this contract.
- Lint: PASS (0 errors). TypeScript: PASS for all 5 files.

---
Task ID: 3
Agent: orchestrator (Z.ai Code)
Task: Backend for secure contact channel + global integration

Work Log:
- Added `ContactMessage` Prisma model (caseId, name, email, subject, message) and pushed schema to SQLite (`bun run db:push`).
- Created `src/app/api/contact/route.ts`: POST handler with zod validation, forensic-style case ID generator (ZA-YYYY-HEX), stores to DB, returns { ok, caseId }.
- Integrated all sections in `src/app/page.tsx` (min-h-screen flex column, sticky footer via mt-auto) and updated `layout.tsx` metadata for Zain Abbas portfolio.
- Added globals: smooth scroll, scroll-padding-top 5rem (navbar offset), cyan selection + themed scrollbars.

Stage Summary:
- Full API contract live: POST /api/contact {name,email,subject,message} -> 201 { ok:true, caseId:"ZA-2025-XXXX" }.
- Page assembled: Navbar / Hero(#home) / About(#about) / Skills(#skills) / CaseStudies(#cases) / Timeline(#timeline) / Contact(#contact) / Footer.
- Lint: PASS.

---
Task ID: 4
Agent: orchestrator (Z.ai Code)
Task: Integration + lint verification

Work Log:
- Merged outputs of Task 2-a and 2-b into page.tsx; verified exports/imports align.
- bun run lint -> 0 errors, 0 warnings.

Stage Summary:
- Ready for browser end-to-end verification (Task 5).

---
Task ID: 5
Agent: orchestrator (Z.ai Code)
Task: Agent Browser end-to-end verification + fixes

Work Log:
- Verified hero: 3D evidence globe (points sphere, 12 bezier data-arcs with traveling packets, orbit rings, evidence markers, starfield, mouse parallax) renders with zero console errors.
- Verified all sections render on scroll: About dossier + counters (25+/8/6/120+), Skills 6 tilt cards, Case Files, Timeline, Contact, Footer.
- Tested golden path: case-file modal (CASE-001 opens with methodology/findings/tools + disclaimer) and contact form end-to-end (POST /api/contact -> 201, terminal success block, case ref ZA-2026-7C30 persisted in SQLite).
- FIX 1: dialog header category text collided with close button -> added pr-8.
- FIX 2: mobile horizontal overflow (454px vs 390px) — implicit auto grid tracks sized to max-content; added explicit grid-cols-1 (minmax(0,1fr)) to all responsive grids + min-w-0 guards in contact.
- FIX 3: hero CTA colors — moved color classes from <a> child to Button className (Radix Slot does plain class concat; tw-merge only runs on the Button props).
- FIX 4: mobile menu now closes on Escape and on resize to desktop.
- Re-verified: NO_H_OVERFLOW at 390x844, desktop 1440x900 clean, lint PASS, dev.log clean, no browser console errors.

Stage Summary:
- Site fully browser-verified: renders, interacts, persists data, responsive, accessible (aria labels, Escape handlers, semantic headings).
- Ready for preview: user should use the Preview Panel / Open in New Tab.

---
Task ID: 6
Agent: orchestrator (Z.ai Code)
Task: Theme swap to green/red + social links

Work Log:
- Swapped palette site-wide: cyan-* -> emerald-* (primary green), amber-* -> red-* (danger accent); hex/rgba counterparts updated in all portfolio components + globals.css (selection, scrollbar). Zero leftovers (rg verified).
- Added social profiles (professional style): Instagram @abbaxi_baig_110, TikTok @abbaxi_baig_110, Facebook page — in Contact DIRECT_CHANNELS panel (new SOCIAL_PROFILES group) and footer (icon buttons + handle).
- Created src/components/portfolio/tiktok-icon.tsx (custom SVG, lucide has no TikTok brand icon).
- Footer sitemap now includes Crime Scene 3D (#csi). ADMIN_PASSCODE=zain2026 added to .env.

Stage Summary:
- Site theme is now emerald-green primary + red danger accents on deep navy.
- Next: Task 7-a (CSI 3D section, new files) + 7-b (admin system) in parallel; then integration renumbering: About=01, Skills=02, CSI=03, Cases=04, Timeline=05, Contact=06.

---
Task ID: 7-a
Agent: Z.ai Code (agent 7-a — CSI 3D section)
Task: Dangerous CSI crime-scene 3D model section (crime-scene.tsx + danger-zone.tsx)

Work Log:
- Read worklog.md design contract + inspected evidence-globe.tsx / hero.tsx / reveal / section-heading / contact.tsx (h-9 w-9 icon row pattern) before writing any code.
- Verified lucide exports: installed lucide-react@0.525 has NO `Radioactivity`; used `Radiation as Radioactivity` alias. TriangleAlert/Biohazard/Bomb/KeyRound all present.
- Created src/components/portfolio/crime-scene.tsx ('use client', named export CrimeScene, ssr:false target): Canvas dpr [1,1.75] camera [0,2.4,7.4] fov 45 alpha+antialias absolute-inset; CameraRig component calls camera.lookAt(0,-0.2,0) so the camera looks slightly down at the model. Scene (all memoized, typed, no any): gridHelper [26,26,'#ef4444','#7f1d1d'] at y -2.2 with transparent/opacity 0.35 set via ref+useEffect traversal (material handled as Material|Material[] via Array.isArray + needsUpdate); pulsing hazard core (ico 1.15 wireframe #ef4444 op 0.55 over ico 0.55 #dc2626 + pointLight #ef4444 intensity 18 distance 9, useFrame scale 1+0.07*sin(2.4t)); spike mine = 8 coneGeometry [0.16,0.85,4] on cube-corner unit dirs at r 1.5 oriented outward via setFromUnitVectors(UP,dir) quaternions, group rotation.y += delta*0.25 and rotation.x = sin(0.3t)*0.25; crosshair rings torus [1.95,0.014,8,100] op 0.6 + [2.35,0.01,8,100] op 0.35 on different tilts counter-rotating around local z, outer ring carries 4 box ticks (0.05,0.05,0.3) at N/E/S/W; laser scanner = flat torus [2.1,0.028,8,90] rotation.x PI/2, #f87171 op 0.8, position.y = sin(0.8t)*1.6; 5 evidence debris (3 wireframe boxes 0.3 + 2 wireframe octas 0.22, r 2.7-3.2, mixed #ef4444/#94a3b8, distinct speeds/phases) each in drei <Float>; 160-point red/white (#ef4444/#e2e8f0) vertex-color particle shell r 3.5-7, size 0.025, op 0.55, slow y-rotation; whole scene in one group with constant spin (delta*0.08) + pointer parallax identical to evidence-globe (lerp 0.04, rotation.x->py*0.18, rotation.y->px*0.28, position drift). Lights: dim ambientLight 0.25 + core pointLight only; no postprocessing.
- Created src/components/portfolio/danger-zone.tsx ('use client', named export DangerZone): section #csi with red radial-glow top layer + faint 56px red grid (rgba(239,68,68,0.04) inline style); SectionHeading index="03" kicker="CRIME_SCENE_UNIT"; grid grid-cols-1 lg:grid-cols-5 gap-8 items-stretch. Left col-span-3 CAMERA FEED frame (aspect-[4/3], border-red-500/25, bg-[#020617], red glow shadow) hosting dynamic CrimeScene (ssr:false, red pulse fallback) + pointer-events-none HUD: 4 corner brackets (28px, 2px red-500/60), CAM_07 // CRIME SCENE — LIVE chip, blinking REC dot, 24px center crosshair plus, live timestamp chip `HH:MM:SS // DD-MM-YYYY` (mounted guard renders --:--:-- pre-hydration; interval 1000ms; first tick deferred to setTimeout callback to satisfy react-hooks/set-state-in-effect), ZOOM 2.4x // LIDAR: ON chip, 1px repeating scanlines, framer-motion scan bar (y -10%->110%, 4s linear, repeat Infinity — full-height wrapper so % y maps to frame height) and inset red vignette. Right col-span-2 HAZARD BRIEFING panel (border-red-500/20, slate-900/70, p-6, backdrop-blur, h-full): DANGER — RESTRICTED shadcn Badge + pulsing TriangleAlert, "Active Hazards On Site", 4 hazard rows (h-9 w-9 red icon squares: Radioactivity/LASER GRID, Biohazard/BIOHAZARD, Bomb/EXPLOSIVE RESIDUE, TriangleAlert/EVIDENCE INTEGRITY), CLEARANCE LEVEL REQUIRED: V footer, Button asChild size sm with classes on Button (Radix Slot rule) -> #contact REQUEST CLEARANCE with KeyRound icon. Columns wrapped in Reveal delays 0.05/0.15.
- Fixed my own lint error (react-hooks/set-state-in-effect in TimestampChip) via timer-callback pattern; re-ran `bun run lint` -> 0 errors 0 warnings; `bunx tsc --noEmit` -> no errors in either new file. dev.log compiles clean.

Stage Summary:
- 2 new files delivered exactly as scoped: crime-scene.tsx (R3F hazard model) + danger-zone.tsx (CSI camera-feed section) — both 'use client', strict TS, no `any`, red-dominant slate/emerald/RED palette only.
- Zero modifications to existing files: no page.tsx, navbar, shared primitives, or other agents' work touched; worklog.md append-only.
- Integration contract for next agent: render <DangerZone /> in page.tsx (anchor #csi, matches footer sitemap entry from Task 6), placed between Skills (02) and Cases (04) per the planned renumbering (About=01, Skills=02, CSI=03, Cases=04, Timeline=05, Contact=06); #contact CTA target already exists.
- CrimeScene is non-interactive by design (pointer parallax only, no OrbitControls) so it never steals scroll/gestures inside the HUD frame; all GPU objects are JSX-declarative (auto-disposed by R3F), no imperative geometry leaks.
- Lint: PASS (0 errors / 0 warnings). TypeScript: PASS for both files. Dev server compile: clean.

---
Task ID: 7-b
Agent: Z.ai Code (agent 7-b — admin command center)
Task: Admin "COMMAND CENTER" (content management + contact inbox): SiteSetting model, site content defaults/merge, admin APIs, SiteContent context provider, AdminPanel overlay, dynamic-content wiring

Work Log:
- Read worklog.md + inspected existing files (about/hero/footer/contact for exact copy + classes, contact route, schema, ui primitives: tabs/scroll-area/dialog/button/input/textarea/label/badge).
- Prisma: added `SiteSetting { key @id, value (JSON string), updatedAt @updatedAt }` to prisma/schema.prisma; `bun run db:push` OK (SQLite in sync, client regenerated).
- Created src/lib/site-defaults.ts: `StatItem`/`SocialLink`/`SiteContent` types, `DEFAULT_SITE_CONTENT` (hero strings, 4 stats 25+/8/6/120+, 3 about paragraphs copied EXACTLY from about.tsx, quote "Every piece of evidence…", attribution "— field notes, Z. Abbas", 3 socials instagram/tiktok/facebook), `SITE_CONTENT_KEY = 'site:v1'`, and `deepMergeSiteContent(partial: unknown): SiteContent` — type-guarded field-by-field merge over defaults (output always has exactly 4 stats / 3 paragraphs / 3 socials; malformed JSON degrades to defaults).
- Created src/lib/admin-auth.ts: `adminPasscode()` (process.env.ADMIN_PASSCODE ?? 'zain2026'), `safeEqual()` (sha256 hex digests + node:crypto timingSafeEqual), `verifyAdminKey(header)`.
- Created src/app/api/admin/login/route.ts: POST {passcode} → constant-time compare → 200 {ok:true} | 401 {ok:false}.
- Created src/app/api/site/route.ts: GET returns {ok:true, content} = stored SiteSetting JSON merged over defaults (any error → defaults); PUT requires `x-admin-key` (401 otherwise), deep-merges body over defaults, upserts SiteSetting, returns {ok:true, content}.
- Extended src/app/api/contact/route.ts (POST untouched): GET admin-only → {ok:true, messages:[{id,caseId,name,email,subject,message,createdAt}]} createdAt desc; DELETE admin-only `?id=` → {ok:true}, 404 if missing, 400 if id absent.
- Created src/components/portfolio/site-content.tsx ('use client'): `SiteContentProvider` + `useSiteContent()` → {content, ready, refresh()}; GET /api/site on mount; graceful fallback = self-fetching local store when no provider is mounted above (stable hook order, no double-fetch once provider exists).
- Created src/components/portfolio/admin-panel.tsx ('use client'): named exports `AdminPanel` + `openAdminPanel()` (dispatches CustomEvent 'open-admin'). Full-screen overlay `fixed inset-0 z-[100] bg-[#020617]/97 backdrop-blur-md`, renders null when closed; body scroll lock + Escape close; role="dialog" aria-modal. Login gate: Fingerprint card `max-w-sm border-emerald-500/25 bg-[#020617] p-6`, "RESTRICTED // COMMAND CENTER", mono password input, POST /api/admin/login; fail → `> ACCESS DENIED — invalid passcode`; success → sessionStorage 'za_admin_key' (restored on reopen; any 401 clears it → back to login + destructive toast). Panel: header COMMAND_CENTER // ADMIN + pulsing LIVE badge + logout (LogOut) + close (X); shadcn Tabs CONTENT | INBOX. CONTENT: mono `// HERO` (5 fields), `// STATS` (4 rows value+label, suffix chip fixed), `// ABOUT` (3 paragraph Textareas + quote + attribution), `// SOCIALS` (3 rows label/value/href, platform chip fixed), sticky save bar with Save icon → PUT /api/site with x-admin-key + full draft JSON → toast "Content updated — live on site" + refresh() from context (page behind updates instantly). INBOX: loads GET /api/contact on tab activate + RefreshCw button; ScrollArea message cards (emerald caseId + toLocaleString date + Trash2 delete with arm→"CONFIRM?"→delete double-click pattern, 4s auto-disarm); empty state `> inbox empty — no transmissions yet`; mono input classes identical to contact.tsx.
- Wired dynamic content (only strings/arrays swapped, structure/classes untouched): hero.tsx (titlePre + gradient titleHighlight, `zain@forensics:~$` prefix kept + terminalCmd, terminalLine + red caret, paragraph); about.tsx (paragraphs mapped with same <p> classes, quote inside existing &ldquo;&rdquo;, attribution, 4 StatCounter value/suffix/label from content.stats — StatCounter component kept, local STATS const + StatItem removed); footer.tsx (socials from content.socials, icon mapping instagram→lucide Instagram / tiktok→TiktokIcon / facebook→lucide Facebook / unknown→Globe, aria-label `${label} — ${value}`); contact.tsx (SOCIALS derived from content.socials with same icon mapping; DIRECT CHANNELS untouched).
- Verified: bun run lint → 0 problems; bunx tsc --noEmit → clean; curl end-to-end: login 401/200, PUT site 401 unauth + merge-on-save, GET site merge, public POST contact → admin GET inbox → DELETE → 404 on re-delete; homepage renders merged content (title, stats, socials); dev.log clean, no console/compile errors. Test edits reverted — stored content back to canonical defaults.

Stage Summary:
- DB: `SiteSetting` model live in SQLite. Key `site:v1`, value = full SiteContent JSON (deep-merged over defaults on read/write — DB may hold any partial).
- API contract: POST /api/admin/login {passcode} → 200 {ok:true} | 401 {ok:false}. GET /api/site → 200 {ok:true, content:SiteContent} (never fails; errors → defaults). PUT /api/site (header x-admin-key) body partial/full SiteContent → 200 {ok:true, content} | 401. GET /api/contact (header x-admin-key) → 200 {ok:true, messages[]} newest-first | 401. DELETE /api/contact?id= (header x-admin-key) → 200 {ok:true} | 400 no id | 401 | 404. POST /api/contact unchanged (public, zod, 201 {ok, caseId}).
- Components: `SiteContentProvider`/`useSiteContent()` in site-content.tsx; `AdminPanel` + `openAdminPanel()` in admin-panel.tsx (self-contained, CustomEvent-driven).
- INTEGRATION NEEDED (next task): in page.tsx wrap the tree with `<SiteContentProvider>`, render `<AdminPanel />`, and add a trigger somewhere (e.g. secret navbar hit-zone) calling `openAdminPanel()` from '@/components/portfolio/admin-panel'. Nothing else requires wiring — site works without provider (fallback), but refresh()-live-updates need it.
- Passcode: ADMIN_PASSCODE=zain2026 (.env), compared timing-safes on sha256 hexes; session key 'za_admin_key' in sessionStorage; every admin API call sends x-admin-key.
- Lint: PASS (0 problems). tsc: PASS. Public POST /api/contact byte-identical.

---
Task ID: 8 + 9
Agent: orchestrator (Z.ai Code)
Task: Integration of CSI section + admin system, full browser verification

Work Log:
- Integrated in page.tsx: SiteContentProvider wrapper, DangerZone (#csi) between Skills and Cases, AdminPanel mounted.
- Footer: ADMIN trigger button (Lock icon) calling openAdminPanel() (CustomEvent bridge).
- Renumbered sections: About=01, Skills=02, CSI=03, Case Files=04, Timeline=05, Contact=06; navbar + footer sitemap updated with "Crime Scene" link.
- Fixed missing Lock/openAdminPanel imports in footer (admin agent had reworked the file).
- Browser-verified (agent-browser): green/red theme live on all sections; CSI camera-feed 3D model (spike mine, laser grid, REC HUD, live timestamp) renders; admin login (zain2026) -> CONTENT editor loads values -> edited hero terminalCmd -> SAVE -> toast + live page update confirmed -> reverted via API -> INBOX shows ZA-2026-7C30 transmission with delete/refresh; socials render in contact (SOCIAL_PROFILES group) + footer; mobile 390px no horizontal overflow; 0 console errors; dev.log clean; lint + tsc PASS.

Stage Summary:
- Admin passcode: zain2026 (env ADMIN_PASSCODE, changeable in .env).
- Site fully controlled from COMMAND_CENTER: hero text, stats, about, socials + contact inbox (view/delete).
- All user requests complete: green+red theme, 3 social links, dangerous CSI 3D model, admin panel.

---
Task ID: 10
Agent: orchestrator (Z.ai Code)
Task: Admin deep-link URL (#admin hash) so the admin login can be opened directly from a URL

Work Log:
- AdminPanel first useEffect extended: listens to `hashchange` + runs initial `#admin` hash check via setTimeout callback (avoids sync setState in effect body / react-hooks set-state-in-effect rule). CustomEvent `open-admin` listener kept intact (footer lock button still works).
- bun run lint: PASS (0 problems).
- Browser-verified (agent-browser): open http://localhost:3000/#admin -> RESTRICTED // COMMAND CENTER login gate auto-opens (screenshot confirmed, passcode field focused); fill zain2026 -> AUTHENTICATE -> COMMAND_CENTER // ADMIN panel opens ("PANEL-OPEN"). Browser closed.

Stage Summary:
- Admin login URL = site URL + `#admin` (e.g. https://<preview-url>/#admin). Passcode: zain2026 (ADMIN_PASSCODE env). Footer lock button remains as secondary trigger. Only / route used, no new route needed.

---
Task ID: 11-b
Agent: Z.ai Code (agent 11-b — Cases + Contact 3D)
Task: 3D for remaining sections — EvidenceField3D (evidence locker field) + tilt integration in Case Studies, TransmissionTunnel3D (encrypted uplink tunnel) in Contact

Work Log:
- Read worklog.md (design contract, palette, conventions) + required files: case-studies.tsx, contact.tsx, crime-scene.tsx (R3F/HUD reference), use-in-view.ts, tilt-card.tsx; also evidence-globe.tsx (orbit-ring pattern) and danger-zone.tsx (HUD frame language). Verified stack: three 0.186.1, @react-three/fiber 9.8.2, @react-three/drei 10.7.9 (Float + RoundedBox confirmed), lucide-react 0.525 (no new icons needed).
- Created src/components/portfolio/evidence-3d.tsx ('use client', named export EvidenceField3D + default): Canvas dpr [1,1.5], camera [0,0,6.5] fov 45, alpha+antialias, absolute-inset, frameloop gated by shared useInView<HTMLDivElement> on the absolute inset-0 wrapper div. Center EvidenceTag: drei RoundedBox [0.9,0.55,0.04] radius 0.03 emerald (#10b981) wireframe, white (#e2e8f0) label plane z 0.022 + 2 navy printed bars + emerald stamp bar, punched torus hole (0.07/0.014) at x -0.37, thin string = tubeGeometry along CatmullRomCurve3 (4 pts toward upper-left corner, radius 0.007), whole tag in drei <Float speed 1.6 rotationIntensity 0.45>. Two decorative tilted orbit rings (r 2.6 emerald op 0.22 / r 3.1 red #f87171 op 0.16) precessing opposite directions (evidence-globe OrbitRing pattern). 6 orbiting wireframe items (VAULT_ITEMS configs, radius 2.5–3.2, distinct speed/phase, elliptical path + Float): magnifier (torus 0.2 + rotated cylinder handle), red cone marker, capsule bullet, photo card (wireframe border box + faint white plane), red tetra shard, wireframe kit box — emerald #34d399 with exactly 2 red accents. VaultParticles: 180 points in shell r 2.6–6.0, 60% emerald / 25% white / 15% red vertex colors, size 0.03 op 0.55, slow y-rotation (delta*0.04). FieldScene group: spin delta*0.1 + pointer parallax identical to crime-scene (lerp 0.04, rotation.x→py*0.18, rotation.y→px*0.28, position drift x*0.15/y*0.1). All geometry/materials declarative JSX (auto-dispose), no any, no lights needed (all meshBasicMaterial).
- Created src/components/portfolio/tunnel-3d.tsx ('use client', named export TransmissionTunnel3D + default): Canvas dpr [1,1.5], camera [0,0,4.5] fov 45, alpha+antialias, useInView-gated frameloop, <fog attach="fog" args={['#020617', 6, 16]} />. 12 wireframe torus rings (tube 0.045, 6x42 segments) stacked z 0→-14, radius 2 + sin(i*1.7)*0.14 variation, every 4th ring red (#ef4444) else alternating #34d399/#10b981, opacity 0.7 (near) → 0.25 (far); each ring spins around own z via own useFrame (alternate directions, speeds 0.12–0.22). TunnelParticles: 200 points in annulus r 0.5–2.1, Float32Array positions mutated in useFrame (z += per-particle speed 1.6–3.8 * delta, recycle to z=-14 when past z 4.2), needsUpdate on the BufferAttribute, emerald/white vertexColors, size 0.03, opacity 0.6, AdditiveBlending, depthWrite false. TunnelScene group: rotation.z drift (delta*0.05) + pointer parallax (rotation.x→py*0.18, rotation.y→px*0.28, lerp 0.04).
- Integrated case-studies.tsx (structure-preserving): added next/dynamic import; EvidenceField3D loaded ssr:false with emerald pulse fallback. Verified the 4 case cards were ALREADY wrapped in shared TiltCard (intensity 6, emerald glare, h-full root) — no conflicting hover transforms exist (only transition-colors + border hover), so cards were left untouched; Dialog OPEN FILE button inside tilt card unaffected. Inserted "EVIDENCE VAULT" HUD strip in Reveal (min-w-0) directly BELOW SectionHeading and ABOVE the card grid: relative overflow-hidden rounded-xl border-emerald-500/25 bg-[#020617], aspect-[16/9] w-full md:aspect-[21/9] md:max-h-[420px], 4 corner brackets (h-6 w-6 2px, border-emerald-500/60), top-left chip with pulsing emerald dot "EVIDENCE VAULT // LOCKED", top-right chip "ITEMS: 06 // INTEGRITY 100%", 1px emerald scanlines overlay (repeating-linear-gradient), all pointer-events-none + aria-hidden; grid margin mt-14 → mt-8 to keep rhythm.
- Integrated contact.tsx (structure-preserving): TransmissionTunnel3D loaded ssr:false with emerald pulse fallback; inserted "SECURE UPLINK" HUD banner in Reveal (min-w-0) after SectionHeading, BEFORE the form grid: same frame language (border-emerald-500/25 bg-[#020617], aspect-[16/9] w-full md:aspect-[21/9] md:max-h-[380px]), corner brackets, top-left chip "SECURE UPLINK // AES-256", top-right chip with pulsing red dot "ENCRYPTED" (text-red-300), 1px scanlines; form grid margin mt-14 → mt-8. No form/terminal/DIRECT CHANNELS markup restructured; no sync setState in effects anywhere.
- Verification: scoped eslint on my 4 files → 0 problems; project-wide `bun run lint` → the ONLY error is in src/components/portfolio/fingerprint-3d.tsx:102 (useMemo not inline — parallel agent 11-a's file, on my do-not-touch list, left alone); `bunx tsc --noEmit` → no errors outside the pre-existing examples/ + skills/ paths; dev.log shows clean incremental compiles (✓ Compiled), GET / 200, no errors; curl homepage confirms all 4 HUD chips render (EVIDENCE VAULT // LOCKED, ITEMS: 06 // INTEGRITY 100%, SECURE UPLINK // AES-256, ENCRYPTED).
- Appended this worklog entry (append-only; no other files touched).

Stage Summary:
- 2 new scene files + 2 edited sections delivered: evidence-3d.tsx (EvidenceField3D), tunnel-3d.tsx (TransmissionTunnel3D), case-studies.tsx (vault HUD strip + TiltCard verified), contact.tsx (uplink HUD banner). All 'use client', named exports, strict TS, emerald/red palette only.
- Both scenes follow the full performance contract: dpr [1,1.5], frameloop 'always'/'never' via shared useInView, no postprocessing, declarative JSX only (auto dispose), particles 180/200 (≤400), pointer parallax lerp 0.04 with rotation.x→py*0.18 / rotation.y→px*0.28, canvas absolute inset-0 inside explicit-size frames.
- TiltCard requirement for the case cards was already satisfied by agent 2-b's implementation (intensity 6 + glare + h-full); I verified grid stretch (Reveal h-full), pointer-click-through to the Dialog button, and absence of transform conflicts instead of re-editing.
- Placement decisions: evidence strip = full-width below "04 CASE_FILES" heading, above md:grid-cols-2 card grid (grid mt-14→mt-8); uplink banner = full-width below "06 SECURE_CHANNEL" heading, above the lg:grid-cols-5 form grid (grid mt-14→mt-8).
- ORCHESTRATOR NOTE: project-wide lint currently reports 1 error in fingerprint-3d.tsx (line 102, useMemo(buildRidges, []) needs to be inline arrow) — that file belongs to parallel agent 11-a; once 11-a fixes it, `bun run lint` returns to 0 errors / 0 warnings. My files are clean.

---
Task ID: 11-a
Agent: Z.ai Code (agent 11-a — About/Timeline 3D scenes)
Task: Two new R3F scenes (fingerprint-3d.tsx, helix-3d.tsx) + integration into About and Timeline sections

Work Log:
- Read worklog.md design contract + inspected about.tsx, timeline.tsx, crime-scene.tsx (R3F style reference), danger-zone.tsx (HUD frame reference), use-in-view.ts, reveal.tsx, section-heading.tsx, tilt-card.tsx before writing code.
- Created src/components/portfolio/fingerprint-3d.tsx ('use client', named export Fingerprint3D + default): "BIOMETRIC SCAN" hologram — 15 concentric whorl ridge loops (innermost t=0 -> outermost t=1; radius lerp 0.3→1.5, rx=r*0.74 / ry=r*0.94 with sin/cos wobble + a shallow gaussian "ridge ending" dent on every 3rd loop; loop centres drift from off-centre core (0.34, -0.22) to origin for the eccentric whorl look; per-ridge y offset ±0.02 via deterministic sin-hash jitter + slight z recession −t*0.06); each loop = 72-pt closed THREE.CatmullRomCurve3 -> <tubeGeometry args={[curve,110,r,6,true]}> r lerp 0.028→0.016, MeshBasicMaterial emerald #34d399 (t<0.55) / #10b981, transparent opacity lerp 0.9→0.4 clamped 0.35–0.9, depthWrite false; red scan line = group at z 0.42 with plane [2.6, 0.018] #ef4444 op 0.95 + additive glow plane [2.6, 0.14] op 0.16 + pointLight #ef4444 intensity 8 distance 4, position.y = sin(t*0.9)*1.4; 150-particle emerald/white additive dust shell r 2.1–4.4 rotating (y delta*0.06, x delta*0.012); inner print group base tilt rotation.x −0.14; outer scene group: spin delta*0.15 + pointer parallax identical to evidence-globe/crime-scene (rotation.x→py*0.18, rotation.y→px*0.28, position drift, lerp 0.04); Canvas dpr [1,1.5] camera [0,0,6] fov 45 alpha+antialias, wrapped in useInView<HTMLDivElement> container with frameloop={inView?'always':'never'}.
- Created src/components/portfolio/helix-3d.tsx ('use client', named export MilestoneHelix + default): double helix — strandPoint(t, strand): angle = t*PI*3.2 (+PI for strand B), radius 1.1, y lerp(−2.2, 2.2, t); strand tubes = 97-pt CatmullRomCurve3 -> tubeGeometry [curve,128,0.022,6,false] emerald #34d399 / red #f87171 op 0.5; 2×14 bead nodes (sphereGeometry 16×16; key milestones i%4===0 size 0.12, others 0.07–0.1 jittered; white highlight nodes at strand A i=2,6 and strand B i=3,9); 14 rungs = cylinders [0.012,0.012,len,6] oriented via setFromUnitVectors(UP, dir) quaternion at midpoint, slate #94a3b8 with emerald #10b981 at i%4===0, opacity 0.35; 4 breathing octahedron wireframe markers (0.16 + solid 0.05 core) at strand A indices [1,5,9,12] pushed radially outward 0.42 +0.16 y, scale 1+0.28*sin(2.2t+phase) + opacity pulse 0.55+0.3*sin, slow tumble + y-float; 120-particle emerald/white dust shell r 2.8–5.6 (rotation.y delta*0.04); scene group spin delta*0.22 + same pointer parallax; Canvas dpr [1,1.5] camera [0,0,6.5] fov 45, useInView frameloop gating.
- Fixed 4 react-hooks/use-memo lint errors (rule requires inline factory): useMemo(buildRidges, []) -> useMemo(() => buildRidges(), []) etc. in both new files.
- Integrated into about.tsx (structure-preserving): added next/dynamic ssr:false Fingerprint3D + local BiometricScanBar (framer-motion, 4.5s emerald sweep) + BiometricPanel (danger-zone CAMERA FEED frame treatment in emerald: aspect-[4/3] w-full rounded-xl border-emerald-500/25 bg-[#020617], 28px corner brackets border-emerald-500/60, chips "BIOMETRIC SCAN // Z.ABBAS" TL, blinking emerald dot + "LIVE" TR, "MATCH 99.7% // WHORL CONFIRMED" BL, "AFIS // DB: FEDERAL" BR, 1px emerald repeating-linear-gradient scanlines, inset emerald vignette, all pointer-events-none). Placement: dossier column (grid lg:col-span-2 kept) — existing TiltCard now wrapped in flex h-full flex-col gap-6 with the panel in a nested Reveal (delay 0.2, min-w-0) directly UNDER the dossier card; grid base grid-cols-1 untouched, outer Reveal got min-w-0. No existing card classes changed.
- Integrated into timeline.tsx (structure-preserving): added ssr:false MilestoneHelix dynamic import; full-width frame inserted AFTER SectionHeading / BEFORE the timeline list wrapper (which keeps its own mt-14): Reveal mt-14 min-w-0 -> relative aspect-[16/9] md:aspect-[21/9] w-full border-emerald-500/25 bg-[#020617] frame with mixed HUD (emerald TL/BL + red TR/BR corner brackets, "MILESTONE TRAJECTORY // 2016—PRESENT" TL, pulsing red dot + "NODES: 08 // SYNCED" TR, "STRAND_A: EDUCATION // STRAND_B: CERTS+EXPERIENCE" BL, "TRAJECTORY: ASCENDING" BR, scanlines + dark vignette). Timeline items/columns untouched.
- Verification: bun run lint -> 0 errors 0 warnings (after the useMemo fix). bunx tsc --noEmit -> zero errors outside the pre-existing examples//skills/ paths. dev.log tail: ✓ Compiled + GET / 200, no compile/runtime errors; homepage curl 200.

Stage Summary:
- 2 new files delivered exactly as scoped: fingerprint-3d.tsx (Fingerprint3D) + helix-3d.tsx (MilestoneHelix); 2 files edited: about.tsx + timeline.tsx (additive only — no restructures, no palette violations, emerald/white/red/slate only).
- Both scenes follow every perf rule: dpr [1,1.5], no postprocessing, useInView frameloop gating on the Canvas container, particles ≤ 150/120, memoized curve/geometry builders (inline factories per react-hooks/use-memo), declarative JSX geometry (auto-dispose), pointer parallax identical to evidence-globe (0.04 lerp, 0.18/0.28 targets), explicit-size canvas containers (aspect frames + absolute inset-0) so zero layout shift.
- HUD chips use exact strings from the task spec ("NODES: 08 // SYNCED" is decorative flavor per spec).
- About panel lives under the dossier card in the same lg:col-span-2 column (task-sanctioned fallback; column now stacks dossier -> biometric scan, staggered Reveal 0.05/0.2). Timeline panel is full-width above the record list.
- Zero modifications outside my 4 files + worklog.md; did NOT touch case-studies.tsx, contact.tsx, evidence-3d.tsx, tunnel-3d.tsx, page.tsx, navbar.tsx, footer.tsx, globals.css, admin-panel.tsx (agent 11-b / orchestrator territory).
- Lint: PASS. tsc: PASS (no new errors). Dev compile: clean.

---
Task ID: 11 (orchestrator)
Agent: Z.ai Code (orchestrator)
Task: 3D animation across ALL sections — shared perf hook + parallel agents 11-a/11-b + fix + full verification

Work Log:
- Created src/components/portfolio/use-in-view.ts: IntersectionObserver hook returning {ref, inView}; all new R3F canvases gate frameloop={inView?'always':'never'} so only visible scenes render (6 canvases on page stay cheap).
- Launched parallel agents: 11-a (fingerprint-3d.tsx -> About "BIOMETRIC SCAN // Z.ABBAS" panel under dossier; helix-3d.tsx -> Timeline "MILESTONE TRAJECTORY // 2016—PRESENT" full-width strip) and 11-b (evidence-3d.tsx -> Cases "EVIDENCE VAULT // LOCKED" strip above grid; tunnel-3d.tsx -> Contact "SECURE UPLINK // AES-256" banner above form; case cards already TiltCard-wrapped — verified, no edit needed).
- Fixed last project lint error in fingerprint-3d.tsx: react-hooks/use-memo rejected the factory; replaced useMemo with module-scope `const RIDGES = buildRidges()` (deterministic seeded jitter — zero per-render cost, cleaner than memo). Final `useMemo` still used for particles.
- lint: 0 errors 0 warnings. tsc: clean outside pre-existing examples//skills/ paths.
- Browser-verified all four new scenes render (screenshots): fingerprint whorl + red scan line, double helix emerald/red nodes + rungs + octahedron markers, orbiting wireframe evidence field, wireframe tunnel rings + flowing particles. All HUD frames + mono chips correct.
- Mobile 390x844: scrollWidth 390, zero x-overflow elements before/after full scroll. Console: no errors (only pre-existing THREE.Clock deprecation warning).

Stage Summary:
- Every section now has 3D: Hero=globe, About=biometric fingerprint, Skills=tilt cards, CSI=hazard model, Cases=evidence vault + tilt cards, Timeline=milestone helix, Contact=uplink tunnel.
- 4 new scene files + use-in-view.ts hook; section files edited structure-preservingly (about/timeline/case-studies/contact).
- Performance contract: dpr [1,1.5], no postprocessing, particles <=200/scene, frameloop paused offscreen via IntersectionObserver.

---
Task ID: 12-a
Agent: Z.ai Code (agent 12-a — theme recolor + navbar ThemeToggle)
Task: Theme-token recolor navbar/hero/globe/about/fingerprint/skills + navbar ThemeToggle
Work Log:
- Read worklog.md tail + theme.tsx + my 6 files before editing; verified globals.css tokens (acc1/acc1-bright/acc2/acc2-bright + *-rgb) and lucide-react@0.525 exports (Palette: true, Sparkles: true — used Palette).
- navbar.tsx: full emerald/red -> acc token recolor (header border, logo chip, links + index spans, CONNECT, hamburger, mobile panel). Scroll-progress gradient moved from Tailwind classes to framer-motion style `background: linear-gradient(90deg, var(--acc1), var(--acc1-bright), var(--acc2-bright))` per CSS-var rule. Added ThemeToggle: desktop shadcn Button (hidden md:inline-flex, size sm, Palette icon, `THEME: {otherLabel}`, border-acc1/30 bg-acc1/5 text-acc1-bright, hover border-acc1/60 + bg-acc1/15 + shadow-[0_0_16px_rgb(var(--acc1-rgb)/0.3)] glow) placed after CONNECT, plus a full-width toggle row (divider + same styling, w-full) inside the mobile menu below the links. onClick=toggle, aria-label="Switch color theme". All prior behavior kept (scroll glass, Escape/matchMedia close, menu open on toggle so the recolor is visible).
- hero.tsx: badge chip + ping dots, gradient headline (from-acc1-bright to-acc1), terminal prompt + caret (text-acc2-bright), primary/outline buttons (bg-acc1/hover:bg-acc1-bright + arbitrary hover glow via rgb(var(--acc1-rgb)/0.35)), meta icons (text-acc2-bright/80), scroll cue, grid overlay inline style rgba(16,185,129,0.05)x2 -> rgb(var(--acc1-rgb) / 0.05), loading fallback bg-acc1/5.
- about.tsx: StatCounter cards, BiometricScanBar via-acc1/10, BiometricPanel (border/shadow/4 corner brackets/3 HUD chips/LIVE dot/scanlines rgba->rgb(var(--acc1-rgb)/0.05)/inset vignette rgb(var(--acc1-rgb)/0.12)), dossier card + red corner accents -> acc2-bright, ACTIVE badge (red-300 -> text-acc2-bright), avatar ring/initials/fingerprint watermark, role line, divider, profile icon chips, blockquote border-acc2-bright/60, loading fallback.
- skills.tsx: GRID_STYLE rgba(16,185,129,0.04)x2 -> rgb(var(--acc1-rgb) / 0.04) (CSS vars resolve live against data-theme), card border/hover border-acc1-bright/40, index numerals text-acc2-bright/70, icon chips + group-hover glow rgb(var(--acc1-rgb)/0.25).
- evidence-globe.tsx: GlobeScene now calls useTheme() (R3F v9 context bridge) and passes palette down. Removed CYAN/CYAN_DEEP/AMBER consts. ArcData/MarkerConfig color fields -> `tone: AccentTone` ('acc1Bright' | 'acc2Bright') discriminator resolved as palette[tone] at render. Imperative arc LineBasicMaterials: built once (no palette in memo deps -> no random-curve rebuild on toggle) + useLayoutEffect repaints material.color.set(palette[tone]) on [arcs, palette] (runs pre-first-paint and on every flip; color.set needs no needsUpdate). Starfield split into positions memo (stable) + colors memo keyed on [acc1Hex, whiteHex] so R3F recreates the vertex-color bufferAttribute via args change — no stale vertex colors. Declarative color props for GlobePoints/WireframeShell/OrbitRings/DataPacket/EvidenceMarker. Geometry counts, camera, dpr, parallax, spin, frameloop untouched.
- fingerprint-3d.tsx: RidgeConfig.color -> tone ('acc1Bright' | 'acc1'); module-scope `const RIDGES = buildRidges()` KEPT (no per-render rebuild) with tone resolved as palette[ridge.tone] in FingerprintRidges. ScanSweep plane/glow/pointLight -> palette.acc2. ScanParticles split positions (stable) / colors (memo on [palette.acc1Bright, palette.acc1, palette.white] -> bufferAttribute args recreate). FingerprintScene calls useTheme() and passes palette to all three children. Animation/geometry/parallax/useInView gating untouched.
- Verified: `bun run lint` -> 0 errors 0 warnings; `bunx tsc --noEmit` -> no output (no new errors, examples//skills/ excluded); rg for emerald/red/amber/cyan/rgba(16,185|239,68|#34d399|#ef4444|#e2e8f0 leftovers in my 6 files -> zero matches; dev.log -> clean incremental compiles + GET / 200 (transient Fast-Refresh full-reload warnings only during mid-edit HMR, no syntax/runtime errors); headless browser: page loads with no page errors (only pre-existing THREE.Clock deprecation warning), clicked ThemeToggle -> data-theme="classic" + label flips to THEME: FORENSIC, screenshot saved, toggled back -> data-theme="forensic", zero errors across both states.
Stage Summary:
- All 6 assigned files now recolor live via data-theme: CSS classes use acc1/acc1-bright/acc2/acc2-bright tokens, inline rgba() uses rgb(var(--acc1-rgb)/var(--acc2-rgb)), framer-motion gradient uses CSS vars, and both R3F scenes resolve colors from useTheme().palette with no stale buffers (JSX props for declarative materials, args-recreated vertex-color attributes for particles, useLayoutEffect color.set for imperative arc materials, tone discriminators instead of baked hexes — module-scope geometry builds preserved).
- Navbar has THEME: {otherLabel} toggle on desktop (next to CONNECT) and as a full-width row in the mobile menu; aria-label="Switch color theme"; all existing navbar behaviors intact.
- Zero changes outside my 6 files + worklog.md. Mapping decisions: emerald-300/400->acc1-bright, emerald-500/600->acc1, red-400->acc2-bright (red-300 also -> acc2-bright), red-500/600/700->acc2; globe's legacy cyan-ish names -> acc1 slots, amber-ish -> acc2 slots; slate-*/#020617/#0f172a/white-* untouched.
- Lint: PASS (0/0). tsc: PASS (no new errors). Dev compile + runtime: clean; theme toggle round-trip verified in browser.

---
Task ID: 12-b
Agent: Z.ai Code (agent 12-b — theme recolor csi/cases/timeline/contact/footer/admin + Lenis lock + 4 scenes)
Task: Theme-token recolor (danger-zone, case-studies, timeline, contact, footer, admin-panel) + Lenis scroll-lock compatibility + theme-aware R3F scenes (crime-scene, evidence-3d, helix-3d, tunnel-3d)

Work Log:
- Read worklog tail + theme.tsx (ThemeProvider/useTheme/palette contract) + globals.css var formats (--acc1-rgb/--acc2-rgb are space-separated RGB triplets, e.g. `16 185 129`, so `rgb(var(--acc2-rgb)/0.12)` is valid inline and in Tailwind arbitrary values) + scroll-area.tsx (props forward to Radix Root).
- JOB 1/4 class recolor, applied per exact JOB 1 mapping with /opacity suffixes preserved (red-300 → acc2-bright by analogy with emerald-300 → acc1-bright): danger-zone.tsx (red→acc2/acc2-bright everywhere: REC badge, corner brackets, crosshair, CAM/ZOOM chips, hazard rows, DANGER badge, clearance button, scan bar via-acc2/10; inline rgba(239,68,68,*) → rgb(var(--acc2-rgb)/*) in radial glow, 56px grid, scanlines, frame shadow-[0_0_50px_rgb(var(--acc2-rgb)/0.12)], vignette), case-studies.tsx (STATUS_STYLES: ONGOING red-400/300 → acc2-bright/40,10,text; CLOSED emerald → acc1-bright; vault HUD brackets/chips/dot/scanlines; card borders hover:border-acc1-bright/40; OPEN FILE button; dialog border + scrollbar thumb bg-acc1/30; methodology ▸ acc1-bright / findings ▸ acc2-bright), timeline.tsx (TYPE_STYLES EDUCATION→acc1-bright, CERTIFICATION→acc2-bright, EXPERIENCE slate kept; helix HUD mixed emerald/red brackets → acc1/acc2; NODES chip red dot → acc2-bright; vertical line from-acc1/60 via-acc1/20; node dots border-acc1-bright + ping bg-acc1-bright + shadow rgb(var(--acc1-rgb)/0.5); cards + org text), contact.tsx (uplink HUD, ENCRYPTED chip → acc2/acc2-bright, terminal frame border-acc1/20 + shadow var, traffic-light dots → acc2-bright/70 + acc1-bright/70, INPUT_CLASS focus ring → acc1-bright, ROW_CLASS hover, submit button bg-acc1 hover:bg-acc1-bright + glow var, success terminal acc1-bright/acc2-bright, error text + failed message → acc2-bright per mapping, channel/social icon squares → acc1), footer.tsx (top border, fingerprint chip, social icon buttons hover tokens + glow var, SITEMAP/DIRECT CHANNELS headings + link hovers, CASE FILE status line → acc2-bright/80, ADMIN lock button hover → acc1).
- admin-panel.tsx recolor with literal-red exception honored: all emerald-* → acc1/acc1-bright (INPUT_CLASS focus, SECTION_TITLE_CLASS, AdminField labels, login card + fingerprint chip + heading + AUTHENTICATE button, header border/title/LIVE badge + ping dot, tabs active bg-acc1/15 text-acc1-bright, SUFFIX/platform chips acc1-bright/70, SAVE button, close + refresh buttons, inbox caseId). KEPT literal red: login ACCESS DENIED error, logout hover, inbox delete CONFIRM? armed/idle states (semantic danger stays red in both themes). Destructive toasts untouched (variant-driven).
- JOB 2 Lenis scroll-lock: added `data-lenis-prevent` to case-studies.tsx DialogContent (Radix forwards it to the rendered div); admin-panel.tsx login-gate overlay root + main command-center overlay root (both fixed inset-0) + the inbox ScrollArea Root (shadcn wrapper spreads props onto Root, so the attribute lands on a real DOM node). Verified smooth-scroll.tsx uses default Lenis options, which honor the documented data-lenis-prevent opt-out.
- JOB 3 scenes theme-aware — common pattern: scene root inside <Canvas> calls `const { palette } = useTheme()` (R3F v9 context bridging) and threads palette/derived colors down as typed props; hardcoded hex constants removed.
  - crime-scene.tsx: new SceneColors interface + buildColors(palette) — acc2/acc2Bright map direct; RED_DEEP/RED_DARK/RED_DIM become luminance-scaled shades of acc2 via module helper shade(hex, factor) (new THREE.Color().multiplyScalar → getHexString) so the core→spike→grid depth hierarchy survives the CLASSIC amber swap; gridHelper rebuilt on toggle via `<LaserGridFloor key={colors.acc2}>` (component remount → the transparent/opacity traversal effect re-runs on the fresh helper — avoids the stale-material trap of keying only the JSX element); EvidenceDebris config.color → tone ('acc2'|'white'); DangerParticles split into mix (stable per-particle rolls, [] deps) + positions ([] deps) + colorArray useMemo([colors, mix]) — theme toggle swaps the color Float32Array and R3F re-creates the bufferAttribute from new args (no stale vertex colors, no particle reshuffle).
  - evidence-3d.tsx: EMERALD/EMERALD_BRIGHT/RED_BRIGHT/WHITE → palette.acc1/acc1Bright/acc2Bright/white; NAVY #0f172a kept literal (printed label ink); AccentTone type + VAULT_ITEMS tone field resolved as palette[config.tone]; OrbitRing call sites pass palette colors; VaultParticles same mix/positions/colorArray split with deps [palette, mix].
  - helix-3d.tsx: buildNodes/buildRungs/buildMarkers now take ScenePalette and are rebuilt via useMemo(..., [palette]); strand tubes/nodes/rungs/markers all palette-driven (rungs: milestone acc1, rest palette.white per slate-white mapping); HelixParticles mix/positions/colorArray split, deps [palette, mix].
  - tunnel-3d.tsx: RINGS color → RingTone ('acc1'|'acc1Bright'|'acc2') resolved as palette[config.tone] in TunnelRing; TunnelParticles positions/speeds memo stays [] deps (array is mutated in useFrame z-flow — must NOT be reallocated on toggle) while colorArray memo deps [palette, mix] re-creates the color attribute; FOG_COLOR #020617 kept literal.
  - No changes to geometry counts, animation logic, camera, dpr, frameloop/useInView gating, or parallax anywhere.
- Verification: rg scan of all 10 files → only sanctioned leftovers (admin literal-red destructive states + doc comments naming old hexes); bun run lint → 0 errors 0 warnings; bunx tsc --noEmit → only the 4 pre-existing examples/ + skills/ errors, nothing new; dev.log tail → ✓ Compiled + repeated GET / 200 + GET /api/site 200, no compile errors (transient Fast-Refresh full-reload notes during the edit session only); curl homepage → 200 with CAM_07 / EVIDENCE VAULT / MILESTONE TRAJECTORY / SECURE UPLINK HUD chips all present.
- Appended this worklog entry (append-only; no files outside my 10 + worklog.md touched — globals.css, page.tsx, theme.tsx, smooth-scroll.tsx, navbar, hero, evidence-globe, about, fingerprint-3d, skills all untouched).

Stage Summary:
- All 6 section/admin files now recolor instantly with `data-theme` flips (FORENSIC emerald/red ↔ CLASSIC cyan/amber) via acc1/acc1-bright/acc2/acc2-bright tokens + rgb(var(--accN-rgb)/x) inline styles; opacity suffixes and layout/spacing untouched, text identical.
- Lenis compatibility: data-lenis-prevent on case-studies DialogContent, both admin overlay roots, and admin inbox ScrollArea — wheel/touch inside those surfaces now scrolls them natively instead of fighting Lenis root hijack.
- All 4 R3F scenes (crime-scene, evidence-3d, helix-3d, tunnel-3d) are theme-reactive end to end: JSX materials update via React re-render, gridHelper remounts via key, and every vertex-color Float32Array rebuilds through palette-keyed useMemo (stable per-particle color roles prevent reshuffles; tunnel position buffer intentionally never reallocated).
- Mapping decisions worth noting: red-300 → acc2-bright (analogy to emerald-300 rule); case ONGOING badge keeps its red-400 brightness as acc2-bright; crime-scene deep/dark/dim reds derived as acc2 luminance shades (0.78/0.62/0.45) to preserve depth in CLASSIC; slate #94a3b8 and white #e2e8f0 both → palette.white in scenes per spec; #020617/#0f172a/navy literals and fog kept.
- Lint: PASS (0/0). tsc: PASS (no new errors). Dev compile: clean.

---
Task ID: 12 (orchestrator)
Agent: Z.ai Code (orchestrator)
Task: Final finishing — 2-theme system + Lenis smooth scrolling (foundation + parallel agents 12-a/12-b + verification)

Work Log:
- Installed lenis@1.3.27. globals.css: registered Tailwind4 @theme inline tokens acc1/acc1-bright/acc2/acc2-bright; :root = FORENSIC (emerald #10b981/#34d399 + red #ef4444/#f87171), [data-theme='classic'] = CLASSIC (cyan #06b6d4/#22d3ee + amber #f59e0b/#fbbf24); rgb triplet vars --acc1-rgb/--acc2-rgb for inline rgba; selection + scrollbar now var-driven (also fixed leftover cyan rgba in globals from pre-green era); html.theme-switching 600ms color cross-fade transition rule; removed scroll-behavior:smooth + scroll-padding-top (Lenis handles both).
- Created theme.tsx (ThemeProvider/useTheme: theme, other, otherLabel, toggle, palette{acc1Bright,acc1,acc2,acc2Bright,white}; localStorage 'za_theme'; sets <html data-theme>; toggle adds .theme-switching for 600ms; timer-callback hydration, lint-safe).
- Created smooth-scroll.tsx (ReactLenis root, lerp 0.09, duration 1.15, anchors offset -88, wheel 0.95/touch 1.4). page.tsx wraps ThemeProvider > SmoothScroll > SiteContentProvider > site.
- Agent 12-a: navbar (tokens + ThemeToggle button desktop & mobile row, progress gradient via CSS vars), hero, about, skills, evidence-globe + fingerprint-3d theme-aware (tone discriminators at module scope, declarative colors from palette, vertex-color buffers recreated on palette change, arcs repainted via useLayoutEffect).
- Agent 12-b: danger-zone, case-studies, timeline, contact, footer, admin-panel recolored (literal red kept ONLY for admin destructive states); crime-scene/evidence-3d/helix-3d/tunnel-3d theme-aware (gridHelper remount via key, luminance-scaled acc2 shades for CSI depth in CLASSIC); data-lenis-prevent on case DialogContent + admin overlays + inbox ScrollArea.
- Verified in browser: lenis class on html; toggle desktop + mobile flips data-theme + persists; CLASSIC screenshots (globe cyan/amber, CSI amber spike-mine, fingerprint cyan+amber scan line); anchor click lands with 168px clearance; admin login zain2026 -> COMMAND_CENTER // ADMIN OK; mobile 390px 0 overflow in BOTH themes; console 0 errors; lint 0/0; tsc clean.

Stage Summary:
- Two themes: FORENSIC (emerald+red, default) and CLASSIC (cyan+amber, the original 2024 palette). Toggle in navbar desktop + mobile menu; persisted in localStorage; <html data-theme> drives CSS vars; ALL Tailwind accents use tokens; ALL 6 R3F scenes read palette via useTheme (R3F v9 context bridging) and recolor live.
- Smoothness: Lenis inertia scrolling site-wide (no CSS smooth-scroll conflict), anchor offset -88, inner-scroll surfaces opted out via data-lenis-prevent (case dialog, admin overlays/inbox), theme switch cross-fades via .theme-switching.
- Env note: passcode still zain2026 via ADMIN_PASSCODE — remind user to change before deploy.

---
Task ID: 13
Agent: Z.ai Code (orchestrator)
Task: User-reported fixes — invisible TRANSMIT MESSAGE button (stale CSS), BLUE+RED theme, prominent theme toggle, font/contrast verification

Work Log:
- ROOT CAUSE of "site boring / no send button": dev server served STALE globals.css — zero acc1/acc2 tokens in compiled CSS (no .bg-acc1 rule, :root had no --acc1, no [data-theme] block). Every acc utility was invalid-at-computed-value → transparent bg (TRANSMIT MESSAGE button invisible: rgba(0,0,0,0) + slate-950 text), inherited text colors → washed-out "boring" look. Scenes still recolored (JS palette) which masked the breakage. Fixed by touching globals.css to force Tailwind recompile — button computed bg instantly rgb(59,130,246).
- Re-themed per user request ("theme blue hai, red add karo"): [data-theme='classic'] (cyan/amber) REPLACED by [data-theme='blue'] — acc1 #3b82f6/#60a5fa (blue-500/400), acc2 red #ef4444/#f87171. theme.tsx: ThemeName = 'blue' | 'forensic'; THEMES.blue{label BLUE} + THEMES.forensic{label GREEN}; DEFAULT = blue; localStorage 'classic' migrates to blue.
- Navbar toggle made prominent (user asked for an obvious theme button): desktop pill with live acc1+acc2 color dots (shadow glow, rounded-full, bold mono "THEME: GREEN/BLUE"), NEW icon-only toggle in mobile header (always reachable without opening menu), menu row upgraded to same pill style. aria-label "Switch color theme — switch to X".
- Font/contrast verified in browser: headings white, body slate-300/400, accents only on mono labels/buttons — blue theme: headline "Science & Data" blue + red arcs in globe; green theme: emerald accents. Both themes screenshot-verified.
- E2E: TRANSMIT MESSAGE visible + functional (filled form → TRANSMISSION COMPLETE, case ZA-2026-2C5B → test row deleted from DB after). Toggle desktop + aria-click both directions verified (blue↔forensic, button bg swaps 59,130,246 ↔ 16,185,129). Mobile 390px: scrollWidth 390, 0 overflow. Console 0 errors. Lint 0/0, tsc clean.

Stage Summary:
- Themes now: BLUE (default; blue+red on navy) and GREEN (forensic emerald+red). Both share red as danger accent → CSI scene stays red/dangerous in both.
- The stale-CSS incident: if accents ever vanish again, touch src/app/globals.css and reload (dev-server Tailwind cache quirk).
- Button/toggle/env: passcode zain2026 unchanged; reminds user to change ADMIN_PASSCODE before deploy.
