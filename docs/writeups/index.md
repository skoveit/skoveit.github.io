---
hide:
  - footer
  - navigation.tabs
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

.wr-wrap {
  font-family: 'Geist Mono', monospace;
  max-width: 860px;
  margin: 2.5rem auto 5rem;
  padding: 0 1.5rem;
  color: var(--cream);
}

/* ── Header ── */
.wr-head {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  margin-bottom: 3rem;
  padding-bottom: 1.2rem;
  border-bottom: 1px solid var(--line);
}
.wr-head-left {}
.wr-eyebrow {
  font-size: 0.6rem;
  letter-spacing: 0.22em;
  text-transform: uppercase;
  color: var(--gold);
  margin-bottom: 0.4rem;
}
.wr-title {
  font-family: 'Cormorant', serif;
  font-size: 3rem;
  font-weight: 300;
  font-style: italic;
  line-height: 1;
  color: var(--cream);
  margin: 0;
}
.wr-count {
  font-size: 0.62rem;
  color: var(--mid);
  letter-spacing: 0.08em;
  text-align: right;
}
.wr-count span {
  color: var(--cream);
  font-size: 1.4rem;
  font-family: 'Cormorant', serif;
  font-weight: 300;
  display: block;
  line-height: 1;
}

/* ── Filter pills ── */
.wr-filters {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
  margin-bottom: 2rem;
}
.wr-pill {
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
.wr-pill:hover,
.wr-pill.active {
  border-color: var(--gold);
  color: var(--gold);
  background: rgba(201,168,76,0.06);
}

/* ── Cards ── */
.wr-list {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.wr-card {
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
.wr-card::before {
  content: '';
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 2px;
  background: var(--gold);
  transform: scaleY(0);
  transition: transform 0.2s ease;
  transform-origin: bottom;
}
.wr-card:hover { background: rgba(201,168,76,0.03); padding-left: 1.4rem; }
.wr-card:hover::before { transform: scaleY(1); }
.wr-card:hover .wr-arrow { opacity: 1; transform: translateX(0); }
.wr-card:hover .wr-card-name { color: var(--cream); }

.wr-num {
  font-size: 0.6rem;
  color: var(--mid);
  width: 1.8rem;
  letter-spacing: 0.04em;
  flex-shrink: 0;
  user-select: none;
}

.wr-card-body {}
.wr-tag {
  font-size: 0.52rem;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--gold);
  margin-bottom: 0.25rem;
}
.wr-card-name {
  font-size: 0.9rem;
  font-weight: 500;
  color: var(--cream);
  transition: color 0.2s;
  line-height: 1.3;
}
.wr-card-desc {
  font-size: 0.62rem;
  color: var(--mid);
  margin-top: 0.2rem;
  font-weight: 300;
  line-height: 1.5;
}

.wr-arrow {
  font-size: 0.8rem;
  color: var(--gold);
  opacity: 0;
  transform: translateX(-6px);
  transition: all 0.2s ease;
  flex-shrink: 0;
}

/* Visibility toggle */
.wr-card[hidden], .wr-card.hidden {
  display: none;
}

/* ── Empty state ── */
.wr-empty {
  text-align: center;
  padding: 3rem 0;
  font-size: 0.7rem;
  color: var(--dim);
  letter-spacing: 0.08em;
  display: none;
}
.wr-empty.visible { display: block; }

/* ── Mobile ── */
@media (max-width: 600px) {
  .wr-head { flex-direction: column; align-items: flex-start; gap: 0.8rem; }
  .wr-title { font-size: 2.2rem; }
  .wr-card { grid-template-columns: auto 1fr auto; gap: 0.8rem; }
}
</style>

<div class="wr-wrap">

  <div class="wr-filters">
    <button class="wr-pill active" data-filter="all">All</button>
    <button class="wr-pill" data-filter="c2">C2</button>
    <button class="wr-pill" data-filter="web">Web</button>
    <button class="wr-pill" data-filter="maldev">Maldev</button>
  </div>

  <div class="wr-list" id="wr-list">

    <a href="C2/Sliver-UnoReverse/" class="wr-card" data-cat="c2">
      <span class="wr-num">01</span>
      <div class="wr-card-body">
        <div class="wr-tag">Command &amp; Control</div>
        <div class="wr-card-name">Sliver — Uno Reverse</div>
        <div class="wr-card-desc">Reversing Sliver C2 traffic to identify detection opportunities and undermine C2 infrastructure.</div>
      </div>
      <span class="wr-arrow">→</span>
    </a>


    <a href="web/FTPbox/" class="wr-card" data-cat="web">
      <span class="wr-num">03</span>
      <div class="wr-card-body">
        <div class="wr-tag">Web Security</div>
        <div class="wr-card-name">FTPbox Hardcoded Credentials</div>
        <div class="wr-card-desc">Discovering hardcoded credentials in the FTPbox open-source client leading to full account compromise.</div>
      </div>
      <span class="wr-arrow">→</span>
    </a>

    <a href="web/PII-discl/" class="wr-card" data-cat="web">
      <span class="wr-num">02</span>
      <div class="wr-card-body">
        <div class="wr-tag">Web Security</div>
        <div class="wr-card-name">JavaScript Recon to PII Exposure</div>
        <div class="wr-card-desc">Chaining recon via exposed JS endpoints to extract PII from a live target.</div>
      </div>
      <span class="wr-arrow">→</span>
    </a>
    
    <a href="maldev/payload-Hiding/" class="wr-card" data-cat="maldev">
      <span class="wr-num">04</span>
      <div class="wr-card-body">
        <div class="wr-tag">Malware Development</div>
        <div class="wr-card-name">Payload Hiding Techniques</div>
        <div class="wr-card-desc">Surveying modern techniques for concealing payloads from AV/EDR at rest and in memory.</div>
      </div>
      <span class="wr-arrow">→</span>
    </a>

  </div>

  <div class="wr-empty" id="wr-empty">no results — try a different filter</div>

</div>

<script>
(function () {
  const pills   = document.querySelectorAll('.wr-pill');
  const cards   = document.querySelectorAll('.wr-card');
  const empty   = document.getElementById('wr-empty');
  const counter = document.getElementById('wr-total');

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
          card.querySelector('.wr-num').textContent = String(idx++).padStart(2, '0');
          visible++;
        } else {
          card.classList.add('hidden');
        }
      });

      counter.textContent = visible;
      empty.classList.toggle('visible', visible === 0);
    });
  });
})();
</script>
