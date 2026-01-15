# hos-ai-dashboard
Advanced medical diagnostic , therapeutic &amp; optimisation AI
1️⃣ Create the project locally (Bash)

Open a terminal and paste everything below:

# === CONFIG ===
REPO_NAME="hos-ai-dashboard"
APP_VERSION="0.3.0"

# === CREATE STRUCTURE ===
mkdir -p $REPO_NAME/.github/workflows
cd $REPO_NAME || exit 1

# Disable Jekyll (important for GitHub Pages)
touch .nojekyll

# === index.html ===
cat > index.html <<'HTML'
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <title>HOS-AI Clinical Case Dashboard</title>
  <link rel="stylesheet" href="style.css"/>
</head>
<body>
<header class="topbar">
  <div class="brand">
    <div class="logo">H</div>
    <div>
      <div class="title">HOS-AI Clinical Case Dashboard</div>
      <div class="subtitle">Clinical reasoning • 22-step deep dive • Doctor approval</div>
    </div>
  </div>
  <div class="actions">
    <button onclick="newCase()">New</button>
    <button onclick="runAnalysis()">Run Deep Dive</button>
    <button onclick="exportJSON()">Export JSON</button>
    <button onclick="exportMD()">Export MD</button>
    <button onclick="window.print()">Print / PDF</button>
  </div>
</header>

<main>
<section class="left">
  <h3>Case Input</h3>
  <input id="title" placeholder="Case title"/>
  <textarea id="caseText" placeholder="Paste clinical case here..."></textarea>
</section>

<section class="right">
  <h3>22-Step Clinical Analysis</h3>
  <div id="sections"></div>
</section>
</main>

<script src="script.js"></script>
</body>
</html>
HTML

# === style.css ===
cat > style.css <<'CSS'
body {
  margin:0;
  font-family: system-ui, sans-serif;
  background:#0b1020;
  color:#e8ecff;
}
.topbar {
  display:flex;
  justify-content:space-between;
  padding:12px;
  background:#101836;
}
.logo {
  width:36px;height:36px;
  background:#7aa2ff;
  color:#081022;
  display:grid;place-items:center;
  border-radius:10px;
  font-weight:900;
}
main {
  display:grid;
  grid-template-columns: 1fr 2fr;
  gap:12px;
  padding:12px;
}
textarea {
  width:100%;
  min-height:300px;
  background:#0e1630;
  color:#e8ecff;
  border-radius:10px;
  padding:10px;
}
.section {
  background:#0e1630;
  margin-bottom:10px;
  padding:10px;
  border-radius:10px;
}
@media print {
  .topbar,.left { display:none; }
  body { background:white;color:black; }
}
CSS

# === script.js ===
cat > script.js <<'JS'
const SECTIONS = [
  "Case summary & presenting problem",
  "Timeline & acuity",
  "Symptom clusters",
  "Red flags & triage",
  "Focused examination",
  "Pattern recognition",
  "Primary diagnosis",
  "Differential diagnosis",
  "Mechanism (aggressors vs defences)",
  "Multi-scale mapping",
  "Complications",
  "Investigations",
  "Risk stratification",
  "Immediate management",
  "Definitive management",
  "Monitoring plan",
  "Disposition",
  "Patient communication",
  "Safety netting",
  "Guidelines alignment",
  "Clinical pearls",
  "Doctor review & approval"
];

function renderSections() {
  const container = document.getElementById("sections");
  container.innerHTML = "";
  SECTIONS.forEach((title, i) => {
    const d = document.createElement("div");
    d.className = "section";
    d.innerHTML = `<strong>${i+1}. ${title}</strong><textarea></textarea>`;
    container.appendChild(d);
  });
}

function newCase() {
  document.getElementById("title").value = "";
  document.getElementById("caseText").value = "";
  renderSections();
}

function runAnalysis() {
  renderSections();
}

function exportJSON() {
  const sections = [...document.querySelectorAll(".section textarea")]
    .map((t,i)=>({step:i+1,content:t.value}));
  const data = {
    title: document.getElementById("title").value,
    input: document.getElementById("caseText").value,
    sections
  };
  download("case.json", JSON.stringify(data,null,2));
}

function exportMD() {
  let md = "# " + document.getElementById("title").value + "\n\n";
  md += "## Case\n" + document.getElementById("caseText").value + "\n\n";
  document.querySelectorAll(".section").forEach((s,i)=>{
    md += `## ${i+1}. ${SECTIONS[i]}\n` + s.querySelector("textarea").value + "\n\n";
  });
  download("case.md", md);
}

function download(name, content) {
  const a = document.createElement("a");
  a.href = URL.createObjectURL(new Blob([content]));
  a.download = name;
  a.click();
}

renderSections();
JS

# === GitHub Pages workflow ===
cat > .github/workflows/pages.yml <<'YAML'
name: Deploy GitHub Pages
on:
  push:
    branches: ["main"]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: .
      - uses: actions/deploy-pages@v4
YAML

# === README ===
cat > README.md <<'MD'
# HOS-AI Clinical Dashboard

Browser-based clinical reasoning dashboard with:
- 22-step structured analysis
- Doctor review & approval
- JSON / Markdown / PDF export
- GitHub Pages deployment

Open `index.html` locally or use the GitHub Pages link.
MD

echo "✅ HOS-AI dashboard created locally."
