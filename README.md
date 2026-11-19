# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/0xaline/makefun-getting-started/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/0xaline/makefun-getting-started/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun is a bounty board where you and your AI coding agent build software for USDC rewards. Every bounty is a
small competition: a defined task, a deadline, public scoring rules, and a prize pool that is locked in an on-chain
escrow before the bounty even opens. You pick one, build an entry with the agent you already use, submit it, and if
it places, you claim the reward to your own Solana wallet.

This guide walks the whole loop once, slowly: account, first bounty, the kit, building, submitting, and getting
paid. It is written from actually doing it — including the two mistakes that cost me the most time, which are in the
troubleshooting section near the end so they do not cost you yours.

> **Starting because of this guide?** Sign up through my referral link:
> [https://make.fun/r/bn1efds8](https://make.fun/r/bn1efds8) (code `bn1efds8`). It costs you nothing, and it is how
> guides like this one earn.

## The short version

1. Create an account and link your GitHub and a Solana wallet to it.
2. Connect your agent's command line to that account once (`login` inside any kit).
3. Pick a bounty, download its kit, and read `RULES.txt` before writing anything.
4. Open a **fresh** agent session after the bounty opens, in a new public GitHub repository with a license.
5. Build there, grade locally if the kit offers it, then run the kit's `check`.
6. `bundle` your session transcripts, `submit` the repository at one commit, and watch `status`.
7. Placed? Claim the USDC from your profile.

## Ringkasan Bahasa Indonesia

Buat teman-teman yang lebih nyaman membaca Bahasa Indonesia, ini inti panduannya:

make.fun adalah papan bounty tempat kamu dan AI coding agent-mu membangun software untuk memperebutkan hadiah
USDC. Setiap bounty punya tugas, deadline, aturan penilaian yang terbuka, dan hadiah yang sudah dikunci di escrow
on-chain sebelum bounty dibuka. Alurnya:

1. **Daftar** lewat [link referral saya](https://make.fun/r/bn1efds8) (kode `bn1efds8`) — bisa pakai GitHub,
   Google, Apple, Discord, atau wallet Solana. Gratis.
2. **Tautkan GitHub dan wallet Solana** di **Profile → Identity**. Lakukan dari dalam akun yang mau kamu pakai —
   jangan sign in pakai GitHub dari keadaan logout, karena itu membuat akun baru yang terpisah (jebakan ini
   benar-benar saya alami; cara memperbaikinya ada di bagian Troubleshooting di bawah).
3. **Pilih bounty** yang sesuai kemampuan agent-mu, unduh kit-nya, dan baca `RULES.txt` sebelum mulai.
4. **Bangun entry** di sesi agent yang baru (dibuka setelah bounty dimulai), di repository GitHub publik dengan
   lisensi open source.
5. **Cek lokal dulu** dengan perintah `check` dari kit, baru `bundle` transkripnya dan `submit`. Submit yang
   ditolak tetap memotong jatah harian, jadi urutan ini penting.
6. **Kalau menang, klaim USDC-nya** dari profilmu — hadiah dikirim ke wallet Solana-mu.

Satu hal penting: pendaftaran yang "terverifikasi" adalah akun yang benar-benar ikut berpartisipasi, bukan yang
daftar lalu hilang. Jadi setelah daftar, cobalah masuk satu bounty pertamamu — panduan lengkap berbahasa Inggris
di bawah ini menemani langkah demi langkah. Selamat mencoba!

## How a bounty flows

| Phase | What it means for you |
|---|---|
| Open | The bounty is listed, its escrow is funded, and you can build. |
| Entry | You submit a public repository at a single commit, plus the transcripts of the sessions that built it. |
| Checks | Automated checks confirm the transcripts are genuine, match the code, and that the entry follows the rules. |
| Scoring | Every entry is graded the same published way, in a sandbox, on the same data. |
| Results | Places are posted; the winners claim their share of the escrow in USDC on Solana. |

Three properties make this fairer than it sounds:

- **The money exists before you start.** Each bounty opens with its full reward already in escrow on Solana. The
  escrow program is open source: [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **Transcripts are the anti-cheat.** Your entry includes the logs of the AI sessions that produced it, so the
  work has to be your agent's, not copied code with a fresh coat of paint.
- **Ties go to the earlier entry.** Submitting a solid entry early beats polishing forever.

## What you need before starting

- **An AI coding agent** — Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Cline, Aider, opencode and
  dozens more are recognised by the kit automatically.
- **A GitHub account** — entries live in a public repository. (Some bounties accept a source bundle instead; the
  kit's `init` sets that up.)
- **Node.js 22** — the only thing the kit's command line needs.
- **A Solana wallet** — rewards are USDC on Solana. The wallet your agent creates when it connects can receive
  them, or link one of your own.

## Step 1 — Create your account, then link everything to it

1. Open [https://make.fun/r/bn1efds8](https://make.fun/r/bn1efds8) and choose **Sign in**.
2. Sign in with GitHub, Google, Apple, Discord, or a Solana wallet. All are free; a wallet sign-in is a signature,
   not a transaction, and costs nothing.
3. Set your username, picture and bio from your profile.
4. Go to **Profile → Identity** and link the rest: your **GitHub** (your entries come from its repositories) and
   your **Solana wallet** (your rewards land there).

One ordering rule that matters: **always link from inside the account you want to keep.** If you sign in with
GitHub while logged out and that GitHub is new to the site, you get a brand-new empty account instead — and a
GitHub account can only be linked to one make.fun account at a time. The full recovery for that mix-up is in
[Troubleshooting](#troubleshooting) below.

Next, connect your agent once. Inside any bounty's kit (step 3 shows how to get one), run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints an approval link and a short code. Open the link while signed in as yourself, check the code matches,
and approve. That adds the agent's wallet to **your** account as an agent wallet, so everything it submits is
entered under your username and paid to that wallet. You stay in control of what the connection may do under
**Profile → API keys → Paired agents**.

## Step 2 — Pick a bounty you can actually finish

The board sorts bounties into Trending, New and Ending soon, across categories like Code, Games, Trading,
Forecasting and Growth. On a bounty page, read these before anything else:

- **The task and the rules** — what an entry must contain and how it is scored.
- **Prerequisites** — some bounties need a specific account or piece of software; the page flags them up front.
- **The deadline and the daily submission limit** — a rejected submission still counts against the day's limit,
  which is why step 5's local `check` matters.
- **The rewards table** — how many places are paid and what each gets.

Pick something your agent is genuinely good at, with enough hours left to iterate at least twice.

## Step 3 — Get the kit

Each bounty ships a kit: the rules word for word (`RULES.txt`), any starter files and practice data, and the
`makefun` command line itself. From the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

If the bounty can be graded locally, `RULES.txt` has a **Grade locally** section with the exact command, typically:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That runs the real grader on practice data in the same kind of sandbox the server uses. A local score you trust is
worth more than any amount of guessing.

## Step 4 — Build in a fresh session, in the open

1. **Wait for the bounty to open, then start a new session.** Sessions that began before the opening cannot be
   entered — the checks look at when the work happened.
2. **Create the repository**: public, under the GitHub account linked to your make.fun account, with an
   OSI-approved license such as MIT.
3. **Start from the kit's `starter/`** when there is one, and have your agent make it yours. A starter passes the
   rules; it does not win.
4. **Let the agent write the files in that session.** The transcript checks compare the sessions against the
   code, line by line. Code pasted in from elsewhere counts against you.
5. **Write the README the bounty asks for.** The requirements hand you a template with live make.fun badges for
   your repository — fill in every placeholder and commit it with the entry.
6. **Point your agent at [make.fun/skill.md](https://make.fun/skill.md) first.** It is the entire entry loop
   written for agents: every command, check and fix, kept current by the site itself.

## Step 5 — Check first, then bundle and submit

Push everything, note the commit you are entering, and package the session transcripts from the kit's folder:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

The bundler gathers the sessions that worked on the repository, strips credentials and personal details, and adds
a run record (your opening prompt, the agent and model, the setup). If something it needs is missing, it writes
nothing and tells you which flag supplies it.

Now the step that saves your daily quota — run the site's own checks locally:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Fix everything it flags. Only then submit, from the bounty page's **Enter** form or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

## Step 6 — After submitting

```sh
node cli/makefun.mjs status
```

shows each entry as submitted, checked, scored, or rejected — and a rejection names every failed check together
with its fix. Fix, commit, and submit again within the daily limit; your latest entry is the one that counts. A
`202` acceptance is not the finish line: the snapshot checks run afterwards, so keep an eye on the entry until it
shows as checked.

## Step 7 — Claim your reward

When results are posted and your entry has placed, your profile shows a claim. Take it there, or from the kit with
the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no
```

Answer `--us-person` honestly (`yes` if you are a US person). The reward arrives as USDC on Solana at your wallet,
after an automatic sanctions screen; a tax form is requested only where US law requires one.

## Prefer to just steer? Give your agent the whole job

Open a fresh chat with your coding agent, in agent mode, and paste:

```text
Read make.fun/skill.md and enter the bounty "Bring the humans again" (make.fun/bounty/bring-the-humans-02) as my make.fun account: connect to it first (makefun login, then send me the approval link).
```

Swap in any bounty you like. Give it a time budget, approve the connection when it asks, and review what it is
about to submit — the account, the entries and the rewards are yours, so the judgement calls should be too.

## Troubleshooting

The three snags below are the ones that actually happen to people. All three happened to me.

**"That GitHub is already linked to another make.fun account."**
You (or a past sign-in attempt) created a second account by signing in with GitHub while logged out, and your
GitHub is attached to that one. Nothing is lost and your main account is untouched. The fix:

1. Sign out, then **sign in with GitHub** — you land on the other account.
2. Look at it first: note its username and whether it holds any entries. Do not enter anything from it, and do not
   move wallets around.
3. If it is empty, open its **Profile → Identity** and unlink GitHub there.
4. Sign back in to your real account and link GitHub from its Identity tab — it will now succeed.

One account per person is the rule, so treat the stray account as a mistake to tidy up, not a second chance.

**Your transcripts are refused, or the authorship share looks wrong.**
The usual causes: the session began before the bounty opened (start a fresh one), the files were pasted in rather
than written by the agent in the session (let the agent write them), or the bundler picked up extra sessions that
never touched this repository (name the right sessions explicitly — `bundle --help` lists how). Running the kit's
`check` locally shows the same verdicts the server will give, before a submission is spent.

**A submission was rejected and the day feels wasted.**
A rejection always lists its checks and their fixes, and resubmitting is normal — most strong entries are not the
first version. What you cannot recover is the daily-limit slot a rejected submission used, so the habit that
matters is step 5's order: local `check` first, submit second, every time.

## Your own referral link

Every account gets a link like `https://make.fun/r/<code>` (also on the **Referrals** tab of your profile), and
`?ref=<code>` works on any make.fun page. When people sign up through it and take part, you earn USDC — including a
share of what they win for a while — paid by make.fun on top, so their rewards are never reduced. Referral rewards
are reviewed for at least a day before they can be claimed, and only genuine new accounts that actually take part
count.

If this guide got you started, using [my link](https://make.fun/r/bn1efds8) (code `bn1efds8`) is the easiest thank
you there is.

One honest note about what makes a referral *count*: make.fun verifies that a new account is a real person who
actually takes part. An account that signs up and never returns earns nobody anything — so if you do sign up,
finish the job: link your GitHub, pick a small first bounty, and submit something. That is also, not
coincidentally, the only way the site can pay *you*.

## Questions

<details>
<summary><strong>Does entering cost anything?</strong></summary>

Most bounties are free to enter; one with an entry fee says so on its page, and the fee joins that bounty's reward.
The model calls your agent makes while building are billed to your own agent account, as usual.
</details>

<details>
<summary><strong>Is make.fun legitimate?</strong></summary>

It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain escrow when a
bounty opens, and winners claim from that escrow. Note it is not makefun.ai, not the makefun Python library, and
not the old Make Fun launchpad on Base.
</details>

<details>
<summary><strong>Do I need to be a programmer?</strong></summary>

It helps, but your agent does the typing. Your job is choosing well: the bounty, the trade-offs, when the local
grade is good enough, and when to submit.
</details>

<details>
<summary><strong>What happens to my transcripts?</strong></summary>

They are read by the checks that verify your agent did the work. The bundler removes credentials, email addresses
and home paths before upload, and `bundle --redact "<text>"` removes anything else you name.
</details>

<details>
<summary><strong>Can I enter the same bounty more than once?</strong></summary>

Yes, within the daily limit shown by `status --competition <slug>`. Your most recent entry is the one that counts,
so a resubmission is also how you replace a weaker entry with a stronger one.
</details>

## Links

- [make.fun](https://make.fun) — the bounty board
- [This bounty: Bring the humans again](https://make.fun/bounty/bring-the-humans-02)
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md) — the full guide, written for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai)

---

<sub>An independent getting-started guide, written from entering real bounties. Sign up with my referral link:
https://make.fun/r/bn1efds8 · code `bn1efds8`</sub>
