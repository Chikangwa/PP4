# PP4

## Goal

In this exercise you will:

* Use SSH to connect to remote servers from WSL, macOS, or Linux shells, understanding the handshake and authentication process.
* Generate an Ed25519 SSH key pair and explain the concept of digital signatures.
* Configure your local SSH client via the `~/.ssh/config` file for streamlined access.
* Securely copy files between local and remote hosts using `scp`, including local-to-remote, remote-to-local, and remote-to-remote transfers.
* Automate startup tasks on the remote server by writing a shell script that runs at login and explaining the role of `~/.bashrc` vs. `~/.profile`.

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. Once time is up, stop immediately and record exactly where you paused.

---

## Workflow

1. **Fork** this repository
2. **Modify & commit** your solution
3. **Submit your link for Review**

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)
* Consult the SSH and SCP man-pages for detailed options and explanations:

  * `man ssh`
  * `man scp`

---

## Tasks

### Task 1: SSH Login

**Objective:** Establish an SSH connection and observe each stage of the process.

1. From your local shell (WSL, macOS Terminal, or Linux), log into the `vorlesungsserver` (or any other remote machine of your choice, e.g. your own raspberry pi):

   ```bash
   ssh youruser@remotehost
   ```
2. Carefully observe and note each step:

   * **TCP connection** to port 22 on `remotehost`.
   * **SSH protocol handshake**: key exchange and algorithm negotiation.
   * **Authentication**: public-key or password exchange.
   * **Shell allocation**: your remote session starts.
3. After login, exit the session with `exit`.

**Provide:**

```bash
# 1) The exact ssh command you ran
ssh NguekepMonique@127.0.0.1
# 2) A detailed, step-by-step explanation of what happened at each stage
TCP-Verbindung zu Port 22 auf dem Remote-Host:
 Eine Verbindung wurde zum Port 22 auf dem Remote-Rechner erstellt.

SSH-Protokoll-Handshake: Schlüsselaustausch und Algorithmusverhandlung
Es wurde ein sicherer Schlüsselaustausch stattfinden (z. B. mit Diffie-Hellman oder Ed25519).

Authentifizierung: Public-Key oder Passwort

Falls ein öffentlicher Schlüssel hinterlegt ist, wird dieser zur Authentifizierung genutzt.

Falls nicht, wirst du nach dem Passwort gefragt.

Shell-Zuweisung: Deine entfernte Sitzung beginnt

Der Server stellt eine Shell zur Verfügung (z. B. Bash).

ich konnte Befehle wie auf meinem lokalen Rechner ausführen.
```

---

### Task 2: Ed25519 Key Pair

**Objective:** Create a secure key pair and explain how digital signatures verify identity.

1. Generate an Ed25519 SSH key pair:

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   * Accept the default file location (`~/.ssh/id_ed25519`). Or provide the `-f <filepath>` option additionally.
   * Enter a passphrase when prompted (optional).
2. Locate and inspect your `id_ed25519` (private key) and `id_ed25519.pub` (public key).
3. Install your key on the remote machine (e.g. `vorlesungsserver`.
4. Explain in writing:

   * How the **private key** is used to sign challenges.
   * How the **public key** on the server verifies signatures without revealing the private key.
   * Why Ed25519 is preferred (performance, security).

**Provide:**

```bash
# 1) The ssh-keygen command you ran
ssh-keygen -t ed25519 -C "nguekepmonique@gmail.com"
# 2) The file paths of the generated keys
Your identification has been saved in /root/NguekepMonique/.ssh/id_ed25519
Your public key has been saved in /root/NguekepMonique/.ssh/id_ed25519.pub
# 3) Your written explanation (3–5 sentences) of the signature process
Wie wird der Private Key verwendet?
 eine Challenge wurde vom Server mit deinem privaten Schlüssel signiert.

 Wie prüft der Server mit dem Public Key die Signatur?
Der Server kennt den öffentlichen Schlüssel (aus ~/.ssh/authorized_keys).

Er prüft, ob die vom Client gesendete Signatur mit dem Public Key gültig ist.

 Warum ist Ed25519 bevorzugt?
Schneller als RSA (weniger Rechenaufwand).

Kompaktere Schlüssel (sicherer bei kürzerer Länge).

Sehr sicher: Ed25519 ist gegen viele moderne Angriffe resistent.

Stand der Technik – empfohlen von OpenSSH.
```

---

### Task 3: SSH Config File

**Objective:** Simplify SSH commands via `~/.ssh/config`.

1. Open (or create) `~/.ssh/config` in `vim`.
2. Add entries for your hosts, for example:

   ```text
   Host my-remote
       HostName remote.example.com
       User youruser
       IdentityFile ~/.ssh/id_ed25519

   Host backup-server
       HostName backup.example.com
       User backupuser
       Port 2222
       IdentityFile ~/.ssh/id_ed25519_backup
   ```
3. Save and close the file, then test:

   ```bash
   ssh my-remote
   ssh backup-server
   ```
4. Explain:

   * How SSH reads `~/.ssh/config` and matches hosts.
   * The difference between `HostName` and `Host`.
   * How aliases prevent long commands.

**Provide:**

```text
# 1) The full contents of your ~/.ssh/config

# ~/.ssh/config
Host my-remote
    HostName 127.0.0.1
    User NguekepMonique
    IdentityFile ~/.ssh/id_ed25519
Host backup-server
    HostName 127.0.0.1
    User backupuser
    Port 2222
    IdentityFile ~/.ssh/id_ed25519_backup

# 2) A short explanation (3–4 sentences) of how the config simplifies connections
```
Die Datei ~/.ssh/config wird von SSH automatisch gelesen, um benutzerdefinierte Verbindungsprofile zu laden. 
Der Alias (Host) ersetzt einen langen Befehl mit HostName, Benutzername, Port und Schlüsselpfad.
So lassen sich Verbindungen schneller und einfacher herstellen. 

---

### Task 4: SCP File Transfers

**Objective:** Practice copying files securely using `scp`.

1. **Local → Remote**:

   ```bash
   scp /path/to/localfile.txt youruser@remotehost:~/destination/
   ```
2. **Remote → Local**:

   ```bash
   scp youruser@remotehost:~/remotefile.log ./local_destination/
   ```
3. **Remote → Remote** (between two directories on the same remote host):

   ```bash
   scp -r youruser@remotehost:/path/dir1 youruser@remotehost:/path/dir2
   ```
4. For each command:

   * Verify file timestamps and sizes after transfer, using `ls -la`
   * Note any flags you used (e.g., `-r`, `-P` for port).
5. Explain:

   * How `scp` initiates an SSH session for each transfer.
   * The role of encryption in protecting data in transit.

**Provide:**

```bash
# 1) Each scp command you ran
# 2) Any flags or options used
# 3) A brief explanation (2–3 sentences) of scp’s mechanism
```
1) Local> remote
   scp ~/Dokumente/testdatei.txt NguekepMonique@127.0.0.1:~/zielordner/
 remote> local
   scp NguekepMonique@127.0.0.1:~/logs/system.log ~/Downloads/
remote> remote
   scp -r NguekepMonique@127.0.0.1:/root/NguekepMonique/ordner1 NguekepMonique@127.0.0.1:/root/NguekepMonique/ordner2

2) -r
3)scp benutzt SSH, um sicher eine Verbindung zum entfernten Rechner aufzubauen.

Dabei werden die Daten verschlüsselt übertragen, so dass niemand sie unterwegs sehen oder verändern kann.

Für jede Dateiübertragung öffnet scp eine neue SSH-Verbindung, kopiert die Dateien und schließt die Verbindung danach wieder.



---

### Task 5: Login Shell Script & Profile Explanation

**Objective:** Automate commands at login and understand shell initialization files.

1. On the **remote** server, create a script `~/login_tasks.sh` containing at least three commands you find useful (e.g., `echo "Welcome $(whoami)"`, `uptime`, `ls ~/projects`). You may either use `vim` or try the following to create a file from your commandline directely:

   ```bash
   cat << 'EOF' > ~/login_tasks.sh
   #!/usr/bin/env bash
   echo "Welcome $(whoami)! Today is $(date)."
   uptime
   ls ~/projects
   EOF
   chmod +x ~/login_tasks.sh
   ```

> The files content should be something akin to:
> ```bash
> #!/usr/bin/env bash
> echo "Welcome $(whoami)! Today is $(date)."
> uptime
> ls ~/projects
> ```

2. Append to your `~/.bashrc` (or `~/.profile` if using a login shell) a line to source this script on each new session:

   ```bash
   echo "source ~/login_tasks.sh" >> ~/.bashrc
   ```
3. Log out and log back in to trigger the script.
4. Explain:

   * The difference between `~/.bashrc` and `~/.profile` (interactive vs. login shells).
   * Why and when each file is read.
   * How sourcing differs from executing.

**Provide:**

```bash
# 1) The contents of login_tasks.sh
# 2) The lines you added to ~/.bashrc or ~/.profile
# 3) Your explanation (3–5 sentences) of shell init files and sourcing vs. executing
```

---

**Remember:** Stop working after **90 minutes** and record where you stopped.
ich habe task 5 nicht gearbeitet, weil ich viel zeit genommen habe, den Remote server zu erstellen.
