// Modern JavaScript - ES6+ Features
class CallistaWebsite {
    constructor() {
        this.cart = JSON.parse(localStorage.getItem('callista-cart')) || [];
        this.wishlist = JSON.parse(localStorage.getItem('callista-wishlist')) || [];
        this.currentUser = JSON.parse(localStorage.getItem('callista-user')) || null;
        
        this.init();
    }

    init() {
        this.setupEventListeners();
        this.loadProducts();
        this.updateCartCount();
        this.updateWishlistCount();
        this.setupIntersectionObserver();
        this.initializePersonalization();
    }

    setupEventListeners() {
        // Mobile menu toggle
        const mobileMenuToggle = document.getElementById('mobileMenuToggle');
        const mobileMenu = document.getElementById('mobileMenu');
        const closeMenu = document.getElementById('closeMenu');

        if (mobileMenuToggle) {
            mobileMenuToggle.addEventListener('click', () => {
                mobileMenu.classList.add('active');
                document.body.style.overflow = 'hidden';
            });
        }

        if (closeMenu) {
            closeMenu.addEventListener('click', () => {
                mobileMenu.classList.remove('active');
                document.body.style.overflow = '';
            });
        }

        // Cart functionality
        const cartBtn = document.getElementById('cartBtn');
        const cartSidebar = document.getElementById('cartSidebar');
        const cartOverlay = document.getElementById('cartOverlay');
        const cartClose = document.querySelector('.cart-close');

        if (cartBtn) {
            cartBtn.addEventListener('click', () => this.toggleCart());
        }

        if (cartClose) {
            cartClose.addEventListener('click', () => this.toggleCart());
        }

        if (cartOverlay) {
            cartOverlay.addEventListener('click', (e) => {
                if (e.target === cartOverlay) {
                    this.toggleCart();
                }
            });
        }

        // Search functionality
        const searchInput = document.getElementById('searchInput');
        const searchForm = document.getElementById('searchForm');

        if (searchInput) {
            searchInput.addEventListener('input', (e) => this.handleSearch(e.target.value));
        }

        if (searchForm) {
            searchForm.addEventListener('submit', (e) => {
                e.preventDefault();
                this.handleSearch(searchInput.value);
            });
        }

        // Newsletter form
        const newsletterForm = document.getElementById('newsletterForm');
        if (newsletterForm) {
            newsletterForm.addEventListener('submit', (e) => this.handleNewsletterSignup(e));
        }

        // Beauty quiz
        const startQuiz = document.getElementById('startQuiz');
        if (startQuiz) {
            startQuiz.addEventListener('click', () => this.startBeautyQuiz());
        }

        // Product filters
        const filterBtns = document.querySelectorAll('.filter-btn');
        filterBtns.forEach(btn => {
            btn.addEventListener('click', (e) => this.filterProducts(e.target.dataset.filter));
        });

        // Load more products
        const loadMore = document.getElementById('loadMoreProducts');
        if (loadMore) {
            loadMore.addEventListener('click', () => this.loadMoreProducts());
        }

        // Back to top button
        const backToTop = document.getElementById('backToTop');
        if (backToTop) {
            backToTop.addEventListener('click', () => this.scrollToTop());
        }

        // Wishlist functionality
        const wishlistBtn = document.getElementById('wishlistBtn');
        if (wishlistBtn) {
            wishlistBtn.addEventListener('click', () => this.toggleWishlist());
        }

        // Smooth scrolling for anchor links
        document.querySelectorAll('a[href^="#"]').forEach(anchor => {
            anchor.addEventListener('click', function (e) {
                e.preventDefault();
                const target = document.querySelector(this.getAttribute('href'));
                if (target) {
                    target.scrollIntoView({
                        behavior: 'smooth',
                        block: 'start'
                    });
                }
            });
        });

        // Language and currency toggle
        const languageSelect = document.getElementById('language');
        const currencySelect = document.getElementById('currency');

        if (languageSelect) {
            languageSelect.addEventListener('change', (e) => this.changeLanguage(e.target.value));
        }

        if (currencySelect) {
            currencySelect.addEventListener('change', (e) => this.changeCurrency(e.target.value));
        }
    }

    // Product Management
    async loadProducts() {
        // Simulate API call
        const products = [
            {
                id: 1,
                name: "Sérum Vitamine C 30%",
                category: "soins",
                subcategory: "visage",
                price: 299,
                originalPrice: 399,
                rating: 4.8,
                reviews: 247,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23FEF3C7' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%23F59E0B' font-size='16' font-family='Arial'%3ESérum Vit C%3C/text%3E%3C/svg%3E",
                badge: "Best-seller",
                description: "Éclat immédiat et taches pigmentaires",
                ingredients: ["Vitamine C 30%", "Acide hyaluronique", "Niacinamide"],
                skinType: ["mixte", "grasse", "normale"]
            },
            {
                id: 2,
                name: "Crème Hydratante 24h",
                category: "soins",
                subcategory: "visage",
                price: 189,
                originalPrice: 259,
                rating: 4.6,
                reviews: 189,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23DBEAFE' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%233B82F6' font-size='16' font-family='Arial'%3ECrème Hydratante%3C/text%3E%3C/svg%3E",
                badge: "Nouveau",
                description: "Hydratation intense 24 heures",
                ingredients: ["Acide hyaluronique", "Ceramides", "Glycerine"],
                skinType: ["seche", "mixte", "normale"]
            },
            {
                id: 3,
                name: "Rouge à Lèvres Mat",
                category: "maquillage",
                subcategory: "levres",
                price: 125,
                originalPrice: null,
                rating: 4.7,
                reviews: 156,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23FCE7F3' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%23EC4899' font-size='16' font-family='Arial'%3ERouge à Lèvres%3C/text%3E%3C/svg%3E",
                badge: null,
                description: "Texture mate longue tenue",
                ingredients: ["Vitamine E", "Huile de jojoba"],
                skinType: ["all"]
            },
            {
                id: 4,
                name: "Mascara Volume Intense",
                category: "maquillage",
                subcategory: "yeux",
                price: 89,
                originalPrice: 119,
                rating: 4.5,
                reviews: 98,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23E0E7FF' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%234F46E5' font-size='16' font-family='Arial'%3EMascara%3C/text%3E%3C/svg%3E",
                badge: "Promo",
                description: "Volume X5 sans paquets",
                ingredients: ["Cire d'abeille", "Huile de ricin"],
                skinType: ["all"]
            },
            {
                id: 5,
                name: "Eau de Parfum Floral",
                category: "parfums",
                subcategory: "femme",
                price: 450,
                originalPrice: 599,
                rating: 4.9,
                reviews: 234,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23F3E8FF' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%238B5CF6' font-size='16' font-family='Arial'%3EParfum%3C/text%3E%3C/svg%3E",
                badge: "Best-seller",
                description: "Notes florales et boisées",
                ingredients: ["Essence de rose", "Musc blanc", "Santal"],
                skinType: ["all"]
            },
            {
                id: 6,
                name: "Lisseur Professionnel",
                category: "appareils",
                subcategory: "cheveux",
                price: 899,
                originalPrice: 1299,
                rating: 4.8,
                reviews: 67,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23FFEDD5' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%23F97316' font-size='16' font-family='Arial'%3ELisseur%3C/text%3E%3C/svg%3E",
                badge: "Nouveau",
                description: "Technologie ionique anti-frisottis",
                ingredients: ["Céramique", "Titanium"],
                skinType: ["all"]
            }
        ];

        this.displayProducts(products);
    }

    displayProducts(products, append = false) {
        const productsGrid = document.getElementById('productsGrid');
        if (!productsGrid) return;

        if (!append) {
            productsGrid.innerHTML = '';
        }

        products.forEach(product => {
            const productCard = this.createProductCard(product);
            productsGrid.appendChild(productCard);
        });

        // Add event listeners to new product cards
        this.attachProductEventListeners();
    }

    createProductCard(product) {
        const card = document.createElement('div');
        card.className = 'product-card';
        card.dataset.category = product.category;
        card.dataset.skinType = product.skinType.join(' ');

        const discount = product.originalPrice ? Math.round(((product.originalPrice - product.price) / product.originalPrice) * 100) : 0;

        card.innerHTML = `
            ${product.badge ? `<div class="product-badge">${product.badge}</div>` : ''}
            <div class="product-image">
                <img src="${product.image}" alt="${product.name}">
                <div class="product-overlay">
                    <button class="btn-quick-view" data-product-id="${product.id}">
                        <i class="fas fa-eye"></i> Aperçu
                    </button>
                    <button class="btn-add-wishlist" data-product-id="${product.id}">
                        <i class="fas fa-heart"></i>
                    </button>
                </div>
            </div>
            <div class="product-info">
                <h4 class="product-title">${product.name}</h4>
                <div class="product-rating">
                    <span class="stars">${'⭐'.repeat(Math.floor(product.rating))}</span>
                    <span class="rating">${product.rating}</span>
                    <span class="reviews">(${product.reviews} avis)</span>
                </div>
                <p class="product-description">${product.description}</p>
                <div class="product-price">
                    <span class="current-price">${product.price} MAD</span>
                    ${product.originalPrice ? `<span class="original-price">${product.originalPrice} MAD</span>` : ''}
                    ${discount > 0 ? `<span class="discount">-${discount}%</span>` : ''}
                </div>
                <button class="btn-primary btn-add-cart" data-product-id="${product.id}">
                    <i class="fas fa-shopping-bag"></i> Ajouter
                </button>
            </div>
        `;

        return card;
    }

    attachProductEventListeners() {
        // Quick view buttons
        document.querySelectorAll('.btn-quick-view').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const productId = parseInt(e.currentTarget.dataset.productId);
                this.showQuickView(productId);
            });
        });

        // Add to cart buttons
        document.querySelectorAll('.btn-add-cart').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const productId = parseInt(e.currentTarget.dataset.productId);
                this.addToCart(productId);
            });
        });

        // Add to wishlist buttons
        document.querySelectorAll('.btn-add-wishlist').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const productId = parseInt(e.currentTarget.dataset.productId);
                this.toggleWishlist(productId);
            });
        });
    }

    // Cart Management
    addToCart(productId) {
        const product = this.getProductById(productId);
        if (!product) return;

        const existingItem = this.cart.find(item => item.id === productId);
        
        if (existingItem) {
            existingItem.quantity += 1;
        } else {
            this.cart.push({
                ...product,
                quantity: 1
            });
        }

        this.saveCart();
        this.updateCartCount();
        this.showCartNotification(product);
    }

    removeFromCart(productId) {
        this.cart = this.cart.filter(item => item.id !== productId);
        this.saveCart();
        this.updateCartCount();
        this.renderCart();
    }

    updateCartQuantity(productId, quantity) {
        const item = this.cart.find(item => item.id === productId);
        if (item) {
            item.quantity = Math.max(1, quantity);
            this.saveCart();
            this.renderCart();
        }
    }

    saveCart() {
        localStorage.setItem('callista-cart', JSON.stringify(this.cart));
    }

    updateCartCount() {
        const count = this.cart.reduce((total, item) => total + item.quantity, 0);
        const cartCountElements = document.querySelectorAll('.cart-btn .count, .cart-count');
        cartCountElements.forEach(el => {
            el.textContent = count;
            el.style.display = count > 0 ? 'block' : 'none';
        });
    }

    toggleCart() {
        const cartSidebar = document.getElementById('cartSidebar');
        const cartOverlay = document.getElementById('cartOverlay');
        
        cartSidebar.classList.toggle('active');
        cartOverlay.classList.toggle('active');
        document.body.classList.toggle('cart-open');
        
        if (cartSidebar.classList.contains('active')) {
            this.renderCart();
        }
    }

    renderCart() {
        const cartItems = document.getElementById('cartItems');
        const cartTotal = document.getElementById('cartTotal');
        
        if (!cartItems || !cartTotal) return;

        if (this.cart.length === 0) {
            cartItems.innerHTML = `
                <div class="empty-cart">
                    <i class="fas fa-shopping-bag"></i>
                    <p>Votre panier est vide</p>
                    <button class="btn-primary" onclick="callista.toggleCart()">Continuer mes achats</button>
                </div>
            `;
            cartTotal.textContent = '0 MAD';
            return;
        }

        cartItems.innerHTML = this.cart.map(item => `
            <div class="cart-item">
                <img src="${item.image}" alt="${item.name}" class="cart-item-image">
                <div class="cart-item-info">
                    <h4>${item.name}</h4>
                    <p>${item.price} MAD</p>
                    <div class="cart-item-quantity">
                        <button class="quantity-btn" onclick="callista.updateCartQuantity(${item.id}, ${item.quantity - 1})">-</button>
                        <span>${item.quantity}</span>
                        <button class="quantity-btn" onclick="callista.updateCartQuantity(${item.id}, ${item.quantity + 1})">+</button>
                    </div>
                </div>
                <button class="cart-item-remove" onclick="callista.removeFromCart(${item.id})">
                    <i class="fas fa-trash"></i>
                </button>
            </div>
        `).join('');

        const total = this.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
        cartTotal.textContent = `${total} MAD`;
    }

    showCartNotification(product) {
        const notification = document.createElement('div');
        notification.className = 'cart-notification';
        notification.innerHTML = `
            <div class="notification-content">
                <img src="${product.image}" alt="${product.name}">
                <div>
                    <p><strong>${product.name}</strong> ajouté au panier</p>
                    <a href="#" onclick="callista.toggleCart(); return false;">Voir mon panier</a>
                </div>
            </div>
        `;

        document.body.appendChild(notification);

        setTimeout(() => {
            notification.classList.add('show');
        }, 100);

        setTimeout(() => {
            notification.classList.remove('show');
            setTimeout(() => {
                document.body.removeChild(notification);
            }, 300);
        }, 3000);
    }

    // Wishlist Management
    toggleWishlist(productId) {
        const product = this.getProductById(productId);
        if (!product) return;

        const index = this.wishlist.findIndex(item => item.id === productId);
        
        if (index > -1) {
            this.wishlist.splice(index, 1);
        } else {
            this.wishlist.push(product);
        }

        this.saveWishlist();
        this.updateWishlistCount();
        this.updateWishlistButton(productId);
    }

    saveWishlist() {
        localStorage.setItem('callista-wishlist', JSON.stringify(this.wishlist));
    }

    updateWishlistCount() {
        const wishlistBtn = document.getElementById('wishlistBtn');
        if (wishlistBtn) {
            const count = wishlistBtn.querySelector('.count');
            if (count) {
                count.textContent = this.wishlist.length;
                count.style.display = this.wishlist.length > 0 ? 'block' : 'none';
            }
        }
    }

    updateWishlistButton(productId) {
        const btn = document.querySelector(`[data-product-id="${productId}"].btn-add-wishlist`);
        if (btn) {
            const isInWishlist = this.wishlist.some(item => item.id === productId);
            btn.innerHTML = `<i class="fas fa-heart${isInWishlist ? '' : '-o'}"></i>`;
            btn.classList.toggle('active', isInWishlist);
        }
    }

    // Search functionality
    handleSearch(query) {
        if (query.length < 2) {
            this.clearSearchResults();
            return;
        }

        // Simulate search
        const results = this.searchProducts(query);
        this.displaySearchResults(results);
    }

    searchProducts(query) {
        const products = [
            { id: 1, name: "Sérum Vitamine C", category: "soins", image: "...", price: 299 },
            { id: 2, name: "Crème Hydratante", category: "soins", image: "...", price: 189 },
            { id: 3, name: "Rouge à Lèvres", category: "maquillage", image: "...", price: 125 }
        ];

        return products.filter(product => 
            product.name.toLowerCase().includes(query.toLowerCase()) ||
            product.category.toLowerCase().includes(query.toLowerCase())
        );
    }

    displaySearchResults(results) {
        const searchResults = document.getElementById('searchResults');
        if (!searchResults) return;

        if (results.length === 0) {
            searchResults.innerHTML = '<div class="search-no-results">Aucun résultat trouvé</div>';
            searchResults.classList.add('active');
            return;
        }

        searchResults.innerHTML = results.map(result => `
            <div class="search-result-item" onclick="callista.showProduct(${result.id})">
                <img src="${result.image}" alt="${result.name}">
                <div>
                    <h4>${result.name}</h4>
                    <p>${result.category}</p>
                    <span>${result.price} MAD</span>
                </div>
            </div>
        `).join('');

        searchResults.classList.add('active');
    }

    clearSearchResults() {
        const searchResults = document.getElementById('searchResults');
        if (searchResults) {
            searchResults.innerHTML = '';
            searchResults.classList.remove('active');
        }
    }

    // Product filtering
    filterProducts(filter) {
        const products = document.querySelectorAll('.product-card');
        
        products.forEach(product => {
            const shouldShow = filter === 'all' || product.dataset.category === filter || product.dataset.skinType.includes(filter);
            product.style.display = shouldShow ? 'block' : 'none';
        });

        // Update active filter button
        document.querySelectorAll('.filter-btn').forEach(btn => {
            btn.classList.toggle('active', btn.dataset.filter === filter);
        });
    }

    // Product quick view
    showQuickView(productId) {
        const product = this.getProductById(productId);
        if (!product) return;

        const modal = document.getElementById('productModal');
        const modalContent = document.getElementById('modalContent');

        modalContent.innerHTML = `
            <div class="quick-view-content">
                <div class="quick-view-image">
                    <img src="${product.image}" alt="${product.name}">
                </div>
                <div class="quick-view-info">
                    <h2>${product.name}</h2>
                    <div class="product-rating">
                        <span class="stars">${'⭐'.repeat(Math.floor(product.rating))}</span>
                        <span class="rating">${product.rating}</span>
                        <span class="reviews">(${product.reviews} avis)</span>
                    </div>
                    <p class="product-description">${product.description}</p>
                    <div class="product-price">
                        <span class="current-price">${product.price} MAD</span>
                        ${product.originalPrice ? `<span class="original-price">${product.originalPrice} MAD</span>` : ''}
                    </div>
                    <div class="product-ingredients">
                        <h4>Ingrédients clés:</h4>
                        <ul>
                            ${product.ingredients.map(ingredient => `<li>${ingredient}</li>`).join('')}
                        </ul>
                    </div>
                    <div class="quick-view-actions">
                        <button class="btn-primary btn-large" onclick="callista.addToCart(${product.id}); callista.closeModal();">
                            <i class="fas fa-shopping-bag"></i> Ajouter au panier
                        </button>
                        <button class="btn-outline" onclick="callista.toggleWishlist(${product.id})">
                            <i class="fas fa-heart"></i> Ajouter à ma wishlist
                        </button>
                    </div>
                </div>
            </div>
        `;

        this.showModal(modal);
    }

    showModal(modal) {
        modal.classList.add('active');
        document.body.style.overflow = 'hidden';
    }

    closeModal() {
        const modals = document.querySelectorAll('.modal.active');
        modals.forEach(modal => {
            modal.classList.remove('active');
        });
        document.body.style.overflow = '';
    }

    // Beauty Quiz
    startBeautyQuiz() {
        const quizSteps = [
            {
                question: "Quel est votre type de peau ?",
                options: ["Peau sèche", "Peau mixte", "Peau grasse", "Peau sensible"]
            },
            {
                question: "Quelles sont vos principales préoccupations ?",
                options: ["Hydratation", "Taches pigmentaires", "Signes de l'âge", "Pores dilatés"]
            },
            {
                question: "Quel est votre budget ?",
                options: ["Moins de 200 MAD", "200-500 MAD", "500-1000 MAD", "Plus de 1000 MAD"]
            }
        ];

        let currentStep = 0;
        const answers = [];

        const showQuizStep = () => {
            if (currentStep >= quizSteps.length) {
                this.showQuizResults(answers);
                return;
            }

            const step = quizSteps[currentStep];
            const modal = document.getElementById('productModal');
            const modalContent = document.getElementById('modalContent');

            modalContent.innerHTML = `
                <div class="quiz-content">
                    <h3>Quiz Beauté Personnalisé</h3>
                    <div class="quiz-progress">
                        <div class="progress-bar" style="width: ${((currentStep + 1) / quizSteps.length) * 100}%"></div>
                    </div>
                    <div class="quiz-step">
                        <h4>${step.question}</h4>
                        <div class="quiz-options">
                            ${step.options.map((option, index) => `
                                <button class="quiz-option" data-option="${option}">
                                    ${option}
                                </button>
                            `).join('')}
                        </div>
                    </div>
                    <div class="quiz-navigation">
                        <button class="btn-outline" ${currentStep === 0 ? 'disabled' : ''} onclick="callista.previousQuizStep()">
                            Précédent
                        </button>
                        <button class="btn-primary" id="nextQuizStep" disabled>Suivant</button>
                    </div>
                </div>
            `;

            // Add event listeners to options
            modalContent.querySelectorAll('.quiz-option').forEach(option => {
                option.addEventListener('click', (e) => {
                    modalContent.querySelectorAll('.quiz-option').forEach(opt => opt.classList.remove('selected'));
                    e.target.classList.add('selected');
                    document.getElementById('nextQuizStep').disabled = false;
                });
            });

            // Next step button
            const nextBtn = document.getElementById('nextQuizStep');
            nextBtn.addEventListener('click', () => {
                const selectedOption = modalContent.querySelector('.quiz-option.selected');
                if (selectedOption) {
                    answers.push(selectedOption.dataset.option);
                    currentStep++;
                    showQuizStep();
                }
            });

            this.showModal(modal);
        };

        showQuizStep();
    }

    previousQuizStep() {
        // Implementation for previous step
    }

    showQuizResults(answers) {
        const modal = document.getElementById('productModal');
        const modalContent = document.getElementById('modalContent');

        // Generate recommendations based on answers
        const recommendations = this.generateRecommendations(answers);

        modalContent.innerHTML = `
            <div class="quiz-results">
                <h3>Vos recommandations personnalisées</h3>
                <p>Basé sur vos réponses, nous avons sélectionné ces produits pour vous:</p>
                <div class="recommended-products">
                    ${recommendations.map(product => this.createProductCard(product).outerHTML).join('')}
                </div>
                <button class="btn-primary btn-large" onclick="callista.closeModal()">
                    Découvrir ma routine
                </button>
            </div>
        `;

        this.attachProductEventListeners();
    }

    generateRecommendations(answers) {
        // Simple recommendation logic based on answers
        const allProducts = [
            { id: 1, name: "Sérum Vitamine C", price: 299, image: "...", category: "soins" },
            { id: 2, name: "Crème Hydratante", price: 189, image: "...", category: "soins" },
            { id: 3, name: "Nettoyant Doux", price: 89, image: "...", category: "soins" }
        ];

        return allProducts.slice(0, 3); // Return first 3 products for demo
    }

    // Newsletter signup
    handleNewsletterSignup(e) {
        e.preventDefault();
        const email = e.target.querySelector('input[type="email"]').value;
        
        // Simulate API call
        setTimeout(() => {
            this.showNewsletterSuccess(email);
        }, 1000);
    }

    showNewsletterSuccess(email) {
        const notification = document.createElement('div');
        notification.className = 'newsletter-success';
        notification.innerHTML = `
            <div class="success-content">
                <i class="fas fa-check-circle"></i>
                <h4>Merci pour votre inscription!</h4>
                <p>Un email de confirmation a été envoyé à ${email}</p>
                <p>Utilisez le code <strong>BIENVENUE10</strong> pour 10% de réduction</p>
            </div>
        `;

        document.body.appendChild(notification);

        setTimeout(() => {
            notification.classList.add('show');
        }, 100);

        setTimeout(() => {
            notification.classList.remove('show');
            setTimeout(() => {
                document.body.removeChild(notification);
            }, 300);
        }, 5000);
    }

    // Utility functions
    getProductById(id) {
        // Simulate product database
        const products = [
            {
                id: 1,
                name: "Sérum Vitamine C 30%",
                price: 299,
                originalPrice: 399,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23FEF3C7' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%23F59E0B' font-size='16' font-family='Arial'%3ESérum Vit C%3C/text%3E%3C/svg%3E",
                description: "Éclat immédiat et taches pigmentaires",
                ingredients: ["Vitamine C 30%", "Acide hyaluronique", "Niacinamide"]
            }
        ];

        return products.find(p => p.id === id);
    }

    // Intersection Observer for animations
    setupIntersectionObserver() {
        const observerOptions = {
            threshold: 0.1,
            rootMargin: '0px 0px -50px 0px'
        };

        const observer = new IntersectionObserver((entries) => {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('animate-in');
                }
            });
        }, observerOptions);

        // Observe elements for animation
        document.querySelectorAll('.product-card, .category-card, .review-card').forEach(el => {
            observer.observe(el);
        });
    }

    // Personalization
    initializePersonalization() {
        if (this.currentUser) {
            this.showPersonalizedContent();
        }
    }

    showPersonalizedContent() {
        const userName = document.getElementById('userName');
        if (userName) {
            userName.textContent = this.currentUser.name || 'Chère cliente';
        }

        // Show personalized recommendations
        const recommendationsGrid = document.getElementById('recommendationsGrid');
        if (recommendationsGrid) {
            const recommendations = this.generatePersonalizedRecommendations();
            recommendationsGrid.innerHTML = recommendations.map(product => 
                this.createProductCard(product).outerHTML
            ).join('');
        }
    }

    generatePersonalizedRecommendations() {
        // Based on user's previous purchases and preferences
        return [
            {
                id: 7,
                name: "Masque Purifiant",
                price: 99,
                originalPrice: 149,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23DCFCE7' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%2316A34A' font-size='16' font-family='Arial'%3EMasque%3C/text%3E%3C/svg%3E",
                badge: "Recommandé",
                description: "Purifie et resserre les pores",
                ingredients: ["Argile blanche", "Charbon actif", "Aloe vera"]
            }
        ];
    }

    // Language and currency
    changeLanguage(lang) {
        // Implementation for language change
        console.log(`Changing language to: ${lang}`);
    }

    changeCurrency(currency) {
        // Implementation for currency change
        console.log(`Changing currency to: ${currency}`);
    }

    // Scroll to top
    scrollToTop() {
        window.scrollTo({
            top: 0,
            behavior: 'smooth'
        });
    }

    // Load more products (infinite scroll simulation)
    loadMoreProducts() {
        const moreProducts = [
            {
                id: 8,
                name: "Gel Nettoyant",
                category: "soins",
                price: 79,
                originalPrice: 119,
                rating: 4.9,
                reviews: 156,
                image: "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 250 250'%3E%3Crect fill='%23E0F2FE' width='250' height='250'/%3E%3Ctext x='50%25' y='50%25' text-anchor='middle' dy='.3em' fill='%230284C7' font-size='16' font-family='Arial'%3EGel Nettoyant%3C/text%3E%3C/svg%3E",
                badge: "Nouveau",
                description: "Nettoie en douceur sans agresser",
                ingredients: ["Aloe vera", "Chamomille", "Panthenol"]
            }
        ];

        this.displayProducts(moreProducts, true);
    }
}

// Initialize the website when DOM is loaded
document.addEventListener('DOMContentLoaded', () => {
    window.callista = new CallistaWebsite();
});

// Additional utility functions
const debounce = (func, wait) => {
    let timeout;
    return function executedFunction(...args) {
        const later = () => {
            clearTimeout(timeout);
            func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
    };
};

// Service Worker for PWA functionality
if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
        navigator.serviceWorker.register('/sw.js')
            .then(registration => {
                console.log('SW registered: ', registration);
            })
            .catch(registrationError => {
                console.log('SW registration failed: ', registrationError);
            });
    });
}
