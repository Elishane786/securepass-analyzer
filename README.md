# SecurePass Analyzer

A password tool that checks how strong a password really is, tells you whether it has already turned up in a known data breach, and generates strong passwords and passphrases for you. Built with Python and Streamlit.

## The goal

Most password checkers only look at a password on its own and count its length or whether it has a symbol in it. That misses the real problem. A password can look strong and still be useless if it has already leaked in a breach somewhere, because attackers work from lists of exactly those leaked passwords. SecurePass Analyzer was built to close that gap, by scoring a password on its actual strength, checking it against real breached data, and helping you create a better one, so you find out not just whether your password looks good but whether it is already known to the people you are trying to keep out.

## What it does

- **Breach detection.** It checks a password against the Have I Been Pwned database, so a password that has appeared in a real leak is flagged even when it looks strong on paper.
- **Real strength analysis.** It uses the zxcvbn library to spot the patterns attackers actually exploit, things like common words, keyboard runs, dates, and simple substitutions, instead of just counting characters. It reports the entropy in bits so you can see how hard the password would be to crack.
- **Policy scoring.** It scores a password against a set of strength rules and tells you where it falls short, so the feedback is specific instead of a bare pass or fail.
- **Password and passphrase generation.** It can create strong random passwords and diceware-style passphrases using Python's `secrets` module, which is the right source of randomness for security work.
- **A usable interface.** It runs as a Streamlit web app with separate screens for analysing, generating, and auditing, so you work in a browser rather than from a terminal.

## How the breach check works

The Have I Been Pwned lookup is done in a privacy-preserving way using the k-anonymity range API. The password is hashed with SHA-1, and only the first five characters of that hash are sent to the API. The API returns every breached hash that starts with those five characters, and the tool does the final match locally. The request also asks the API to pad its response, so even the size of the reply gives nothing away. The full password, and even its full hash, never leaves your machine, so the tool can check against breach data without the password itself being exposed.

## How it was built

- **Language:** Python 3
- **Framework:** Streamlit, for the web interface
- **External service:** Have I Been Pwned (Pwned Passwords range API)
- **Key libraries:** `streamlit` for the interface, `requests` for the API call, `zxcvbn` for pattern-based strength analysis, and the standard-library `hashlib` and `secrets` modules

The logic is kept separate from the interface. The strength analysis, the breach lookup, and the generators all live in `engine.py`, the Streamlit screens live in `ui.py` and the `views` folder, and `test_engine.py` holds the tests for the engine.

## Running it locally

You will need Python 3 installed.

1. Clone the repository:
   ```bash
   git clone https://github.com/Elishane786/securepass-analyzer.git
   cd securepass-analyzer
   ```

2. (Recommended) Create and activate a virtual environment:
   ```bash
   python -m venv .venv
   source .venv/Scripts/activate      # on macOS or Linux: source .venv/bin/activate
   ```

3. Install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Run the app:
   ```bash
   streamlit run app.py
   ```

5. Streamlit opens the tool in your browser. Type a password into the Analyse screen to see its strength, entropy, and whether it has been found in a breach, or use the Generate screen to create a strong one.

## Running the tests

```bash
pip install pytest
pytest
```

## Notes

- No password you enter is stored, and the breach check only ever sends a partial hash, never the password.
- This is a learning and auditing tool. Do not enter a password you currently use in production into any third-party tool, including this one.
