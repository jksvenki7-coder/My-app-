# My-app-
Fresh food to home
<!DOCTYPE html>
<html>
  <head>
    <title>My Food Website</title>
  </head>
  <body>
    <h1>Welcome to My Food Website</h1>
    <img src="2031e1c8-816b-4ab6-9c2e-ab1396608d98.jpg" alt="Image1">// init-db.js
const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('./db.sqlite3');

db.serialize(() => {
  db.run(`
    CREATE TABLE IF NOT EXISTS crackers (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT NOT NULL,
      description TEXT,
      price REAL NOT NULL,
      stock INTEGER DEFAULT 0,
      image_url TEXT
    )
  `);

  db.run(`
    CREATE TABLE IF NOT EXISTS orders (// server.js
require('dotenv').config();
const express = require('express');
const bodyParser = require('body-parser');
const session = require('express-session');
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

const app = express();
const db = new sqlite3.Database('./db.sqlite3');

const PORT = process.env.PORT || 3000;
const ADMIN_PASSWORD = process.env.ADMIN_PASSWORD || 'admin123';
const WHATSAPP_NUMBER = process.env.WHATSAPP_NUMBER || '919876543210'; // international format without +

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static(path.join(__dirname, 'public')));

app.use(session({
  secret: process.env.SESSION_SECRET || 'keyboardcat',
  resave: false,
  saveUninitialized: false
}));

// API: get crackers
app.get('/api/crackers', (req, res) => {
  db.all("SELECT * FROM crackers ORDER BY id DESC", [], (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows);
  });
});

// API: admin login
app.post('/api/admin/login', (req, res) => {
  const { password } = req.body;
  if (password === ADMIN_PASSWORD) {
    req.session.isAdmin = true;
    return res.json({ ok: true });
  }
  res.status(401).json({ ok: false, error: 'Invalid password' });
});

// Middleware to protect admin API
function ensureAdmin(req, res, next) {
  if (req.session && req.session.isAdmin) return next();
  return res.status(401).json({ error: 'Unauthorized' });
}

// Admin: add cracker
app.post('/api/admin/crackers', ensureAdmin, (req, res) => {
  const { name, description, price, stock, image_url } = req.body;
  const stmt = db.prepare("INSERT INTO crackers (name, description, price, stock, image_url) VALUES (?, ?, ?, ?, ?)");
  stmt.run(name, description, price || 0, stock || 0, image_url || '', function(err) {
    if (err) return res.status(500).json({ error: err.message });
    res.json({ id: this.lastID });
  });
});

// Admin: update cracker
app.put('/api/admin/crackers/:id', ensureAdmin, (req, res) => {
  const id = req.params.id;
  const { name, description, price, stock, image_url } = req.body;
  db.run(`UPDATE crackers SET name=?, description=?, price=?, stock=?, image_url=? WHERE id=?`,
    [name, description, price, stock, image_url, id],
    function(err) {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ changes: this.changes });
    });
});

// Admin: delete cracker
app.delete('/api/admin/crackers/:id', ensureAdmin, (req, res) => {
  const id = req.params.id;
  db.run("DELETE FROM crackers WHERE id=?", [id], function(err) {
    if (err) return res.status(500).json({ error: err.message });
    res.json({ deleted: this.changes });
  });
});

// Admin: list crackers (for admin panel)
app.get('/api/admin/crackers', ensureAdmin, (req, res) => {
  db.all("SELECT * FROM crackers ORDER BY id DESC", [], (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows);
  });
});

// Orders endpoint (store order for records)
app.post('/api/orders', (req, res) => {
  // details: object or string containing cart details
  const { name, phone, address, total, details } = req.body;
  const stmt = db.prepare("INSERT INTO orders (name, phone, address, total, details) VALUES (?, ?, ?, ?, ?)");
  stmt.run(name, phone, address, total, JSON.stringify(details), function(err) {
    if (err) return res.status(500).json({ error: err.message });
    res.json({ id: this.lastID });
  });
});<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Diwali Crackers - Order</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <link rel="stylesheet" href="/styles.css">
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-warning">
  <div class="container">
    <a class="navbar-brand" href="#">Diwali Crackers</a>
    <a class="btn btn-success" href="/admin.html">Admin</a>
  </div>
</nav>

<div class="container my-4">
  <h2>Crackers Catalog</h2>
  <div id="catalog" class="row gy-3"></div>

  <hr>
  <h4>Your Cart</h4>
  <div id="cart-empty">Cart is empty.</div>
  <table id="cart-table" class="table d-none"><!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Admin - Diwali Crackers</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <link rel="stylesheet" href="/styles.css">
</head>
<body>
<div class="container my-4">
  <h2>Admin Panel</h2>

  <div id="login-box">
    <p>Enter admin password</p>
    <input id="admin-pass" class="form-control mb-2" type="password" placeholder="Password">
    <button id="admin-login" class="btn btn-primary">Login</button>
  </div>

  <div id="admin-area" class="d-none">
    <div class="mb-3">
      <button id="logout-btn" class="btn btn-secondary">Logout</button>
    </div>

    <h4>Add / Edit Cracker</h4>
    <form id="cracker-form" class="row g-2">
      <input type="hidden" id="cr-id">
      <div class="col-md-4">
        <input id="cr-name" class="form-control" placeholder="Name" required>
      </div>
      <div class="col-md-4">
        <input id="cr-price" class="form-control" placeholder="Price" required type="number" step="0.01">
      </div>
      <div class="col-md-4">
        <input id="cr-stock" class="form-control" placeholder="Stock" type="number">
      </div>
      <div class="col-12">
        <input id="cr-image" class="form-control" placeholder="Image URL">
      </div>
      <div class="col-12">
        <textarea id="cr-desc" class="form-control" placeholder="Description"></textarea>
      </div>
      <div class="col-12">
        <button class="btn btn-success" id="save-cracker">Save</button>
      </div>/* styles.css */
body { background: #fffaf0; }
.card { box-shadow: 0 2px 8px rgba(0,0,0,0.06); }
.catalog-item { border: 1px solid #eee; padding: 12px; border-radius: 6px; background: #fff; }
.catalog-item img { max-width: 100%; height: 120px; object-fit: cover; }
    </form>

    <hr>
    <h4>Existing Crackers</h4>
    <div id="admin-list" class="list-group"></div>

    <hr>
    <h4>Orders</h4>
    <div id="orders-list"></div>
  </div>
</div>

<script src="/app.js"></script>
</body>
</html>
    <thead><tr><th>Item</th><th>Qty</th><th>Price</th><th></th></tr></thead>
    <tbody></tbody>
    <tfoot><tr><td></td><td><strong id="cart-total">0</strong></td><td></td><td></td></tr></tfoot>
  </table>

  <div id="checkout" class="card p-3 d-none">
    <h5>Checkout</h5>
    <div class="mb-2">
      <input id="cust-name" class="form-control" placeholder="Your name">
    </div>
    <div class="mb-2">
      <input id="cust-phone" class="form-control" placeholder="Phone number (with country code)">
    </div>
    <div class="mb-2">
      <textarea id="cust-address" class="form-control" placeholder="Delivery address"></textarea>
    </div>
    <button id="order-whatsapp" class="btn btn-success">Place Order via WhatsApp</button>
    <small class="text-muted d-block mt-2">We will open WhatsApp with your order message. Confirm & send to place the order.</small>
  </div>
</div>

<script src="/app.js"></script>
</body>
</html>

// Admin: view orders
app.get('/api/admin/orders', ensureAdmin, (req, res) => {
  db.all("SELECT * FROM orders ORDER BY created_at DESC", [], (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows.map(r => ({...r, details: JSON.parse(r.details || '[]')})));
  });
});

// Logout
app.post('/api/admin/logout', (req, res) => {
  req.session.destroy(err => {
    if (err) return res.status(500).json({ error: 'Logout failed' });
    res.json({ ok: true });
  });
});

// fallback to index.html (SPA)
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.listen(PORT, () => {
  console.log(`Server started at http://localhost:${PORT}`);
});// app.js
const WHATSAPP_NUMBER = null; // server will embed number via API when needed if needed
// Shared functions
async function fetchJSON(url, opts = {}) {
  const res = await fetch(url, opts);
  if (!res.ok) throw new Error(await res.text());
  return res.json();
}

/* ---------- Customer side ---------- */
if (document.querySelector('#catalog')) {
  let crackers = [];
  const cart = {};

  const catalogEl = document.getElementById('catalog');
  const cartTable = document.getElementById('cart-table');
  const cartTbody = cartTable.querySelector('tbody');
  const cartEmpty = document.getElementById('cart-empty');
  const cartTotalEl = document.getElementById('cart-total');
  const checkout = document.getElementById('checkout');

  function renderCatalog() {
    catalogEl.innerHTML = '';
    crackers.forEach(c => {
      const col = document.createElement('div');
      col.className = 'col-md-4';
      col.innerHTML = `
        <div class="catalog-item">
          <img src="${c.image_url || 'https://via.placeholder.com/300x180'}" alt="${c.name}">
          <h5>${c.name}</h5>
          <p>${c.description || ''}</p>
          <p><strong>₹ ${c.price}</strong> | Stock: ${c.stock}</p>
          <div class="d-flex gap-2">
            <input type="number" min="1" value="1" id="qty-${c.id}" class="form-control" style="width:80px">
            <button class="btn btn-primary" data-id="${c.id}">Add to cart</button>
          </div>
        </div>
      `;
      catalogEl.appendChild(col);
    });

    catalogEl.querySelectorAll('button[data-id]').forEach(b => {
      b.addEventListener('click', () => {
        const id = b.getAttribute('data-id');
        const qty = parseInt(document.getElementById('qty-'+id).value || 1);
        const item = crackers.find(x => x.id == id);
        if(!item) return;
        if (item.stock !== null && item.stock !== undefined && item.stock < qty) {
          alert('Requested quantity not available in stock.');
          return;
        }
        if (!cart[id]) cart[id] = {...item, qty:0};
        cart[id].qty += qty;
        renderCart();
      });
    });
  }

  function renderCart() {
    const items = Object.values(cart);
    if (items.length === 0) {
      cartTable.classList.add('d-none');
      cartEmpty.classList.remove('d-none');
      checkout.classList.add('d-none');
      return;
    }
    cartTable.classList.remove('d-none');
    cartEmpty.classList.add('d-none');
    checkout.classList.remove('d-none');

    cartTbody.innerHTML = '';
    let total = 0;
    items.forEach(it => {
      const tr = document.createElement('tr');
      const lineTotal = it.price * it.qty;
      total += lineTotal;
      tr.innerHTML = `
        <td>${it.name}</td>
        <td>${it.qty}</td>
        <td>₹ ${lineTotal.toFixed(2)}</td>
        <td><button class="btn btn-sm btn-danger remove" data-id="${it.id}">Remove</button></td>
      `;
      cartTbody.appendChild(tr);
    });
    cartTotalEl.textContent = `₹ ${total.toFixed(2)}`;

    cartTbody.querySelectorAll('button.remove').forEach(b => {
      b.addEventListener('click', () => {
        const id = b.getAttribute('data-id');
        delete cart[id];
        renderCart();
      });
    });
  }

  // load crackers
  fetchJSON('/api/crackers').then(data => {
    crackers = data;
    renderCatalog();
  }).catch(err => { console.error(err); alert('Failed to load catalog'); });

  // WhatsApp order
  document.getElementById('order-whatsapp').addEventListener('click', async () => {
    const name = document.getElementById('cust-name').value.trim();
    const phone = document.getElementById('cust-phone').value.trim();
    const address = document.getElementById('cust-address').value.trim();
    if (!name || !phone || !address) {
      alert('Please fill name, phone and address.');
      return;
    }
    const items = Object.values(cart);
    if (items.length === 0) { alert('Cart empty'); return; }

    let total = 0;
    let detailsText = '';
    items.forEach(it => {
      const lineT = it.price * it.qty;
      total += lineT;
      detailsText += `${it.name} x ${it.qty} = ₹${lineT.toFixed(2)}%0A`;
    });

    const message = `New Order - Diwali Crackers%0AName: ${encodeURIComponent(name)}%0APhone: ${encodeURIComponent(phone)}%0AAddress: ${encodeURIComponent(address)}%0A%0AItems:%0A${detailsText}%0ATotal: ₹${total.toFixed(2)}%0A%0A(Please confirm delivery time)`;

    // first save order to backend for records
    try {
      await fetchJSON('/api/orders', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          name, phone, address, total, details: items
        })
      });
    } catch (e) {
      console.warn('Failed to save order on server:', e);
    }

    // open WhatsApp (number comes from server-side .env)
    // fetch server-provided number by opening / which will default to .env value above on backend
    // We'll open wa.me link using server env as used in backend (replace + with nothing)
    const serverNumber = (window.WHATSAPP_NUMBER || '') ; // fallback
    // If server didn't inject number, we'll use default from server .env via same variable in frontend? Not injected.
    // Instead, we use common pattern: open wa.me/<number>?text=...
    // Use number from the .env on server side hardcoded in server.js variable. We'll use that number in the link via server variable by creating endpoint? Simpler to hardcode here:
    // But to keep simple: attempt to fetch /api/admin/login to check? No. So open using number from server.js .env value — developer must set it there.
    // We'll read it from global: window.WHATSAPP_NUMBER - if not present, set to placeholder.
    const targetNumber = (window.WHATSAPP_NUMBER && window.WHATSAPP_NUMBER.trim()) || 'REPLACE_WITH_NUMBER';
    if (targetNumber === 'REPLACE_WITH_NUMBER') {
      // fallback: open share link (no specific recipient) so user picks contact in WhatsApp Web/mobile
      const shareLink = `https://api.whatsapp.com/send?text=${message}`;
      window.open(shareLink, '_blank');
    } else {
      const cleaned = targetNumber.replace(/\D/g,'');
      const waLink = `https://wa.me/${cleaned}?text=${message}`;
      window.open(waLink, '_blank');
    }
  });

}

/* ---------- Admin side ---------- */
if (document.querySelector('#login-box')) {
  const loginBox = document.getElementById('login-box');
  const adminArea = document.getElementById('admin-area');

  const adminLoginBtn = document.getElementById('admin-login');
  const adminPassInput = document.getElementById('admin-pass');
  const logoutBtn = document.getElementById('logout-btn');

  adminLoginBtn.addEventListener('click', async () => {
    const pass = adminPassInput.value.trim();
    if (!pass) return alert('Enter password');
    try {
      await fetchJSON('/api/admin/login', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({ password: pass })
      });
      loginBox.classList.add('d-none');
      adminArea.classList.remove('d-none');
      loadAdminData();
    } catch (e) {
      alert('Login failed');
    }
  });

  logoutBtn.addEventListener('click', async () => {
    await fetch('/api/admin/logout', { method: 'POST' });
    adminArea.classList.add('d-none');
    loginBox.classList.remove('d-none');
  });

  async function loadAdminData() {
    try {
      const crackers = await fetchJSON('/api/admin/crackers');
      const orders = await fetchJSON('/api/admin/orders');
      renderAdminList(crackers);
      renderOrders(orders);
    } catch (e) {
      alert('Failed to load admin data');
      console.error(e);
    }
  }

  const adminList = document.getElementById('admin-list');
  function renderAdminList(list) {
    adminList.innerHTML = '';
    list.forEach(item => {
      const el = document.createElement('div');
      el.className = 'list-group-item';
      el.innerHTML = `
        <div class="d-flex justify-content-between">
          <div>
            <h5>${item.name} - ₹${item.price}</h5>
            <p>${item.description || ''}</p>
            <small>Stock: ${item.stock}</small>
          </div>
          <div>
            <button class="btn btn-sm btn-primary edit" data-id="${item.id}">Edit</button>
            <button class="btn btn-sm btn-danger del" data-id="${item.id}">Delete</button>
          </div>
        </div>
      `;
      adminList.appendChild(el);
    });

    adminList.querySelectorAll('button.edit').forEach(b => {
      b.addEventListener('click', async () => {
        const id = b.getAttribute('data-id');
        const item = list.find(x => x.id == id);
        if (!item) return;
        document.getElementById('cr-id').value = item.id;
        document.getElementById('cr-name').value = item.name;
        document.getElementById('cr-price').value = item.price;
        document.getElementById('cr-stock').value = item.stock;
        document.getElementById('cr-image').value = item.image_url;
        document.getElementById('cr-desc').value = item.description;
        window.scrollTo(0,0);
      });
    });

    adminList.querySelectorAll('button.del').forEach(b => {
      b.addEventListener('click', async () => {
        const id = b.getAttribute('data-id');
        if (!confirm('Delete this cracker?')) return;
        try {
          await fetchJSON('/api/admin/crackers/' + id, { method: 'DELETE' });
          loadAdminData();
        } catch (e) {
          alert('Delete failed');
        }
      });
    });
  }

  document.getElementById('save-cracker').addEventListener('click', async (ev) => {
    ev.preventDefault();
    const id = document.getElementById('cr-id').value;
    const payload = {
      name: document.getElementById('cr-name').value,
      price: parseFloat(document.getElementById('cr-price').value || 0),
      stock: parseInt(document.getElementById('cr-stock').value || 0),
      image_url: document.getElementById('cr-image').value,
      description: document.getElementById('cr-desc').value
    };
    try {
      if (id) {
        await fetchJSON('/api/admin/crackers/' + id, {
          method: 'PUT',
          headers: {'Content-Type':'application/json'},
          body: JSON.stringify(payload)
        });
      } else {
        await fetchJSON('/api/admin/crackers', {
          method: 'POST',
          headers: {'Content-Type':'application/json'},
          body: JSON.stringify(payload)
        });
      }
      // clear form
      document.getElementById('cr-id').value = '';
      document.getElementById('cr-name').value = '';
      document.getElementById('cr-price').value = '';
      document.getElementById('cr-stock').value = '';
      document.getElementById('cr-image').value = '';
      document.getElementById('cr-desc').value = '';
      loadAdminData();
    } catch (e) {
      alert('Save failed');
      console.error(e);
    }
  });

  function renderOrders(list) {
    const ordersList = document.getElementById('orders-list');
    if (!list || list.length === 0) {
      ordersList.innerHTML = '<p>No orders yet</p>';
      return;
    }
    ordersList.innerHTML = '';
    list.forEach(o => {
      const el = document.createElement('div');
      el.className = 'card mb-2 p-2';
      el.innerHTML = `
        <strong>Order #${o.id}</strong>
        <div>${o.created_at}</div>
        <div>Name: ${o.name} | Phone: ${o.phone} | Total: ₹${o.total}</div>
        <div>Address: ${o.address}</div>
        <details><summary>Items</summary><pre>${JSON.stringify(o.details, null, 2)}</pre></details>
      `;
      ordersList.appendChild(el);
    });
  }
}
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
      name TEXT,
      phone TEXT,
      address TEXT,
      total REAL,
      details TEXT
    )
  `);

  // insert some sample crackers
  const stmt = db.prepare("INSERT INTO crackers (name, description, price, stock, image_url) VALUES (?, ?, ?, ?, ?)");
  stmt.run("Flower Pots (Anars)", "Small flower pot rocket - pack of 10", 80, 50, "https://via.placeholder.com/150");
  stmt.run("Rocket (Chakri)", "Spinning sparkler wheel - single", 25, 200, "https://via.placeholder.com/150");
  stmt.run("Sparklers", "Hand-held sparklers - pack of 20", 40, 100, "https://via.placeholder.com/150");
  stmt.finalize();

  console.log("Database initialized with sample data.");
});

db.close();
    <img src="29ef7e37-a02f-44ac-8ab5-bfb089ebe9c2.jpg" alt="Image2">
    <!-- More image tags... -->ADMIN_PASSWORD=admin123
WHATSAPP_NUMBER=919876543210   # change this to your WhatsApp number in international format without +
SESSION_SECRET=some_very_secret_string
PORT=3000
  </body>
</html>
{
  "name": "diwali-crackers-site",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "init-db": "node init-db.js"
  },
  "dependencies": {
    "body-parser": "^1.20.0",
    "dotenv": "^16.0.0",
    "express": "^4.18.2",
    "express-session": "^1.17.3",
    "sqlite3": "^5.1.2"
  }
}
