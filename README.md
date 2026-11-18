![TJK](logo.png)

# AI gateway account registrar

Registers accounts on **Token Harbor**, **Cloudflare**, and/or **BazaarLink** and saves
their API keys to text files.

![Token Harbor registrar running in a terminal](snapshot.jpeg)

## What you need

1. **Node.js** (free) — https://nodejs.org — click the green **LTS** button and run the installer.
2. **A supported browser** — Google Chrome, Microsoft Edge, Brave, or Chromium.
   (Firefox and Safari do not work.)
   Tested working on **Brave Browser**.
3. **Internet connection.**

## Run

- **macOS / Linux:**  `./run.sh`
- **Windows:**  double-click `run.cmd`

That is all. The launcher already contains the whole tool, so there is nothing to
install by hand and nothing else to copy.

The first run does a one-time setup by itself: it writes the tool into a private
folder in your home directory (`~/.tokenharbor-registrar`, or
`%USERPROFILE%\.tokenharbor-registrar` on Windows) and downloads a small helper
(~40 MB, about a minute). Later runs skip that and start straight away.

The tool then asks a few questions:

1. Which providers to create accounts for. A small checklist with a box per provider
   (`[ ]` / `[x]`): press **1**, **2** or **3** to tick or untick one, then **Enter**.
   Pick as many as you like — one, two, or all three — and they run **at the same time**,
   each provider writing its own file. The number of accounts is asked per provider.
2. Which browser to use (a numbered list of the ones it found, plus the bundled
   Chromium). Cloudflare and BazaarLink both run a security check, and they are most
   reliable with an installed browser — pick one of those if the list has it.
3. How many accounts to create.
4. Where to save the API keys — a folder window opens so you just pick one.
5. How many seconds to pause between two accounts.

Then it creates the accounts and writes the keys to the files. When the batch is finished
it prints a short summary - every account made, with the start of its key - and where the
files were saved:

    Done. Created 2 account(s).

      alex96@voltmail.com        thk_live_9Kd2mQ7pR3...
      maya.putri@guerrillamail.com   thk_live_3Rc7yHq1wTz...

    Saved to : /path/you/picked  API-Keys-TokenHarbor.txt
    Log      : /path/you/picked/runs/run-2026-11-18-10-00-12345.log

## The API keys files

Each provider writes its **own file** in the folder you picked:

| Provider | File | One line per account |
| --- | --- | --- |
| Token Harbor | `API-Keys-TokenHarbor.txt` | `email|API key` |
| Cloudflare | `API-Keys-CloudFlare.txt` | `email|API key|account id` |
| BazaarLink | `API-Keys-BazaarLink.txt` | `email|API key` |

Example `API-Keys-TokenHarbor.txt` with two accounts created:

    alex96@voltmail.com|thk_live_9Kd2mQ7pR3tXf5Yb1nLc8dWzAe6hUj0SgV4oPiTrEmNqBkZl2wRMvHyCd8tKpQ3e
    maya.putri@guerrillamail.com|thk_live_3Rc7yHq1wTz8bNv4mKd6sPj0aXe2gUf5oLh9iCnQrStVuBkMd2pWg7ZyE4tJxHmN

Example `API-Keys-CloudFlare.txt`:

    alex96@voltmail.com|cfut_9Kd2mQ7pR3tXf5Yb1nLc8dWzAe6hUj0SgV4oPi|1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d

Everything before the first `|` is the throwaway email; after it comes the API key, and
for Cloudflare the account id at the end.

### Add the keys to [9router](https://github.com/decolua/9router)

No typing needed. Open the file for the provider you want, select all, copy, then paste
the whole block into [9router](https://github.com/decolua/9router)'s **Bulk Add** field.
9router pulls the key out of every line, so the addresses (and the Cloudflare account id)
just come along with it.

## Notes

- The service limits how many accounts can be created from one network per hour.
  When it hits that limit, the tool waits and keeps going on its own - it never
  stops early, so if you ask for ten it keeps working until it has made ten.
  The first wait is 2 minutes and each one after that is longer (5, 10, 15...).
  If it waits for a long time, a different internet connection (a mobile hotspot
  or a VPN) makes it work faster - or just let the tool change network by itself
  (see "The tool switches network by itself" below).
- A VPN or hotspot is picked up automatically: turn one on, then run the tool.
  If the tool is already waiting because of the limit, switching network cancels
  the wait immediately - it notices the new connection and carries on.
- If you stop the tool (Ctrl-C) in the middle of one of these waits and start it
  again, it tries straight away instead of sitting out the rest of the countdown.
  The counting-up starts over from 2 minutes, so a restart is never punished with
  a longer wait than before.
- The wait before trying again is the tool being polite; the site is the one that
  actually decides. If it is still refusing, the tool will be told so and will wait
  again - you never have to sit and watch it.
- Each account's email is a free throwaway address, run for free by other people:
  [VoltMail](https://voltmail.my.id), [Inbox Kitten](https://inboxkitten.com) and
  [Guerrilla Mail](https://www.guerrillamail.com).
  The tool picks a different one at random for every account (and moves on
  automatically if one is down), and varies the domain per address so the addresses
  do not all look alike. A single mailbox site being unavailable never stops the run. A
  service that keeps sitting on the confirmation mail is skipped on the site that
  suffers most from it (VoltMail on Token Harbor, Inbox Kitten on BazaarLink).
- They, and [Cloudflare's free WARP](https://1.1.1.1/), are what make this whole thing
  possible at no cost to anyone - thanks to them.
- Keep the API key safe - it is the only thing you need afterwards.
- A log of every run is written next to the API keys files, in a `runs` folder.
- The tool also keeps a hidden `.browser-profile-<provider>-...` folder next to the API keys
  files while it works. It is the browser's memory (cookies + device identity) for the account
  being created right now. It is wiped automatically before every account, so each account looks
  like a brand-new device - the site counts sign-ups per network *and* per device, and reusing one
  device is what stops the batch after a few accounts. The folder is removed again when the run
  finishes. Nothing for you to do here.
- You can run several copies in **one and the same folder**, at the same time - all of them on
  one provider, or any mix of the three. Each copy keeps its own browser profile, its own log
  file, and its own signup-window timer, so they never trip over each other. Unless the tool is
  switching networks for them (below), they share your internet connection, so the site's hourly
  limit applies to them together.

### When you see the "a few new accounts per hour" message

If this appears on screen, the current network is out of quota:

    the site only allows a few new accounts per hour from this network.
    waiting 2 minute(s), then retrying account {next_account_number}/{target_account_number} (try 14 in total)
    tip: switching to a phone hotspot or a VPN clears this limit straight away

**Switch your internet connection (mobile hotspot or VPN) right away - do not wait
for the countdown to finish.** Waiting it out on the same connection only gets
refused again, and each retry makes the next wait longer (2, 5, 10, 15 minutes...).

The tool watches your connection the whole time it is waiting. As soon as it sees a
new one, it skips the rest of the wait and continues on the spot:

    network changed - the limit does not apply here, retrying now

### The tool switches network by itself (nothing to install)

The limit belongs to the network, so the way out of it is to switch to another one - and
the tool does that on its own, with no hotspot, no VPN app to click, and nothing for you
to install. It uses Cloudflare WARP, a free WireGuard tunnel, and sets the whole thing up
by itself the first time it is needed.

When the site says "a few new accounts per hour", the tool changes network and retries
straight away:

    a fresh network, so the hourly limit does not apply here - retrying now

- Before the first account, the tool gets itself ready: it fetches one small helper program
  (about 10 MB) into its own folder, and checks it against the checksum the release
  publishes. Nothing is installed on your computer and **no password is ever asked**.
- **Only this tool uses the tunnel.** The connection change happens inside the tool's own
  process and is handed to one thing: the browser it starts. Everything else on your
  computer - meetings, downloads, the browser you already have open - keeps using your
  normal connection, untouched, and stays that way whether the run is working or stopped.
- Nothing about the connection shows up on screen while it works; those lines go into
  the log file (in `runs/`), not on top of the account counter.
- A network does not last forever: the tool tries up to three times (a fresh session
  first, then a brand-new device) until the connection it ends up on is really a
  different one, and it verifies that before moving on.
- A network that already ran out of quota is written down as used up. The next
  attempt skips straight past it, and that memory survives restarts - so tomorrow's
  run does not walk into today's dead ends.
- **Each provider gets its own tunnel.** Three providers going at once means three separate
  connections, so one of them changing network leaves the other two exactly where they were.
- If a tunnel drops while the batch runs, that provider gets a new one and carries on - the
  run does not stop and does not keep retrying through a dead connection.
- When the run is done - or when you stop it with Ctrl-C - the tunnels are closed with the
  run, because they were never anywhere but inside it.
- The **Cloudflare WARP app is not needed**, and it does not have to be quit: this tunnel
  lives in the tool's process, so it cannot fight with the app over the connection.
- It is all in your own folder (`~/.tokenharbor-registrar` on macOS/Linux): the free
  anonymous WARP device it registered (one file per provider, `warp-device-<provider>.json`),
  the config it writes for each tunnel, and the helper program. Delete those device files
  there to start over with new devices.
- Not wanted? Run with `TH_WARP=0` and the tool only waits, as before.
- Want it by hand? `./run.sh --warp status` (Windows: `run.cmd --warp status`) shows the
  current connection, and `--warp rotate` makes one hop. If the helper could not be fetched
  (no internet, or a network that blocks GitHub), the log says so; pointing `TH_WARP_PROXY`
  at a copy of `wireproxy` you already have is then enough for it to take over.

## Advanced (only if you need more than one network at once)

Send the accounts through a list of proxies, one per account in turn:

    TH_PROXIES="http://user1:pass1@host1:port, http://user2:pass2@host2:port" ./run.sh

- The browser always runs **invisibly** (windowless headless), for every provider.
  No window ever opens on your screen.
- If the site refuses an attempt, the tool retries on its own using a different
  installed browser (Brave / Chrome / Edge), still invisibly. It never gives up on
  the first refusal.
- To skip the questions, name the providers on the command line: `./run.sh --provider 1,3`
  (numbers, `tokenharbor,bazaarlink`, or `all`), plus `--bulk N` for how many accounts and
  `--now` to start without waiting out a saved cooldown. The output folder comes from
  `OUT=/path ./run.sh --bulk 5 --now --provider all`.
