<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <meta name="description" content="LinkNest — curated affiliate shopping links and deals" />
  <title>LinkNest – Smart Shopping</title>
  <style>
    *{box-sizing:border-box;font-family:system-ui,Arial}
    body{margin:0;background:#f1f3f6}
    /* HEADER */
    header{
      background:#2874f0;
      padding:12px;
      display:flex;
      gap:10px;
      align-items:center;
      color:#fff;
    }
    header h1{margin:0;font-size:22px}
    header input{
      flex:1;
      padding:10px;
      border:none;
      border-radius:4px;
      outline:4px solid transparent;
    }
    header input:focus{outline:4px solid rgba(255,255,255,0.15)}
    /* CATEGORY */
    .cat{
      display:flex;
      gap:10px;
      background:#fff;
      padding:10px;
      overflow-x:auto;
    }
    .cat button{
      border:1px solid #ddd;
      background:#fff;
      padding:8px 14px;
      border-radius:20px;
      cursor:pointer;
    }
    .cat button:hover{background:#2874f0;color:#fff}
    /* GRID */
    .grid{
      padding:14px;
      display:grid;
      grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
      gap:14px;
    }
    /* CARD */
    .card{
      background:#fff;
      border-radius:6px;
      padding:10px;
      box-shadow:0 2px 8px rgba(0,0,0,.1);
      position:relative;
      transition:.25s;
    }
    .card:hover{transform:translateY(-4px)}
    .badge{
      position:absolute;
      top:8px;left:8px;
      background:#388e3c;
      color:#fff;
      padding:3px 6px;
      font-size:12px;
      border-radius:4px;
    }
    .card img{
      width:100%;
      height:150px;
      object-fit:contain;
      border-radius:4px;
      background:#fafafa;
    }
    .card h3{font-size:14px;margin:6px 0}
    .price{font-weight:bold}
    .old{color:#888;text-decoration:line-through;font-size:13px}
    .rating{color:#ff9800;font-size:13px}
    /* BUTTON/ANCHOR */
    .buy{
      display:inline-block;
      text-align:center;
      width:100%;
      padding:10px;
      margin-top:6px;
      border:none;
      background:#ff9f00;
      border-radius:4px;
      cursor:pointer;
      font-size:15px;
      color:#111;
      text-decoration:none;
    }
    .buy:hover{background:#fb8c00}
    .buy:active{transform:scale(.96)}
    .buy:focus{outline:3px solid rgba(0,0,0,0.12)}
    /* FORM */
    form{
      background:#fff;
      padding:14px;
      margin:14px;
      border-radius:6px;
    }
    form h2{margin-top:0}
    form input,form select{
      width:100%;
      padding:10px;
      margin-bottom:10px;
    }
    form button{
      padding:10px;
      width:100%;
      background:#2874f0;
      border:none;
      color:#fff;
      font-size:15px;
    }
    footer{
      text-align:center;
      padding:16px;
      font-size:13px;
      color:#555;
    }
  </style>
</head>
<body>
  <header>
    <h1>LinkNest</h1>
    <label for="search" class="sr-only" style="position:absolute;left:-10000px">Search products</label>
    <input id="search" type="search" placeholder="Search products" aria-label="Search products">
  </header>

  <nav class="cat" aria-label="Product categories">
    <button type="button" data-cat="all">All</button>
    <button type="button" data-cat="mobile">Mobiles</button>
    <button type="button" data-cat="electronics">Electronics</button>
    <button type="button" data-cat="fashion">Fashion</button>
  </nav>

  <main class="grid" id="grid" aria-live="polite">
    <!-- Product cards rendered by JS (see below). Example static cards kept for fallback -->
    <article class="card" data-cat="mobile" data-name="5G Smartphone">
      <span class="badge" aria-hidden="true">20% OFF</span>
      <img src="https://via.placeholder.com/300x200?text=Phone" alt="5G Smartphone" loading="lazy">
      <h3>5G Smartphone</h3>
      <div class="rating" aria-hidden="true">★★★★☆</div>
      <div class="price">₹14,999 <span class="old">₹18,999</span></div>
      <!-- Use anchor so rel attributes can be applied -->
      <a class="buy" href="https://dl.flipkart.com/s/YOUR_LINK" target="_blank" rel="noopener noreferrer">Buy Now</a>
    </article>

    <article class="card" data-cat="electronics" data-name="Wireless Headphones">
      <span class="badge" aria-hidden="true">BEST</span>
      <img src="https://via.placeholder.com/300x200?text=Headphone" alt="Wireless Headphones" loading="lazy">
      <h3>Wireless Headphones</h3>
      <div class="rating" aria-hidden="true">★★★★★</div>
      <div class="price">₹1,999</div>
      <a class="buy" href="https://dl.flipkart.com/s/YOUR_LINK" target="_blank" rel="noopener noreferrer">Buy Now</a>
    </article>
  </main>

  <form id="productForm" aria-label="Submit your affiliate product" novalidate>
    <h2>Submit Your Affiliate Product</h2>
    <label>
      <span class="sr-only">Product Name</span>
      <input name="productName" placeholder="Product Name" required>
    </label>
    <label>
      <span class="sr-only">Affiliate Link</span>
      <input name="affiliateLink" placeholder="Affiliate Link (https://...)" type="url" required>
    </label>
    <label>
      <select name="category" required>
        <option value="">Select category</option>
        <option value="mobile">Mobile</option>
        <option value="electronics">Electronics</option>
        <option value="fashion">Fashion</option>
      </select>
    </label>
    <button type="submit">Submit (Admin Approval)</button>
    <p id="formMsg" aria-live="polite" style="color:#444"></p>
  </form>

  <footer>
    Affiliate disclosure: Links may earn commission • LinkNest 2026
  </footer>

  <script>
    // Utility: simple debounce
    function debounce(fn, wait=250){
      let t;
      return (...args)=>{ clearTimeout(t); t=setTimeout(()=>fn(...args), wait) }
    }

    // Filtering
    document.querySelectorAll('.cat button').forEach(btn=>{
      btn.addEventListener('click', ()=>filter(btn.dataset.cat))
    })

    function filter(c){
      document.querySelectorAll('.card').forEach(x=>{
        x.style.display = (c === 'all' || x.dataset.cat === c) ? 'block' : 'none'
      })
    }

    // Search with debounce and case-insensitive matching against data-name (more reliable)
    const search = document.getElementById('search')
    const doSearch = debounce(e=>{
      const v = e.target.value.trim().toLowerCase()
      document.querySelectorAll('.card').forEach(c=>{
        const name = (c.dataset.name || c.querySelector('h3')?.innerText || '').toLowerCase()
        c.style.display = name.includes(v) ? 'block' : 'none'
      })
    }, 200)
    search.addEventListener('input', doSearch)

    // Form submit — client-side validation and safe preview send to server
    const form = document.getElementById('productForm')
    const formMsg = document.getElementById('formMsg')
    form.addEventListener('submit', async (ev)=>{
      ev.preventDefault()
      const fd = new FormData(form)
      const name = fd.get('productName')?.toString().trim()
      const link = fd.get('affiliateLink')?.toString().trim()
      const category = fd.get('category')
      if(!name || !link || !category){ formMsg.textContent = 'Please fill all fields.'; return }
      try{
        // Basic link validation
        const url = new URL(link)
        // Send to server for verification/approval; placeholder endpoint
        await fetch('/api/submit-product', {
          method: 'POST',
          headers: { 'Content-Type':'application/json' },
          body: JSON.stringify({ name, link: url.href, category })
        })
        formMsg.textContent = 'Submitted — awaiting admin approval.'
        form.reset()
      }catch(err){
        formMsg.textContent = 'Invalid URL or network error.'
      }
    })

    // OPTIONAL: If you must open external links via JS, ensure no opener:
    // document.querySelectorAll('.buy').forEach(a=>{
    //   a.addEventListener('click', e=>{
    //     // The anchor already uses target + rel. If you opened via window.open:
    //     // const w = window.open(a.href, '_blank')
    //     // if(w) w.opener = null
    //   })
    // })
  </script>
</body>
</html>
