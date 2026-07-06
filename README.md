# Ski Stunt Simulator 🎿

A web recreation of the classic **Ski Stunt Simulator** (DDsim). Bomb down the hill with real
momentum, launch off the ramp, tuck and untuck in mid-air to spin flips over the wall, and land
upright on the far side.

## Run it

Just open `index.html` in any browser — double-click it, or drag it onto a browser window.
No build step, no server, no dependencies. It's one self-contained file.

## Controls

| Input | What it does |
|-------|--------------|
| **Move mouse down / up** | Crouch/**tuck** (spin faster) vs stand tall/**extend** (slow the spin) |
| **Move mouse right / left** | **Lean** — right = **front flip**, left = **backflip** |
| **Hold mouse / Space** | Convenience override — snaps to a full tuck |
| **R** | Watch an instant **replay** of your last run (loops; any key stops it) |
| **Click / Space** (on result card) | Drop back in and go again |

Control is **relative**: at the start of each run an anchor is captured at the cursor's current
spot, and the pose is driven by how far you've since moved from there — not the absolute screen
position. Move **down** to crouch/tuck and **up** to stand tall and extend (hands up); move
**right** to lean forward (front flip) and **left** to lean back (backflip). Lean seeds the spin
at the lip and keeps applying torque in the air, so you steer flip direction live; tucking whips
it faster. The reference distances live in `tuckTarget()` / `leanTarget()` (`H*0.32`, `W*0.15`) —
smaller = more sensitive.

### How to actually land it
1. **Hold through the lip.** Crouching as you hit the kicker pops a bigger, longer jump — you
   *need* it to clear the wall. Let go too early and you'll come up short into the gap (PLUNGE).
2. **Tuck to spin, extend to stop.** Pulling in makes you rotate faster; opening up slows you
   down — real conservation of angular momentum, like a figure skater. Time your extend so you
   come around to **upright** as you meet the landing slope.
3. Land within ~40° of upright *and* past the wall = clean landing. More flips = more points.

## The physics (for tuning)

Everything lives in `index.html`. The tuning constants are grouped at the top of the `<script>`
under `// tuning`:

- `G`, `RUN_GRAV`, `FRICTION`, `MIN_SPEED`/`MAX_SPEED` — how fast the skier accelerates down the
  hill and off the jump.
- `POP` — extra launch kick you get for being tucked at the lip (the timing reward).
- `SEED_L`, `I_OPEN`, `I_TUCK` — the spin model. Spin rate is `ω = L / I`; `I` shrinks as you
  tuck, so a smaller `I_TUCK` = snappier flips. `SEED_L` is how much spin the ramp gives you.
- `LAND_TOL` — how forgiving the upright-landing check is (radians).

The course itself (run-up, ramp lip, gap width, wall height, landing slope) is defined in
`buildCourse()`. Wall height nudges slightly each drop for variety.

## Deploying to GitHub Pages (skistuntsimulator.com)

The repo already contains everything Pages needs: `index.html` at the root, a `CNAME` file
(`skistuntsimulator.com`), and `.nojekyll`. It's committed locally on the `main` branch.

**1. Create the repo** (web UI): https://github.com/new → owner **SanKlein**, name **skistuntsimulator**,
visibility **Public**, do NOT add a README/.gitignore/license (we already have them) → Create.

**2. Push it:**
```bash
cd ~/Documents/ski-sim
git remote add origin https://github.com/SanKlein/skistuntsimulator.git
git push -u origin main
```

**3. Enable Pages:** repo → **Settings → Pages** → Source: *Deploy from a branch* → Branch: **main** / **/(root)** → Save.
The custom domain should auto-fill from the `CNAME` file; if not, set **skistuntsimulator.com** under *Custom domain*.

**4. Point the domain in Squarespace** (Settings → Domains → skistuntsimulator.com → DNS Settings):
- Add four **A** records, Host `@`, pointing to GitHub's Pages IPs:
  `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
- (Optional IPv6) four **AAAA** records, Host `@`:
  `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`
- One **CNAME** record, Host `www` → `sanklein.github.io`
- Remove any default parking A records Squarespace added for `@` so they don't conflict.

**5. Enforce HTTPS:** back in Settings → Pages, once DNS propagates (10 min–24 h) tick **Enforce HTTPS**.

After that, `https://skistuntsimulator.com` serves the game. Any future change is just
`git commit` + `git push` — Pages redeploys automatically.

## Roadmap

- **Mobile/touch build** — the controls are deliberately one-button (hold = tuck), so a finger
  tap-and-hold maps directly. Next step is an Expo/React-Native or mobile-web wrapper.
- Ideas parked for later: front-flip vs back-flip direction, saving/sharing replays, sound,
  multiple courses, and (the ambitious one) full ragdoll/rope physics like the original.
