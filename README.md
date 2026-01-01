# ENGLISH ###
💻💻💻💻💻💻💻💻 **INITIAL SETTINGS** ⚙️🖥️🎨

* 🧩 **Purpose:** set GRUB’s basic behavior (timeout, default), load the necessary modules, etc...).

* 🧠 **Repo note:** this `grub.cfg` is **hand-written**, not automatically generated. It’s designed for a specific layout (disks, partitions, EFI paths, ISO labels, subvol). On other systems it **doesn’t work “plug & play”**: it must be adapted carefully. 🧷

* ⏱️ `set default=0` → boots the first menu entry by default.

* ⏳ `set timeout=10` → waits 10 seconds before booting the default entry, I don’t like when boot is too fast

* 🧱 `insmod ...` → loads modules for:

  * 🧭 GPT (`part_gpt`)
  * 🖥️ graphical terminal (`gfxterm`)
  * 📁 filesystem
  * 🧬 EFI video (`efi_gop`, `efi_uga`)
  * 🖼️ images (`png`)
  * 🌀 ISO boot (`loopback`, `iso9660`)

* 🔤 `loadfont ... unicode.pf2` + `terminal_output gfxterm` → enables graphical output with a Unicode font.

* 🖼️ `background_image sfondo.png` → sets the GRUB menu background.

* 🎨 `menu_color_normal` / `menu_color_highlight` → UI colors (normal / selection).

🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻 **DREAMS ~ LINUX FROM SCRATCH** 🐧🛠️🖤

* 🧩 **Purpose:** boot the main system *Dreams_LFS* with a “signed” boot screen (banner via echo) and the kernel started explicitly.

* 📝 **Style:** no magic autodetect: kernel and root are declared clearly (maximum control). ⚙️

* 🟩 **Entry:** `Dreams ~ Linux from Scratch`

  * 🎭 Shows a textual/artistic banner via lots of `echo`.
  * ⏲️ `sleep 2` → a small pause to show the banner.
  * 🧠 `linux /EFI/Dreams-LFS/vmlinuz_Dreams-LFS root=/dev/sda5 rw` → boots the main kernel on root `/dev/sda5` in read/write mode (btrfs).

* 🧰 **Submenu:** `Dreams ~ Linux from Scratch - EXTRA OPTIONS`

  * 🧷 Defines reusable variables:

    * `dreams_lfs="/EFI/Dreams-LFS"` (path)
    * `root_dreams="/dev/sda5"` (system root)

  * 🔀 Collects alternative boot modes, recovery and experimentation:

  * 🟦 **Entry:** `runlevel 3`

    * 🧱 `rw 3` → boots directly into multi-user text mode (runlevel 3), useful for troubleshooting without a graphical interface.

  * 🟨 **Entry:** `/bin/bash`

    * 🧪 `init=/bin/bash` → minimal boot straight into a shell, “open-heart surgery” mode for repairs.

  * 🟪 **Entry:** `Snapshot`

    * 📸 `rootflags=subvol=Dreams_LFS-Snapshot` → boots a snapshot (Btrfs subvolume) instead of the main live system.

  * 🟥 **Entry:** `RO Send/Receive`

    * 🧊 boot from `/dev/sdc1` with a read-only snapshot + runlevel 3 (`ro 3`) → perfect for using a backup without risking changes and without risking that the GUI gets started and gets stuck because it requires writing.

  * 🟫 **Entry:** `Nomodeset`

    * 🖥️ `nomodeset` → disables KMS/automatic graphics drivers, useful when graphics break everything.

  * ⚪ **Entry:** `Kernel .old`

    * 🕰️ boots the previous kernel `vmlinuz_Dreams-LFS.old` as a fallback.

  * 🧬 **Entry:** `Experimental (Linux Next)`

    * 🧪 boots `vmlinuz_Dreams-LFS_Next` to test kernels/experiments without touching the stable one.

  * 🌀 **Entry:** `ISO Copytoram` (note: declared “To be fixed”)

    * 🧠 searches for the partition labeled `ISO_VM`, mounts the ISO via loopback and attempts a “copytoram” boot.

🔷🔷🔷🔷🔷🔷🔷🔷 **ISOLATED ENVIRONMENTS IN DREAMS_LFS** 🧪📦🧬

* 🧩 **Purpose:** manage **complete and bootable** distros as separate environments (subvolumes with dedicated roots), which are also **mounted inside Dreams-LFS** to use *directly* what the distros contain (binaries, tools...).

* 🧠 **Why “isolated”:** because they are **separate environments** that I use in two ways:

  * 🚪 standalone boot (as full distros in every respect)
  * 🧷 mount inside LFS to use applications and contents *from LFS*, without changing OS via scripts I have on my LFS

* 🧷 Variables:

  * `ambienti_isolati="/EFI/Ambienti_Isolati"`
  * `root_ambienti="/dev/sdc2"`

* 🟨 **Entry:** `TEMPORARY`

  * 🔁 it’s a variable “slot”: it changes distro depending on what I need to study in that period.
  * 🧠 boots “temporary” kernel+initrd on subvol `@Momentaneo`.

* 🟧 **Entry:** `Gentoo`

  * 🐧 boots Gentoo as a complete distro on subvol `@Gentoo` using the Dreams-LFS kernel (minimalist choice, it works perfectly and lets me avoid creating an additional kernel for gentoo).

* 🟩 **Entry:** `OpenSUSE`

  * 🦎 boots OpenSUSE with dedicated kernel+initrd, on subvol `@OpenSUSE`, with `rd.timeout=10`.

* 🟦 **Entry:** `Fedora`

  * 🎩 boots Fedora with dedicated kernel+initramfs, on subvol `@Fedora`.

* 🟪 **Entry:** `NixOS`

  * ❄️ boots NixOS with `init=` pointing directly to a Nix store path.
  * 🧊 dedicated initrd and root on subvol `@NixOS`.

* 🗃️ **Commented entries (WIP):** Exherbo, Void, Crux, Alpine, Debian, Artix, Slackware…

  * 🧪 present as a documented “expansion space”.

🪟🪟🪟🪟🪟🪟🪟🪟 **WINDOWS 10** 🪟🧷

* 🧩 **Purpose:** boot Windows via EFI chainloading.

* 🧷 Practical note: **Windows 10**, because I prefer it over Windows 11.

* 🟦 **Entry:** `Windows 10 - Home`

  * 🔗 `chainloader /EFI/Microsoft/Boot/bootmgfw.efi` → hands control to Microsoft’s EFI bootloader.

💿💿💿💿💿💿💿💿 **ISO ~ LOOPBACK** 💿🌀🧰

* 🧩 **Purpose:** boot live ISOs directly from disk, without USB/DVD, using loopback + distro-specific parameters.

* 🧠 Philosophy: an always-ready “library of live systems”, with boot recipes already configured (copytoram, persistence, findiso, label, etc.).

* 🧷 Variables:

  * `iso_label="ISO_VM"` → label of the partition that contains the ISOs
  * `iso_dir="/ISO"` → directory where the ISOs are

* 🧱 Dedicated modules: `loopback` and `iso9660`

* 🟥 **Entry:** `System Rescue`

  * 🛟 searches for the partition by label, mounts `System_Rescue.iso`, boots SysRescue kernel+initrd with rescue parameters (including copytoram).

* 🟧 **Entry:** `System Rescue - Persistent`

  * 🧫 adds persistence: `cow_label=ISO_VM` + `cow_directory=/ISO/Persistenza` → persistent overlay.

* 🟨 **Entry:** `Gentoo`

  * 🐧 Gentoo live with `.igz` initrd, `iso-scan/filename` parameters and squashfs references, includes `insmod gzio`.

* 🟦 **Entry:** `Debian`

  * 🌀 Debian live with `findiso` + `copytoram`, kernel and initrd versioned in the live path.

* 🟩 **Entry:** `Mint`

  * 🌿 casper boot, classic Mint/Ubuntu parameters (`boot=casper`, username/hostname, iso-scan, quiet splash).

* 🟪 **Entry:** `Ufficio Zero`

  * 🧑‍💼 similar to Mint: casper, copytoram, iso-scan/filename, lz initrd.

* 🦎 **Entry:** `OpenSuse` (Thumbleweed)

  * 🧬 OpenSUSE live with parameters `root=live:CDLABEL=...` + persistent overlay + copytoram.

* 🟥 **Entry:** `Kali`

  * 🐉 Kali live with `findiso` + `copytoram`, `noeject`, quiet splash.

* 🕶️ **Entry:** `Tails`

  * 🧅 Tails live with many hardening/security parameters, `nopersistence`, copytoram, explicit initrd.

* 🗃️ **Commented entries (WIP / reference):** NixOS, Parrot, Guix, Nobara, PopOS, Alpine, Void, etc.

  * 🧪 These entries are UNDER CONSTRUCTION, I will add them later, I don’t guarantee they work, I myself haven’t tested them.

⛓️⛓️⛓️⛓️ **EXTRA OPTIONS AT THE BOTTOM** 🧰🔧🧨

* 🧩 **Purpose:** low-level tools and machine controls from GRUB.

* 🧱 **Separator entry:** `------------------------------`

  * 🧊 `true` → fake entry used only as a visual separator in the menu.

* 🟦 **Entry:** `EFI Shell`

  * 🧬 loads fat/chain modules, forces console output, chainloads the EFI shell (`Shell_Full.efi`).

* 🟩 **Entry:** `Memtest86+`

  * 🧠 chainloads `memtest.efi64` for pre-boot RAM testing.

* 🟨 **Entry:** `Reboot`

  * 🔁 `reboot` → immediate reboot.

* 🟥 **Entry:** `Power off`

  * ⛔ `halt` → power off from the loader.

---

This `grub.cfg` is a manual and “hand-crafted” boot menu that acts as a hub: booting my main system (Dreams_LFS), advanced recovery/test options, isolated environments (complete distros usable both via boot and mounted inside LFS), Windows 10 via chainloading, a library of live ISOs bootable from disk, and service tools like EFI Shell and Memtest.

💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻💻

# ITALIANO ###

💻💻💻💻💻💻💻💻 **SETTAGGI INIZIALI** ⚙️🖥️🎨

* 🧩 **Scopo:** impostare il comportamento base di GRUB (timeout, default), caricare i moduli necessari eccetera...).

* 🧠 **Nota repo:** questo `grub.cfg` è **scritto a mano**, non generato automaticamente. È pensato per un layout specifico (dischi, partizioni, percorsi EFI, label ISO, subvol). Su altri sistemi **non funziona “plug & play”**: va adattato con attenzione. 🧷

* ⏱️ `set default=0` → avvia di default la prima voce del menu.

* ⏳ `set timeout=10` → attende 10 secondi prima di avviare la voce di default, non amo quando l'avvio è troppo veloce

* 🧱 `insmod ...` → carica moduli per:

  * 🧭 GPT (`part_gpt`)
  * 🖥️ terminale grafico (`gfxterm`)
  * 📁 filesystem
  * 🧬 EFI video (`efi_gop`, `efi_uga`)
  * 🖼️ immagini (`png`)
  * 🌀 boot ISO (`loopback`, `iso9660`)

* 🔤 `loadfont ... unicode.pf2` + `terminal_output gfxterm` → abilita output grafico con font Unicode.

* 🖼️ `background_image sfondo.png` → imposta lo sfondo del menu GRUB.

* 🎨 `menu_color_normal` / `menu_color_highlight` → colori della UI (normale / selezione).

🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻🧝🏻 **DREAMS ~ LINUX FROM SCRATCH** 🐧🛠️🖤

* 🧩 **Scopo:** avvio del sistema principale *Dreams_LFS* con una schermata di boot “firmata” (banner in echo) e kernel avviato in modo esplicito.

* 📝 **Stile:** niente autodetect magico: kernel e root sono dichiarati chiaramente (massimo controllo). ⚙️

* 🟩 **Voce:** `Dreams ~ Linux from Scratch`

  * 🎭 Mostra banner testuale/artistico tramite tanti `echo`.
  * ⏲️ `sleep 2` → piccola pausa per far vedere il banner.
  * 🧠 `linux /EFI/Dreams-LFS/vmlinuz_Dreams-LFS root=/dev/sda5 rw` → avvia il kernel principale su root `/dev/sda5` in lettura/scrittura (btrfs).

* 🧰 **Sottomenù:** `Dreams ~ Linux from Scratch - OPZIONI EXTRA`

  * 🧷 Definisce variabili riutilizzabili:

    * `dreams_lfs="/EFI/Dreams-LFS"` (percorso)
    * `root_dreams="/dev/sda5"` (root del sistema)

  * 🔀 Raccoglie modalità alternative di boot, recovery e sperimentazione:

  * 🟦 **Voce:** `runlevel 3`

    * 🧱 `rw 3` → avvia direttamente in modalità testuale multi-user (runlevel 3), utile per troubleshooting senza interfaccia grafica.

  * 🟨 **Voce:** `/bin/bash`

    * 🧪 `init=/bin/bash` → avvio minimale diretto in shell, modalità “chirurgia a cuore aperto” per riparazioni.

  * 🟪 **Voce:** `Snapshot`

    * 📸 `rootflags=subvol=Dreams_LFS-Snapshot` → avvia uno snapshot (Btrfs subvolume) invece del sistema live principale.

  * 🟥 **Voce:** `RO Send/Receive`

    * 🧊 avvio da `/dev/sdc1` con snapshot in sola lettura + runlevel 3 (`ro 3`) → perfetto per usare un backup senza rischiare modifiche e senza rischiare che venga avviata la grafica che viene bloccata perchè necessita scrittura.

  * 🟫 **Voce:** `Nomodeset`

    * 🖥️ `nomodeset` → disabilita KMS/driver grafici automatici, utile quando la grafica rompe tutto.

  * ⚪ **Voce:** `Kernel .old`

    * 🕰️ avvia il kernel precedente `vmlinuz_Dreams-LFS.old` come fallback.

  * 🧬 **Voce:** `Sperimentale (Linux Next)`

    * 🧪 avvia `vmlinuz_Dreams-LFS_Next` per test kernel/esperimenti senza toccare quello stabile.

  * 🌀 **Voce:** `ISO Copytoram` (nota: dichiarata “Da sistemare”)

    * 🧠 cerca la partizione con label `ISO_VM`, monta ISO in loopback e tenta avvio “copytoram”.

🔷🔷🔷🔷🔷🔷🔷🔷 **AMBIENTI ISOLATI IN DREAMS_LFS** 🧪📦🧬

* 🧩 **Scopo:** gestire distro **complete e avviabili** come ambienti separati (subvolumi con root dedicate), che però vengono anche **montati dentro Dreams-LFS** per usare *direttamente* ciò che contengono (binari, tool...).

* 🧠 **Perché “isolati”:** perché sono **ambienti separati** che uso in due modi:

  * 🚪 boot autonomo (come distro a tutti gli effetti)
  * 🧷 mount dentro LFS per usare applicazioni e contenuti *da LFS*, senza cambiare OS tramite script che ho sul mio LFS

* 🧷 Variabili:

  * `ambienti_isolati="/EFI/Ambienti_Isolati"`
  * `root_ambienti="/dev/sdc2"`

* 🟨 **Voce:** `MOMENTANEO`

  * 🔁 è una “slot” variabile: cambia distro in base a cosa mi serve studiare in quel periodo.
  * 🧠 avvia kernel+initrd “momentanei” su subvol `@Momentaneo`.

* 🟧 **Voce:** `Gentoo`

  * 🐧 avvia Gentoo come distro completa su subvol `@Gentoo` usando kernel Dreams-LFS (scelta minimalista, funziona perfettamente e mi permette di non creare un ulteriore kernel per gentoo).

* 🟩 **Voce:** `OpenSUSE`

  * 🦎 avvia OpenSUSE con kernel+initrd dedicati, su subvol `@OpenSUSE`, con `rd.timeout=10`.

* 🟦 **Voce:** `Fedora`

  * 🎩 avvia Fedora con kernel+initramfs dedicati, su subvol `@Fedora`.

* 🟪 **Voce:** `NixOS`

  * ❄️ avvio NixOS con `init=` puntato direttamente a uno store path Nix.
  * 🧊 initrd dedicato e root su subvol `@NixOS`.

* 🗃️ **Voci commentate (WIP):** Exherbo, Void, Crux, Alpine, Debian, Artix, Slackware…

  * 🧪 presenti come “spazio di espansione” documentato.

🪟🪟🪟🪟🪟🪟🪟🪟 **WINDOWS 10** 🪟🧷

* 🧩 **Scopo:** avviare Windows tramite EFI chainloading.

* 🧷 Nota pratica: **Windows 10**, perchè lo preferisco rispetto a Windows 11.

* 🟦 **Voce:** `Windows 10 - Home`

  * 🔗 `chainloader /EFI/Microsoft/Boot/bootmgfw.efi` → passa il controllo al bootloader EFI di Microsoft.

💿💿💿💿💿💿💿💿 **ISO ~ LOOPBACK** 💿🌀🧰

* 🧩 **Scopo:** avviare ISO live direttamente da disco, senza USB/DVD, usando loopback + parametri specifici per ogni distro.

* 🧠 Filosofia: una “libreria di sistemi live” sempre pronta, con ricette di boot già configurate (copytoram, persistenza, findiso, label, ecc.).

* 🧷 Variabili:

  * `iso_label="ISO_VM"` → label della partizione che contiene le ISO
  * `iso_dir="/ISO"` → directory dove stanno le ISO

* 🧱 Moduli dedicati: `loopback` e `iso9660`

* 🟥 **Voce:** `System Rescue`

  * 🛟 cerca partizione per label, monta `System_Rescue.iso`, avvia kernel+initrd SysRescue con parametri da rescue (copytoram incluso).

* 🟧 **Voce:** `System Rescue - Persistente`

  * 🧫 aggiunge persistenza: `cow_label=ISO_VM` + `cow_directory=/ISO/Persistenza` → overlay persistente.

* 🟨 **Voce:** `Gentoo`

  * 🐧 live Gentoo con initrd `.igz`, parametri `iso-scan/filename` e riferimenti squashfs, include `insmod gzio`.

* 🟦 **Voce:** `Debian`

  * 🌀 live Debian con `findiso` + `copytoram`, kernel e initrd versionati nel path live.

* 🟩 **Voce:** `Mint`

  * 🌿 avvio casper, parametri classici Mint/Ubuntu (`boot=casper`, username/hostname, iso-scan, quiet splash).

* 🟪 **Voce:** `Ufficio Zero`

  * 🧑‍💼 simile a Mint: casper, copytoram, iso-scan/filename, initrd lz.

* 🦎 **Voce:** `OpenSuse` (Thumbleweed)

  * 🧬 live OpenSUSE con parametri `root=live:CDLABEL=...` + overlay persistente + copytoram.

* 🟥 **Voce:** `Kali`

  * 🐉 live Kali con `findiso` + `copytoram`, `noeject`, quiet splash.

* 🕶️ **Voce:** `Tails`

  * 🧅 live Tails con molti parametri di hardening/sicurezza, `nopersistence`, copytoram, initrd esplicito.

* 🗃️ **Voci commentate (WIP / reference):** NixOS, Parrot, Guix, Nobara, PopOS, Alpine, Void, ecc.

  * 🧪 Queste voci sono IN COSTRUZIONE, le inserirò successivamente, non garantisco che funzionano, io stesso non le ho testate.

⛓️⛓️⛓️⛓️ **OPZIONI EXTRA IN FONDO** 🧰🔧🧨

* 🧩 **Scopo:** strumenti di basso livello e controlli macchina da GRUB.

* 🧱 **Voce separatore:** `------------------------------`

  * 🧊 `true` → voce finta usata solo come separatore visivo nel menu.

* 🟦 **Voce:** `EFI Shell`

  * 🧬 carica moduli fat/chain, forza output console, chainload della shell EFI (`Shell_Full.efi`).

* 🟩 **Voce:** `Memtest86+`

  * 🧠 chainload `memtest.efi64` per test RAM pre-boot.

* 🟨 **Voce:** `Riavvia`

  * 🔁 `reboot` → reboot immediato.

* 🟥 **Voce:** `Spegni`

  * ⛔ `halt` → spegnimento dal loader.

---

Questo `grub.cfg` è un boot menu manuale e “curato a mano” che fa da hub: avvio del mio sistema principale (Dreams_LFS), opzioni avanzate di recovery/test, ambienti isolati (distro complete usabili sia via boot sia montate dentro LFS), Windows 10 via chainloading, una libreria di ISO live avviabili da disco, e strumenti di servizio come EFI Shell e Memtest.
