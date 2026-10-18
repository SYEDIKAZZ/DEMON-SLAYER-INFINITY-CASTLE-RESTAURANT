# DEMON-SLAYER-INFINITY-CASTLE-RESTAURANT
Welcome to the digital gateway of Infinity Castle Dining,Pakistan’s premier anime-themed restaurant located on Bhatta No 1,Ahmedpur Road Opposite Aleena Hospital , BWP. Designed to bring the dark fantasy universe of Demon Slayer into the culinary realm, this interactive web platform merges cutting-edge web design, real-time 3D visualisation
<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Infinity Castle Dining | 3D Interactive Experience</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;800;900&family=Noto+Sans+JP:wght@400;700;900&family=Plus+Jakarta+Sans:wght@300;400;600;700&display=swap" rel="stylesheet">
    <!-- Three.js and OrbitControls -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            gold: '#E5A93C',
                            red: '#DC2626',
                            crimson: '#991B1B',
                            dark: '#070509',
                            panel: '#130D18',
                            wood: '#231815'
                        }
                    },
                    fontFamily: {
                        cinzel: ['Cinzel', 'serif'],
                        japanese: ['Noto Sans JP', 'sans-serif'],
                        sans: ['Plus Jakarta Sans', 'sans-serif']
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background-color: #070509;
            color: #F3F4F6;
            overflow-x: hidden;
            user-select: none;
        }
        
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #070509;
        }
        ::-webkit-scrollbar-thumb {
            background: #DC2626;
            border-radius: 3px;
        }

        .glow-red {
            box-shadow: 0 0 20px rgba(220, 38, 38, 0.4);
        }
        .glow-gold {
            box-shadow: 0 0 20px rgba(229, 169, 60, 0.35);
        }

        #wisteria-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            pointer-events: none;
            z-index: 1;
        }

        .modal-blur {
            backdrop-filter: blur(14px);
        }

        input[type=range] {
            accent-color: #DC2626;
        }

        /* 3D Hotspot pulse animation */
        .hotspot-pulse {
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(229, 169, 60, 0.7); }
            70% { transform: scale(1.1); box-shadow: 0 0 0 10px rgba(229, 169, 60, 0); }
            100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(229, 169, 60, 0); }
        }
    </style>
</head>
<body class="font-sans antialiased text-gray-200 bg-brand-dark">

    <!-- Wisteria & Embers Background Canvas -->
    <canvas id="wisteria-canvas"></canvas>

    <!-- NAVIGATION BAR -->
    <nav class="fixed top-0 left-0 right-0 z-40 bg-brand-dark/85 backdrop-blur-md border-b border-red-900/40 px-4 lg:px-8 py-3 transition-all duration-300">
        <div class="max-w-7xl mx-auto flex items-center justify-between">
            <div class="flex items-center gap-3 cursor-pointer" onclick="reset3DCamera()">
                <div class="w-10 h-10 rounded-full bg-gradient-to-br from-red-600 to-black border border-red-500/50 flex items-center justify-center shadow-lg">
                    <span class="font-japanese text-brand-gold font-bold text-lg">鬼</span>
                </div>
                <div>
                    <h1 class="font-cinzel text-lg md:text-xl font-bold tracking-wider text-white">INFINITY CASTLE</h1>
                    <p class="text-[10px] text-red-500 tracking-widest font-japanese uppercase">無限城 DINING • BAHAWALPUR</p>
                </div>
            </div>

            <!-- Action Buttons -->
            <div class="flex items-center gap-2 md:gap-3">
                <button onclick="toggleAudio()" id="audio-btn" class="p-2.5 rounded-xl bg-brand-panel border border-red-900/40 text-gray-300 hover:text-brand-gold transition" title="Toggle Sound">
                    <i class="fa-solid fa-volume-xmark text-base" id="audio-icon"></i>
                </button>
                
                <button onclick="openMenuModal()" class="hidden md:flex items-center gap-2 px-4 py-2 rounded-xl bg-gradient-to-r from-red-950 to-red-800 hover:from-red-900 hover:to-red-700 border border-red-500/40 text-white text-xs font-bold transition shadow-md">
                    <i class="fa-solid fa-utensils text-brand-gold"></i>
                    <span>Menu</span>
                </button>

                <button onclick="openReservationModal()" class="flex items-center gap-2 px-4 py-2 rounded-xl bg-brand-gold hover:bg-yellow-500 text-black text-xs font-extrabold transition shadow-lg glow-gold">
                    <i class="fa-solid fa-torii-gate"></i>
                    <span class="hidden sm:inline">Book Table</span>
                </button>

                <button onclick="toggleCartModal()" class="relative p-2.5 rounded-xl bg-brand-panel border border-red-900/40 text-white hover:border-brand-gold/50 transition">
                    <i class="fa-solid fa-basket-shopping text-base text-brand-gold"></i>
                    <span id="cart-badge" class="absolute -top-1.5 -right-1.5 bg-red-600 text-white text-[10px] font-bold w-5 h-5 rounded-full flex items-center justify-center hidden">0</span>
                </button>
            </div>
        </div>
    </nav>

    <!-- MAIN VIEWPORT CONTAINER -->
    <main class="relative z-10 pt-20 min-h-screen flex flex-col justify-between">
        
        <!-- HEADER COMMAND BANNER -->
        <div class="px-4 py-3 max-w-7xl mx-auto w-full">
            <div class="flex flex-col md:flex-row items-center justify-between gap-4 bg-brand-panel/80 border border-red-900/40 p-4 rounded-2xl backdrop-blur-md shadow-xl">
                
                <div>
                    <div class="inline-flex items-center gap-2 px-3 py-0.5 rounded-full bg-red-950 border border-red-600/50 text-red-400 text-[11px] font-semibold uppercase tracking-wider mb-1">
                        <i class="fa-solid fa-terminal text-brand-gold"></i> Command Executed: /explodeview
                    </div>
                    <h2 class="text-xl md:text-2xl font-cinzel font-bold text-white flex items-center gap-2">
                        <span>3D Exploded View Architecture</span>
                        <span class="text-xs font-japanese font-normal text-gray-400">(無限城分解図)</span>
                    </h2>
                </div>

                <!-- Explode Control Bar -->
                <div class="flex items-center gap-3 w-full md:w-auto bg-black/60 px-4 py-2 rounded-xl border border-red-900/40">
                    <i class="fa-solid fa-cubes-stacked text-brand-gold text-sm"></i>
                    <span class="text-xs font-bold tracking-wider uppercase text-gray-300">Explode</span>
                    <input id="explode-slider" type="range" min="0" max="100" value="40" class="w-28 sm:w-40 h-2 bg-gray-800 rounded-lg appearance-none cursor-pointer">
                    <span id="explode-val" class="text-xs font-mono text-brand-gold font-bold w-8">40%</span>
                    <button onclick="toggleExplodeState()" id="explode-toggle-btn" class="text-xs px-3 py-1.5 rounded-lg bg-red-900/80 hover:bg-red-700 text-white font-bold transition border border-red-500/40">
                        OFF
                    </button>
                </div>
            </div>
        </div>

        <!-- 3D CANVASES & VIEWPORT -->
        <div class="relative max-w-7xl mx-auto w-full flex-1 min-h-[520px] md:min-h-[620px] px-4 my-2">
            
            <!-- 3D WebGL Canvas Container -->
            <div id="three-container" class="w-full h-full min-h-[520px] md:min-h-[620px] rounded-3xl overflow-hidden border border-red-900/40 relative shadow-2xl bg-gradient-to-b from-brand-dark via-brand-panel to-black">
                
                <!-- Overlay Instructions -->
                <div class="absolute top-4 left-4 z-20 pointer-events-none bg-black/70 backdrop-blur-md px-3.5 py-2 rounded-xl border border-red-900/40 text-xs text-gray-300 flex items-center gap-2">
                    <i class="fa-solid fa-hand-pointer text-brand-gold animate-bounce"></i>
                    <span>Drag to Orbit | Scroll to Zoom | Click Layers or Hotspots</span>
                </div>

                <!-- Camera Quick Presets -->
                <div class="absolute top-4 right-4 z-20 flex flex-col gap-2">
                    <button onclick="setCameraView('iso')" class="px-3 py-1.5 rounded-xl bg-black/80 hover:bg-red-900/50 border border-red-900/40 text-xs text-gray-200 font-semibold transition">
                        <i class="fa-solid fa-cube text-brand-gold mr-1"></i> 3D ISO
                    </button>
                    <button onclick="setCameraView('front')" class="px-3 py-1.5 rounded-xl bg-black/80 hover:bg-red-900/50 border border-red-900/40 text-xs text-gray-200 font-semibold transition">
                        <i class="fa-solid fa-eye text-brand-gold mr-1"></i> Front
                    </button>
                    <button onclick="setCameraView('top')" class="px-3 py-1.5 rounded-xl bg-black/80 hover:bg-red-900/50 border border-red-900/40 text-xs text-gray-200 font-semibold transition">
                        <i class="fa-solid fa-arrows-up-to-line text-brand-gold mr-1"></i> Top View
                    </button>
                </div>

                <!-- Dynamic Layer Info Overlay Card -->
                <div id="floor-card" class="absolute bottom-6 left-6 right-6 md:right-auto md:max-w-md z-20 bg-brand-panel/95 backdrop-blur-xl border border-brand-gold/50 p-5 rounded-2xl shadow-2xl hidden transition-all duration-300">
                    <div class="flex justify-between items-start mb-2">
                        <div>
                            <span id="floor-tag" class="text-[10px] font-bold uppercase tracking-widest px-2.5 py-0.5 rounded bg-red-950 text-brand-gold border border-red-800">FLOOR LAYER</span>
                            <h3 id="floor-title" class="font-cinzel text-lg font-bold text-white mt-1">Hashira Grand VIP Lounge</h3>
                        </div>
                        <button onclick="hideFloorCard()" class="text-gray-400 hover:text-white p-1"><i class="fa-solid fa-xmark"></i></button>
                    </div>
                    <p id="floor-desc" class="text-xs text-gray-300 leading-relaxed mb-4">
                        Custom Demon Slayer anime seating, glowing wisteria blossoms, and table-side melted cheese sizzler service.
                    </p>
                    <div class="flex items-center gap-2">
                        <button onclick="openMenuModal()" class="flex-1 py-2 rounded-xl bg-red-800 hover:bg-red-700 text-white text-xs font-bold transition flex items-center justify-center gap-1.5">
                            <i class="fa-solid fa-utensils text-brand-gold"></i> View Menu
                        </button>
                        <button onclick="openReservationModal()" class="flex-1 py-2 rounded-xl bg-brand-gold hover:bg-yellow-500 text-black text-xs font-bold transition flex items-center justify-center gap-1.5">
                            <i class="fa-solid fa-chair"></i> Reserve Floor
                        </button>
                    </div>
                </div>

            </div>
        </div>

        <!-- QUICK ACTION CARDS -->
        <div class="max-w-7xl mx-auto w-full px-4 py-6 grid grid-cols-1 md:grid-cols-3 gap-4">
            
            <div onclick="openMenuModal()" class="group cursor-pointer bg-brand-panel/80 hover:bg-brand-panel border border-red-900/40 hover:border-brand-gold/50 p-5 rounded-2xl transition duration-300 flex items-center gap-4 shadow-lg">
                <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-red-950 to-black border border-red-500/40 flex items-center justify-center text-brand-gold text-2xl group-hover:scale-110 transition">
                    <i class="fa-solid fa-pizza-slice"></i>
                </div>
                <div>
                    <h4 class="font-cinzel font-bold text-white text-base group-hover:text-brand-gold transition">Black Charcoal Pizza</h4>
                    <p class="text-xs text-gray-400">Signature dark crust with custom Demon Slayer artwork</p>
                </div>
            </div>

            <div onclick="openReservationModal()" class="group cursor-pointer bg-brand-panel/80 hover:bg-brand-panel border border-red-900/40 hover:border-brand-gold/50 p-5 rounded-2xl transition duration-300 flex items-center gap-4 shadow-lg">
                <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-yellow-600/30 to-black border border-brand-gold/40 flex items-center justify-center text-brand-gold text-2xl group-hover:scale-110 transition">
                    <i class="fa-solid fa-torii-gate"></i>
                </div>
                <div>
                    <h4 class="font-cinzel font-bold text-white text-base group-hover:text-brand-gold transition">Hashira VIP Lounges</h4>
                    <p class="text-xs text-gray-400">Book themed dining spaces for groups & family</p>
                </div>
            </div>

            <div onclick="scrollToMap()" class="group cursor-pointer bg-brand-panel/80 hover:bg-brand-panel border border-red-900/40 hover:border-brand-gold/50 p-5 rounded-2xl transition duration-300 flex items-center gap-4 shadow-lg">
                <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-red-950 to-black border border-red-500/40 flex items-center justify-center text-red-500 text-2xl group-hover:scale-110 transition">
                    <i class="fa-solid fa-map-location-dot"></i>
                </div>
                <div>
                    <h4 class="font-cinzel font-bold text-white text-base group-hover:text-brand-gold transition">Bahawalpur Location</h4>
                    <p class="text-xs text-gray-400">Bhatta No 1, Ahmedpur Road, Opp Aleena Hospital</p>
                </div>
            </div>

        </div>

        <!-- LOCATION & FIXED GOOGLE MAPS SECTION -->
        <section id="location-section" class="max-w-7xl mx-auto w-full px-4 py-8 my-4">
            <div class="bg-brand-panel/80 border border-red-900/40 rounded-3xl p-6 md:p-8 backdrop-blur-md shadow-2xl">
                <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-center">
                    
                    <div class="lg:col-span-5 space-y-4">
                        <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-red-950 border border-red-600/50 text-brand-gold text-xs font-semibold tracking-wider">
                            <i class="fa-solid fa-location-dot"></i> PHYSICAL LOCATION
                        </div>
                        <h2 class="font-cinzel text-2xl md:text-3xl font-bold text-white">Visit Infinity Castle</h2>
                        <p class="text-xs md:text-sm text-gray-300 leading-relaxed">
                            Step into the realm of Demon Slayer in Bahawalpur. Experience wisteria blossom decor, dark timber architecture, and flame-grilled dishes.
                        </p>
                        
                        <div class="space-y-3 pt-2">
                            <div class="flex items-start gap-3 text-xs text-gray-300">
                                <i class="fa-solid fa-map-pin text-red-500 text-base mt-0.5"></i>
                                <div>
                                    <strong class="text-white block">Address:</strong>
                                    Bhatta No 1, Ahmedpur Road, Opposite Aleena Hospital, Bahawalpur, Punjab, Pakistan
                                </div>
                            </div>

                            <div class="flex items-center gap-3 text-xs text-gray-300">
                                <i class="fa-solid fa-clock text-brand-gold text-base"></i>
                                <div>
                                    <strong class="text-white block">Hours:</strong>
                                    Daily: 4:00 PM - 1:00 AM
                                </div>
                            </div>

                            <div class="flex items-center gap-3 text-xs text-gray-300">
                                <i class="fa-solid fa-phone text-green-500 text-base"></i>
                                <div>
                                    <strong class="text-white block">Contact / Queries:</strong>
                                    +92 300 1234567
                                </div>
                            </div>
                        </div>

                        <div class="pt-3 flex flex-wrap gap-3">
                            <a href="https://maps.google.com/?q=Ahmedpur+Road+Bahawalpur+Aleena+Hospital" target="_blank" class="px-5 py-2.5 rounded-xl bg-red-800 hover:bg-red-700 text-white font-bold text-xs transition inline-flex items-center gap-2 shadow-lg glow-red">
                                <i class="fa-solid fa-diamond-turn-right"></i> Open Directions
                            </a>
                            <button onclick="copyAddress()" class="px-4 py-2.5 rounded-xl bg-black/60 hover:bg-black border border-red-900/40 text-gray-300 hover:text-white font-semibold text-xs transition">
                                <i class="fa-solid fa-copy mr-1"></i> Copy Address
                            </button>
                        </div>
                    </div>

                    <!-- Embed Map Frame -->
                    <div class="lg:col-span-7 h-[350px] rounded-2xl overflow-hidden border border-red-900/50 shadow-inner relative bg-black">
                        <iframe 
                            class="w-full h-full filter brightness-90 contrast-110" 
                            src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d13904.580231923018!2d71.6600!3d29.3800!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x393b90f4236371cb%3A0x892a088df6721fb0!2sAhmedpur%20Rd%2C%20Bahawalpur%2C%20Punjab%2C%20Pakistan!5e0!3m2!1sen!2s!4v1700000000000!5m2!1sen!2s" 
                            style="border:0;" 
                            allowfullscreen="" 
                            loading="lazy">
                        </iframe>
                    </div>

                </div>
            </div>
        </section>

        <!-- FOOTER -->
        <footer class="border-t border-red-900/40 bg-black/90 py-6 px-4 text-center text-xs text-gray-500">
            <div class="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
                <p>© 2026 Infinity Castle Dining Bahawalpur. All anime themes inspired by Demon Slayer.</p>
                <div class="flex items-center gap-6 text-gray-400">
                    <a href="https://www.instagram.com/infinitycastledining" target="_blank" class="hover:text-brand-gold"><i class="fa-brands fa-instagram text-lg"></i></a>
                    <a href="#" class="hover:text-brand-gold"><i class="fa-brands fa-facebook text-lg"></i></a>
                    <a href="#" class="hover:text-brand-gold"><i class="fa-brands fa-tiktok text-lg"></i></a>
                </div>
            </div>
        </footer>

    </main>

    <!-- MENU MODAL -->
    <div id="menu-modal" class="fixed inset-0 z-50 modal-blur bg-black/80 flex items-center justify-center p-4 hidden">
        <div class="bg-brand-panel border border-red-900/60 rounded-3xl max-w-4xl w-full max-h-[90vh] flex flex-col overflow-hidden shadow-2xl">
            
            <div class="p-6 border-b border-red-900/40 flex justify-between items-center bg-black/40">
                <div>
                    <h3 class="font-cinzel text-xl font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-scroll text-brand-gold"></i> Castle Menu & Specialties
                    </h3>
                    <p class="text-xs text-gray-400">Order theme charcoal pizzas, lava burgers & continental steaks</p>
                </div>
                <button onclick="closeMenuModal()" class="w-9 h-9 rounded-full bg-black/60 border border-red-900/40 text-gray-400 hover:text-white flex items-center justify-center">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <!-- Categories -->
            <div class="flex items-center gap-2 px-6 py-3 border-b border-red-900/30 overflow-x-auto bg-black/20">
                <button onclick="filterMenu('all', event)" class="menu-tab active px-4 py-1.5 rounded-xl text-xs font-bold transition bg-red-800 text-white border border-red-500/40">All Items</button>
                <button onclick="filterMenu('pizza', event)" class="menu-tab px-4 py-1.5 rounded-xl text-xs font-bold transition bg-black/50 text-gray-400 hover:text-white border border-red-900/30">Black Pizzas</button>
                <button onclick="filterMenu('burger', event)" class="menu-tab px-4 py-1.5 rounded-xl text-xs font-bold transition bg-black/50 text-gray-400 hover:text-white border border-red-900/30">Lava Burgers</button>
                <button onclick="filterMenu('steaks', event)" class="menu-tab px-4 py-1.5 rounded-xl text-xs font-bold transition bg-black/50 text-gray-400 hover:text-white border border-red-900/30">Continental Steaks</button>
                <button onclick="filterMenu('drinks', event)" class="menu-tab px-4 py-1.5 rounded-xl text-xs font-bold transition bg-black/50 text-gray-400 hover:text-white border border-red-900/30">Elixirs & Tea</button>
            </div>

            <!-- Items -->
            <div id="menu-grid" class="p-6 grid grid-cols-1 md:grid-cols-2 gap-4 overflow-y-auto max-h-[60vh]">
            </div>

        </div>
    </div>

    <!-- CART MODAL -->
    <div id="cart-modal" class="fixed inset-0 z-50 modal-blur bg-black/80 flex items-center justify-end p-0 md:p-4 hidden">
        <div class="bg-brand-panel border-l md:border border-red-900/60 w-full max-w-md h-full md:h-[90vh] md:rounded-3xl flex flex-col justify-between shadow-2xl">
            
            <div class="p-5 border-b border-red-900/40 flex justify-between items-center bg-black/40">
                <h3 class="font-cinzel text-lg font-bold text-white flex items-center gap-2">
                    <i class="fa-solid fa-basket-shopping text-brand-gold"></i> Your Feast Basket
                </h3>
                <button onclick="toggleCartModal()" class="text-gray-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
            </div>

            <div id="cart-items" class="p-5 overflow-y-auto flex-1 space-y-3">
            </div>

            <div class="p-5 border-t border-red-900/40 bg-black/60 space-y-3">
                <div class="flex justify-between text-xs text-gray-300">
                    <span>Subtotal</span>
                    <span id="cart-subtotal" class="font-mono font-bold text-white">PKR 0</span>
                </div>
                <div class="flex justify-between text-xs text-gray-300">
                    <span>Tax (GST 16%)</span>
                    <span id="cart-tax" class="font-mono text-gray-400">PKR 0</span>
                </div>
                <div class="flex justify-between text-base font-bold text-white pt-2 border-t border-red-900/30">
                    <span>Total Amount</span>
                    <span id="cart-total" class="font-mono text-brand-gold text-lg">PKR 0</span>
                </div>
                
                <button onclick="checkoutOrder()" class="w-full py-3 rounded-xl bg-gradient-to-r from-red-800 to-red-600 hover:from-red-700 hover:to-red-500 text-white font-bold text-xs transition shadow-lg glow-red">
                    Confirm Order & Send to Kitchen
                </button>
            </div>

        </div>
    </div>

    <!-- RESERVATION MODAL -->
    <div id="reservation-modal" class="fixed inset-0 z-50 modal-blur bg-black/80 flex items-center justify-center p-4 hidden">
        <div class="bg-brand-panel border border-red-900/60 rounded-3xl max-w-lg w-full p-6 md:p-8 shadow-2xl relative">
            <button onclick="closeReservationModal()" class="absolute top-5 right-5 text-gray-400 hover:text-white">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>

            <div class="text-center mb-6">
                <span class="text-[10px] font-bold text-brand-gold uppercase tracking-widest px-3 py-1 bg-red-950 rounded-full border border-red-800">VIP RESERVATIONS</span>
                <h3 class="font-cinzel text-2xl font-bold text-white mt-2">Book a Hashira Table</h3>
                <p class="text-xs text-gray-400">Reserve your spot at Infinity Castle Dining Bahawalpur</p>
            </div>

            <form onsubmit="submitReservation(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-gray-300 mb-1">Full Name</label>
                    <input id="res-name" type="text" required placeholder="Tanjiro Kamado" class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-gray-300 mb-1">Date</label>
                        <input type="date" required class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-300 mb-1">Time Slot</label>
                        <select required class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                            <option value="17:00">5:00 PM - 6:30 PM</option>
                            <option value="19:00">7:00 PM - 8:30 PM</option>
                            <option value="21:00">9:00 PM - 10:30 PM</option>
                            <option value="23:00">11:00 PM - 12:30 AM</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-gray-300 mb-1">Guests</label>
                        <select required class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                            <option value="2">2 Persons</option>
                            <option value="4">4 Persons</option>
                            <option value="6">6 Persons (Family)</option>
                            <option value="10">10+ Persons (Corps Group)</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-300 mb-1">Preferred Tier</label>
                        <select id="res-floor" required class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                            <option value="Floor 2: Demon Slayer Lounge">Floor 2: Hashira Lounge</option>
                            <option value="Floor 1: Main Dining">Floor 1: Main Dining</option>
                            <option value="Floor 3: Pagoda Roof">Floor 3: Pagoda Roof</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-gray-300 mb-1">Phone Number (WhatsApp)</label>
                    <input type="tel" required placeholder="+92 300 0000000" class="w-full px-4 py-2.5 rounded-xl bg-black/60 border border-red-900/40 text-white text-xs focus:outline-none focus:border-brand-gold">
                </div>

                <button type="submit" class="w-full py-3 rounded-xl bg-brand-gold hover:bg-yellow-500 text-black font-extrabold text-xs transition shadow-lg glow-gold mt-2">
                    Confirm Reservation Pass
                </button>
            </form>
        </div>
    </div>

    <!-- DIGITAL PASS TICKET MODAL -->
    <div id="ticket-modal" class="fixed inset-0 z-50 modal-blur bg-black/80 flex items-center justify-center p-4 hidden">
        <div class="bg-brand-panel border border-brand-gold/60 rounded-3xl max-w-md w-full p-6 text-center shadow-2xl relative">
            <div class="w-16 h-16 bg-brand-gold/20 rounded-full flex items-center justify-center text-brand-gold text-3xl mx-auto mb-3">
                <i class="fa-solid fa-circle-check"></i>
            </div>
            <h3 class="font-cinzel text-xl font-bold text-white">Pass Verified!</h3>
            <p class="text-xs text-gray-400 mt-1">Present this ticket upon arrival at Infinity Castle Dining.</p>

            <div class="bg-black/70 border border-dashed border-red-900/60 p-4 rounded-2xl my-5 text-left space-y-2">
                <div class="flex justify-between text-xs">
                    <span class="text-gray-400">Pass Code:</span>
                    <span id="ticket-code" class="font-mono font-bold text-brand-gold">INF-9921-BWP</span>
                </div>
                <div class="flex justify-between text-xs">
                    <span class="text-gray-400">Guest Name:</span>
                    <span id="ticket-name" class="text-white font-bold">Tanjiro Kamado</span>
                </div>
                <div class="flex justify-between text-xs">
                    <span class="text-gray-400">Reserved Area:</span>
                    <span id="ticket-floor" class="text-white">Floor 2: Hashira Lounge</span>
                </div>
            </div>

            <button onclick="closeTicketModal()" class="w-full py-2.5 rounded-xl bg-red-800 hover:bg-red-700 text-white font-bold text-xs transition">
                Done & Close
            </button>
        </div>
    </div>

    <!-- JAVASCRIPT LOGIC -->
    <script>
        /* ----------------------------------------------------
           1. MENU & CART DATA
        ---------------------------------------------------- */
        const menuData = [
            { id: 1, name: "Upper Moon Black Pizza", category: "pizza", price: 1850, desc: "Charcoal infused dark crust topped with custom Demon Slayer artwork, smoked chicken & melted mozzarella.", icon: "fa-pizza-slice" },
            { id: 2, name: "Hashira Cheese Lava Volcano Burger", category: "burger", price: 950, desc: "Double grilled beef patty smothered in hot molten cheddar poured right at your table.", icon: "fa-burger" },
            { id: 3, name: "Tarragon Flame Steak", category: "steaks", price: 2100, desc: "Prime filet mignon served with spicy tarragon cream sauce and garlic fries.", icon: "fa-drumstick-bite" },
            { id: 4, name: "Stuffed Mushroom Sizzler", category: "steaks", price: 1950, desc: "Sizzling chicken breast stuffed with jalapenos, cheese, & herbs.", icon: "fa-fire" },
            { id: 5, name: "Muzan Crimson Zinger Burger", category: "burger", price: 750, desc: "Crispy chicken fillet with red chili aioli & fresh purple cabbage slaw.", icon: "fa-burger" },
            { id: 6, name: "Wisteria Blossom Elixir", category: "drinks", price: 450, desc: "Purple passionfruit mocktail with sparkling mint & edible glitter.", icon: "fa-glass-water" },
            { id: 7, name: "Cheese Overload Fries", category: "burger", price: 650, desc: "Fries drowned in hot cheese blend, minced beef & jalapenos.", icon: "fa-bowl-food" }
        ];

        let cart = [];

        function renderMenu(items) {
            const grid = document.getElementById('menu-grid');
            grid.innerHTML = items.map(item => `
                <div class="bg-black/50 border border-red-900/30 rounded-2xl p-4 flex justify-between items-center hover:border-red-600/50 transition">
                    <div class="flex items-start gap-3">
                        <div class="w-12 h-12 rounded-xl bg-red-950 border border-red-800/50 flex items-center justify-center text-brand-gold text-xl flex-shrink-0">
                            <i class="fa-solid ${item.icon}"></i>
                        </div>
                        <div>
                            <h4 class="font-cinzel font-bold text-white text-xs md:text-sm">${item.name}</h4>
                            <p class="text-[11px] text-gray-400 line-clamp-2 mt-0.5">${item.desc}</p>
                            <span class="text-xs font-mono font-bold text-brand-gold mt-1 block">PKR ${item.price}</span>
                        </div>
                    </div>
                    <button onclick="addToCart(${item.id})" class="px-3 py-1.5 rounded-xl bg-red-800 hover:bg-red-700 text-white text-xs font-bold transition flex items-center gap-1 shadow">
                        <i class="fa-solid fa-plus"></i> Add
                    </button>
                </div>
            `).join('');
        }

        function filterMenu(category, event) {
            document.querySelectorAll('.menu-tab').forEach(tab => {
                tab.classList.remove('bg-red-800', 'text-white');
                tab.classList.add('bg-black/50', 'text-gray-400');
            });
            event.target.classList.add('bg-red-800', 'text-white');

            if (category === 'all') {
                renderMenu(menuData);
            } else {
                renderMenu(menuData.filter(i => i.category === category));
            }
        }

        function addToCart(id) {
            playSlashSound();
            const item = menuData.find(i => i.id === id);
            const existing = cart.find(i => i.id === id);
            if (existing) {
                existing.qty += 1;
            } else {
                cart.push({ ...item, qty: 1 });
            }
            updateCartUI();
        }

        function updateCartUI() {
            const badge = document.getElementById('cart-badge');
            const cartList = document.getElementById('cart-items');
            
            const totalQty = cart.reduce((sum, i) => sum + i.qty, 0);
            if (totalQty > 0) {
                badge.innerText = totalQty;
                badge.classList.remove('hidden');
            } else {
                badge.classList.add('hidden');
            }

            if (cart.length === 0) {
                cartList.innerHTML = `<p class="text-center text-xs text-gray-500 py-10">Your basket is empty. Add dishes from the menu!</p>`;
            } else {
                cartList.innerHTML = cart.map(item => `
                    <div class="bg-black/40 border border-red-900/30 p-3 rounded-xl flex justify-between items-center">
                        <div>
                            <h5 class="text-xs font-bold text-white">${item.name}</h5>
                            <span class="text-[11px] font-mono text-brand-gold">PKR ${item.price * item.qty}</span>
                        </div>
                        <div class="flex items-center gap-2 bg-black px-2 py-1 rounded-lg border border-red-900/40">
                            <button onclick="changeQty(${item.id}, -1)" class="text-xs text-gray-400 hover:text-white">-</button>
                            <span class="text-xs font-mono text-white font-bold w-4 text-center">${item.qty}</span>
                            <button onclick="changeQty(${item.id}, 1)" class="text-xs text-gray-400 hover:text-white">+</button>
                        </div>
                    </div>
                `).join('');
            }

            const subtotal = cart.reduce((sum, i) => sum + (i.price * i.qty), 0);
            const tax = Math.round(subtotal * 0.16);
            const total = subtotal + tax;

            document.getElementById('cart-subtotal').innerText = `PKR ${subtotal}`;
            document.getElementById('cart-tax').innerText = `PKR ${tax}`;
            document.getElementById('cart-total').innerText = `PKR ${total}`;
        }

        function changeQty(id, delta) {
            const item = cart.find(i => i.id === id);
            if (!item) return;
            item.qty += delta;
            if (item.qty <= 0) cart = cart.filter(i => i.id !== id);
            updateCartUI();
        }

        function checkoutOrder() {
            if (cart.length === 0) return alert('Please add items to your basket first!');
            playSlashSound();
            alert('Order transmitted to Infinity Castle Kitchen!');
            cart = [];
            updateCartUI();
            toggleCartModal();
        }

        /* ----------------------------------------------------
           2. MODAL & UI ACTIONS
        ---------------------------------------------------- */
        function openMenuModal() { renderMenu(menuData); document.getElementById('menu-modal').classList.remove('hidden'); }
        function closeMenuModal() { document.getElementById('menu-modal').classList.add('hidden'); }
        function toggleCartModal() { document.getElementById('cart-modal').classList.toggle('hidden'); }
        function openReservationModal() { document.getElementById('reservation-modal').classList.remove('hidden'); }
        function closeReservationModal() { document.getElementById('reservation-modal').classList.add('hidden'); }
        function closeTicketModal() { document.getElementById('ticket-modal').classList.add('hidden'); }

        function submitReservation(e) {
            e.preventDefault();
            const name = document.getElementById('res-name').value || "Guest";
            const floor = document.getElementById('res-floor').value;
            
            document.getElementById('ticket-name').innerText = name;
            document.getElementById('ticket-floor').innerText = floor;
            document.getElementById('ticket-code').innerText = "INF-" + Math.floor(1000 + Math.random() * 9000) + "-BWP";

            closeReservationModal();
            document.getElementById('ticket-modal').classList.remove('hidden');
            playSlashSound();
        }

        function copyAddress() {
            navigator.clipboard.writeText("Bhatta No 1, Ahmedpur Road, Opposite Aleena Hospital, Bahawalpur, Punjab, Pakistan");
            alert("Address copied to clipboard!");
        }

        function scrollToMap() {
            document.getElementById('location-section').scrollIntoView({ behavior: 'smooth' });
        }

        /* ----------------------------------------------------
           3. THREE.JS 3D EXPLODED VIEW ENGINE
        ---------------------------------------------------- */
        let scene, camera, renderer, controls;
        let buildingLayers = [];
        let explodeFactor = 0.4;
        let isExploded = true;

        const layerConfigs = [
            { id: 0, tag: "FLOOR 1", title: "Traditional Dining & Entrance", desc: "Dark wooden entrance hall with glowing lanterns and open grill station.", color: 0x2A1B18, height: 2.2 },
            { id: 1, tag: "FLOOR 2", title: "Hashira VIP Lounge & Booths", desc: "Anime character booths, wisteria ceilings, and melted cheese burger service.", color: 0x38191C, height: 2.2 },
            { id: 2, tag: "FLOOR 3", title: "Upper Demon Corps Sanctuary", desc: "Private family dining spaces decorated with glowing neon Japanese Kanji artwork.", color: 0x4A1215, height: 2.2 },
            { id: 3, tag: "ROOF TIER", title: "Infinity Castle Pagoda & Tower", desc: "Traditional dark curved roof tiles with illuminated crest lanterns.", color: 0x1A0A0C, height: 1.8 }
        ];

        function init3D() {
            const container = document.getElementById('three-container');
            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2(0x070509, 0.02);

            camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 1000);
            camera.position.set(16, 12, 20);

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(container.clientWidth, container.clientHeight);
            renderer.setPixelRatio(window.devicePixelRatio);
            renderer.shadowMap.enabled = true;
            container.appendChild(renderer.domElement);

            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.maxPolarAngle = Math.PI / 2 + 0.05;

            // LIGHTING
            const ambientLight = new THREE.AmbientLight(0xfff0dd, 0.9);
            scene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xffa500, 1.2);
            dirLight.position.set(12, 25, 15);
            dirLight.castShadow = true;
            scene.add(dirLight);

            const crimsonPoint = new THREE.PointLight(0xdc2626, 2.5, 25);
            crimsonPoint.position.set(0, 6, 0);
            scene.add(crimsonPoint);

            // BUILD ARCHITECTURAL LAYERS
            buildInfinityCastle();

            // RAYCASTING FOR INTERACTIVE FLOORS
            const raycaster = new THREE.Raycaster();
            const mouse = new THREE.Vector2();

            renderer.domElement.addEventListener('pointerdown', (e) => {
                const rect = renderer.domElement.getBoundingClientRect();
                mouse.x = ((e.clientX - rect.left) / container.clientWidth) * 2 - 1;
                mouse.y = -((e.clientY - rect.top) / container.clientHeight) * 2 + 1;

                raycaster.setFromCamera(mouse, camera);
                const intersects = raycaster.intersectObjects(scene.children, true);

                if (intersects.length > 0) {
                    let clickedObj = intersects[0].object;
                    while (clickedObj.parent && !clickedObj.userData.title) {
                        clickedObj = clickedObj.parent;
                    }
                    if (clickedObj.userData.title) {
                        displayFloorData(clickedObj.userData);
                        playSlashSound();
                    }
                }
            });

            window.addEventListener('resize', onWindowResize);
            animate();
        }

        function buildInfinityCastle() {
            layerConfigs.forEach((cfg, index) => {
                const group = new THREE.Group();
                group.userData = cfg;

                // Base Floor Slab
                const slabGeo = new THREE.BoxGeometry(9, 0.5, 9);
                const slabMat = new THREE.MeshStandardMaterial({ color: cfg.color, roughness: 0.5, metalness: 0.3 });
                const slab = new THREE.Mesh(slabGeo, slabMat);
                slab.castShadow = true;
                slab.receiveShadow = true;
                group.add(slab);

                if (index < 3) {
                    // Pillars
                    const pillarGeo = new THREE.BoxGeometry(0.4, cfg.height, 0.4);
                    const pillarMat = new THREE.MeshStandardMaterial({ color: 0x8B0000 });
                    const positions = [[-4, cfg.height/2, -4], [4, cfg.height/2, -4], [-4, cfg.height/2, 4], [4, cfg.height/2, 4]];
                    positions.forEach(pos => {
                        const pillar = new THREE.Mesh(pillarGeo, pillarMat);
                        pillar.position.set(...pos);
                        group.add(pillar);
                    });

                    // Interior Walls with Amber Light
                    const wallGeo = new THREE.BoxGeometry(8.4, cfg.height, 0.1);
                    const wallMat = new THREE.MeshStandardMaterial({ color: 0xE5A93C, transparent: true, opacity: 0.35 });
                    const backWall = new THREE.Mesh(wallGeo, wallMat);
                    backWall.position.set(0, cfg.height/2, -4.1);
                    group.add(backWall);

                    // Central Lantern Glow
                    const lanternGeo = new THREE.SphereGeometry(0.35, 12, 12);
                    const lanternMat = new THREE.MeshBasicMaterial({ color: 0xFF9900 });
                    const lantern = new THREE.Mesh(lanternGeo, lanternMat);
                    lantern.position.set(0, cfg.height * 0.7, 0);
                    group.add(lantern);
                } else {
                    // Pagoda Roof Top
                    const roofGeo = new THREE.ConeGeometry(6.5, 2, 4);
                    const roofMat = new THREE.MeshStandardMaterial({ color: 0x991B1B, roughness: 0.3 });
                    const roof = new THREE.Mesh(roofGeo, roofMat);
                    roof.position.y = 1;
                    roof.rotation.y = Math.PI / 4;
                    group.add(roof);
                }

                group.position.y = index * 2.5;
                scene.add(group);
                buildingLayers.push(group);
            });
        }

        function animate() {
            requestAnimationFrame(animate);

            // Interpolate explosion offset
            buildingLayers.forEach((layer, idx) => {
                const targetY = idx * (2.2 + explodeFactor * 4.8);
                layer.position.y += (targetY - layer.position.y) * 0.1;
                layer.rotation.y += 0.0015; // Slow rotation effect
            });

            controls.update();
            renderer.render(scene, camera);
        }

        function onWindowResize() {
            const container = document.getElementById('three-container');
            camera.aspect = container.clientWidth / container.clientHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(container.clientWidth, container.clientHeight);
        }

        // Explode Controls
        document.getElementById('explode-slider').addEventListener('input', (e) => {
            explodeFactor = e.target.value / 100;
            document.getElementById('explode-val').innerText = `${e.target.value}%`;
            isExploded = explodeFactor > 0.05;
            document.getElementById('explode-toggle-btn').innerText = isExploded ? "ON" : "OFF";
        });

        function toggleExplodeState() {
            const btn = document.getElementById('explode-toggle-btn');
            const slider = document.getElementById('explode-slider');
            if (isExploded) {
                explodeFactor = 0;
                slider.value = 0;
                btn.innerText = "OFF";
                isExploded = false;
            } else {
                explodeFactor = 0.5;
                slider.value = 50;
                btn.innerText = "ON";
                isExploded = true;
            }
            document.getElementById('explode-val').innerText = `${slider.value}%`;
            playSlashSound();
        }

        function displayFloorData(data) {
            document.getElementById('floor-tag').innerText = data.tag;
            document.getElementById('floor-title').innerText = data.title;
            document.getElementById('floor-desc').innerText = data.desc;
            document.getElementById('floor-card').classList.remove('hidden');
        }

        function hideFloorCard() {
            document.getElementById('floor-card').classList.add('hidden');
        }

        function setCameraView(view) {
            playSlashSound();
            if (view === 'front') {
                camera.position.set(0, 6, 24);
            } else if (view === 'top') {
                camera.position.set(0, 28, 0.1);
            } else {
                camera.position.set(16, 12, 20);
            }
            controls.target.set(0, 4, 0);
        }

        function reset3DCamera() { setCameraView('iso'); }

        /* ----------------------------------------------------
           4. AUDIO SYNTHESIS
        ---------------------------------------------------- */
        let soundActive = false;
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

        function toggleAudio() {
            soundActive = !soundActive;
            const icon = document.getElementById('audio-icon');
            if (soundActive) {
                icon.className = "fa-solid fa-volume-high text-brand-gold";
                playSlashSound();
            } else {
                icon.className = "fa-solid fa-volume-xmark";
            }
        }

        function playSlashSound() {
            if (!soundActive) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(1100, audioCtx.currentTime);
            osc.frequency.exponentialRampToValueAtTime(120, audioCtx.currentTime + 0.12);

            gain.gain.setValueAtTime(0.25, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.12);

            osc.connect(gain);
            gain.connect(audioCtx.destination);

            osc.start();
            osc.stop(audioCtx.currentTime + 0.12);
        }

        /* ----------------------------------------------------
           5. CANVAS EMBERS & PARTICLES
        ---------------------------------------------------- */
        const wCanvas = document.getElementById('wisteria-canvas');
        const wCtx = wCanvas.getContext('2d');
        let particles = [];

        function resizeCanvas() {
            wCanvas.width = window.innerWidth;
            wCanvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        for (let i = 0; i < 45; i++) {
            particles.push({
                x: Math.random() * wCanvas.width,
                y: Math.random() * wCanvas.height,
                radius: Math.random() * 3 + 1,
                speedY: Math.random() * 0.8 + 0.3,
                speedX: Math.random() * 0.4 - 0.2,
                color: Math.random() > 0.4 ? '#E5A93C' : '#DC2626'
            });
        }

        function renderParticles() {
            wCtx.clearRect(0, 0, wCanvas.width, wCanvas.height);
            particles.forEach(p => {
                wCtx.fillStyle = p.color;
                wCtx.beginPath();
                wCtx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                wCtx.fill();

                p.y += p.speedY;
                p.x += p.speedX;

                if (p.y > wCanvas.height) {
                    p.y = -10;
                    p.x = Math.random() * wCanvas.width;
                }
            });
            requestAnimationFrame(renderParticles);
        }
        renderParticles();

        window.onload = function() {
            init3D();
        };
    </script>
</body>
</html>
