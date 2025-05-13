Got it — here’s your complete and polished README.md file, ready to copy-paste into GitHub:

⸻


# ⚡️ Wfuzz + PycURL + OpenSSL on macOS (ARM64/Intel)

This project documents how to get **Wfuzz 3.1.0** working with **PycURL compiled against OpenSSL** on macOS — avoiding issues caused by the default **SecureTransport/LibreSSL** backend.

> ✅ Verified working on macOS + pyenv + Homebrew + Python 3.10.14

---

## 💬 Why?

By default, macOS links `pycurl` against **SecureTransport** or **LibreSSL**, which causes:
- SSL functionality to break in Wfuzz
- Warnings like `Pycurl is not compiled against OpenSSL`
- Inability to properly fuzz HTTPS targets

This guide fixes that by ensuring everything compiles **against OpenSSL** using `pyenv` and Homebrew.

---

## 📦 Requirements

- [Homebrew](https://brew.sh)
- [`pyenv`](https://github.com/pyenv/pyenv)
- Python 3.10.x (via `pyenv`)
- macOS (ARM64 or Intel)
- `pip <= 23.3.2`

---

## 🛠 Step-by-Step Setup

### 1. Install Python via pyenv

```bash
brew install pyenv
pyenv install 3.10.14
pyenv global 3.10.14


⸻

2. Install OpenSSL via Homebrew

brew install openssl@3
brew link openssl@3 --force


⸻

3. Set Up Environment Variables

Set the following in your terminal or add them to your ~/.zshrc:

export PYCURL_SSL_LIBRARY=openssl
export LDFLAGS="-L$(brew --prefix openssl)/lib"
export CPPFLAGS="-I$(brew --prefix openssl)/include"
export CURL_CONFIG="$(brew --prefix curl)/bin/curl-config"

Example .zshrc section:

# OpenSSL for PycURL + Wfuzz
export PYCURL_SSL_LIBRARY=openssl
export LDFLAGS="-L$(brew --prefix openssl)/lib"
export CPPFLAGS="-I$(brew --prefix openssl)/include"
export CURL_CONFIG="$(brew --prefix curl)/bin/curl-config"

Then apply it:

source ~/.zshrc


⸻

4. Downgrade pip (metadata parsing is broken in newer versions)

pip install pip==23.3.2


⸻

5. Install PycURL (compiled from source)

pip install --no-cache-dir --compile --no-binary :all: pycurl==7.43.0.6


⸻

6. Install Wfuzz 3.1.0

pip install wfuzz==3.1.0


⸻

7. Verify PycURL Is Using OpenSSL

python3 -c "import pycurl; print(pycurl.version)"

You should see something like this:

PycURL/7.43.0.6 libcurl/8.12.1 OpenSSL/3.x.x ...

If it says SecureTransport or LibreSSL, something is wrong.

⸻

✅ Run Wfuzz

wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt --hc 404,403 "$URL"

Example:

URL="https://example.com"
wfuzz -c -z file,~/SecLists/Discovery/Web-Content/common.txt --hc 404 "$URL"


⸻

💡 Tips
	•	You can use ~ or $HOME in wordlist paths — both work in Wfuzz.
	•	If you’re using zsh, don’t forget to source ~/.zshrc after changes.

⸻

🧠 Summary
	•	✅ Python isolated via pyenv
	•	✅ pip downgraded to avoid metadata errors
	•	✅ PycURL compiled against OpenSSL
	•	✅ Wfuzz runs without SSL warnings
	•	✅ Works on macOS ARM64 or Intel

⸻

📸 Screenshot

Target: https://cophe.se/FUZZ
Total requests: 29999

ID           Response   Word   Chars   Payload
000000001:   403        20 W   199 Ch  "cgi-bin"
000000015:   301        20 W   229 Ch  "css"
000000009:   301        20 W   228 Ch  "js"
...


⸻

📚 References
	•	https://github.com/EdgeSecurityTeam/wfuzz
	•	https://github.com/pycurl/pycurl
	•	https://brew.sh
	•	https://github.com/pyenv/pyenv

⸻

Made with 🧠 by [your-name-or-handle]

--- 

Let me know if you want it saved to a file (`README.md`) or styled for dark mode preview on GitHub.
