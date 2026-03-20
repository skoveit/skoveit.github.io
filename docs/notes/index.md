---
hide:
  - footer
---

<style>
@import url('https://fonts.googleapis.com/css2?family=Cormorant:ital,wght@0,300;0,400;1,300;1,400&family=Geist+Mono:wght@300;400;500&display=swap');

:root {
  --ink:   #1e2129;
  --line:  rgba(255,255,255,0.1);
  --gold:  #5e8bde;
  --cream: #ede8df;
  --mid:   #a09a93;
  --dim:   #6b6560;
}

.n-wrap {
  font-family: 'Geist Mono', monospace;
  max-width: 860px;
  margin: 2.5rem auto 5rem;
  padding: 0 1.5rem;
  color: var(--cream);
}

/* ── Filter pills ── */
.n-filters {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
  margin-bottom: 2rem;
}
.n-pill {
  font-size: 0.6rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  padding: 0.3rem 0.9rem;
  border: 1px solid var(--dim);
  background: transparent;
  color: var(--mid);
  cursor: pointer;
  transition: all 0.2s ease;
  font-family: 'Geist Mono', monospace;
}
.n-pill:hover,
.n-pill.active {
  border-color: var(--gold);
  color: var(--gold);
  background: rgba(201,168,76,0.06);
}

/* ── Cards ── */
.n-list {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.n-card {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 1.4rem;
  padding: 1.1rem 0.8rem;
  border-bottom: 1px solid var(--line);
  text-decoration: none !important;
  color: inherit;
  position: relative;
  transition: all 0.2s ease;
  cursor: pointer;
  overflow: hidden;
}
.n-card::before {
  content: '';
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 2px;
  background: var(--gold);
  transform: scaleY(0);
  transition: transform 0.2s ease;
  transform-origin: bottom;
}
.n-card:hover { background: rgba(201,168,76,0.03); padding-left: 1.4rem; }
.n-card:hover::before { transform: scaleY(1); }
.n-card:hover .n-arrow { opacity: 1; transform: translateX(0); }
.n-card:hover .n-card-name { color: var(--gold); }

.n-num {
  font-size: 0.6rem;
  color: var(--mid);
  width: 1.8rem;
  letter-spacing: 0.04em;
  flex-shrink: 0;
  user-select: none;
}

.n-tag {
  font-size: 0.52rem;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--gold);
  margin-bottom: 0.25rem;
}
.n-card-name {
  font-size: 0.9rem;
  font-weight: 500;
  color: var(--cream);
  transition: color 0.2s;
  line-height: 1.3;
}
.n-card-desc {
  font-size: 0.62rem;
  color: var(--mid);
  margin-top: 0.2rem;
  font-weight: 300;
  line-height: 1.5;
}

.n-arrow {
  font-size: 0.8rem;
  color: var(--gold);
  opacity: 0;
  transform: translateX(-6px);
  transition: all 0.2s ease;
  flex-shrink: 0;
}

.n-card.hidden { display: none; }

.n-empty {
  text-align: center;
  padding: 3rem 0;
  font-size: 0.7rem;
  color: var(--dim);
  letter-spacing: 0.08em;
  display: none;
}
.n-empty.visible { display: block; }

@media (max-width: 600px) {
  .n-card { grid-template-columns: auto 1fr auto; gap: 0.8rem; }
}
</style>

<div class="n-wrap">

  <div class="n-filters">
    <button class="n-pill active" data-filter="all">All</button>
    <button class="n-pill" data-filter="winint">Windows Internals</button>
    <button class="n-pill" data-filter="ad">Active Directory</button>
    <button class="n-pill" data-filter="red">Red Team</button>
    <button class="n-pill" data-filter="web">Web Security</button>
  </div>

  <div class="n-list" id="n-list">

    <!-- Windows Internals -->
    <a href="windows%20internals/DLL/" class="n-card" data-cat="winint">
      <span class="n-num">01</span>
      <div>
        <div class="n-tag">Windows Internals</div>
        <div class="n-card-name">DLL Internals</div>
        <div class="n-card-desc">DLL loading, hijacking, proxying, and injection techniques.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="windows%20internals/PE%20structure/" class="n-card" data-cat="winint">
      <span class="n-num">02</span>
      <div>
        <div class="n-tag">Windows Internals</div>
        <div class="n-card-name">PE Structure</div>
        <div class="n-card-desc">Portable Executable format: headers, sections, imports, and exports.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="windows%20internals/process/" class="n-card" data-cat="winint">
      <span class="n-num">03</span>
      <div>
        <div class="n-tag">Windows Internals</div>
        <div class="n-card-name">Processes &amp; Threads</div>
        <div class="n-card-desc">Process creation, hollowing, injection, and thread manipulation.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="windows%20internals/security/" class="n-card" data-cat="winint">
      <span class="n-num">04</span>
      <div>
        <div class="n-tag">Windows Internals</div>
        <div class="n-card-name">Security Mechanisms</div>
        <div class="n-card-desc">ASLR, DEP, CFG, PPL, and Windows security subsystem internals.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="windows%20internals/memory/memory%20mapping/" class="n-card" data-cat="winint">
      <span class="n-num">05</span>
      <div>
        <div class="n-tag">Windows Internals</div>
        <div class="n-card-name">Memory Mapping</div>
        <div class="n-card-desc">Virtual memory, section objects, VAD trees, and mapped files.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <!-- Active Directory -->
    <a href="active%20directory/Kerbros/" class="n-card" data-cat="ad">
      <span class="n-num">06</span>
      <div>
        <div class="n-tag">Active Directory</div>
        <div class="n-card-name">Kerberos</div>
        <div class="n-card-desc">Kerberoasting, AS-REP, delegation attacks, and ticket abuse.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="active%20directory/LAPS/" class="n-card" data-cat="ad">
      <span class="n-num">07</span>
      <div>
        <div class="n-tag">Active Directory</div>
        <div class="n-card-name">LAPS</div>
        <div class="n-card-desc">Local Administrator Password Solution — enumeration and bypass.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="active%20directory/Certificate%20Services/" class="n-card" data-cat="ad">
      <span class="n-num">08</span>
      <div>
        <div class="n-tag">Active Directory</div>
        <div class="n-card-name">Certificate Services (AD CS)</div>
        <div class="n-card-desc">ESC1–ESC8 abuse, template misconfigurations, and certificate theft.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <!-- Red Team -->
    <a href="red/windows/70%25/Enum/" class="n-card" data-cat="red">
      <span class="n-num">09</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">Post-Exploitation Enumeration</div>
        <div class="n-card-desc">Host and domain enumeration after initial foothold.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="red/windows/70%25/PrivEsc/" class="n-card" data-cat="red">
      <span class="n-num">10</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">Privilege Escalation</div>
        <div class="n-card-desc">Local and domain privilege escalation techniques on Windows.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="red/windows/70%25/lateral%20Movement/lateral%20Movement/" class="n-card" data-cat="red">
      <span class="n-num">11</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">Lateral Movement</div>
        <div class="n-card-desc">Pass-the-Hash, Pass-the-Ticket, WMI, DCOM, and SMB relay.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="red/windows/70%25/persistence/persistence/" class="n-card" data-cat="red">
      <span class="n-num">12</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">Persistence</div>
        <div class="n-card-desc">Registry, scheduled tasks, services, and AD persistence mechanisms.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="red/windows/70%25/OPSEC/OPSEC/" class="n-card" data-cat="red">
      <span class="n-num">13</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">OPSEC</div>
        <div class="n-card-desc">Avoiding detection: log evasion, AMSI bypass, and EDR blinding.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="red/proxying/" class="n-card" data-cat="red">
      <span class="n-num">14</span>
      <div>
        <div class="n-tag">Red Team</div>
        <div class="n-card-name">Proxying &amp; Traffic Channeling</div>
        <div class="n-card-desc">SOCKS proxies, port forwarding, and traffic redirection techniques.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <!-- Web Security -->
    <a href="web/CSRF/" class="n-card" data-cat="web">
      <span class="n-num">15</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">CSRF</div>
        <div class="n-card-desc">Cross-site request forgery — patterns, bypasses, and SameSite evasion.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/GraphQL/" class="n-card" data-cat="web">
      <span class="n-num">16</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">GraphQL</div>
        <div class="n-card-desc">Introspection, batching attacks, IDOR, and auth bypass in GraphQL APIs.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/HTTP%20request%20smuggling/" class="n-card" data-cat="web">
      <span class="n-num">17</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">HTTP Request Smuggling</div>
        <div class="n-card-desc">CL.TE and TE.CL desync, server-side request poisoning.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/JWT/" class="n-card" data-cat="web">
      <span class="n-num">18</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">JWT Attacks</div>
        <div class="n-card-desc">Algorithm confusion, none algorithm, kid injection, and secret cracking.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/Logic-flaws/" class="n-card" data-cat="web">
      <span class="n-num">19</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">Logic Flaws</div>
        <div class="n-card-desc">Business logic vulnerabilities, price manipulation, and workflow bypass.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/Race%20Condition/" class="n-card" data-cat="web">
      <span class="n-num">20</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">Race Conditions</div>
        <div class="n-card-desc">Single-endpoint and multi-endpoint races, limit overrun, and TOCTOU.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

    <a href="web/WebSocket/" class="n-card" data-cat="web">
      <span class="n-num">21</span>
      <div>
        <div class="n-tag">Web Security</div>
        <div class="n-card-name">WebSocket Attacks</div>
        <div class="n-card-desc">CSWSH, message hijacking, and WebSocket-based SSRF chains.</div>
      </div>
      <span class="n-arrow">→</span>
    </a>

  </div>

  <div class="n-empty" id="n-empty">no results — try a different filter</div>

</div>

<script>
(function () {
  const pills = document.querySelectorAll('.n-pill');
  const cards = document.querySelectorAll('.n-card');
  const empty = document.getElementById('n-empty');

  pills.forEach(pill => {
    pill.addEventListener('click', () => {
      pills.forEach(p => p.classList.remove('active'));
      pill.classList.add('active');

      const filter = pill.dataset.filter;
      let visible = 0;
      let idx = 1;

      cards.forEach(card => {
        const match = filter === 'all' || card.dataset.cat === filter;
        if (match) {
          card.classList.remove('hidden');
          card.querySelector('.n-num').textContent = String(idx++).padStart(2, '0');
          visible++;
        } else {
          card.classList.add('hidden');
        }
      });

      empty.classList.toggle('visible', visible === 0);
    });
  });
})();
</script>