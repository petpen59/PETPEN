<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>PetPen - Boutique Animaux</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;600&display=swap" rel="stylesheet" />
  <style>
    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background-color: #f8f9fa;
    }
    header {
      background-color: #4CAF50;
      padding: 20px;
      color: white;
      text-align: center;
    }
    nav {
      display: flex;
      justify-content: center;
      gap: 20px;
      background-color: #388E3C;
      padding: 10px 0;
    }
    nav a {
      color: white;
      text-decoration: none;
      font-weight: 600;
    }
    .hero {
      background-image: url('https://images.unsplash.com/photo-1619983081547-7f0ddc3a1a12');
      background-size: cover;
      background-position: center;
      height: 300px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      font-size: 2em;
      font-weight: bold;
      text-shadow: 2px 2px 4px #000;
    }
    .products {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 20px;
      padding: 40px;
    }
    .product {
      background: white;
      border-radius: 12px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      padding: 20px;
      text-align: center;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
    .product img {
      width: 100%;
      max-height: 180px;
      object-fit: cover;
      border-radius: 8px;
      margin-bottom: 10px;
    }
    .product h3 {
      margin: 10px 0 5px;
    }
    .product p {
      color: #555;
      flex-grow: 1;
    }
    .product .price {
      font-weight: 600;
      margin: 10px 0;
      color: #2E7D32;
    }
    .product button {
      margin-top: 10px;
      padding: 10px 20px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    #panier {
      position: fixed;
      top: 80px;
      right: 20px;
      width: 300px;
      max-height: 80vh;
      overflow-y: auto;
      background: white;
      box-shadow: 0 2px 10px rgba(0,0,0,0.3);
      border-radius: 10px;
      padding: 20px;
      display: none;
      flex-direction: column;
      z-index: 1000;
    }
    #panier h3 {
      margin-top: 0;
      color: #388E3C;
      text-align: center;
    }
    #panier ul {
      list-style: none;
      padding: 0;
      margin: 0 0 10px 0;
      flex-grow: 1;
      overflow-y: auto;
    }
    #panier ul li {
      display: flex;
      justify-content: space-between;
      margin-bottom: 8px;
      border-bottom: 1px solid #ddd;
      padding-bottom: 6px;
    }
    #panier ul li button {
      background-color: #d32f2f;
      border: none;
      color: white;
      cursor: pointer;
      border-radius: 6px;
      padding: 2px 6px;
    }
    #btn-toggle-panier {
      position: fixed;
      top: 20px;
      right: 20px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 24px;
      cursor: pointer;
      z-index: 1100;
    }
    #btn-commander {
      background-color: #2E7D32;
      color: white;
      border: none;
      padding: 10px;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 600;
      margin-top: 10px;
    }
  </style>
</head>
<body>

<header>
  <h1>PetPen</h1>
  <p>Tout pour le bonheur de vos animaux</p>
</header>

<nav>
  <a href="#">Accueil</a>
  <a href="#">Produits</a>
  <a href="admin.html">Espace Admin</a>
  <a href="#contact">Contact</a>
</nav>

<div class="hero">
  Bienvenue chez PetPen
</div>

<section class="products" id="productsContainer">
  <!-- Produits chargés ici -->
</section>

<button id="btn-toggle-panier" title="Voir le panier">🛒</button>

<div id="panier">
  <h3>Panier</h3>
  <ul id="panierListe"></ul>
  <div><strong>Total: </strong><span id="totalPrix">0</span> DH</div>
  <button id="btn-commander">Commander</button>
</div>

<footer>
  &copy; 2025 PetPen. Tous droits réservés.
</footer>

<script>
  // Charger les produits depuis localStorage
  let produits = JSON.parse(localStorage.getItem('produits') || '[]');

  const productsContainer = document.getElementById('productsContainer');
  const panierDiv = document.getElementById('panier');
  const panierListe = document.getElementById('panierListe');
  const totalPrixSpan = document.getElementById('totalPrix');
  const btnTogglePanier = document.getElementById('btn-toggle-panier');
  const btnCommander = document.getElementById('btn-commander');

  // Panier = tableau d'objets {nom, prix, quantite}
  let panier = [];

  // Afficher les produits sur la page
  function afficherProduits() {
    productsContainer.innerHTML = '';
    if(produits.length === 0) {
      productsContainer.innerHTML = '<p style="text-align:center; font-size:1.2em; color:#555;">Aucun produit disponible pour le moment.</p>';
      return;
    }
    produits.forEach((p, i) => {
      const div = document.createElement('div');
      div.classList.add('product');
      div.innerHTML = `
        <img src="${p.image}" alt="${p.nom}" />
        <h3>${p.nom}</h3>
        <p>${p.description}</p>
        <div class="price">${p.prix} DH</div>
        <button onclick="ajouterAuPanier(${i})">Ajouter au panier</button>
      `;
      productsContainer.appendChild(div);
    });
  }

  // Ajouter un produit au panier
  function ajouterAuPanier(index) {
    const produit = produits[index];
    const item = panier.find(p => p.nom === produit.nom);
    if (item) {
      item.quantite++;
    } else {
      panier.push({...produit, quantite: 1});
    }
    afficherPanier();
  }

  // Afficher le panier
  function afficherPanier() {
    if (panier.length === 0) {
      panierDiv.style.display = 'none';
      return;
    }
    panierDiv.style.display = 'flex';
    panierListe.innerHTML = '';
    let total = 0;
    panier.forEach((item, i) => {
      const li = document.createElement('li');
      li.innerHTML = `
        <span>${item.nom} x${item.quantite}</span>
        <span>
          ${item.prix * item.quantite} DH
          <button onclick="retirerDuPanier(${i})" title="Retirer">x</button>
        </span>
      `;
      panierListe.appendChild(li);
      total += item.prix * item.quantite;
    });
    totalPrixSpan.textContent = total.toFixed(2);
  }

  // Retirer un produit du panier (1 unité)
  function retirerDuPanier(index) {
    panier[index].quantite--;
    if (panier[index].quantite <= 0) {
      panier.splice(index, 1);
    }
    afficherPanier();
  }

  // Toggle affichage panier
  btnTogglePanier.addEventListener('click', () => {
    if (panierDiv.style.display === 'flex') {
      panierDiv.style.display = 'none';
    } else if (panier.length > 0) {
      panierDiv.style.display = 'flex';
    }
  });

  // Commander (simple alerte)
  btnCommander.addEventListener('click', () => {
    if (panier.length === 0) {
      alert('Votre panier est vide.');
      return;
    }
    let recap = 'Résumé de votre commande :\\n';
    panier.forEach(item => {
      recap += `${item.nom} x${item.quantite} - ${item.prix * item.quantite} DH\\n`;
    });
    recap += `\\nTotal : ${totalPrixSpan.textContent} DH\\n\\nMerci pour votre achat chez PetPen !`;
    alert(recap);
    panier = [];
    afficherPanier();
  });

  // Initialisation
  afficherProduits();
</script>

</body>
</html>

