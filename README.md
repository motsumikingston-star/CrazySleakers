<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CrazySleakers - Step Into Crazy Comfort</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial,sans-serif}
body{background:#f5f5f5;color:#111}
header{background:#0f0f0f;color:#fff;padding:15px 20px;display:flex;justify-content:space-between;align-items:center;position:sticky;top:0;z-index:10}
.logo{font-size:24px;font-weight:900} .logo span{color:#00e676}
header button{background:#00e676;border:none;padding:10px 16px;border-radius:20px;font-weight:bold;cursor:pointer}
.hero{background:linear-gradient(90deg,#111,#222);color:#fff;padding:50px 20px;text-align:center}
.hero h1{font-size:38px;line-height:1.1} .hero h1 span{color:#00e676}
.hero p{margin:15px 0;color:#ccc}
.hero a{background:#00e676;color:#000;padding:12px 25px;border-radius:25px;text-decoration:none;font-weight:bold;display:inline-block}
.filters{display:flex;gap:8px;overflow-x:auto;padding:15px 20px}
.filters button{padding:8px 16px;border:1px solid #ddd;border-radius:20px;background:#fff;cursor:pointer;white-space:nowrap}
.filters button.active{background:#111;color:#fff}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:15px;padding:0 15px 100px}
.card{background:#fff;border-radius:15px;overflow:hidden;box-shadow:0 2px 10px rgba(0,0,0,.08)}
.card img{width:100%;height:150px;object-fit:cover}
.card-body{padding:12px}
.card-body h3{font-size:15px} .card-body p{font-size:13px;color:#777;margin:4px 0}
.price{font-weight:900;font-size:17px;margin:6px 0}
.card-body button{width:100%;background:#111;color:#fff;border:none;padding:10px;border-radius:10px;cursor:pointer}
.card-body button:hover{background:#00e676;color:#000}
.cart-bar{position:fixed;bottom:0;left:0;right:0;background:#111;color:#fff;padding:15px 20px;display:flex;justify-content:space-between;align-items:center}
.cart-bar button{background:#00e676;border:none;padding:10px 18px;border-radius:20px;font-weight:bold}
#cartModal{display:none;position:fixed;inset:0;background:rgba(0,0,0,.6);z-index:20;align-items:flex-end;justify-content:center}
.modal{background:#fff;width:100%;max-width:500px;border-radius:20px 20px 0 0;padding:20px;max-height:85vh;overflow:auto}
.item{display:flex;justify-content:space-between;padding:10px 0;border-bottom:1px solid #eee}
@media(min-width:700px){.grid{grid-template-columns:repeat(4,1fr);max-width:1100px;margin:auto}.card img{height:200px}}
</style>
</head>
<body>

<header>
  <div class="logo">CRAZY<span>SLEAKERS</span></div>
  <button onclick="openCart()">🛒 Cart (<span id="count">0</span>)</button>
</header>

<div class="hero">
  <h1>Step Into <span>Crazy Comfort</span></h1>
  <p>All types of footwear - Sneakers, Runners, Boots, Sandals & More. Delivered in SA.</p>
  <a href="#shop">SHOP NOW ↓</a>
  <p style="margin-top:12px;font-size:13px">📍 Krugersdorp |🚚 DM for Delivery | 💬 WhatsApp Orders</p>
</div>

<div class="filters" id="shop">
  <button class="active" onclick="filterCat('all',this)">All</button>
  <button onclick="filterCat('sneakers',this)">Sneakers</button>
  <button onclick="filterCat('running',this)">Running</button>
  <button onclick="filterCat('boots',this)">Boots</button>
  <button onclick="filterCat('sandals',this)">Sandals</button>
</div>

<div class="grid" id="grid"></div>

<div class="cart-bar">
  <div><b id="totalText">Total: R0</b><br><small id="itemsText">0 items</small></div>
  <button onclick="openCart()">CHECKOUT →</button>
</div>

<div id="cartModal" onclick="if(event.target==this) closeCart()">
  <div class="modal">
    <h2>Your Cart 🛒</h2>
    <div id="cartItems" style="margin:15px 0"><p>Your cart is empty.</p></div>
    <h3 id="cartTotal">Total: R0</h3>
    <label style="display:block;margin:15px 0 5px">Your Name:</label>
    <input id="custName" placeholder="e.g Joshua" style="width:100%;padding:12px;border:1px solid #ddd;border-radius:10px">
    <label style="display:block;margin:10px 0 5px">Delivery Address:</label>
    <input id="custAddress" placeholder="e.g Krugersdorp" style="width:100%;padding:12px;border:1px solid #ddd;border-radius:10px">
    <button onclick="checkoutWhatsApp()" style="width:100%;background:#25D366;border:none;padding:15px;border-radius:12px;font-weight:900;margin-top:15px;cursor:pointer">ORDER ON WHATSAPP →</button>
    <button onclick="closeCart()" style="width:100%;background:#eee;border:none;padding:12px;border-radius:12px;margin-top:8px;cursor:pointer">Close</button>
  </div>
</div>

<script>
const products=[
{id:1,name:"Street Runner Black",cat:"running",price:899,img:"https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=500",desc:"Sizes 6-10 • Lightweight"},
{id:2,name:"Classic White Sneaker",cat:"sneakers",price:749,img:"https://images.unsplash.com/photo-1549298916-b41d501d3772?w=500",desc:"Sizes 5-10 • Everyday"},
{id:3,name:"High-Top Urban",cat:"sneakers",price:999,img:"https://images.unsplash.com/photo-1595950653106-6c9ebd614d3a?w=500",desc:"Sizes 6-11 • Street Style"},
{id:4,name:"Trail Explorer Boot",cat:"boots",price:1299,img:"https://images.unsplash.com/photo-1520639888713-7851133b1ed0?w=500",desc:"Sizes 6-11 • Durable"},
{id:5,name:"Comfort Slide Sandal",cat:"sandals",price:349,img:"https://images.unsplash.com/photo-1603487742131-4160ec999306?w=500",desc:"Sizes 5-10 • Summer"},
{id:6,name:"Air Flex Runner",cat:"running",price:1099,img:"https://images.unsplash.com/photo-1597045566677-8cf032ed6634?w=500",desc:"Sizes 6-10 • Gym & Run"},
{id:7,name:"Leather Chelsea Boot",cat:"boots",price:1499,img:"https://images.unsplash.com/photo-1638247025967-b4e38f787b76?w=500",desc:"Sizes 6-10 • Premium"},
{id:8,name:"Retro Court Sneaker",cat:"sneakers",price:849,img:"https://images.unsplash.com/photo-1560769629-975ec94e6a86?w=500",desc:"Sizes 5-11 • Retro"},
];
let cart=[];
let currentFilter='all';
function render(){
  const grid=document.getElementById('grid');
  grid.innerHTML='';
  products.filter(p=>currentFilter=='all'||p.cat==currentFilter).forEach(p=>{
    grid.innerHTML+=`<div class="card"><img src="${p.img}"><div class="card-body"><h3>${p.name}</h3><p>${p.desc}</p><div class="price">R${p.price}</div><button onclick="addToCart(${p.id})">Add to Cart +</button></div></div>`;
  });
}
function filterCat(cat,btn){
  currentFilter=cat;
  document.querySelectorAll('.filters button').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active'); render();
}
function addToCart(id){
  const p=products.find(x=>x.id==id);
  cart.push(p); updateCart();
  alert(p.name+" added to cart!");
}
function updateCart(){
  document.getElementById('count').innerText=cart.length;
  let total=cart.reduce((s,p)=>s+p.price,0);
  document.getElementById('totalText').innerText='Total: R'+total;
  document
