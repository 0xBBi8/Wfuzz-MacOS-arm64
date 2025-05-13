
# ⚡️ Wfuzz + PycURL + OpenSSL on macOS (ARM64)

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
- macOS (ARM64)
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


🧠 Summary
	•	✅ Python isolated via pyenv
	•	✅ pip downgraded to avoid metadata errors
	•	✅ PycURL compiled against OpenSSL
	•	✅ Wfuzz runs without SSL warnings
	•	✅ Works on macOS ARM64 or Intel

⸻


📚 References
	•	https://github.com/EdgeSecurityTeam/wfuzz
	•	https://github.com/pycurl/pycurl
	•	https://brew.sh
	•	https://github.com/pyenv/pyenv

⸻

