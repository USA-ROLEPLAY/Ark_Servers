# Contributing Guide

## Welcome to the Ark_Servers repo!

This repository powers our ASA cluster configuration.

Most community contributions touch Game.ini and GameUserSettings.ini for individual maps.

Keeping these files clean, consistent, and tested prevents bad deploys and player-impacting outages.

This guide explains what we expect, how to test, and how to open a great PR.

Thanks for helping us keep the cluster healthy! 🦖💚

---

### Why standards matter

- Consistency – Predictable formatting makes reviews fast and diffs small.

- Safety – Tested, minimal changes reduce the risk of broken servers.

- Maintainability – Clear structure helps us audit configs and roll forward safely.

- Collaboration – Shared rules keep reviews about what changed, not “why is this file noisy?”.

---

### What you can contribute

Most contributions are to:

- `ASA/**/**/GameUserSettings.ini`

- `ASA/**/**/Game.ini`

Please do `NOT` commit secrets (admin passwords), binaries, or unrelated file changes.
Compose and automation changes are typically handled separately by maintainers.

> [!IMPORTANT]
> If you have experiance with `docker` or `github automation` you are welcome to contribute.
> However, be aware these types of changes are highly scrutinized and may be rejected.

---

### Directory layout (where files live)

```perl
ASA/
  asa-server-<N>/
    <map_name>/
      GameUserSettings.ini
      Game.ini
      docker-compose.yaml          # maintained by core team
```

Pick the exact map you are targeting (e.g., `asa-server-2/ragnarok/`)

---

### Local testing (required)

Before you open a PR, verify your config compiles and behaves:

1. **Use Beacon (recommended)**
   - Validate and generate your settings with [Beacon](https://usebeacon.app/)
   - Export only the sections you are changing. Keep diffs small.

2. **Spin up a local test** (any one of the following):
   - **Single-player** if your testing Game.ini changes only. Place the file in `C:\​Program Files (x86)\​Steam\​steamapps\​ARK\​ShooterGame\​Saved\​Config\​WindowsNoEditor\​Game.ini`
   - **Windows-server** if your testing GameUserSettings.ini or Game.ini changes. Place the files in `ShooterGame\Saved\Config\WindowsServer`
   - **Docker** if you are testing using docker. I recommend using this [container image](https://github.com/mschnitzer/ark-survival-ascended-linux-container-image) and placing the files in `/var/lib/docker/volumes/your-volume-name/_data/ShooterGame/Saved/Config/WindowsServer`

3. **Sanity checks**
   - No syntax errors, dulicate keys, or typos.
   - The server starts and reaches RCON.
   - You did not unintentally change unrelated settigs.

If you can't test locally, mark your PR as `Draft` and clearly state what you could/couldn't verify.

---

### Formatting & content rules

- **Line endings**: `LF` (Unix). UTF-8 `without BOM`. End file with a newline.
- **Comments**: `;` for comments in INI files. Keep comments brief and helpful.
- **Whitespace**: No trailing spaces. One key per line. No tabs.
- **Ordering**: Group keys under the correct headers. Keep ordering stable to minimize diffs.
- **Duplicates**: Avoid duplicate keys in the same section. If you must override, remove the prior line.
- **Secrets**: Never commit real passwords or secrets of any kind. For `GameUserSettings.ini` use:

  ```ini
  ServerAdminPassword=${SERVER_PASSWORD}
  ```
  (The pipeline injects the real secret on the server.)

- **Don't change operational keys** unless your PR is specifically about them:
  - `RCONEnabled`, `RCONPort`
  - Cluster, networking, or storage paths
  - Anything that would break cross-server  travel
- **Keep diffs tight**: Only change the lines you intend to modify. Avoid whole-file rewrites.

---

### Example of good, minimal diff

```diff
 [ServerSettings]
-StructurePickupTimeAfterPlacement=15
+StructurePickupTimeAfterPlacement=30

 [SessionSettings]
-SessionName=USA ROLEPLAY - Ragnarok
+SessionName=USA ROLEPLAY - Ragnarok | QoL Update
```

---

### How to contribute (step by step

1. `Fork` the repository to your GitHub account.
2. `Clone` your fork:

```bash
git clone https://github.com/USA-ROLEPLAY/Ark_Servers.git
cd Ark_Servers
```

3. **Create a branch**:

```bash
git switch -c my-branch
```

4. **Make your changes** to the correct map folder. Test locally.
5. **Run quick hygiene checks**:

```bash
# ensure LF endings
find ASA -name "Game*.ini" -type f -print0 | xargs -0 dos2unix -q

# show your diff is minimal
git diff --word-diff=color
```

6. **Commit** with a clear message:

```bash
git commit -am "ragnarok: increase pickup time; update session name"
```

7. **Push** your branch and **open a PR** against `main`:

```bash
git push --set-upstream origin my-branch
```

8. **PR checklist** (fill in the template):
   - [ ] I have read the [Contributing Guide](https://github.com/USA-ROLEPLAY/Ark_Servers/blob/main/.github/CONTRIBUTING.md)
   - [ ] I have read the [User Guides](https://github.com/USA-ROLEPLAY/Ark_Servers/blob/main/.github/CONTRIBUTOR_GUIDES/USER_GUIDES.md)
   - [ ] I validated with Beacon or a local test server or single player game
   - [ ] Targeted the correct map directory
   - [ ] I explainmed why the change benefits the server

**Draft PRs** are welcome while you test or collect feedback.

---

### Review & merge process

- CI will run format/health checks and internal automation.
- A maintainer will review for scope, safety, and player impact.
- We may request tweaks to minimize diffs or move settings to the proper section.
- Once approved, the PR is merged. Relevant deploy workflows will pick it up on the next scheduled or manual run.

For emergancy changes, ping a maintainer in your PR description.

---

### Tips for great INI changes

- Prefer `incremental` over sweeping edits.
- Include `player-facing rationale` (e.g, "increase pickup time to reduce accidental placments").
- If changing balance or rates, mention `expected impacts` (breeding, harvesting, boss fights, etc.).
- Avoid toggling experimental or unstable engine flags unless coordinated with maintainers.

---

### What not to do

- ❌ Commit real passwords, tokens, or IPs.
- ❌ Reformat entire files or change line endings.
- ❌ Edit multiple maps in one PR unless the change is intentionally cluster-wide.
- ❌ Modify RCON settings or cluster paths.
- ❌ Modify docker-compose.yaml or automation files - unless you know what you are doing.

---

### Need help?

- Ask questions in your PR or in our community channels.
- Join our [Discord](https://discord.gg/YURaDVRdet) for support.
- Share Beacon exports or minimal repro steps when reporting issues.
- Unsure which map to target? Mention the server name and we’ll point you to the right path.

---

Thanks again for contributing!
Your careful changes keep our worlds stable and fun for everyone. 🛠️🗺️