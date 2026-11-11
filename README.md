![TJK](logo.svg)

# Token Harbor account registrar

Registers Token Harbor accounts and saves their API keys to a text file.

![Token Harbor registrar running in a terminal](snapshot.svg)

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

The tool then asks three questions:

1. Which browser to use (a numbered list of the ones it found).
2. How many accounts to create.
3. Where to save the API keys file — a folder window opens so you just pick one.

Then it creates the accounts and writes the keys to the file.

## The API keys file

The tool writes **one line per account**, in this form:

    email|API key

Example `API-Keys.txt` with two accounts created:

    alex96@voltmail.com|thk_live_9Kd2mQ7pR3tXf5Yb1nLc8dWzAe6hUj0SgV4oPiTrEmNqBkZl2wRMvHyCd8tKpQ3e
    maya.putri@guerrillamail.com|thk_live_3Rc7yHq1wTz8bNv4mKd6sPj0aXe2gUf5oLh9iCnQrStVuBkMd2pWg7ZyE4tJxHmN

Everything before the `|` is the throwaway email, everything after it is the API key.

### Add the keys to [9router](https://github.com/decolua/9router)

No typing needed. Open `API-Keys.txt`, select all, copy, then paste the whole block
into [9router](https://github.com/decolua/9router)'s **Bulk Add** field. 9router pulls
the key out of every line, so the addresses just come along with it.

## Notes

- The service limits how many accounts can be created from one network per hour.
  When it hits that limit, the tool waits and keeps going on its own - it never
  stops early, so if you ask for ten it keeps working until it has made ten.
  The first wait is 2 minutes and each one after that is longer (5, 10, 15...).
  If it waits for a long time, a different internet connection (a mobile hotspot
  or a VPN) makes it work faster.
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
- Each account's email is a free throwaway address. The tool picks a different
  provider at random for every account (and moves on automatically if one is down),
  rotating through each provider's domains so the addresses look varied. A single
  mailbox site being unavailable never stops the run.
- Keep the API key safe - it is the only thing you need afterwards.
- A log of every run is written next to the API keys file, in a `runs` folder.
- The tool also keeps a hidden `.browser-profile` folder next to the API keys file.
  It is the browser's memory (cookies + device identity) for the account being created
  right now. It is wiped automatically before every account, so each account looks like
  a brand-new device - the site counts sign-ups per network *and* per device, and reusing
  one device is what stops the batch after a few accounts. Nothing for you to do here.

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

## Advanced (only if you need more than one network at once)

Send the accounts through a list of proxies, one per account in turn:

    TH_PROXIES="http://user1:pass1@host1:port, http://user2:pass2@host2:port" ./run.sh

- The browser always runs **invisibly**. No window opens on your screen.
  If you ever want to watch what it is doing, start it with
  `TH_BROWSER_MODE=headed ./run.sh` (a window appears; nothing else changes).
- If the site refuses an attempt, the tool retries on its own using a different
  installed browser (Brave / Chrome / Edge), still invisibly. It never gives up on
  the first refusal.
