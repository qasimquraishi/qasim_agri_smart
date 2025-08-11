from pathlib import Path
import zipfile
import re

# --- Step 1: Create the project folder ---
out_dir = Path("/mnt/data/agrismart_final_with_bg")
out_dir.mkdir(parents=True, exist_ok=True)

# === Step 2: Generate basic prototype files ===
# (Minimal HTML, CSS, JS setup)
index_html = """<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AgriSmart Market</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <header>
    <h1>AgriSmart Market</h1>
  </header>
  <main>
  </main>
  <footer>
    <p>© 2025 AgriSmart Market</p>
  </footer>
  <script src="script.js"></script>
</body>
</html>
"""
styles_css = """body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}
header {
  background: #2d6a4f;
  color: white;
  padding: 1rem;
  text-align: center;
}
footer {
  background: #2d6a4f;
  color: white;
  text-align: center;
  padding: 1rem;
}
"""
script_js = """console.log('AgriSmart Market loaded.');"""

# Write these base files
(out_dir / "index.html").write_text(index_html, encoding="utf-8")
(out_dir / "styles.css").write_text(styles_css, encoding="utf-8")
(out_dir / "script.js").write_text(script_js, encoding="utf-8")

# --- Step 3: Inject Hero Section + Content ---
hero_html = """
<section class="hero">
  <h1>High-Quality Seeds & Fertilizers</h1>
</section>
<section class="content">
  <img src="https://images.unsplash.com/photo-1501004318641-b39e6451bec6?auto=format&fit=crop&w=800&q=80" alt="Seeds" loading="lazy">
  <img src="https://images.unsplash.com/photo-1578321316424-40cd32a8dd52?auto=format&fit=crop&w=800&q=80" alt="Fertilizer" loading="lazy">
</section>
"""

extra_css = """
.hero {
  position: relative;
  height: 60vh;
  background: url('https://images.unsplash.com/photo-1593121957289-1f1892878b8b?auto=format&fit=crop&w=1500&q=80') center/cover no-repeat;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.hero h1 {
  background: rgba(255, 255, 255, 0.7);
  padding: 0.5em 1em;
  border-radius: 8px;
  font-size: 2rem;
  max-width: 80%;
}
.content {
  padding: 2rem;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 2rem;
  background: #f9f9f9;
}
.content img {
  width: 300px;
  max-width: 90%;
  border-radius: 8px;
}
@media (max-width: 600px) {
  .hero h1 { font-size: 1.5rem; }
  .content { flex-direction: column; align-items: center; }
}
"""

# Insert hero section after <main> tag
index_path = out_dir / "index.html"
index_content = index_path.read_text(encoding="utf-8")
index_content = re.sub(r"(<main[^>]*>)", r"\1" + hero_html, index_content, count=1)
index_path.write_text(index_content, encoding="utf-8")

# Append CSS
styles_path = out_dir / "styles.css"
styles_path.write_text(styles_path.read_text(encoding="utf-8") + extra_css, encoding="utf-8")

# --- Step 4: Create ZIP file ---
zip_path = Path("/mnt/data/AgriSmart_Final_BG.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zf:
    for f in sorted(out_dir.iterdir()):
        zf.write(f, arcname=f.name)

print("ZIP created at:", zip_path)
