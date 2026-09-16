# Ollama Installation Instructions

Complete these steps **before the workshop**. The model download is ~3GB so do this on a good internet connection.

---

## macOS

1. Download the installer from [ollama.com/download](https://ollama.com/download) and select **macOS**
2. Open the downloaded `.zip`, drag **Ollama** into your Applications folder
3. Launch Ollama — you'll see a llama icon in your menu bar
4. Open **Terminal** and pull the model:
   ```bash
   ollama pull llama3.2:3b
   ```
5. Verify it works:
   ```bash
   ollama run llama3.2:3b "say hello"
   ```

---

## Windows

1. Download the installer from [ollama.com/download](https://ollama.com/download) and select **Windows**
2. Run `OllamaSetup.exe` and follow the prompts
3. Ollama will start automatically and appear in your system tray
4. Open **Command Prompt** or **PowerShell** and pull the model:
   ```powershell
   ollama pull llama3.2:3b
   ```
5. Verify it works:
   ```powershell
   ollama run llama3.2:3b "say hello"
   ```

---

## Linux

1. Run the official install script:
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ```
2. The Ollama service starts automatically. Pull the model:
   ```bash
   ollama pull llama3.2:3b
   ```
3. Verify it works:
   ```bash
   ollama run llama3.2:3b "say hello"
   ```

---

## Verify Ollama is Running

On any OS, open a browser and go to:

```
http://localhost:11434
```

You should see: `Ollama is running`

---

## Troubleshooting

**Ollama not in PATH (macOS/Linux)**
```bash
export PATH=$HOME/.ollama/bin:$PATH
```

**Port 11434 not reachable (Windows)**
Check that Ollama is running in the system tray. If not, search for Ollama in the Start menu and launch it.

**Model pull is slow or interrupted**
The pull can be resumed — just re-run `ollama pull llama3.2:3b` and it will continue from where it left off.

**Not enough disk space**
`llama3.2:3b` requires ~3GB of free disk space. Clear space if needed before pulling.
