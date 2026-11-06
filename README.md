<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>كشك كافيتيريا الجامعة - Kiosk</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Cairo -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800;900&display=swap">
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
  
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            brand: {
              DEFAULT: '#1F5B4A',
              dark: '#143C31',
              light: '#2D826A',
              soft: '#E8F2EE'
            },
            amber: {
              DEFAULT: '#D97706',
              warm: '#E08A1E',
              glow: '#FFF7ED'
            },
            surface: '#F8FAF9'
          },
          fontFamily: {
            cairo: ['Cairo', 'sans-serif']
          }
        }
      }
    }
  </script>

  <style>
    body {
      font-family: 'Cairo', sans-serif;
      user-select: none;
      -webkit-user-select: none;
      background-color: #0F172A;
      touch-action: manipulation;
    }

    /* Custom Scrollbar for touch screens */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #F1F5F9;
      border-radius: 8px;
    }
    ::-webkit-scrollbar-thumb {
      background: #CBD5E1;
      border-radius: 8px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94A3B8;
    }

    /* Pulse & Animation effects */
    @keyframes pulse-ring {
      0% { transform: scale(0.95); opacity: 0.8; }
      50% { transform: scale(1.05); opacity: 1; }
      100% { transform: scale(0.95); opacity: 0.8; }
    }
    .pulse-btn {
      animation: pulse-ring 2s infinite ease-in-out;
    }

    .glass-card {
      background: rgba(255, 255, 255, 0.95);
      backdrop-filter: blur(10px);
    }
  </style>
</head>
<body class="h-screen w-screen overflow-hidden flex items-center justify-center p-0 md:p-4 bg-slate-900">

  <!-- Main Kiosk Frame (Emulating physical kiosk aspect ratio) -->
  <div id="kiosk-container" class="w-full h-full max-w-2xl md:max-w-md lg:max-w-lg xl:max-w-xl h-full md:h-[96vh] bg-surface rounded-none md:rounded-3xl shadow-2xl flex flex-col overflow-hidden relative border-0 md:border-4 border-slate-700">

    <!-- Top Header -->
    <header class="bg-brand text-white px-6 py-4 flex items-center justify-between shadow-lg z-20 shrink-0">
      <div class="flex items-center gap-3">
        <div class="w-11 h-11 bg-white/10 rounded-2xl flex items-center justify-center border border-white/20 shadow-inner">
          <i class="fa-solid fa-graduation-cap text-amber-warm text-2xl"></i>
        </div>
        <div>
          <h1 class="text-xl font-black tracking-wide leading-tight">كافيتيريا الجامعة</h1>
          <p class="text-xs text-brand-soft/80 font-semibold">نظام الطلب الذاتي السريع</p>
        </div>
      </div>

      <!-- Action Button / Reset -->
      <button id="cancel-btn" onclick="resetOrderModal()" class="hidden bg-white/10 hover:bg-white/20 active:scale-95 text-xs font-bold px-3 py-2 rounded-xl border border-white/20 transition-all flex items-center gap-1.5">
        <i class="fa-solid fa-rotate-left text-amber-warm"></i>
        <span>إلغاء الطلب</span>
      </button>
    </header>

    <!-- Stepper Navigation (Visible in Screen 2, 3, 4) -->
    <nav id="stepper" class="bg-white border-b border-slate-200 px-4 py-2.5 shadow-sm z-10 hidden shrink-0">
      <div class="flex items-center justify-between max-w-md mx-auto">
        <!-- Step 1 -->
        <div class="flex flex-col items-center step-item" data-step="2">
          <div class="step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-brand text-white shadow-md">1</div>
          <span class="text-[11px] font-bold mt-1 text-brand">الائحة</span>
        </div>
        <div class="h-0.5 flex-1 bg-slate-200 mx-1 step-line" data-line="2"></div>

        <!-- Step 2 -->
        <div class="flex flex-col items-center step-item" data-step="3">
          <div class="step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-slate-200 text-slate-500">2</div>
          <span class="text-[11px] font-bold mt-1 text-slate-400">المراجعة</span>
        </div>
        <div class="h-0.5 flex-1 bg-slate-200 mx-1 step-line" data-line="3"></div>

        <!-- Step 3 -->
        <div class="flex flex-col items-center step-item" data-step="4">
          <div class="step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-slate-200 text-slate-500">3</div>
          <span class="text-[11px] font-bold mt-1 text-slate-400">الدفع</span>
        </div>
      </div>
    </nav>

    <!-- Main Dynamic Content Container -->
    <main id="app-content" class="flex-1 overflow-y-auto relative flex flex-col">
      <!-- Screens rendered via JS -->
    </main>

    <!-- Floating Bottom Bar (Menu Screen Only) -->
    <footer id="bottom-bar" class="hidden bg-white border-t border-slate-200 p-4 shadow-2xl z-20 shrink-0">
      <div class="flex items-center justify-between gap-4">
        <div>
          <div class="text-xs font-bold text-slate-500 flex items-center gap-1">
            <span>عدد العناصر:</span>
            <span id="cart-count-badge" class="bg-brand-soft text-brand font-black px-2 py-0.5 rounded-full text-xs">0</span>
          </div>
          <div class="text-2xl font-black text-slate-800 tracking-tight" id="cart-total-price">0.000 <span class="text-sm font-bold text-amber-warm">د.أ</span></div>
        </div>

        <button id="review-cart-btn" onclick="goToScreen(3)" disabled class="bg-slate-300 text-slate-500 font-extrabold px-6 py-3.5 rounded-2xl flex items-center gap-3 transition-all duration-200 active:scale-95 disabled:opacity-50 disabled:pointer-events-none shadow-lg">
          <span>مراجعة السلة</span>
          <i class="fa-solid fa-arrow-left text-sm"></i>
        </button>
      </div>
    </footer>

  </div>

  <!-- Notification Toast -->
  <div id="toast" class="fixed top-6 left-1/2 -translate-x-1/2 bg-slate-900/90 text-white text-sm font-bold px-5 py-3 rounded-2xl shadow-2xl flex items-center gap-3 opacity-0 pointer-events-none transition-all duration-300 z-50 border border-slate-700">
    <i class="fa-solid fa-circle-check text-emerald-400 text-lg" id="toast-icon"></i>
    <span id="toast-msg">تمت الإضافة بنجاح</span>
  </div>

  <!-- Reset Confirmation Modal -->
  <div id="reset-modal" class="fixed inset-0 bg-slate-950/70 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl p-6 max-w-xs w-full text-center shadow-2xl border border-slate-100">
      <div class="w-16 h-16 bg-amber-100 text-amber-600 rounded-2xl flex items-center justify-center mx-auto mb-4 text-2xl">
        <i class="fa-solid fa-triangle-exclamation"></i>
      </div>
      <h3 class="text-xl font-black text-slate-800 mb-2">إلغاء الطلب؟</h3>
      <p class="text-xs text-slate-500 font-medium mb-6">سيتم تفريغ السلة والعودة للشاشة الرئيسية.</p>
      <div class="flex gap-2">
        <button onclick="confirmReset()" class="flex-1 bg-rose-600 hover:bg-rose-700 active:scale-95 text-white font-bold py-3 rounded-xl text-sm shadow-md">نعم، إلغاء</button>
        <button onclick="closeResetModal()" class="flex-1 bg-slate-100 hover:bg-slate-200 active:scale-95 text-slate-700 font-bold py-3 rounded-xl text-sm">تراجع</button>
      </div>
    </div>
  </div>

  <!-- APP LOGIC & DATA -->
  <script>
    // --- Data Store ---
    const CATEGORIES = [
      { id: 'hot', name: 'مشروبات ساخنة', icon: 'fa-mug-hot' },
      { id: 'cold', name: 'مشروبات باردة', icon: 'fa-glass-water' },
      { id: 'bake', name: 'معجنات ومأكولات', icon: 'fa-bread-slice' },
      { id: 'sweet', name: 'حلويات', icon: 'fa-cookie-bite' }
    ];

    const PRODUCTS = [
      // Hot
      { id: 'h1', category: 'hot', name: 'قهوة تركية', price: 1.500, desc: 'قهوة عربية أصيلة مطحونة طازجة', icon: '☕' },
      { id: 'h2', category: 'hot', name: 'كابتشينو', price: 2.500, desc: 'إسبريسو غني مع رغوة الحليب الكثيفة', icon: '☕' },
      { id: 'h3', category: 'hot', name: 'لاتيه', price: 2.500, desc: 'إسبريسو متوازن مع حليب مبخر لطيف', icon: '🥛' },
      { id: 'h4', category: 'hot', name: 'شاي أحمر/أخضر', price: 1.000, desc: 'شاي فاخر مع اختيارك من النعناع', icon: '🍵' },
      
      // Cold
      { id: 'c1', category: 'cold', name: 'عصير برتقال طازج', price: 2.500, desc: 'برتقال طبيعي معصور طازجاً', icon: '🍊' },
      { id: 'c2', category: 'cold', name: 'ليموناضة بالنعناع', price: 2.500, desc: 'منعش ومحضر يومياً في الكافيتيريا', icon: '🍋' },
      { id: 'c3', category: 'cold', name: 'آيسد لاتيه', price: 3.500, desc: 'قهوة مثلجة مع حليب وشراب سيروب', icon: '🧋' },
      { id: 'c4', category: 'cold', name: 'ماء نقي', price: 0.500, desc: 'عبوة مياه معدنية 500 مل', icon: '💧' },

      // Bakery
      { id: 'b1', category: 'bake', name: 'مناقيش زعتر', price: 2.000, desc: 'زعتر بلدي مع زيت زيتون نقي', icon: '🥙' },
      { id: 'b2', category: 'bake', name: 'مناقيش جبنة', price: 2.000, desc: 'جبنة عكّاوي وموزاريلا محمرة', icon: '🧀' },
      { id: 'b3', category: 'bake', name: 'بيتزا مارغريتا', price: 3.500, desc: 'صلصة طماطم وشبنة موزاريلا', icon: '🍕' },
      { id: 'b4', category: 'bake', name: 'بطاطا مقلية', price: 2.000, desc: 'مقرمشة ومبهرة مع صلصة الكاتشب', icon: '🍟' },

      // Sweets
      { id: 's1', category: 'sweet', name: 'تشيز كيك التوت', price: 3.000, desc: 'طبقة غنية من الجبن مع التوت الطبيعي', icon: '🍰' },
      { id: 's2', category: 'sweet', name: 'براونيز الشوكولاتة', price: 2.500, desc: 'قطعة شوكولاتة غنية ودافئة', icon: '🍫' }
    ];

    const PAYMENT_METHODS = [
      { id: 'card', name: 'بطاقة بنكية', desc: 'دفع سريع عبر الفيزا / ماستركارد / مدى', icon: 'fa-credit-card', highlight: 'تلامسي NFC' },
      { id: 'wallet', name: 'محفظة إلكترونية', desc: 'زين كاش، أورانج ماني، أو أي محفظة', icon: 'fa-wallet', highlight: 'رمز QR' },
      { id: 'cash', name: 'نقداً عند الاستلام', desc: 'الدفع مباشرة لدى موظف تسليم الطلبات', icon: 'fa-money-bill-wave', highlight: 'عند النافذة' }
    ];

    // --- State Object ---
    let state = {
      screen: 1,           // 1: Welcome, 2: Menu, 3: Cart Review, 4: Payment, 5: Confirmation
      selectedCategory: 'hot',
      cart: {},            // { item_id: quantity }
      selectedPayment: 'card',
      orderNum: ''
    };

    // --- Helper Functions ---
    const formatJOD = (amount) => amount.toFixed(3);

    const getCartTotal = () => {
      return Object.entries(state.cart).reduce((total, [id, qty]) => {
        const item = PRODUCTS.find(p => p.id === id);
        return total + (item ? item.price * qty : 0);
      }, 0);
    };

    const getCartCount = () => {
      return Object.values(state.cart).reduce((a, b) => a + b, 0);
    };

    const playBeep = () => {
      try {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.frequency.value = 587.33; // D5 note
        gain.gain.setValueAtTime(0.08, ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + 0.15);
        osc.start();
        osc.stop(ctx.currentTime + 0.15);
      } catch(e) {}
    };

    const showToast = (message) => {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toast-msg');
      toastMsg.textContent = message;
      toast.classList.remove('opacity-0', 'pointer-events-none');
      toast.classList.add('opacity-100');
      setTimeout(() => {
        toast.classList.remove('opacity-100');
        toast.classList.add('opacity-0', 'pointer-events-none');
      }, 1600);
    };

    // --- State Actions ---
    function updateQty(itemId, change) {
      playBeep();
      const currentQty = state.cart[itemId] || 0;
      const newQty = Math.max(0, currentQty + change);

      if (newQty > 0) {
        state.cart[itemId] = newQty;
      } else {
        delete state.cart[itemId];
      }

      renderUI();
    }

    function removeItem(itemId) {
      playBeep();
      delete state.cart[itemId];
      showToast('تم إزالة العنصر من السلة');
      renderUI();
    }

    function selectCategory(catId) {
      playBeep();
      state.selectedCategory = catId;
      renderUI();
    }

    function selectPayment(methodId) {
      playBeep();
      state.selectedPayment = methodId;
      renderUI();
    }

    function goToScreen(screenNum) {
      playBeep();
      state.screen = screenNum;
      
      if (screenNum === 5) {
        // Generate random order number between 100 and 999
        state.orderNum = String(Math.floor(100 + Math.random() * 900));
      }

      renderUI();
    }

    function resetOrderModal() {
      if (getCartCount() === 0 && state.screen === 2) {
        confirmReset();
        return;
      }
      document.getElementById('reset-modal').classList.remove('hidden');
    }

    function closeResetModal() {
      document.getElementById('reset-modal').classList.add('hidden');
    }

    function confirmReset() {
      closeResetModal();
      state = {
        screen: 1,
        selectedCategory: 'hot',
        cart: {},
        selectedPayment: 'card',
        orderNum: ''
      };
      renderUI();
    }

    // --- Render Navigation & Stepper ---
    function renderNavigation() {
      const stepper = document.getElementById('stepper');
      const cancelBtn = document.getElementById('cancel-btn');
      const bottomBar = document.getElementById('bottom-bar');

      // Top Cancel Button visibility
      if (state.screen > 1 && state.screen < 5) {
        cancelBtn.classList.remove('hidden');
      } else {
        cancelBtn.classList.add('hidden');
      }

      // Stepper visibility
      if (state.screen >= 2 && state.screen <= 4) {
        stepper.classList.remove('hidden');
        
        // Update Stepper Items
        document.querySelectorAll('.step-item').forEach(el => {
          const stepNum = parseInt(el.getAttribute('data-step'));
          const iconEl = el.querySelector('.step-icon');
          const textEl = el.querySelector('span');

          if (stepNum < state.screen) {
            // Done
            iconEl.className = 'step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-emerald-600 text-white shadow-md';
            iconEl.innerHTML = '<i class="fa-solid fa-check"></i>';
            textEl.className = 'text-[11px] font-bold mt-1 text-emerald-700';
          } else if (stepNum === state.screen) {
            // Current
            iconEl.className = 'step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-amber-warm text-white shadow-md ring-4 ring-amber-100';
            iconEl.innerHTML = stepNum - 1;
            textEl.className = 'text-[11px] font-bold mt-1 text-amber-600';
          } else {
            // Future
            iconEl.className = 'step-icon w-8 h-8 rounded-full flex items-center justify-center text-xs font-extrabold bg-slate-200 text-slate-500';
            iconEl.innerHTML = stepNum - 1;
            textEl.className = 'text-[11px] font-bold mt-1 text-slate-400';
          }
        });

        // Update Stepper Lines
        document.querySelectorAll('.step-line').forEach(el => {
          const lineNum = parseInt(el.getAttribute('data-line'));
          if (lineNum < state.screen) {
            el.className = 'h-0.5 flex-1 bg-emerald-500 mx-1 step-line';
          } else {
            el.className = 'h-0.5 flex-1 bg-slate-200 mx-1 step-line';
          }
        });

      } else {
        stepper.classList.add('hidden');
      }

      // Bottom Bar visibility (Menu Screen only)
      if (state.screen === 2) {
        bottomBar.classList.remove('hidden');
        
        const total = getCartTotal();
        const count = getCartCount();
        
        document.getElementById('cart-count-badge').textContent = count;
        document.getElementById('cart-total-price').innerHTML = `${formatJOD(total)} <span class="text-sm font-bold text-amber-warm">د.أ</span>`;

        const reviewBtn = document.getElementById('review-cart-btn');
        if (count > 0) {
          reviewBtn.disabled = false;
          reviewBtn.className = 'bg-amber-warm hover:bg-amber-600 active:scale-95 text-white font-extrabold px-6 py-3.5 rounded-2xl flex items-center gap-3 transition-all duration-200 shadow-lg shadow-amber-500/25 pulse-btn';
        } else {
          reviewBtn.disabled = true;
          reviewBtn.className = 'bg-slate-200 text-slate-400 font-extrabold px-6 py-3.5 rounded-2xl flex items-center gap-3 transition-all duration-200 disabled:opacity-60 disabled:pointer-events-none';
        }
      } else {
        bottomBar.classList.add('hidden');
      }
    }

    // --- Render Screen Contents ---
    function renderScreen() {
      const container = document.getElementById('app-content');

      // ----------------------------------------------------
      // SCREEN 1: WELCOME SCREEN
      // ----------------------------------------------------
      if (state.screen === 1) {
        container.innerHTML = `
          <div class="flex-1 flex flex-col items-center justify-between p-6 text-center bg-gradient-to-b from-brand-soft/50 via-surface to-amber-glow/30 relative overflow-hidden">
            <!-- Background Decorative Elements -->
            <div class="absolute -top-12 -right-12 w-48 h-48 bg-brand/5 rounded-full blur-2xl"></div>
            <div class="absolute -bottom-12 -left-12 w-48 h-48 bg-amber-warm/10 rounded-full blur-2xl"></div>

            <div class="my-auto py-6 flex flex-col items-center max-w-sm w-full z-10">
              <div class="w-28 h-28 bg-white rounded-3xl shadow-xl border-4 border-white flex items-center justify-center mb-6 text-brand relative group">
                <i class="fa-solid fa-utensils text-5xl text-brand"></i>
                <span class="absolute -top-2 -right-2 bg-amber-warm text-white w-8 h-8 rounded-full flex items-center justify-center font-black text-sm shadow-md">
                  <i class="fa-solid fa-star text-xs"></i>
                </span>
              </div>

              <h2 class="text-3xl font-black text-slate-800 mb-2 leading-snug">
                أهلاً بك في <br>
                <span class="text-brand">كافيتيريا الجامعة</span>
              </h2>
              <p class="text-sm font-semibold text-slate-500 mb-8 leading-relaxed max-w-xs">
                احصل على مشروبك ووجبتك المفضلة بسرعة بدون انتظار في الدور!
              </p>

              <!-- 3 Easy Steps Showcase -->
              <div class="grid grid-cols-3 gap-2 w-full mb-8">
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col items-center">
                  <div class="w-8 h-8 rounded-xl bg-brand-soft text-brand flex items-center justify-center mb-1 text-sm font-bold">1</div>
                  <span class="text-xs font-bold text-slate-700">اختر الوجبة</span>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col items-center">
                  <div class="w-8 h-8 rounded-xl bg-amber-100 text-amber-700 flex items-center justify-center mb-1 text-sm font-bold">2</div>
                  <span class="text-xs font-bold text-slate-700">حدد الدفع</span>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col items-center">
                  <div class="w-8 h-8 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center mb-1 text-sm font-bold">3</div>
                  <span class="text-xs font-bold text-slate-700">استلم رقمك</span>
                </div>
              </div>

              <!-- Start Button -->
              <button onclick="goToScreen(2)" class="w-full bg-amber-warm hover:bg-amber-600 active:scale-95 text-white font-black text-xl py-5 rounded-2xl shadow-xl shadow-amber-500/30 flex items-center justify-center gap-3 transition-all duration-200 border-2 border-amber-400 pulse-btn">
                <span>ابدأ الطلب الآن</span>
                <i class="fa-solid fa-arrow-left text-lg"></i>
              </button>
            </div>

            <div class="text-xs font-bold text-slate-400 z-10 flex items-center gap-2">
              <i class="fa-solid fa-hand-pointer text-amber-warm"></i>
              <span>الشاشة تعمل باللمس المباشر</span>
            </div>
          </div>
        `;
        return;
      }

      // ----------------------------------------------------
      // SCREEN 2: MENU & CATALOG SCREEN
      // ----------------------------------------------------
      if (state.screen === 2) {
        const filteredProducts = PRODUCTS.filter(p => p.category === state.selectedCategory);

        container.innerHTML = `
          <div class="flex-1 flex flex-col h-full bg-surface">
            <!-- Category Tabs Navigation -->
            <div class="bg-white border-b border-slate-200 px-3 py-2.5 flex gap-2 overflow-x-auto shrink-0 shadow-sm">
              ${CATEGORIES.map(cat => `
                <button onclick="selectCategory('${cat.id}')" class="flex-1 min-w-[110px] py-2.5 px-3 rounded-xl font-bold text-xs flex flex-col items-center gap-1 transition-all active:scale-95 border ${cat.id === state.selectedCategory ? 'bg-brand text-white border-brand shadow-md shadow-brand/20' : 'bg-slate-50 text-slate-600 border-slate-200 hover:bg-slate-100'}">
                  <i class="fa-solid ${cat.icon} text-base"></i>
                  <span>${cat.name}</span>
                </button>
              `).join('')}
            </div>

            <!-- Product Grid -->
            <div class="flex-1 p-4 overflow-y-auto">
              <div class="grid grid-cols-2 gap-3.5">
                ${filteredProducts.map(prod => {
                  const qty = state.cart[prod.id] || 0;
                  return `
                    <div class="bg-white rounded-2xl p-3.5 border border-slate-200 shadow-sm flex flex-col justify-between hover:shadow-md transition-shadow relative">
                      ${qty > 0 ? `<div class="absolute -top-2 -right-2 bg-amber-warm text-white font-black text-xs w-6 h-6 rounded-full flex items-center justify-center shadow-md ring-2 ring-white">${qty}</div>` : ''}

                      <div>
                        <div class="text-3xl mb-2 text-center bg-slate-50 p-3 rounded-xl border border-slate-100">${prod.icon}</div>
                        <h3 class="font-extrabold text-slate-800 text-sm leading-tight mb-1">${prod.name}</h3>
                        <p class="text-[11px] font-semibold text-slate-400 line-clamp-2 mb-3">${prod.desc}</p>
                      </div>

                      <div>
                        <div class="text-brand font-black text-base mb-2">
                          ${formatJOD(prod.price)} <span class="text-xs font-bold text-amber-warm">د.أ</span>
                        </div>

                        ${qty === 0 ? `
                          <button onclick="updateQty('${prod.id}', 1)" class="w-full bg-brand-soft hover:bg-brand text-brand hover:text-white active:scale-95 font-black py-2.5 rounded-xl text-xs transition-all flex items-center justify-center gap-1.5 border border-brand/20">
                            <i class="fa-solid fa-plus"></i>
                            <span>إضافة</span>
                          </button>
                        ` : `
                          <div class="flex items-center justify-between bg-slate-100 rounded-xl p-1 border border-slate-200">
                            <button onclick="updateQty('${prod.id}', -1)" class="w-8 h-8 bg-white text-rose-600 rounded-lg shadow-sm font-black flex items-center justify-center active:scale-95 text-sm">
                              <i class="fa-solid fa-minus"></i>
                            </button>
                            <span class="font-black text-slate-800 text-sm px-2">${qty}</span>
                            <button onclick="updateQty('${prod.id}', 1)" class="w-8 h-8 bg-brand text-white rounded-lg shadow-sm font-black flex items-center justify-center active:scale-95 text-sm">
                              <i class="fa-solid fa-plus"></i>
                            </button>
                          </div>
                        `}
                      </div>
                    </div>
                  `;
                }).join('')}
              </div>
            </div>
          </div>
        `;
        return;
      }

      // ----------------------------------------------------
      // SCREEN 3: CART REVIEW SCREEN
      // ----------------------------------------------------
      if (state.screen === 3) {
        const cartEntries = Object.entries(state.cart);

        container.innerHTML = `
          <div class="flex-1 flex flex-col p-4 bg-surface h-full">
            <div class="flex items-center justify-between mb-4">
              <h2 class="text-xl font-black text-slate-800 flex items-center gap-2">
                <i class="fa-solid fa-basket-shopping text-brand"></i>
                <span>مراجعة الطلب</span>
              </h2>
              <span class="text-xs font-bold bg-slate-200 text-slate-600 px-3 py-1 rounded-full">
                ${getCartCount()} أصناف
              </span>
            </div>

            <!-- Items List -->
            <div class="flex-1 overflow-y-auto space-y-3 pr-1">
              ${cartEntries.length === 0 ? `
                <div class="h-full flex flex-col items-center justify-center text-center p-6 text-slate-400">
                  <i class="fa-solid fa-cart-flatbed text-5xl mb-3 text-slate-300"></i>
                  <p class="font-extrabold text-slate-600 mb-1">السلة فارغة!</p>
                  <p class="text-xs text-slate-400 mb-4">لم تقم بإضافة أي صنف للطلب بعد</p>
                  <button onclick="goToScreen(2)" class="bg-brand text-white font-bold text-xs px-5 py-2.5 rounded-xl shadow-md">العودة للقائمة</button>
                </div>
              ` : cartEntries.map(([id, qty]) => {
                const prod = PRODUCTS.find(p => p.id === id);
                if (!prod) return '';
                const itemTotal = prod.price * qty;

                return `
                  <div class="bg-white rounded-2xl p-3.5 border border-slate-200 shadow-sm flex items-center justify-between gap-3">
                    <div class="text-2xl p-2 bg-slate-50 rounded-xl border border-slate-100 shrink-0">${prod.icon}</div>
                    
                    <div class="flex-1 min-w-0">
                      <h4 class="font-extrabold text-slate-800 text-sm truncate">${prod.name}</h4>
                      <p class="text-xs font-extrabold text-brand">
                        ${formatJOD(itemTotal)} <span class="text-[10px] text-amber-warm">د.أ</span>
                        <span class="text-[10px] text-slate-400 font-normal">(${formatJOD(prod.price)} / للحبة)</span>
                      </p>
                    </div>

                    <!-- Quantity Control -->
                    <div class="flex items-center gap-2 shrink-0">
                      <div class="flex items-center bg-slate-100 rounded-xl p-1 border border-slate-200">
                        <button onclick="updateQty('${prod.id}', -1)" class="w-7 h-7 bg-white text-slate-700 rounded-lg shadow-sm font-black flex items-center justify-center active:scale-95 text-xs">
                          <i class="fa-solid ${qty === 1 ? 'fa-trash-can text-rose-500' : 'fa-minus'}"></i>
                        </button>
                        <span class="font-black text-slate-800 text-xs px-2.5">${qty}</span>
                        <button onclick="updateQty('${prod.id}', 1)" class="w-7 h-7 bg-brand text-white rounded-lg shadow-sm font-black flex items-center justify-center active:scale-95 text-xs">
                          <i class="fa-solid fa-plus"></i>
                        </button>
                      </div>

                      <button onclick="removeItem('${prod.id}')" class="text-slate-400 hover:text-rose-600 p-2 text-sm transition-colors">
                        <i class="fa-solid fa-xmark"></i>
                      </button>
                    </div>
                  </div>
                `;
              }).join('')}
            </div>

            <!-- Summary & Actions -->
            ${cartEntries.length > 0 ? `
              <div class="mt-4 pt-3 border-t border-slate-200 bg-white rounded-2xl p-4 shadow-md border border-slate-100">
                <div class="space-y-1.5 text-xs font-semibold text-slate-500 mb-3">
                  <div class="flex justify-between">
                    <span>المجموع الفرعي</span>
                    <span class="font-extrabold text-slate-700">${formatJOD(getCartTotal())} د.أ</span>
                  </div>
                  <div class="flex justify-between">
                    <span>الضريبة المضافة (0%)</span>
                    <span class="font-extrabold text-slate-700">0.000 د.أ</span>
                  </div>
                  <div class="flex justify-between text-base font-black text-slate-800 pt-2 border-t border-slate-100">
                    <span>المجموع الإجمالي</span>
                    <span class="text-brand font-black text-lg">${formatJOD(getCartTotal())} <span class="text-xs font-bold text-amber-warm">د.أ</span></span>
                  </div>
                </div>

                <div class="flex gap-2.5">
                  <button onclick="goToScreen(2)" class="flex-1 bg-slate-100 hover:bg-slate-200 active:scale-95 text-slate-700 font-extrabold py-3.5 rounded-xl text-xs flex items-center justify-center gap-2 border border-slate-200">
                    <i class="fa-solid fa-plus"></i>
                    <span>إضافة المزيد</span>
                  </button>
                  <button onclick="goToScreen(4)" class="flex-1 bg-amber-warm hover:bg-amber-600 active:scale-95 text-white font-extrabold py-3.5 rounded-xl text-xs flex items-center justify-center gap-2 shadow-lg shadow-amber-500/20">
                    <span>متابعة للدفع</span>
                    <i class="fa-solid fa-arrow-left"></i>
                  </button>
                </div>
              </div>
            ` : ''}
          </div>
        `;
        return;
      }

      // ----------------------------------------------------
      // SCREEN 4: PAYMENT SELECTION SCREEN
      // ----------------------------------------------------
      if (state.screen === 4) {
        container.innerHTML = `
          <div class="flex-1 flex flex-col p-4 bg-surface h-full justify-between">
            <div>
              <div class="mb-4">
                <h2 class="text-xl font-black text-slate-800 mb-1">اختر طريقة الدفع</h2>
                <p class="text-xs text-slate-500 font-semibold">حدد الوسيلة المناسبة لك لإتمام الطلب</p>
              </div>

              <!-- Payment Total Highlight Card -->
              <div class="bg-gradient-to-r from-brand to-brand-dark text-white p-4 rounded-2xl mb-5 shadow-lg flex items-center justify-between">
                <div>
                  <span class="text-xs text-brand-soft font-semibold block">المبلغ المطلوب سداده</span>
                  <span class="text-2xl font-black">${formatJOD(getCartTotal())} <span class="text-sm font-bold text-amber-warm">دينار أردني</span></span>
                </div>
                <div class="w-10 h-10 bg-white/10 rounded-xl flex items-center justify-center border border-white/20">
                  <i class="fa-solid fa-receipt text-amber-warm text-xl"></i>
                </div>
              </div>

              <!-- Methods Options -->
              <div class="space-y-3">
                ${PAYMENT_METHODS.map(m => `
                  <div onclick="selectPayment('${m.id}')" class="p-4 rounded-2xl border-2 transition-all cursor-pointer flex items-center justify-between ${state.selectedPayment === m.id ? 'bg-amber-glow/60 border-amber-warm shadow-md ring-2 ring-amber-200' : 'bg-white border-slate-200 hover:border-slate-300'}">
                    <div class="flex items-center gap-3.5">
                      <div class="w-12 h-12 rounded-xl flex items-center justify-center text-xl shrink-0 ${state.selectedPayment === m.id ? 'bg-amber-warm text-white shadow-md' : 'bg-slate-100 text-slate-600'}">
                        <i class="fa-solid ${m.icon}"></i>
                      </div>
                      <div>
                        <div class="flex items-center gap-2">
                          <h4 class="font-extrabold text-slate-800 text-sm">${m.name}</h4>
                          <span class="text-[10px] font-bold px-2 py-0.5 rounded-md ${state.selectedPayment === m.id ? 'bg-amber-200 text-amber-800' : 'bg-slate-100 text-slate-500'}">${m.highlight}</span>
                        </div>
                        <p class="text-xs text-slate-400 font-semibold mt-0.5">${m.desc}</p>
                      </div>
                    </div>

                    <div class="w-6 h-6 rounded-full border-2 flex items-center justify-center shrink-0 ${state.selectedPayment === m.id ? 'border-amber-warm bg-amber-warm text-white' : 'border-slate-300'}">
                      ${state.selectedPayment === m.id ? '<i class="fa-solid fa-check text-xs"></i>' : ''}
                    </div>
                  </div>
                `).join('')}
              </div>
            </div>

            <!-- Buttons Footer -->
            <div class="flex gap-2.5 pt-4 border-t border-slate-200 mt-auto">
              <button onclick="goToScreen(3)" class="flex-1 bg-slate-100 hover:bg-slate-200 active:scale-95 text-slate-700 font-extrabold py-4 rounded-xl text-xs flex items-center justify-center gap-2 border border-slate-200">
                <i class="fa-solid fa-arrow-right"></i>
                <span>رجوع للمراجعة</span>
              </button>
              <button onclick="goToScreen(5)" class="flex-2 bg-emerald-600 hover:bg-emerald-700 active:scale-95 text-white font-extrabold py-4 rounded-xl text-sm flex items-center justify-center gap-2 shadow-lg shadow-emerald-600/30 pulse-btn">
                <i class="fa-solid fa-check-double"></i>
                <span>تأكيد ودفع الطلب</span>
              </button>
            </div>
          </div>
        `;
        return;
      }

      // ----------------------------------------------------
      // SCREEN 5: ORDER CONFIRMATION SCREEN
      // ----------------------------------------------------
      if (state.screen === 5) {
        const payMethodObj = PAYMENT_METHODS.find(p => p.id === state.selectedPayment);

        container.innerHTML = `
          <div class="flex-1 flex flex-col items-center justify-center p-6 text-center bg-gradient-to-b from-brand-soft/40 to-surface h-full">
            <div class="w-20 h-20 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center text-4xl mb-4 shadow-lg ring-8 ring-emerald-50 animate-bounce">
              <i class="fa-solid fa-circle-check"></i>
            </div>

            <h2 class="text-2xl font-black text-slate-800 mb-1">تم استلام طلبك بنجاح!</h2>
            <p class="text-xs text-slate-500 font-semibold mb-6">بالهناء والشفاء، جاري تحضير طلبك الآن</p>

            <!-- Big Order Number Token Card -->
            <div class="bg-white rounded-3xl p-6 border-4 border-amber-warm shadow-2xl max-w-xs w-full mb-6 relative overflow-hidden">
              <div class="bg-amber-warm text-white text-[11px] font-black py-1 px-4 rounded-full uppercase tracking-wider inline-block mb-2 shadow-sm">
                رقم الطلب الخاص بك
              </div>
              <div class="text-6xl font-black text-brand tracking-widest my-2 font-mono">
                #${state.orderNum}
              </div>
              <p class="text-xs font-bold text-slate-500 mt-2">
                يرجى الانتظار حتى يُنادى رقمك عند نافذة الاستلام
              </p>
            </div>

            <!-- Receipt Summary -->
            <div class="bg-white/80 rounded-2xl p-4 border border-slate-200 max-w-xs w-full text-xs space-y-2 mb-8 text-slate-600 font-semibold">
              <div class="flex justify-between">
                <span>طريقة الدفع:</span>
                <span class="font-extrabold text-slate-800">${payMethodObj ? payMethodObj.name : ''}</span>
              </div>
              <div class="flex justify-between">
                <span>المبلغ الإجمالي:</span>
                <span class="font-extrabold text-brand">${formatJOD(getCartTotal())} د.أ</span>
              </div>
              <div class="flex justify-between">
                <span>عدد المواد:</span>
                <span class="font-extrabold text-slate-800">${getCartCount()} أصناف</span>
              </div>
            </div>

            <!-- New Order Button -->
            <button onclick="confirmReset()" class="w-full max-w-xs bg-brand hover:bg-brand-dark active:scale-95 text-white font-black py-4 rounded-2xl shadow-xl shadow-brand/30 flex items-center justify-center gap-2 transition-all">
              <i class="fa-solid fa-rotate-right"></i>
              <span>البدء بطلب جديد</span>
            </button>
          </div>
        `;
        return;
      }
    }

    // Main UI Render Function
    function renderUI() {
      renderNavigation();
      renderScreen();
    }

    // Initialize Application
    window.addEventListener('DOMContentLoaded', () => {
      renderUI();
    });
  </script>
</body>
</html>
