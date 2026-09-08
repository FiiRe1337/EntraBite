# EntraBite

macOS onboarding for Microsoft Entra ID and a traditional domain.

The user enters an Entra username (UPN) and password. The domain is taken from the email (`max@firma.de` → `firma.de`). EntraBite then binds the Mac, creates local groups that match Entra rights, mounts home and shares, and starts TeamViewer QuickSupport.

## What it does

- Entra sign-in (UPN + password)
- Domain join via `dsconfigad`
- Preparation for Platform SSO / Company Portal
- Local POSIX groups aligned with Entra groups
- SMB home and shares based on group membership
- TeamViewer QuickSupport plus a rights profile (PPPC)
- UI in 12 languages

Languages: English, Deutsch, Français, Español, Português, 中文, 日本語, 한국어, Русский, Polski, Svenska, Italiano.

## What it does not do

With admin rights, EntraBite can install a configuration profile for Accessibility and Screen Recording. It cannot silently rewrite the macOS privacy database (TCC). Apple blocks that.

Without MDM (Intune or similar), Screen Recording often still needs **one** tap on Allow.

A full Entra join on a Mac also needs Company Portal / Platform SSO and valid tenant policy. The script prepares that; it does not replace an Intune profile.

## Quick start on the Mac

1. Unzip `EntraBite-mac.zip`.
2. Double-click `EntraBite.app` (not a browser).
3. Enter UPN and password.
4. Wait until groups, shares, and TeamViewer QS are ready.

On first launch: right-click → Open if Gatekeeper blocks the unsigned app.

## Build in Visual Studio Code

Open the project folder in VS Code. Recommended extensions: Swift, ShellCheck.

```text
Terminal → Run Task → Build EntraBite.app
```

The app is written to `dist/EntraBite.app`.

Without the task, in a terminal:

```bash
chmod +x native/macos/build-mac-app.sh
zsh native/macos/build-mac-app.sh
```

For a signed build: Xcode + Developer ID, then build the Swift targets under `native/macos`.

## Layout

```text
src/                  control plane and i18n (TanStack Start)
src/lib/join/         join logic, script and PPPC generators
public/EntraBite.app  double-click bundle for macOS
native/macos          Swift package + build script for VS Code / Xcode
.vscode               tasks and recommended extensions
```

## Join script (outline)

`join.sh` runs as root inside the app:

- `dsconfigad -add` with computer name and account
- `dseditgroup` for local groups and admin / printer rights
- `mount_smbfs` for home and shares
- download TeamViewer QS and copy it to `/Applications`
- `profiles install` for the TeamViewer PPPC profile

The password stays in the running process only, then `unset`.

## TeamViewer QuickSupport

The download URL lives in policy (default: official QS DMG). After install, EntraBite opens QS so the ID is on screen immediately.

Bundle IDs in the profile:

- `com.teamviewer.TeamViewerQS`
- `com.teamviewer.TeamViewer`
- `com.teamviewer.TeamViewerHost`

## Requirements

- macOS with administrator rights for the join
- Reachable domain controller (VPN if needed)
- Entra account that is valid in the domain
- Optional: MDM enrollment so Screen Recording applies without an extra tap

## Note

This repository holds the control UI and the native wrappers. A real Mac join only succeeds when tenant, AD, and network match. Demo accounts in the UI are simulation only.


---

# EntraBite (Deutsch)

macOS-Onboarding für Microsoft Entra ID und klassische Domäne.

Der Anwender gibt Entra-Benutzer (UPN) und Kennwort ein. Die Domäne wird aus der E-Mail gelesen (`max@firma.de` → `firma.de`). Danach bindet EntraBite den Mac, legt lokale Gruppen mit den Entra-Rechten an, hängt Home und Shares ein und startet TeamViewer QuickSupport.

## Was es tut

- Entra-Anmeldung (UPN + Kennwort)
- Domänenbeitritt über `dsconfigad`
- Vorbereitung für Platform SSO / Company Portal
- Lokale POSIX-Gruppen passend zu Entra-Gruppen
- SMB-Home und Freigaben laut Gruppenmitgliedschaft
- TeamViewer QuickSupport inkl. Rechteprofil (PPPC)
- Oberfläche in 12 Sprachen

Sprachen: English, Deutsch, Français, Español, Português, 中文, 日本語, 한국어, Русский, Polski, Svenska, Italiano.

## Was es nicht tut

EntraBite kann mit Admin-Rechten ein Konfigurationsprofil für Bedienungshilfen und Bildschirmaufnahme einspielen. Es darf die macOS-Datenschutzdatenbank (TCC) nicht still überschreiben — das sperrt Apple.

Ohne MDM (Intune o. Ä.) bleibt Bildschirmaufnahme oft **ein** Klick auf „Erlauben“.

Ein vollständiger Entra-Join auf dem Mac braucht zusätzlich Company Portal / Platform SSO und gültige Tenant-Richtlinien. Das Skript bereitet das vor; es ersetzt kein Intune-Profil.

## Schnellstart auf dem Mac

1. `EntraBite-mac.zip` entpacken.
2. `EntraBite.app` doppelklicken (kein Browser).
3. UPN und Kennwort eingeben.
4. Warten, bis Gruppen, Shares und TeamViewer QS fertig sind.

Beim ersten Start: Rechtsklick → Öffnen, falls Gatekeeper die unsignierte App blockiert.

## In Visual Studio Code bauen

Projektordner in VS Code öffnen. Empfohlene Erweiterungen: Swift, ShellCheck.

```text
Terminal → Aufgabe ausführen → Build EntraBite.app
```

Danach liegt die App unter `dist/EntraBite.app`.

Ohne Task, im Terminal:

```bash
chmod +x native/macos/build-mac-app.sh
zsh native/macos/build-mac-app.sh
```

Für eine signierte Version: Xcode + Developer-ID, dann die Swift-Targets unter `native/macos` bauen.

## Projektstruktur

```text
src/                  Steuerung und i18n (TanStack Start)
src/lib/join/         Join-Logik, Skript- und PPPC-Generator
public/EntraBite.app  Doppelklick-Bundle für macOS
native/macos          Swift-Paket + Build-Skript für VS Code / Xcode
.vscode               Tasks und empfohlene Extensions
```

## Join-Skript (Auszug)

Das generierte `join.sh` (läuft als root in der App):

- `dsconfigad -add` mit Computername und Konto
- `dseditgroup` für lokale Gruppen und Admin-/Drucker-Rechte
- `mount_smbfs` für Home und Shares
- TeamViewer QS laden und nach `/Applications` kopieren
- `profiles install` für das TeamViewer-PPPC-Profil

Kennwort bleibt nur im laufenden Prozess; danach `unset`.

## TeamViewer QuickSupport

Download-URL ist in der Richtlinie hinterlegt (Standard: offizielles QS-DMG). Nach der Installation öffnet EntraBite QS, damit die ID sofort sichtbar ist.

Bundle-IDs im Profil:

- `com.teamviewer.TeamViewerQS`
- `com.teamviewer.TeamViewer`
- `com.teamviewer.TeamViewerHost`

## Voraussetzungen

- macOS mit Administratorrechten für den Join
- Erreichbarer Domain Controller (VPN falls nötig)
- Entra-Konto, das in der Domäne gültig ist
- Optional: MDM-Enrollment, damit Screen Recording ohne Extra-Klick gilt

## Hinweis

Dieses Repository enthält die Steuerungs-UI und die nativen Hüllen. Der Join auf einem echten Mac gilt erst, wenn Tenant, AD und Netzwerk passen. Testkonten in der UI sind nur Simulation.
