<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Moody Coastline Candles</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- PayPal SDK (replace YOUR_CLIENT_ID) -->
<script src="https://www.paypal.com/sdk/js?client-id=YOUR_CLIENT_ID&currency=CAD"></script>

<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #f7f4ef;
    color: #2b2b2b;
}

/* HEADER */
header {
    background: linear-gradient(to right, #2f3e46, #354f52);
    color: white;
    padding: 40px;
    text-align: center;
    letter-spacing: 2px;
}

header h1 {
    margin: 0;
    font-size: 36px;
}

/* PRODUCTS */
.container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    padding: 20px;
}

.product {
    background: white;
    border-radius: 12px;
    margin: 15px;
    width: 250px;
    padding: 15px;
    box-shadow: 0 5px 15px rgba(0,0,0,0.1);
}

.product img {
    width: 100%;
    border-radius: 10px;
}

.price {
    font-weight: bold;
    margin: 10px 0;
}

/* ADMIN PANEL */
.admin {
    background: #ffffff;
    padding: 20px;
    margin: 20px;
    border-radius: 10px;
    max-width: 600px;
}

input, textarea {
    width: 100%;
    margin: 5px 0;
    padding: 10px;
}

/* REVIEWS */
.review-box {
    background: #fff;
    margin: 10px auto;
    padding: 10px;
    width: 80%;
    border-radius: 10px;
}

/* FOOTER */
footer {
    text-align: center;
    padding: 20px;
    background: #2f3e46;
    color: white;
}
</style>
</head>

<body>

<header>
    <h1>Moody Coastline Candles</h1>
    <p>Soaps & More — West Coast Chic Handmade Goods</p>
</header>

<!-- ADMIN ADD PRODUCT -->
<div class="admin">
    <h2>Add Product (Etsy-style listing)</h2>
    <input id="name" placeholder="Product name">
    <input id="price" placeholder="Price (CAD)">
    <input id="image" placeholder="Image URL (or GIF link)">
    <textarea id="desc" placeholder="Description"></textarea>
    <button onclick="addProduct()">Add Product</button>
</div>

<!-- GIF / WIDGET AREA -->
<div class="admin">
    <h3>GIF / Widget Area</h3>
    <input id="widget" placeholder="Paste GIF or widget embed code">
    <button onclick="addWidget()">Add</button>
    <div id="widgets"></div>
</div>

<!-- PRODUCTS -->
<div class="container" id="productList"></div>

<!-- REVIEWS -->
<h2 style="text-align:center;">Customer Reviews</h2>

<div style="text-align:center;">
    <input id="reviewText" placeholder="Write a review..." style="width:60%;padding:10px;">
    <button onclick="addReview()">Post</button>
</div>

<div id="reviews"></div>

<footer>
    © 2026 Moody Coastline Candles — Handmade on the West Coast
</footer>

<script>
let products = JSON.parse(localStorage.getItem("products")) || [];
let reviews = JSON.parse(localStorage.getItem("reviews")) || [];

/* PRODUCTS */
function renderProducts() {
    const list = document.getElementById("productList");
    list.innerHTML = "";

    products.forEach((p, index) => {
        list.innerHTML += `
        <div class="product">
            <img src="${p.image}">
            <h3>${p.name}</h3>
            <p>${p.desc}</p>
            <div class="price">$${p.price} CAD</div>

            <div id="paypal-button-${index}"></div>
        </div>
        `;

        setTimeout(() => {
            paypal.Buttons({
                createOrder: function(data, actions) {
                    return actions.order.create({
                        purchase_units: [{
                            amount: { value: p.price }
                        }]
                    });
                },
                onApprove: function(data, actions) {
                    return actions.order.capture().then(function(details) {
                        alert("Payment successful! Thank you " + details.payer.name.given_name);
                    });
                }
            }).render(`#paypal-button-${index}`);
        }, 100);
    });

    localStorage.setItem("products", JSON.stringify(products));
}

/* ADD PRODUCT */
function addProduct() {
    let product = {
        name: document.getElementById("name").value,
        price: document.getElementById("price").value,
        image: document.getElementById("image").value,
        desc: document.getElementById("desc").value
    };

    products.push(product);
    renderProducts();
}

/* WIDGETS / GIFS */
function addWidget() {
    let div = document.createElement("div");
    div.innerHTML = document.getElementById("widget").value;
    document.getElementById("widgets").appendChild(div);
}

/* REVIEWS */
function addReview() {
    let text = document.getElementById("reviewText").value;
    reviews.push(text);
    localStorage.setItem("reviews", JSON.stringify(reviews));
    renderReviews();
}

function renderReviews() {
    let box = document.getElementById("reviews");
    box.innerHTML = "";

    reviews.forEach(r => {
        box.innerHTML += `<div class="review-box">⭐ ${r}</div>`;
    });
}

renderProducts();
renderReviews();
</script>

</body>
</html>
