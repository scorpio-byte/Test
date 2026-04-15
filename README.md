<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>GenAI Weekly News Tracker</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    :root {
      --bg:   #030712;
      --card: #0d1424;
      --bd:   #1f2937;
      --bd2:  #374151;
      --t1:   #f3f4f6;
      --t2:   #9ca3af;
      --t3:   #4b5563;
      --t4:   #6b7280;
    }
    html { color-scheme: dark; scroll-behavior: smooth; }
    body { background: var(--bg); color: var(--t1); font-family: 'Inter', ui-sans-serif, sans-serif; -webkit-font-smoothing: antialiased; }

    ::-webkit-scrollbar { width: 5px; }
    ::-webkit-scrollbar-thumb { background: var(--bd2); border-radius: 3px; }

    @keyframes ping    { 75%,100%{ transform:scale(2); opacity:0 } }
    @keyframes spin    { to{ transform:rotate(360deg) } }
    @keyframes shimmer { 0%{ background-position:-200% 0 } 100%{ background-position:200% 0 } }
    @keyframes fadeUp  { from{ opacity:0; transform:translateY(10px) } to{ opacity:1; transform:none } }

    .live-ping   { animation: ping 1.8s cubic-bezier(0,0,.2,1) infinite; }
    .is-spinning { animation: spin .9s linear infinite; }
    .fade-up     { animation: fadeUp .22s ease both; }
    .skeleton    { background: linear-gradient(90deg,#1a2535 25%,#243044 50%,#1a2535 75%); background-size:200% 100%; animation:shimmer 1.6s infinite; }

    /* ── Header ── */
    header {
      position: sticky; top: 0; z-index: 50;
      background: rgba(3,7,18,.93); backdrop-filter: blur(16px);
      border-bottom: 1px solid var(--bd);
    }
    .hdr {
      max-width: 1600px; margin: 0 auto; padding: 10px 24px;
      display: flex; align-items: center; gap: 14px; flex-wrap: wrap;
    }
    .hdr-brand { display:flex; align-items:center; gap:9px; }
    .dot-wrap  { position:relative; width:10px; height:10px; }
    .dot-ring  { position:absolute; inset:0; border-radius:50%; background:#4ade80; opacity:.7; }
    .dot-core  { position:relative; width:10px; height:10px; border-radius:50%; background:#22c55e; }
    .live-lbl  { font-size:10px; font-weight:700; color:#4ade80; text-transform:uppercase; letter-spacing:.1em; }
    h1         { font-size:16px; font-weight:700; color:#fff; white-space:nowrap; }
    .week-badge{
      font-size:11px; color:var(--t3); white-space:nowrap;
      background:var(--bd); border:1px solid var(--bd2); border-radius:6px; padding:3px 8px;
    }
    .hdr-right { display:flex; align-items:center; gap:10px; margin-left:auto; flex-wrap:wrap; }
    #update-meta { font-size:11px; color:var(--t3); white-space:nowrap; }
    .btn {
      display:flex; align-items:center; gap:5px;
      background:var(--bd); border:1px solid var(--bd2); border-radius:8px;
      padding:6px 11px; font-size:12px; color:var(--t2); cursor:pointer;
      transition:background .15s, color .15s; font-family:inherit; white-space:nowrap;
    }
    .btn:hover:not(:disabled) { background:var(--bd2); color:var(--t1); }
    .btn:disabled { opacity:.4; cursor:not-allowed; }
    .btn svg { width:13px; height:13px; }

    /* ── Layout ── */
    main { max-width:1600px; margin:0 auto; padding:0 24px 60px; }
    #error-banner {
      display:none; margin:12px 0; padding:9px 14px;
      background:rgba(239,68,68,.1); border:1px solid rgba(239,68,68,.25);
      border-radius:8px; font-size:12px; color:#f87171;
    }

    /* ════════════════════════════════════
       TILES VIEW
    ════════════════════════════════════ */
    #view-tiles    { display:block; }
    #view-articles { display:none; }

    .tiles-header { padding:18px 0 14px; display:flex; align-items:baseline; gap:10px; flex-wrap:wrap; }
    .tiles-heading { font-size:13px; font-weight:600; color:var(--t2); }
    .tiles-sub     { font-size:12px; color:var(--t3); }

    .tiles-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
      gap: 14px;
    }

    /* Category tile — clean, no article content inside */
    .cat-tile {
      background: var(--card); border: 1px solid var(--bd); border-radius: 14px;
      overflow: hidden; display: flex; flex-direction: column;
      cursor: pointer; text-decoration: none;
      transition: border-color .2s, transform .2s, box-shadow .2s;
    }
    .cat-tile:hover {
      border-color: var(--bd2); transform: translateY(-3px);
      box-shadow: 0 12px 40px rgba(0,0,0,.45);
    }

    .cat-tile-bar  { height: 4px; flex-shrink: 0; }

    .cat-tile-body {
      padding: 22px 20px 20px;
      display: flex; flex-direction: column; gap: 14px; flex: 1;
    }

    .cat-tile-icon { font-size: 2.2em; line-height: 1; }

    .cat-tile-label { display: flex; flex-direction: column; gap: 4px; }
    .cat-tile-name  { font-size: 15px; font-weight: 700; color: var(--t1); }
    .cat-tile-cnt   {
      font-size: 12px; font-weight: 600;
      display: inline-flex; align-items: center; gap: 4px;
    }

    .cat-tile-footer {
      padding: 10px 20px 14px;
      display: flex; align-items: center; justify-content: space-between;
    }
    .cat-tile-latest { font-size: 10.5px; color: var(--t3); }
    .cat-tile-arrow  { font-size: 12px; font-weight: 600; opacity: 0; transition: opacity .15s; }
    .cat-tile:hover .cat-tile-arrow { opacity: 1; }

    /* Skeleton tiles */
    .skel-tile { background:var(--card); border:1px solid var(--bd); border-radius:14px; overflow:hidden; }
    .skel-bar  { height:3px; }
    .skel-body { padding:14px 16px; display:flex; flex-direction:column; gap:12px; }
    .skel-line { border-radius:4px; }

    /* ════════════════════════════════════
       ARTICLES VIEW — vertical list
    ════════════════════════════════════ */
    .art-header {
      display:flex; align-items:center; gap:14px;
      padding:16px 0 14px; border-bottom:1px solid var(--bd);
      margin-bottom:6px; flex-wrap:wrap;
    }
    .back-btn {
      display:flex; align-items:center; gap:5px;
      background:var(--bd); border:1px solid var(--bd2); border-radius:8px;
      padding:7px 12px; font-size:12px; color:var(--t2); cursor:pointer;
      transition:all .15s; font-family:inherit; flex-shrink:0;
    }
    .back-btn:hover { background:var(--bd2); color:var(--t1); }
    .back-btn svg { width:13px; height:13px; }

    .art-cat-label { display:flex; align-items:center; gap:8px; }
    .art-cat-icon  { font-size:1.4em; }
    .art-cat-name  { font-size:17px; font-weight:700; }
    .art-cat-count { font-size:12px; color:var(--t3); margin-left:2px; }

    .art-search { margin-left:auto; position:relative; }
    .art-search svg { position:absolute; left:8px; top:50%; transform:translateY(-50%); width:13px; height:13px; color:var(--t3); pointer-events:none; }
    #search-input {
      background:var(--bd); border:1px solid var(--bd2); border-radius:8px;
      padding:6px 12px 6px 28px; font-size:12px; color:var(--t1); width:200px; outline:none;
      transition:border-color .15s;
    }
    #search-input:focus { border-color:#3b82f6; }
    #search-input::placeholder { color:var(--t3); }

    /* Vertical article list */
    #article-list { display:flex; flex-direction:column; }

    .list-item {
      display:flex; flex-direction:column; gap:8px;
      padding:18px 0 18px 16px;
      border-bottom:1px solid var(--bd);
      border-left:3px solid transparent;
      text-decoration:none;
      transition:border-left-color .15s, background .15s;
    }
    .list-item:hover { background:rgba(255,255,255,.015); border-left-color:var(--cat-color,#6b7280); }

    .list-meta  { display:flex; align-items:center; gap:7px; }
    .list-src   { font-size:11px; font-weight:600; color:var(--t3); }
    .list-dot   { font-size:10px; color:var(--bd2); }
    .list-time  { font-size:11px; color:var(--t3); }

    .list-title {
      font-size:15px; font-weight:600; color:var(--t1); line-height:1.45;
      transition:color .15s;
    }
    .list-item:hover .list-title { color:#fff; }

    .list-summary { font-size:13px; color:var(--t2); line-height:1.65; }

    .list-read { font-size:11.5px; font-weight:600; margin-top:2px; opacity:0; transition:opacity .15s; }
    .list-item:hover .list-read { opacity:1; }

    /* Empty + loading states */
    .list-skeleton { display:flex; flex-direction:column; gap:0; }
    .list-skel-item { padding:18px 0 18px 16px; border-bottom:1px solid var(--bd); display:flex; flex-direction:column; gap:10px; }

    .empty { display:flex; flex-direction:column; align-items:center; justify-content:center; padding:80px 0; text-align:center; }
    .empty-emoji { font-size:48px; margin-bottom:14px; }
    .empty p     { color:var(--t2); font-weight:600; font-size:16px; margin-bottom:6px; }
    .empty small { color:var(--t3); font-size:13px; }
  </style>
</head>
<body>

<!-- ── Header ──────────────────────────────────────── -->
<header>
  <div class="hdr">
    <div class="hdr-brand">
      <div class="dot-wrap">
        <span class="dot-ring live-ping"></span>
        <span class="dot-core"></span>
      </div>
      <span class="live-lbl">Live</span>
      <h1>GenAI Weekly Tracker</h1>
      <span id="week-badge" class="week-badge"></span>
    </div>
    <div class="hdr-right">
      <span id="update-meta"></span>
      <button id="refresh-btn" class="btn">
        <svg id="refresh-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
          <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
        </svg>
        Refresh
      </button>
    </div>
  </div>
</header>

<main>
  <div id="error-banner"></div>

  <!-- TILES VIEW -->
  <div id="view-tiles">
    <div class="tiles-header">
      <span class="tiles-heading" id="tiles-heading">Loading…</span>
      <span class="tiles-sub" id="tiles-sub"></span>
    </div>
    <div id="tiles-grid" class="tiles-grid"></div>
  </div>

  <!-- ARTICLES VIEW -->
  <div id="view-articles">
    <div class="art-header">
      <button class="back-btn" id="back-btn">
        <svg fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
          <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18"/>
        </svg>
        All Categories
      </button>
      <div class="art-cat-label">
        <span id="art-icon" class="art-cat-icon"></span>
        <span id="art-name" class="art-cat-name"></span>
        <span id="art-count" class="art-cat-count"></span>
      </div>
      <div class="art-search">
        <svg fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-4.35-4.35M17 11A6 6 0 1 1 5 11a6 6 0 0 1 12 0z"/>
        </svg>
        <input id="search-input" type="text" placeholder="Search…" autocomplete="off" />
      </div>
    </div>
    <div id="article-list"></div>
  </div>
</main>

<script>
// ════════════════════════════════════════════════════════════════════
//  CONFIG
// ════════════════════════════════════════════════════════════════════

// Optional: free API key from https://rss2json.com — unlocks 25 items/feed
const API_KEY = '';

const REFRESH_MS   = 5 * 60 * 1000;  // 5 minutes
const WEEK_MS      = 7 * 24 * 60 * 60 * 1000;
const MAX_TILE_ART = 4; // articles shown inside each tile

const FEEDS = [
  { url: 'https://techcrunch.com/category/artificial-intelligence/feed/',           source: 'TechCrunch',  aiOnly: true  },
  { url: 'https://venturebeat.com/category/ai/feed/',                               source: 'VentureBeat', aiOnly: true  },
  { url: 'https://www.theverge.com/ai-artificial-intelligence/rss/index.xml',       source: 'The Verge',   aiOnly: true  },
  { url: 'https://www.wired.com/feed/tag/artificial-intelligence/latest/rss',       source: 'Wired',       aiOnly: true  },
  { url: 'https://www.artificialintelligence-news.com/feed/',                       source: 'AI News',     aiOnly: true  },
  { url: 'https://news.google.com/rss/search?q=generative+AI+OR+LLM+launches+OR+new+model&hl=en&gl=US&ceid=US:en',                                source: 'Google News', aiOnly: false },
  { url: 'https://news.google.com/rss/search?q=AI+startup+funding+OR+raises+series+OR+AI+acquisition&hl=en&gl=US&ceid=US:en',                     source: 'Google News', aiOnly: false },
  { url: 'https://news.google.com/rss/search?q=artificial+intelligence+regulation+OR+AI+partnership+OR+AI+infrastructure&hl=en&gl=US&ceid=US:en', source: 'Google News', aiOnly: false },
];

// ════════════════════════════════════════════════════════════════════
//  CATEGORIES
// ════════════════════════════════════════════════════════════════════

const CATEGORIES = [
  { id: 'funding',        label: 'Funding',             icon: '💰', hex: '#22c55e' },
  { id: 'new-offerings',  label: 'New Offerings',       icon: '🚀', hex: '#3b82f6' },
  { id: 'partnerships',   label: 'Partnerships',        icon: '🤝', hex: '#a855f7' },
  { id: 'infrastructure', label: 'Infrastructure',      icon: '🏗️', hex: '#f97316' },
  { id: 'research',       label: 'Research',            icon: '🔬', hex: '#14b8a6' },
  { id: 'regulation',     label: 'Regulation & Policy', icon: '⚖️', hex: '#ef4444' },
  { id: 'acquisitions',   label: 'M&A',                 icon: '🏢', hex: '#eab308' },
  { id: 'talent',         label: 'Talent',              icon: '👤', hex: '#ec4899' },
  { id: 'open-source',    label: 'Open Source',         icon: '🌐', hex: '#06b6d4' },
  { id: 'enterprise',     label: 'Enterprise',          icon: '💼', hex: '#6366f1' },
  { id: 'general',        label: 'General',             icon: '📰', hex: '#6b7280' },
];
const CAT = Object.fromEntries(CATEGORIES.map(c => [c.id, c]));

function rgba(hex, a) {
  const r=parseInt(hex.slice(1,3),16), g=parseInt(hex.slice(3,5),16), b=parseInt(hex.slice(5,7),16);
  return `rgba(${r},${g},${b},${a})`;
}

// ════════════════════════════════════════════════════════════════════
//  CATEGORISATION
// ════════════════════════════════════════════════════════════════════

const KEYWORD_MAP = [
  ['acquisitions',   ['acqui','merger','merging','buyout','takeover','buys out','bought by']],
  ['funding',        ['raises','raised','funding round','series a','series b','series c','series d','seed round','pre-seed','valuation',' ipo','venture capital','vc-backed','investment round','new funding','secures funding','closes round','million in funding','billion in funding','led round','leads round']],
  ['partnerships',   ['partnership','partners with','partnering','collaboration','collaborates','integrates with','teams up','joint venture','signed a deal','strategic agreement','signed agreement','allian','co-develop','co-launch']],
  ['infrastructure', ['data center','datacenter',' gpu ','gpu cluster','compute cluster','ai chip','semiconductor','cloud computing',' aws ','amazon web services',' azure ','google cloud',' gcp ','tpu','training cluster','nvidia',' amd ','hyperscaler','colocation','energy consumption','power grid']],
  ['research',       ['researchers','new research','study finds','paper proposes','arxiv','benchmark','state-of-the-art','breakthrough','university','scientists','published findings','new model architecture','outperforms','surpasses human','novel approach']],
  ['regulation',     ['regulation','regulat','legislation','ai policy','ai law','eu ai act','congress','senate','white house','executive order','compliance','ai governance','government','federal','ban on ai','ftc','antitrust','ai safety board','ai oversight','parliament','nist ai']],
  ['talent',         ['hires ','hired ',' joins ','appointed ','appoints ','new ceo','new cto','new cpo','chief ai','head of ai','co-founder leaves','resigns','steps down','poaches','talent war','founding team']],
  ['open-source',    ['open source','open-source','open weight','open-weight','open model','open release','github release','mit license','apache license','hugging face','community model','publicly released weights']],
  ['enterprise',     ['enterprise','for business','b2b','commercial deployment','production use','corporate clients','saas platform','workplace ai','business adoption','enterprise customer','cost savings']],
  ['new-offerings',  ['launches','launch ','launched','unveils','unveil','introduces','introduce','new model','new version','releases','release ','debuts','debut','announces new','new api','new feature','new product','now available','gpt-','claude ','gemini ','llama ','mistral','sora','dall-e','new assistant','new agent','new tool']],
];

const AI_KW = ['ai','artificial intelligence','machine learning','large language model','llm','generative ai','genai','openai','anthropic','deepmind','chatgpt','claude','gemini','gpt','diffusion model','neural network','midjourney','stable diffusion','hugging face','foundation model','copilot','nvidia ai','agentic','ai agent','llama','mistral','cohere','perplexity','sora','dall-e','multimodal','language model'];

function isAIRelevant(t) { const s=t.toLowerCase(); return AI_KW.some(k=>s.includes(k)); }
function categorize(title, summary='') {
  const t=`${title} ${summary}`.toLowerCase();
  for(const [cat,kws] of KEYWORD_MAP) if(kws.some(k=>t.includes(k))) return cat;
  return 'general';
}
function isWithin7Days(dateStr) {
  const ms = Date.now() - new Date(dateStr).getTime();
  return ms >= 0 && ms <= WEEK_MS;
}

// ════════════════════════════════════════════════════════════════════
//  RSS FETCHING
// ════════════════════════════════════════════════════════════════════

function stripHtml(s) {
  return (s||'').replace(/<[^>]+>/g,'').replace(/&amp;/g,'&').replace(/&lt;/g,'<').replace(/&gt;/g,'>').replace(/&quot;/g,'"').replace(/&#39;/g,"'").trim();
}
function simpleHash(s) {
  let h=0; for(let i=0;i<s.length;i++) h=Math.imul(31,h)+s.charCodeAt(i)|0; return Math.abs(h).toString(36);
}
function esc(s) {
  return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
}

async function fetchFeed({ url, source, aiOnly }) {
  const p = new URLSearchParams({ rss_url: url });
  if (API_KEY) { p.set('api_key', API_KEY); p.set('count','25'); }
  try {
    const res  = await fetch(`https://api.rss2json.com/v1/api.json?${p}`, { signal: AbortSignal.timeout(9000) });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    if (data.status !== 'ok' || !Array.isArray(data.items)) return [];

    return data.items.map(item => {
      let title = stripHtml(item.title||''); if (!title) return null;
      let src = source;
      if (source==='Google News' && title.includes(' - ')) {
        const parts=title.split(' - '); src=parts.pop()||source; title=parts.join(' - ');
      }
      const summary = stripHtml(item.description||'').slice(0, 320);
      const link    = item.link || item.url || '';
      const pubDate = item.pubDate || new Date().toISOString();
      if (!isWithin7Days(pubDate)) return null;   // ← 7-day filter
      return { id: simpleHash(link||title), title, link, pubDate, source: src, category: categorize(title,summary), summary };
    })
    .filter(a => a && (aiOnly || isAIRelevant(a.title+' '+a.summary)));
  } catch(e) { console.warn(`[feed] ${source}:`, e.message); return []; }
}

function mergeArticles(incoming) {
  const seen = new Set(allArticles.map(a => a.title.toLowerCase().replace(/[^a-z0-9]/g,'').slice(0,45)));
  const novel = incoming.filter(a => {
    const k = a.title.toLowerCase().replace(/[^a-z0-9]/g,'').slice(0,45);
    if (seen.has(k)) return false; seen.add(k); return true;
  });
  allArticles = [...allArticles, ...novel].sort((a,b) => new Date(b.pubDate)-new Date(a.pubDate));
}

// ════════════════════════════════════════════════════════════════════
//  STATE
// ════════════════════════════════════════════════════════════════════

let allArticles   = [];
let isLoading     = false;
let lastUpdated   = null;
let nextRefreshAt = null;
let autoTimer     = null;
let currentCat    = null;

// ════════════════════════════════════════════════════════════════════
//  HELPERS
// ════════════════════════════════════════════════════════════════════

function timeAgo(d) {
  const ms=Date.now()-new Date(d).getTime();
  const m=Math.floor(ms/60000), h=Math.floor(ms/3600000), dy=Math.floor(ms/86400000);
  if (m<1) return 'just now'; if (m<60) return `${m}m ago`; if (h<24) return `${h}h ago`; return `${dy}d ago`;
}
function weekRange() {
  const now=new Date(), start=new Date(now);
  start.setDate(start.getDate()-6);
  const fmt=d=>d.toLocaleDateString('en-US',{month:'short',day:'numeric'});
  return `${fmt(start)} – ${fmt(now)}`;
}

// ════════════════════════════════════════════════════════════════════
//  TILES VIEW
// ════════════════════════════════════════════════════════════════════

function renderTiles() {
  const grid    = document.getElementById('tiles-grid');
  const heading = document.getElementById('tiles-heading');
  const sub     = document.getElementById('tiles-sub');

  if (isLoading && !allArticles.length) {
    heading.textContent = 'Loading this week\'s articles…';
    sub.textContent = '';
    grid.innerHTML = Array(8).fill(0).map(() => `
      <div class="skel-tile">
        <div class="skel-bar skeleton"></div>
        <div class="skel-body" style="gap:14px;">
          <div class="skel-line skeleton" style="width:40px;height:40px;border-radius:8px;"></div>
          <div style="display:flex;flex-direction:column;gap:7px;">
            <div class="skel-line skeleton" style="height:14px;width:65%;"></div>
            <div class="skel-line skeleton" style="height:11px;width:35%;"></div>
          </div>
        </div>
      </div>`).join('');
    return;
  }

  const counts = {};
  for (const a of allArticles) counts[a.category] = (counts[a.category]||0)+1;
  const total    = allArticles.length;
  const catCount = Object.keys(counts).length;

  heading.textContent = total ? `${total} articles this week` : 'No articles found for this week.';
  sub.textContent     = total ? `across ${catCount} categories — click a tile to explore` : '';

  grid.innerHTML = CATEGORIES.map(cat => {
    const arts = allArticles.filter(a => a.category === cat.id);
    if (!arts.length) return '';
    const latest = timeAgo(arts[0].pubDate);

    return `
    <div class="cat-tile fade-up" data-cat-id="${cat.id}">
      <div class="cat-tile-bar" style="background:${cat.hex};"></div>
      <div class="cat-tile-body">
        <div class="cat-tile-icon">${cat.icon}</div>
        <div class="cat-tile-label">
          <div class="cat-tile-name">${cat.label}</div>
          <div class="cat-tile-cnt" style="color:${cat.hex};">
            ${arts.length} article${arts.length!==1?'s':''}
          </div>
        </div>
      </div>
      <div class="cat-tile-footer">
        <span class="cat-tile-latest">Latest ${latest}</span>
        <span class="cat-tile-arrow" style="color:${cat.hex};">View all →</span>
      </div>
    </div>`;
  }).filter(Boolean).join('');
}

// ════════════════════════════════════════════════════════════════════
//  ARTICLES VIEW — vertical list
// ════════════════════════════════════════════════════════════════════

function renderArticleList() {
  const cat  = CAT[currentCat] || CAT['general'];
  const q    = (document.getElementById('search-input').value||'').toLowerCase();

  const filtered = allArticles.filter(a => {
    const matchCat    = !currentCat || a.category === currentCat;
    const matchSearch = !q || a.title.toLowerCase().includes(q) || a.source.toLowerCase().includes(q) || a.summary.toLowerCase().includes(q);
    return matchCat && matchSearch;
  });

  document.getElementById('art-icon').textContent  = cat.icon;
  document.getElementById('art-name').textContent  = cat.label;
  document.getElementById('art-name').style.color  = cat.hex;
  document.getElementById('art-count').textContent = `${filtered.length} article${filtered.length!==1?'s':''}`;

  const list = document.getElementById('article-list');
  if (!filtered.length) {
    list.innerHTML = `<div class="empty"><div class="empty-emoji">🤖</div><p>No articles found</p><small>${q?'Try clearing the search.':'Nothing in this category this week.'}</small></div>`;
    return;
  }

  list.innerHTML = filtered.map(a => `
    <a class="list-item fade-up" href="${a.link}" target="_blank" rel="noopener noreferrer"
       style="--cat-color:${cat.hex};">
      <div class="list-meta">
        <span class="list-src">${esc(a.source)}</span>
        <span class="list-dot">·</span>
        <span class="list-time">${timeAgo(a.pubDate)}</span>
      </div>
      <div class="list-title">${esc(a.title)}</div>
      ${a.summary ? `<div class="list-summary">${esc(a.summary)}</div>` : ''}
      <div class="list-read" style="color:${cat.hex};">Read article →</div>
    </a>`).join('');
}

// ════════════════════════════════════════════════════════════════════
//  NAVIGATION
// ════════════════════════════════════════════════════════════════════

function openCategory(catId) {
  currentCat = catId;
  document.getElementById('view-tiles').style.display    = 'none';
  document.getElementById('view-articles').style.display = 'block';
  document.getElementById('search-input').value = '';
  renderArticleList();
  window.scrollTo({ top: 0, behavior: 'smooth' });
}
function goBack() {
  currentCat = null;
  document.getElementById('view-tiles').style.display    = 'block';
  document.getElementById('view-articles').style.display = 'none';
  window.scrollTo({ top: 0, behavior: 'smooth' });
}

// ════════════════════════════════════════════════════════════════════
//  META
// ════════════════════════════════════════════════════════════════════

function updateMeta() {
  if (!lastUpdated) return;
  const rem = Math.max(0, nextRefreshAt - Date.now());
  const s   = Math.ceil(rem/1000);
  document.getElementById('update-meta').textContent =
    `Updated ${lastUpdated.toLocaleTimeString()} · next in ${Math.floor(s/60)}:${String(s%60).padStart(2,'0')}`;
}

function rerender() {
  if (currentCat) renderArticleList(); else renderTiles();
}

// ════════════════════════════════════════════════════════════════════
//  REFRESH
// ════════════════════════════════════════════════════════════════════

async function doRefresh() {
  if (isLoading) return;
  const btn=document.getElementById('refresh-btn'), ico=document.getElementById('refresh-icon');
  ico.classList.add('is-spinning'); btn.disabled = true;

  if (!allArticles.length) { isLoading=true; rerender(); }

  let done = 0;
  const promises = FEEDS.map(feed =>
    fetchFeed(feed).then(arts => {
      done++;
      if (arts.length) { mergeArticles(arts); document.getElementById('error-banner').style.display='none'; }
      if (done===1 || done%2===0 || done===FEEDS.length) {
        isLoading=false; lastUpdated=new Date(); nextRefreshAt=Date.now()+REFRESH_MS;
        rerender();
      }
    }).catch(()=>done++)
  );

  await Promise.allSettled(promises);

  isLoading=false; lastUpdated=new Date(); nextRefreshAt=Date.now()+REFRESH_MS;
  rerender();

  if (!allArticles.length) {
    const eb = document.getElementById('error-banner');
    eb.textContent = 'No articles found for the past 7 days. Some feeds may be unavailable — will retry automatically.';
    eb.style.display = 'block';
  }

  ico.classList.remove('is-spinning'); btn.disabled=false;
  clearTimeout(autoTimer); autoTimer=setTimeout(doRefresh, REFRESH_MS);
}

// ════════════════════════════════════════════════════════════════════
//  INIT
// ════════════════════════════════════════════════════════════════════

document.getElementById('week-badge').textContent = weekRange();
document.getElementById('search-input').addEventListener('input', () => { if (currentCat) renderArticleList(); });
document.getElementById('refresh-btn').addEventListener('click', doRefresh);
document.getElementById('back-btn').addEventListener('click', goBack);
document.getElementById('tiles-grid').addEventListener('click', e => {
  const tile = e.target.closest('.cat-tile');
  if (tile && tile.dataset.catId) openCategory(tile.dataset.catId);
});
setInterval(updateMeta, 1000);

isLoading = true; rerender(); isLoading = false;
doRefresh();
</script>
</body>
</html>
