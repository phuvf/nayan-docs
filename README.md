# Intro
First, I'm not sure I've actaully met everyone working on the AV side of this project. If you don't know me, I'm Nick and I've been (along with Harrison) working on the control logic, video output and content for this event.

You can reach me here:
- Phone 07815 200339
- Email: nr@fix8group.com
- Via the "EE AV Techs" WhatApp group

# Deployment

This is not a comprehensive guide on how to deploy this system - it details the basic bulding blocks so you have an idea of the architecture. Once your on-site and ready to work on the control system, I recommend you:

- Unpack the control rack
- Get it powered up
- Connect the PepLink and laptop (see below for details)
- Give me a call on 07815 200339 for further instructions, or WhatsApp me on the EE AV Techs group.

*Note: There's a stand-alone TouchDesigner game which will need to be installed on site but is not addressed here.*

# Overview

Nayan is a simple push-button-win-prizes game. Each time a contestant pushes the buttton a game sequence runs, with various results:

- A lose: Contestant loses the game but gets a free coffee
- A near miss: Contestant loses the game but gets a free coffee
- A win: Contestant wins a prize
- A hero win: Contestant wins a bigger prize plus there's CO2 cannons and live cameras

# Components

We're going to ignore the output devices here and just focus on the control equipment. The system has the following elements:

## Cloud server

This hands out game results and handle video sharing. You shouldn't need to do anything with this other than be aware it exists.

## Laptop

This runs several systems on one box:

### Node server

This runs the game logic, does some of the sequencing and talks to the cloud server for game results. It'll also handle video uploads after hero wins.

### vMix

vMix is used to play the video content for the main LED screen. It is also used to record the live camera feed.

All game audio comes from vMix via an exteral USB sound card.

This is controlled via Companion using OSC.

### Companion

Companion is used to sequence the game states, and also has pages to provide individual control of various system elements (e.g. Hippo states, plinth actions etc.).

Companion actions are triggered via OSC from the node server.

### Laptop connections

- Power
- USB sound card
- USB webcam
- Wired networking
- HDMI to LED processor

## Hippotizer

This runs the video content for the LED tickers & LED pixel tape. It is controlled via Companion using OSC.

**Be aware that this also controls the CO2 cannon via ArtNet**

## Plinth

The plinth is a custom-built device that has the game-play button on top, but also includes functions to serve cards and balls to winners as well as internal sound and lighting. It is controlled via Companion via UDP and requires 12v & wired networking.

## PepLink

This provides internet access to the system and connects direct to the rack switch. There are two EE SIMs in each and they can be configured remotely - they should need no attention from us.

# Game states

Internally, the various win states are numbered:

### sleep

This is the sleep state, for use when the stand is open but no-one is playing. a c. 20s looping video with separate background music.

### win-1

This is the lose state. The system will play through a 40s-ish loop of video before returning to the sleep state.

### win-2

This is the near-miss state. The system will play through a 42s-ish loop of video before returning to the sleep state.

### win-11

This is the general win state. The system will play through a 40s-ish loop of video before returning to the sleep state.
During this scene, a card will be dispensed from the plinth.

### win-21 to win-24

These are the hero wins states. There is one of each every day, making for 4 hero wins/day/site. The system will:

- Play through a 40s-ish loop of video
- Show 10s of live camerea on screen
- Return to the sleep state

Around 30s into the state we'll

- Dispense a ball from the plinth
- Fire the CO2 cannons

Videos shown during the a hero win are uploaded to the cloud server for sharing.

### playout

Periodically the system will check with the cloud server to see if there are any videos to play out. If there are, it will download the most recent video ready for playout.

Playouts will only happen when the system has been in the sleep state for at least 15s.

A playout takes 14s, after which the sytem will return to the sleep state.

### lock

There is a lock state with a 'back shortly' style message. This can be triggered manually (via Companion) if needed for e.g. technical reasons.

### testCards

vMix and Hippo have test cards that can be used to check the system is working correctly. These are triggered manually via Companion.

# Button states

The game button is only active when the game is in sleep state. In all other states, the button will be disabled - this is to avoid mis-timed presses interrupting an existing game/playout.

Additionally, there's a button lock feature (available in Companion) which allows the user to stop all button presses. This can be used if we need to stop new game plays for any reason. Button lock needs to be manually deactived.

As a handy guide, the plinth button lights are only on when the button is enabled and ready to play.

# File & data sharing, remote support

I have access to all the laptops (and Hippos) via TeamViewer, so most of the time will dial in and provide support direct. In addition, every laptop has a couple of bookmarks in Chrome:

- Local companion GUI
- A shared Dropbox folder (read-only) that I can use to get updates to you.

Inside the Dropbox folder is a text document 'Notes' - I'll use this is I need to send hyperlinks etc. to your machine.

# IP table

Most devices have static IPs, as per table below.

| Name/Description               | Hardware                                  | Address | Subnet | Wired or Wireless | Ports in use |
| ------------------------------ | ----------------------------------------- | ------- | ------ | ----------------- | ------------ |
| Router (LAN 01)                | Peplink, typically                        | 172.20.41.1            | 255.255.255.0 | Wired |  |
| Network Switch                 |                                           | 172.20.41.2            | 255.255.255.0 | Wired |  |
| DHCP Range                     | for iPads, phones, dev/test machines etc. | 172.20.41.51-100       | 255.255.255.0 | Wired + Wireless |  |
| Game control, Companion & vMix | Laptop                                    | 172.20.41.101          | 255.255.255.0 | Wired | 57125/60345 (game server OSC/UDP listen), 12321 (Companion OSC listen) |
| LED Screen Processor           | Varies                                         | 172.20.41.102          | 255.255.255.0 | Wired |  |
| Ticker Tape Processor          | Hippo                                     | 172.20.41.103          | 255.255.255.0 | Wired | 4000 (OSC listen) |
| Touch Designer Machine         | VSRV                                      | 172.20.41.104          | 255.255.255.0 | Wired |  |
| Buttton controller             | Olivier's button machine                  | 172.20.41.105          | 255.255.255.0 | Wired | 4001 (UDP listen) |
| Sound desk                     | Varies                                    | 172.20.41.106          | 255.255.255.0 | Wired |
