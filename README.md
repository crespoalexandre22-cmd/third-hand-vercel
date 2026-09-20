# Third Hand — Vercel Edition

> An independent fork of [Third Hand by Shiv Shanmugam](https://github.com/shhivv/third-hand), adapted to use **Jev through Vercel AI Gateway**. Not an official TypeSafe or Vercel release. Each user supplies their own Vercel API key; no shared key or credits are included.

A small macOS menu bar assistant. Focus an app, press **Control–Space**, and tell it what to do.

Third Hand reads accessible controls, types, clicks, and checks the result. Press **Control–Space** again or click **×** to stop.

## Download

- [Download source code (ZIP)](https://github.com/crespoalexandre22-cmd/third-hand-vercel/archive/refs/heads/master.zip).
- [Releases and application downloads](https://github.com/crespoalexandre22-cmd/third-hand-vercel/releases).

Requires an **Apple Silicon Mac, macOS 14+**. The initial application build is Developer ID-signed but **not notarized by Apple**; macOS may block downloaded copies. It is a testing release, not a frictionless installer. Building from source is also supported below.

Do not use the upstream release to connect to Vercel: it expects a direct TypeSafe key.

## Vercel setup

1. Create an AI Gateway API key in your own [Vercel dashboard](https://vercel.com/ai-gateway).
2. Register a valid payment card with the Vercel team owning the key. Without it, evaluation requests may return HTTP 403 (`customer_verification_required`). Usage is billed to that team; configure a budget there.
3. In Third Hand, choose **Set API Key…** and paste only the key value. It is stored in macOS Keychain.
4. Enable Accessibility and Screen Recording. If replacing the original app, remove its old permission entry and add this copy: the signing identity differs.
5. Focus the app to control and press **Control–Space**.

This edition calls `typesafe-ai/jev` using the AI Gateway evaluation v4 protocol. It does not accept direct TypeSafe keys. Your task and observed screen text pass through Vercel to TypeSafe; screenshots remain local.

## Build from source

You’ll need **Xcode 15 or newer**, an Apple Development or Developer ID signing certificate, and a Vercel AI Gateway API key.

```sh
git clone https://github.com/crespoalexandre22-cmd/third-hand-vercel.git
cd third-hand-vercel
./rebuild.sh
open "Third Hand.app"
```

In the setup window:

1. Enable **Accessibility** so Third Hand can read and control apps.
2. Enable **Screen Recording** for local text recognition when an app’s controls aren’t accessible.
3. Add your **Vercel AI Gateway API key**. It’s saved in macOS Keychain.

Switch to an app, press **Control–Space**, and try a specific task, such as “Search for Adele.”

The app runs on macOS 14+. Jev is the only model; Apple Intelligence is not required.

## How it works

- **Accessibility** reads controls and their current values.
- **Apple Vision** reads screen text locally when needed. Screenshots aren’t uploaded.
- **Jev** chooses actions from text descriptions. Your request, app name, screen labels and values, and recent action history are sent through Vercel to TypeSafe. Third Hand is **not fully offline**.
- **Structured text entry** lets Jev select search phrases or literal text from your current request. Free-form writing and arbitrary command generation are not supported.

No bundled model weights or extra runtime dependencies. Third Hand never restarts the apps it controls.

## Development

```sh
./rebuild.sh       # Build, sign, and update Third Hand.app
swift test         # Run tests without calling the live API
```

Always run the repository-root `Third Hand.app`. The build script keeps the same signing identity to preserve macOS permissions and retains the previous app in `.build/install.*`. Keep `.thirdhand-signing-identity` on your machine; it is excluded from Git. If no certificate is available, create an Apple Development certificate in Xcode before building.

Setup shows current permission status. If macOS asks you to quit and reopen after granting access, reopen this same copy.

Diagnostic logs are written to `~/Desktop/thirdhand.log`. They include action status, timing, and bounded API rejection messages. Review logs before sharing: service error messages can contain request details. API keys are redacted from those messages.

For terminal entry, focus a shell prompt and provide the exact command, such as `type "ls -la"`. Third Hand preserves the supplied command and submits only when Jev selects Return. It does not construct commands from navigation requests or append verification commands. It will not retype a terminal command automatically. Interactive editors and non-shell terminal programs are not supported by this entry mode.

## Validation

The modified Swift client was checked with simulated responses and live Jev requests for action selection, text selection, and completion checking. Production compilation succeeded. The XCTest suite was not run successfully on the preparation machine because it has only Command Line Tools and no XCTest module; use full Xcode to run it. A complete cross-application control scenario has not been validated for this release.

## Status

An early, experimental project. Some apps expose incomplete controls; icon-only interfaces, custom editors, and complex gestures may not work. A task can stop without completing, and reported completion still needs your judgment. Stay nearby while it works.

Issues and pull requests are welcome. Please include your macOS version, the app involved, and the steps to reproduce. Don’t include API keys or private screen content.

## License

[MIT](LICENSE) — Shiv Shanmugam · [shiv@tryisle.com](mailto:shiv@tryisle.com)
